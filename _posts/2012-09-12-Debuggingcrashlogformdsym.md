---
title: "Debugging Crash Log from dSYM"
layout: post
category: iphone
---



	

Here is how to symbolicate iOS crash reports. Make sure the .app or .ipa and the .dSYM files are the same ones created at the time of the compilation.
Step 1

If you have an .app file
Place the .app file and the .dSYM file into a folder.

If you have an .ipa file
Rename it to a .zip file and extract it. It should create a directory called Payload with the the .app and .dSYM files.
Step 2

In terminal, go into the directory with the .app and .dSYM files and run the command.

atos -arch armv6|armv7 -o yourApp.app/yourApp address

Example:
atos -arch armv7 -o testApp.app/testApp 0x0006468e