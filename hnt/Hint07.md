# Hints for Objective 7: Printer Exploitation

<br>[Go back](../Hints.md)

## Overview
Requested by Ruby Oyster, found in FrostFest - Jack’s Office
<br>
**Task**: Objective 06) Shellcode Primer   
**Description**: 

See [Objective 06) Shellcode Primer](../obj/Objective06.md)

![Ruby Oyster](../img/Ruby_Oyster.png)

## Hints given

* **Hash Extension Attacks:** [Hash Extension Attacks](https://blog.skullsecurity.org/2012/everything-you-need-to-know-about-hash-length-extension-attacks) can be super handy when there's some type of validation to be circumvented.
* **Printer Firmware:** When analyzing a device, it's always a good idea to pick apart the firmware. Sometimes these things come down Base64-encoded.
* **Dropping Files:** Files placed in /app/lib/public/incoming will be accessible under https://printer.kringlecastle.com/incoming/.

<br>[Go back](../Hints.md)
