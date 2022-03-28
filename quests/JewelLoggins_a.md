Let’s ping for further IPv6 hosts

``` bash
ping6 -I 2604:6000:1528:cd:d55a:f8a7:d30a:3 ff02::1%eth0
```

We have a few candidates

``` 
2604:6000:1528:cd:d55a:f8a7:d30a:1
2604:6000:1528:cd:d55a:f8a7:d30a:2
2604:6000:1528:cd:d55a:f8a7:d30a:3
2604:6000:1528:cd:d55a:f8a7:d30a:e405
```

Let’s see what’s running on that last machine

``` bash
nmap -6 2604:6000:1528:cd:d55a:f8a7:d30a:e405
```

Port 80 and 9000 open so we use curl and Netcat to inspect them

``` bash
curl -6 -g 'http://[2604:6000:1528:cd:d55a:f8a7:d30a:e405]:80/'
netcat -6 '2604:6000:1528:cd:d55a:f8a7:d30a:e405' 9000
```

The second port gives us the passphrase

*PieceOnEarth*
