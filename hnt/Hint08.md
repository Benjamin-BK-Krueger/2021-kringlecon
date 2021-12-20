# Hints for Objective 8: Kerberoasting on an Open Fire

<br>[Go back](../Hints.md)

## Overview
Requested by Eve Snowshoes, found in KringleCon - Santa’s Office
<br>
**Task**: HoHo ... No     
**Description**: 

```
Jack is trying to break into Santa's workshop!

Santa's elves are working 24/7 to manually look through logs, identify the
malicious IP addresses, and block them. We need your help to automate this so
the elves can get back to making presents!

Can you configure Fail2Ban to detect and block the bad IPs?

 * You must monitor for new log entries in /var/log/hohono.log
 * If an IP generates 10 or more failure messages within an hour then it must
   be added to the naughty list by running naughtylist add <ip>
        /root/naughtylist add 12.34.56.78
 * You can also remove an IP with naughtylist del <ip>
        /root/naughtylist del 12.34.56.78
 * You can check which IPs are currently on the naughty list by running
        /root/naughtylist list

You'll be rewarded if you correctly identify all the malicious IPs with a
Fail2Ban filter in /etc/fail2ban/filter.d, an action to ban and unban in
/etc/fail2ban/action.d, and a custom jail in /etc/fail2ban/jail.d. Don't
add any nice IPs to the naughty list!

*** IMPORTANT NOTE! ***

Fail2Ban won't rescan any logs it has already seen. That means it won't
automatically process the log file each time you make changes to the Fail2Ban
config. When needed, run /root/naughtylist refresh to re-sample the log file
and tell Fail2Ban to reprocess it.
```

![Eve Snowshoes](../img/Eve_Snowshoes.png)

## Resources
* Using fail2ban To Secure Your Own Server: https://www.linode.com/docs/guides/using-fail2ban-to-secure-your-server-a-tutorial/
* Regular Expressions tutorial: https://regexland.com/regex-tutorial/

## Solution

The fail2ban filter can be implemented like this (there are 4 types of error messages which can occur in the log file):

``` bash
root@01550c223905:/etc/fail2ban# cat filter.d/naughtylist.conf 
[Definition]
failregex = ^.* Failed login from <HOST> for .*$
            ^.* Invalid heartbeat .* from <HOST>.*$
            ^.* Login from <HOST> rejected due to unknown user name.*$
            ^.* <HOST> sent a malformed request.*$
```

Let’s test if these are matching:

``` bash
root@01550c223905:/etc/fail2ban# fail2ban-regex /var/log/hohono.log /etc/fail2ban/filter.d/naughtylist.conf 

Running tests
=============

Use   failregex filter file : naughtylist, basedir: /etc/fail2ban
Use         log file : /var/log/hohono.log
Use         encoding : UTF-8


Results
=======

Failregex: 3711 total
|-  #) [# of hits] regular expression
|   1) [969] ^.* Failed login from <HOST> for .*$
|   2) [856] ^.* Invalid heartbeat .* from <HOST>.*$
|   3) [928] ^.* Login from <HOST> rejected due to unknown user name.*$
|   4) [958] ^.* <HOST> sent a malformed request.*$
`-
```

We also need some actions to ban/unban IPs:

``` bash
root@6f94ebf60fe4:/etc/fail2ban# cat action.d/naughtylist.conf 
[Definition]
actionban = /root/naughtylist add <ip>
actionunban = /root/naughtylist del <ip>
```

And of course a proper jail:

``` bash
root@6f94ebf60fe4:/etc/fail2ban# cat jail.d/naughtylist.conf 
[naughtylist]
enabled  = true
filter = naughtylist
logpath = /var/log/hohono.log

banaction = naughtylist

maxretry = 10
findtime = 3600
bantime = -1
```

Let’s refresh the config and reload the entries to see if they are triggering fail2ban:

``` bash
root@618198b70f67:/etc/fail2ban/jail.d# Log file refreshed! It may take fail2ban a few moments to re-process.
192.149.76.183 has been added to the naughty list!
161.5.184.168 has been added to the naughty list!
35.171.120.122 has been added to the naughty list!
90.147.152.94 has been added to the naughty list!
205.123.75.206 has been added to the naughty list!
117.29.14.221 has been added to the naughty list!
167.44.47.199 has been added to the naughty list!
124.231.150.195 has been added to the naughty list!
80.9.52.210 has been added to the naughty list!
182.205.125.239 has been added to the naughty list!
105.178.134.208 has been added to the naughty list!
41.8.151.201 has been added to the naughty list!
146.33.2.184 has been added to the naughty list!
139.172.235.155 has been added to the naughty list!
186.136.135.235 has been added to the naughty list!
192.19.166.7 has been added to the naughty list!
57.55.116.25 has been added to the naughty list!
111.194.24.204 has been added to the naughty list!
201.10.224.72 has been added to the naughty list!
You correctly identifed 19 IPs out of 19 bad IPs
You incorrectly added 0 benign IPs to the naughty list

*******************************************************************
* You stopped the attacking systems! You saved our systems!
*
* Thank you for all of your help. You are a talented defender!
*******************************************************************
```

## Hints given

* **Active Directory Interrogation:** Investigating Active Directory errors is harder without [Bloodhound](https://github.com/BloodHoundAD/BloodHound), but there are [native](https://social.technet.microsoft.com/Forums/en-US/df3bfd33-c070-4a9c-be98-c4da6e591a0a/forum-faq-using-powershell-to-assign-permissions-on-active-directory-objects?forum=winserverpowershell) [methods](https://www.specterops.io/assets/resources/an_ace_up_the_sleeve.pdf).
* **Kerberoasting and Hashcat Syntax:** Learn about [Kerberoasting](https://gist.github.com/TarlogicSecurity/2f221924fef8c14a1d8e29f3cb5c5c4a) to leverage domain credentials to get usernames and crackable hashes for service accounts.
* **Stored Credentials:** Administrators often store credentials in scripts. These can be coopted by an attacker for other purposes!
* **Kerberoast and AD Abuse Talk:** Check out [Chris Davis' talk](https://www.youtube.com/watch?v=iMh8FTzepU4) and [scripts](https://github.com/chrisjd20/hhc21_powershell_snippets) on Kerberoasting and Active Directory permissions abuse.
* **Finding Domain Controllers:** There will be some 10.X.X.X networks in your routing tables that may be interesting. Also, consider adding -PS22,445 to your nmap scans to "fix" default probing for unprivileged scans.
* **Hashcat Mangling Rules:** [OneRuleToRuleThemAll.rule](https://github.com/NotSoSecure/password_cracking_rules) is great for mangling when a password dictionary isn't enough.
* **CeWL for Wordlist Creation:** [CeWL](https://github.com/digininja/CeWL) can generate some great wordlists from website, but it will ignore digits in terms by default.


<br>[Go back](../Hints.md)
