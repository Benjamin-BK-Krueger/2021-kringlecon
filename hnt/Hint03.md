# Hints for Objective 3: Thaw Frost Tower's Entrance

<br>[Go back](../Hints.md)

## Overview
Requested by Greasy GopherGuts, found in The North Pole - The North Pole
<br>
**Task**: Grepping for Gold    
**Description**: 

```
Howdy howdy!  Mind helping me with this homew- er, challenge?
Someone ran nmap -oG on a big network and produced this bigscan.gnmap file.
The quizme program has the questions and hints and, incidentally,
has NOTHING to do with an Elf University assignment. Thanks!

Answer all the questions in the quizme executable:
- What port does 34.76.1.22 have open?
- What port does 34.77.207.226 have open?
- How many hosts appear "Up" in the scan?
- How many hosts have a web port open?  (Let's just use TCP ports 80, 443, and 8080)
- How many hosts with status Up have no (detected) open TCP ports?
- What's the greatest number of TCP ports any one host has open?

Check out bigscan.gnmap and type quizme to answer each question.
```

![Greasy GopherGuts](../img/Greasy_GopherGuts.png)

## Resources
* grep man page: https://man7.org/linux/man-pages/man1/grep.1p.html
* Regular Expressions tutorial: https://regexland.com/regex-tutorial/

## Solution

Grep can be used to look for certain keywords/lines inside a file. The man page shows which parameters are available. The later steps are easier to solve if you're using regular expressions and/or piping a command output to another command.   
The quizme binary offers some help to get the proper grep statements:

``` bash
# What port does 34.76.1.22 have open?
elf@372b805671ad:~$ grep 34.76.1.22 bigscan.gnmap 
Host: 34.76.1.22 ()     Status: Up
Host: 34.76.1.22 ()     Ports: 62078/open/tcp//iphone-sync///      Ignored State: closed (999)

# What port does 34.77.207.226 have open?
elf@372b805671ad:~$ grep 34.77.207.226 bigscan.gnmap 
Host: 34.77.207.226 ()     Status: Up
Host: 34.77.207.226 ()     Ports: 8080/open/tcp//http-proxy///      Ignored State: filtered (999)

# How many hosts appear "Up" in the scan?
elf@372b805671ad:~$ grep "Status: Up" bigscan.gnmap | wc
  26054  130270  967583

# How many hosts have a web port open?  (Let's just use TCP ports 80, 443, and 8080)
elf@b603d20214a1:~$ grep -E "(80/open)|(443/open)|(8080/open)" bigscan.gnmap | wc
  14372  180190 2539348

# How many hosts with status Up have no (detected) open TCP ports?
elf@b603d20214a1:~$ grep "Status: Up" bigscan.gnmap | wc ; grep "/open" bigscan.gnmap | wc
  26054  130270  967583
  25652  321396 4731570
# 26054 - 25652 = 402

# What's the greatest number of TCP ports any one host has open?
elf@9bb355913c98:~$ cat bigscan.gnmap | grep "/open" | awk -F',' '{print NF-1}' | sort | uniq
0
1
10
11
2
3
4
5
6
7
8
9
# Max is 11(+1) = 12
```

## Hints given

* **Adding Data to cURL requests:** When sending a [POST request with data](https://www.educative.io/edpresso/how-to-perform-a-post-request-using-curl), add --data-binary to your curl command followed by the data you want to send.
* **Linux Wi-Fi Commands:** The [iwlist](https://linux.die.net/man/8/iwlist) and [iwconfig](https://linux.die.net/man/8/iwconfig) utilities are key for managing Wi-Fi from the Linux command line.
* **Web Browsing with cURL:** [cURL](https://linux.die.net/man/1/curl) makes HTTP requests from a terminal - in Mac, Linux, and modern Windows!



<br>[Go back](../Hints.md)
