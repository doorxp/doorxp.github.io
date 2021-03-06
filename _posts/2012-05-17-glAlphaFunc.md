---
layout: post
title: glAlphaFunc
category: openGL
---

glAlphaFunc
设置图形透明度的显示规则
参数说明：
glAlphaFunc(GLenum func,GLclampf ref)
GLenum          是unsigned int类型
GLclampf        是float类型
func                  规则要求
ref                     透明度层度要求。0.0f 到 1.0f 之间
代码示例：
    lEnable(GL_ALPHA_TEST);
    glAlphaFunc(GL_GEQUAL,0.4f);
    
    glBegin(4);
    
    glColor4f(1.0,0.0,0.0,0.3);
    glVertex3f(0.0,0.1,0.0);
    
    glColor4f(1.0,1.0,0.0,1.0);
    glVertex3f(0.6,0.6,0.6);
    
    glColor4f(1.0,0.0,1.0,0.8);
    glVertex3f(0.5,0.8,0.7);
    
    glEnd();
说明：
以上代码运行后决定图形透明度只有大于且等于0.4f 的坐标点才显示。所以这个半透明的三角形看起来象四边形。
参数func取值：

{background-color:gray;}.|类型|值|说明|
|GL_NEVER|512|始终不显示|
|GL_LESS|513|小于透明层度要求值就显示|
|GL_EQUAL| 514|等于透明层度要求值就显示|
|GL_LEQUAL|515|小于且等于透明层度要求值就显示|
|GL_GREATER|516|大于透明层度要求值就显示|
|GL_NOTEQUAL|517|不等于透明层度要求值就显示|
|GL_GEQUAL|518|大于且等于透明层度要求值就显示|
|GL_ALWAYS|519| 始终显示|