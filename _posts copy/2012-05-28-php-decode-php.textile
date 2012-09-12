---
layout: post
title: "php decode encode php"
category: php
---

今天在学习一个ThinkPHP框架——sgcms时，发现一个文件被加密

bc.. <?php
// This file is protected by sgcms & provided under license.
Copyright(C) 2007-2010 www.sgcms.cn, All rights reserved.
 
$OOO0O0O00=__FILE__;
$OOO000000=urldecode('%74%68%36%73%62%65%68%71%6c%61%34%63%6f%5f%73%61%64%66%70%6e%72');
$OO00O0000=21496;
$OOO0000O0=$OOO000000{4}.
$OOO000000{9}.$OOO000000{3}.$OOO000000{5};
$OOO0000O0.=$OOO000000{2}.$OOO000000{10}.$OOO000000{13}.$OOO000000{16};
$OOO0000O0.=$OOO0000O0{3}.$OOO000000{11}.$OOO000000{12}.$OOO0000O0{7}.$OOO000000{5};
$O0O0000O0='OOO0000O0';
eval(($$O0O0000O0('JE9PME9PMDAwMD0kT09PMDAwMDAwezE3fS4kT09PMDAwM...

p. 很明显，是使用了某种PHP代码混淆工具混淆了下，Google网上搜了下，问题解决，给遇到同样问题的朋友一个方便。

解密php文件：

bc.. <?php
/***********************************
*威盾PHP加密专家解密算法 By：Neeao
*http://Neeao.com
*2009-09-10
***********************************/
 
$filename="GlobalAction.class.php";//要解密的文件
$lines = file($filename);//0,1,2行
 
//第一次base64解密
$content="";
if(preg_match("/O0O0000O0\('.*'\)/",$lines[1],$y))
{
    $content=str_replace("O0O0000O0('","",$y[0]);
    $content=str_replace("')","",$content);
    $content=base64_decode($content);
}
//第一次base64解密后的内容中查找密钥
$decode_key="";
if(preg_match("/\),'.*',/",$content,$k))
{
    $decode_key=str_replace("),'","",$k[0]);
    $decode_key=str_replace("',","",$decode_key);
}
//查找要截取字符串长度
$str_length="";
if(preg_match("/,\d*\),/",$content,$k))
{
    $str_length=str_replace("),","",$k[0]);
    $str_length=str_replace(",","",$str_length);
}
//截取文件加密后的密文
$Secret=substr($lines[2],$str_length);
//echo $Secret;
 
//直接还原密文输出
echo "<?php\n".base64_decode(strtr($Secret,$decode_key,
'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/')).
"?>";
?>

p. 
