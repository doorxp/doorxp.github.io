---
title: "cocos2dx 在 ipad2上花屏"
layout: post
categroy: iphone
---

很正常的一个游戏，在模拟器和ipad1上都可以正常运行，但是在ipad2上运行的时候，有时候会出现图层混乱，下层的图像会在上层显示，或者某一层的图像消失


1.关闭深度测试
2.采用2d渲染，cocos2d默认采用3d渲染

2者选1即可

bc. 
//pDirector->enableRetinaDisplay(true);
  pDirector->setProjection(CCDirectorProjection2D);

