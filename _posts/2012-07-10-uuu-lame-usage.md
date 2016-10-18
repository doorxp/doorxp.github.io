---
title: "LAME 命令行格式-mp3处理工具"
layout: post
category: Apple
---

LAME 命令行格式: lame [选项] <输入档名> [<输出档名>] 

档案输入选项 

-r 
指定输入档案是 Raw PCM 格式 (.PCM 档案)。 

-x 
强迫来源档案的位元组顺序 (byte-order) 调换（压缩跨平台的档案时使用）。 
如果压缩出来的档案是杂音，可以试试看这一个选项。 

-s sfreq 
指定来源档案的取样频率（单位为 KHz），预设为 44.1 KHz。 
只有在输入档案为 PCM 格式时才需要使用，其余情形会自动从档头判断。 

--mp1nput 
输入的档案是 MP1 格式（利用内建的 mpglib 解码成 Wave 档案，下同）。 

--mp2input 
输入的档案是 MP2 格式。 

--mp3input 
输入的档案是 MP3 格式。 

--ogginput 
输入的档案是 Ogg Vorbis 格式。 

动作模式选项 

-m mode 
mode : (s) Stereo (j) Joint Stereo (f) Force ms_stereo (m) Mono 

当使用的 VBR 品质为 4-9 或者 CBR <= 160 Kbps 时，预设为 Joint Stereo。 
其余时则预设为 Stereo。 

Stereo: 单纯双声道立体声模式，在此种压缩模式中，LAME 将不会计算双声道之间的资料相关性，但是会协调分配双声道的资料流量，自动分配较多的 Bit 给复杂的声道使用。 
Joint Stereo: 在这个压缩模式下，LAME 会利用双声道之间的资料相关性进行演算。左右声道资料类似时，会利用 M/S (Mid/Side) 编码技术，计算中央声道 (L+R) 和两侧声道差异 (L-R) 的值，并且会分配较多的 Bit 给中央声道，增加资料记录频宽。 
不当的使用 Joint Stereo 模式可以造成人耳可辨的压缩失真，太多在 Stereo <-> Joint Stereo 之间的切换也不好听。为了决定何时要在两种模式之间切换，LAME 使用了比 ISO 文件复杂许多的演算法进行判别，因此可以放心使用 Joint Stereo 模式。 
Force: 强迫所有的 frame 全部使用 M/S Joint Stereo 编码，速度可以增快很多，但是不建议使用。 
Mono: 单声道编码模式。 

-a 
将立体声档案混音成单声道後编码。 
混音的演算法是将左右声道加总後，衰减 6dB。 

这个选项只有在输入档案为 PCM 格式时才需要，其他情形会从档头自动判断。 
要将立体声的 PCM 档案编码成单声道时，可以使用 "-m s -a"，对於 WAV 或 AIFF 档案，不管是立体声或是单声道输入， 使用 "-m m" 都会编码成单声道模式。 

-d 
允许各声道有不同的编码区块模式 (block type) 

-S 
不要印出执行进度和 VBR Bitrate 使用率图表 

--disptime seconds 
每隔 seconds 秒印出一次执行进度 

--ogg 
编码成 Ogg Vorbis 档案 

--freeformat 
编码成 Free-Format 格式的档案。 
下了此参数後，可以使用 -b 参数，指定压缩成任何 8 以上的整数 Bitrate，但是大部分的 Player 均不支援这种格式的播放。 

支援的 Player 列表： 
FreeAmp: 440 Kbps max 
in_mpg123: 560 Kbps max 
l3dec: 310 Kbps max 
LAME: 560 Kbps max 

--decode 
输入 MPEG 或 OGG 档案，解码成 Wave 档。 

-t 
解码成 Wave 档案时，不写入档头资讯。 

--comp arg 
自动调整 Bitrate，尽量接近 arg 的压缩百分比率。 

--scale arg 
将输入的声音资料，放大 arg 倍之後编码。 

--athonly 
只使用 ATH (Absolute Threshold of Hearing) 作遮罩运算。 
这个选项使 LAME 忽略掉心理音响学模型 (Psycho-acoustic Model) 的输出结果，只采用绝对听觉底限作压缩的判断。 
在高 Bitrate 的情形下或许可以增进音质，或者是拿来进行实验。 

--noath 
不使用 ATH 进行遮罩运算，只使用心理音响模型。 

--athlower arg 
将 ATH 的底限降低 arg dB 

--raise-smr arg 
0 <= arg <= 1 
调节 SMR 参数。 

--short 
使用短区块。 

--noshort 
不使用短区块。 

--voice 
（实验性）人声编码模式。 

--preset type 
type : phone, phon+, lw, mw-eu, mw-us, sw, fm, voice, radio, tape, hifi, cd, stereo 
使用一些预设的编码参数，可以输入 --preset help 获得详细资料。 

固定 Bitrate 模式 (CBR) 选项 

