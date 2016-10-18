---
layout: post
title: "Lion boot 32bit mode"
category: Apple
---


Thank you for the precious tips ! You made me able to boot Parallels 7 under Mountain Lion, this worked for me:
1) I used this command in the terminal to start in 32 bits
@sudo systemsetup -setkernelbootarchitecture i386@
( to default start in 64 bits again...
@sudo systemsetup -setkernelbootarchitecture x86_64@
)
2) The file "Parallels Service" has again to be put into the folder /library/parallels
(Image 1)
this because Mountain Lion places it into a new root folder "Incompatible Software" when booting in 64 bits
3) I'm now booting Parallels 7 with the Program "FileServe Parallels Desktop Launcher.zip" mentioned here by 87racer
"http://bs87.com/post/17757954386/par...tain-lion-10-8":http://bs87.com/post/17757954386/parallels-desktop-7-on-mountain-lion-10-8

I'm also hoping for a quick fix by the Parallels team, thanks in advance
Have a nice w-e everyone