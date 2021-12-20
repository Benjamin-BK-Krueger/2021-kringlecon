# Hints for Objective 9: Splunk!

<br>[Go back](../Hints.md)

## Overview
Requested by Fitzy Shortstack, found in KringleCon - Entry
<br>
**Task**: Yara Analysis     
**Description**: 

```
HELP!!!

This critical application is supposed to tell us the sweetness levels of our candy
manufacturing output (among other important things), but I can't get it to run.

It keeps saying something something yara. Can you take a look and see if you
can help get this application to bypass Sparkle Redberry's Yara scanner?

If we can identify the rule that is triggering, we might be able change the program
to bypass the scanner.

We have some tools on the system that might help us get this application going:
vim, emacs, nano, yara, and xxd

The children will be very disappointed if their candy won't even cause a single cavity.

snowball2@e3932e4afe25:~$ 
```

![Fitzy Shortstack](../img/Fitzy_Shortstack.png)

## Resources
* Writing YARA rules: https://yara.readthedocs.io/en/stable/writingrules.html
* CyberChef: https://gchq.github.io/CyberChef/

## Solution

First run of the blocked binary:

``` bash
snowball2@312ab12cd1a9:~$ ./the_critical_elf_app 
yara_rule_135 ./the_critical_elf_app
```

That yara rule says

```
 strings:
      $s = "candycane"
```

So just open the binary (a simple editor like vi is sufficient), look for that string and replace the lower case c with a capital C
<br>
Second run:

``` bash
snowball2@45408967b971:~$ ./the_critical_elf_app 
yara_rule_1056 ./the_critical_elf_app 
```

That yara rule says

```
    strings:
        $s1 = {6c 6962 632e 736f 2e36}
        $hs2 = {726f 6772 616d 2121}
```

Just feed that numbers into CyberChef and apply the magic recipe:

```
From_Hexdump()
c.so.6gram!!	Valid UTF8
Entropy: 3.25
```

So just open the binary again, look for the second string and replace the ! With another character like -
<br>
Third run:

``` bash
snowball2@45408967b971:~$ ./the_critical_elf_app 
yara_rule_1732 ./the_critical_elf_app
```

That yara rule looks for a lot of string but we can take the easy route looking at the conditions:

```
   condition:
      uint32(1) == 0x02464c45 and filesize < 50KB and
      10 of them
```

Let’s append some null bytes at the end of the file:

``` bash
snowball2@45408967b971:~$ dd if=/dev/zero count=100 >> the_critical_elf_app 
100+0 records in
100+0 records out
51200 bytes (51 kB, 50 KiB) copied, 0.000323874 s, 158 MB/s
snowball2@45408967b971:~$ ls -l the_critical_elf_app 
-rwxr-xr-x 1 snowball2 snowball2 67889 Dec 13 21:34 the_critical_elf_app
```

The binary will be able to bypass the yara checks now:

``` bash
snowball2@45408967b971:~$ ./the_critical_elf_app 
Machine Running.. 
Toy Levels: Very Merry, Terry
Naughty/Nice Blockchain Assessment: Untampered
Candy Sweetness Gauge: Exceedingly Sugarlicious
Elf Jolliness Quotient: 4a6f6c6c7920456e6f7567682c204f76657274696d6520417070726f766564
```

## Hints given

* **Sysmon Monitoring in Splunk:** Sysmon network events don't reveal the process parent ID for example. Fortunately, we can pivot with a query to investigate process creation events once you get a process ID.
* **Malicious NetCat??:** Did you know there are multiple versions of the Netcat command that can be used maliciously? nc.openbsd, for example.
* **GitHub Monitoring in Splunk:** Between GitHub audit log and webhook event recording, you can monitor all activity in a repository, including common git commands such as git add, git status, and git commit.

<br>[Go back](../Hints.md)