-h 
高品质模式，启动某些可以增进品质的运算，但是会降低一些速度（建议使用）。 
这个选项在 VBR 压缩模式中是预设开启的。 

-f 
快速模式，但会降低品质（预设）。 
杂音成型 (Noise Shaping) 将会被关闭，但是心理音响模型还是会被用来计算 Bit 分配和 Pre-Echo 侦测。 

-b bitrate 
指定压缩的 Bitrate。可以使用的 Bitrate 如下： 
MPEG Layer 1 (32K, 44.1K, 48K): 32 40 48 56 64 80 96 112 128 160 192 224 256 320 
MPEG Layer 2 (16K, 22.05K, 24K): 8 16 24 32 40 48 56 64 80 96 112 128 144 160 
预设值 MPEG1 为 128Kbps，MPEG2 为 80Kbps。 
不管做哪一种设定，为了节省空间，被判断为静音的部分，会自动采用最小的 Bitrate。 

平均 Bitrate 模式 (ABR) 选项 

--abr bitrate 
不指定品质，而指定想要的平均 Bitrate，LAME 会自动使用不同的 frame 大小。 bitrate 许可的范围是 4-310，可以使用这个范围内的任一个整数。 
同时可以使用 -b 和 -B 指定变动范围的上下限，像是 lame --abr 123 -b 64 -B 192 a.wav a.mp3 将会限制可以使用的 frame 大小范围在 64-192 Kbits 之间。 
使用 -B 是不建议的。 一个 128Kbps 的 MP3 资料流，因为可以使用 Bit Reservoir，所以有一些 frame 其实可以使用到与 320Kbps frame 相同大小的空间。 VBR 并不使用 Bit Reservoir，因此需要允许使用 320Kbps 大小的 frame 才能达到与 CBR 相同的弹性。 

可变 Bitrate 模式 (VBR) 选项 

-v 
使用 VBR 模式。 

--vbr-old 
使用旧的 VBR 压缩演算法。 

--vbr-new 
使用新的 VBR 压缩演算法（预设）。 

-V quality 
指定 VBR 的压缩品质，范围为 0-9 （数字越小品质越高），预设值为 4。 

-b bitrate 
指定 Bitrate 变动的下限，预设为 32 Kbps。 

-B bitrate 
指定 Bitrate 变动的上限，预设为 320 Kbps。 

-F 
强迫遵守 -b 的限制，适合无法解码低 Bitrate 的 MP3 随身听使用。 
不启动这一个选项，当输入为类比静音（强度低於 ATH）时，将会忽略最低 Bitrate 的限制，使用压缩格式中的最低 Bitrate。 

-t 
不写入 Xing VBR 档头，会造成时间总长显示不正确以及搜寻功能失效等问题。 

--nohist 
压缩档案时，不显示 VBR Bitrate 使用率图表。 

MP3 档头/资料流选项 

-c 
标记档案为有版权 (copyrighted)。 

-o 
标记档案为非原始档 (non-original)。 

-p 
错误保护，会在每个 frame 占用 16bit 进行 CRC 编码（可能减损音质）。 

--nores 
不使用 Bit Reservoir 功能（会严重减损 CBR 音质）。 

滤波器选项 

-k 
保留住所有频率。 
压缩时，为了把资料流量保留给最重要的频段，提高压缩出来的品质，LAME 会自动随着所选用的 Bitrate 指定高低通滤波器的数值。 如果把频宽设定的比预设值还要大的话，可能会造成无法预期的鸣响以及失真，小心使用！ 

--lowpass freq 
设定低通滤波器的起始点为 freq KHz 
高於这个频率的声音会被截除。 

--lowpass-width freq 
设定低通滤波器的宽度，预设为 15% 的 --lowpass 频率。 

--highpass freq 
设定高通滤波起始点为 freq KHz 
低於这个频率的声音会被截除。 

--highpass-width freq 
设定高通滤波器的宽度，预设为 15% 的 --highpass 频率。 

--resample freq 
输出档案的取样频率。 

--cwlimit freq 
计算音色到指定的频率。

最高品质参数：
@--alt-preset insane 或者 --alt-preset cbr 320 @
320k CBR，音质最好，文件体积最大 

VBR参数： 
1.--alt-preset extreme 
220-270k左右的VBR，音质与上面参数相仿，但文件体积小25%，推荐此参数 
2.--alt-preset fast extreme 
音质比上面参数稍微差一些 
3.--alt-preset standard 
180-220k左右的VBR，在音质和文件大小之间比较好的平衡 
4.--alt-preset fast standard 
音质比上面参数稍微差一些 
5.--alt-preset standard -Y 
虽然品质稍差，但文件体积非常小 

 
需要重点说明的是：8位的单moto格式pcm转换wav或其他格式，经过多次实验，噪音很大。因为我们IVR的语音板卡是东进公司的，所以就在东进公司下载了一个专门的转换软件叫ADPCM，效果很好的。