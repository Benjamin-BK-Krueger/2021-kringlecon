# Hints for Objective 2: Where in the World is Caramel Santaigo?

<br>[Go back](Hints.md)

## Overview
Requested by Piney Sappington, found in KringleCon - Courtyard
<br>
**Task**: Exifdate
**Description**: 

```
HELP! That wily Jack Frost modified one of our naughty/nice records, and right 
before Christmas! Can you help us figure out which one? We've installed exiftool
for your convenience!

Filename (including .docx extension) > 
```

![Piney Sappington](/Piney_Sappington.png)

## Resources
* Exiftool man page: http://manpages.org/exiftool

## Solution

One of the files seem to contain altered metadata. So let's loop over all files and compare the output of the exiftool helper:

``` bash
elf@e2533911c94b:~$ for i in *; do echo $i; exiftool $i | wc; done
..
2021-12-20.docx
     44     186    1928
2021-12-21.docx
     44     186    1927
2021-12-22.docx
     44     186    1928
```

So the 21-file seems to be the target:

``` bash
elf@e2533911c94b:~$ exiftool 2021-12-21.docx
..
Last Modified By       : Jack Frost
..
```

<br>[Go back](Hints.md)
