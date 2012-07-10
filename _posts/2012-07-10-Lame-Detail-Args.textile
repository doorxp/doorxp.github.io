---
title: "LAME 使用/参数说明"
layout: post
category: Apple
---

LAME 使用/参数说明
因为 LAME 是一个文字模式的程序，直接执行并不方便，下一次指令又只能够压缩一个档案，因此使用前导程序辅助可以说是必要的事情。 RazorLame 的接口简单明了，方便使用，要设定的地方也并不多；在 Options -> General 之中，指定 LAME 执行文件的位置，然后就可以在其它的页面调节各种压缩的参数。如果想试试一些实验性（尚未正式公布）的参数，或者懒得慢慢检查每一页的设定，可以直接在 Options -> Advanced 中把写好的参数填入 Custom Options 中，并选取 Only use custom options 即可。
Options -> Advanced 里面，有一个 Delete source file after encoding 的选项，选取之后，编码完成的 WAV 文件会被自动删除；蛮方便的。
这里是 RazorLame 设定对照 LAME 参数的表格，可以对照以下的参数说明查阅各项设定值的数据。

|General 页面|
|-b|Bitrate|-m|Mode|
|Advanced 页面|
|-h -f –voice|Optimization|-p|Include CRC-Checksums|
|-o|Copy|-c|Copyrighted|
|VBR 页面|
|-v|Enable Variable Bitrate (VBR)|-B|Maximum VBR Bitrate|
|-V|Quality|-t|Disable writing of the VBR tag|
|-F|Strictly enforce minimum bitrate|–abr|Use ABR instead of VBR, Target bitrate for ABR|
|Expert 页面|
|–athonly –noath|ATH Control|-d|Allow block types to differ between channels|
|-k|Disable all filtering|–nores|Disable bit reservoir|
|–noshort|Disable short blocks|–strictly-enforce-iso|Comply as much as possible to ISO MPEG spec|
|Audio Processing 页面|
|–highpass|Highpass filtering frequency|–highpass-width|Width of Highpass filter|
|–lowpass|Lowpass filtering frequency|–lowpass-width|Width of Lowpass filter|
|–resample|Output sampling frequency|

LAME 参数说明
以下的中文解释，是直接翻译 LAME 3.87 的在线说明得来的，都只是一些概念性的解说；个别参数的详细说明翻译部分参照 Scorpio 网友在数字音讯处理讨论区所贴的 LAME 常用参数用法 这一篇文章，在这里要对他表达谢意。
以下斜体的部分，表示可以带入后面说明的其它选项，而说明中粗体字的部分就是这些多重选项的默认值。


LAME 命令行格式: lame [选项] <输入文件名> [<输出档名>]
档案输入选项

|-r|指定输入档案是 Raw PCM 格式 (.PCM 档案)。|
|-x|强迫来源档案的字节顺序 (byte-order) 调换（压缩跨平台的档案时使用）。<br/>如果压缩出来的档案是杂音，可以试试看这一个选项。|
|-s sfreq|指定来源档案的取样频率（单位为 KHz），预设为 44.1 KHz。<br/> 只有在输入档案为 PCM 格式时才需要使用，其余情形会自动从档头判断。|
|–mp1nput|输入的档案是 MP1 格式（利用内建的 mpglib 解碼成 Wave 档案，下同）.|
|–mp2input|输入的档案是 MP2 格式。|
|–mp3input|输入的档案是 MP3 格式。|
|–ogginput|输入的档案是 Ogg Vorbis 格式。|

|动作模式选项|
|-m mode|mode : (s) Stereo (j) Joint Stereo (f) Force ms_stereo (m) Mono<br/>当使用的 VBR 品质为 4-9 或者 CBR <= 160 Kbps 时，预设为 Joint Stereo。
其余时则预设为 Stereo<br/>Stereo: 单纯双声道立体声模式，在此种压缩模式中，LAME 将不会计算双声道之间的数据相关性，但是会协调分配双声道的数据流量，自动分配较多的 Bit 给复杂的声道使用。
Joint Stereo: 在这个压缩模式下，LAME 会利用双声道之间的数据相关性进行演算。左右声道数据类似时，会利用 M/S (Mid/Side) 编码技术，计算中央声道 (L+R) 和两侧声道差异 (L-R) 的值，并且会分配较多的 Bit 给中央声道，增加数据记录频宽。
不当的使用 Joint Stereo 模式可以造成人耳可辨的压缩失真，太多在 Stereo <-> Joint Stereo 之间的切换也不好听。为了决定何时要在两种模式之间切换，LAME 使用了比 ISO 文件复杂许多的算法进行判别，因此可以放心使用 Joint Stereo 模式。
Force: 强迫所有的 frame 全部使用 M/S Joint Stereo 编码，速度可以增快很多，但是不建议使用。<br/>Mono: 单声道编码模式。|
|-a|将立体声档案混音成单声道后编码。<br/>混音的算法是将左右声道加总后，衰减 6dB。<br/><br/>这个选项只有在输入档案为 PCM 格式时才需要，其它情形会从档头自动判断。
要将立体声的 PCM 档案编码成单声道时，可以使用 "-m s -a"，对于 WAV 或 AIFF 档案，不管是立体声或是单声道输入， 使用 "-m m" 都会编码成单声道模式。|
|-d|允许各声道有不同的编码区块模式 (block type)|
|-S|不要印出执行进度和 VBR Bitrate 使用率图表|
|–disptime seconds|每隔 seconds秒印出一次执行进度|
|–ogg|编码成 Ogg Vorbis 档案|
|–freeformat|编码成 Free-Format 格式的档案。<br/>下了此参数后，可以使用 -b参数，指定压缩成任何 8 以上的整数 Bitrate，但是大部分的 Player 均不支持这种格式的播放。<br/>支援的 Player 列表：<br/>FreeAmp: 440 Kbps max<br/>in_mpg123: 560 Kbps max<br/>l3dec: 310 Kbps max<br/>LAME: 560 Kbps max|
|–decode|输入 MPEG 或 OGG 档案，解碼成 Wave 文件。|
|-t|解碼成 Wave 档案时，不写入文件头信息。|
|–comp arg|自动调整 Bitrate，尽量接近 arg的压缩百分比率。|
|–scale arg|将输入的声音数据，放大 arg倍之后编码。|
|–athonly|只使用 ATH (Absolute Threshold of Hearing) 作屏蔽运算。<br/>这个选项使 LAME 忽略掉心理音响学模型 (Psycho-acoustic Model) 的输出结果，只采用绝对听觉底限作压缩的判断。<br/>在高 Bitrate 的情形下或许可以增进音质，或者是拿来进行实验。|
|–noath|不使用 ATH 进行屏蔽运算，只使用心理音响模型。|
|–athlower arg|将 ATH 的底限降低 arg dB|
|–raise-smr arg|0 <= arg <= 1<br/><br/>调节 SMR 参数。|
|–short|使用短区块。|
|–noshort|不使用短区块。|
|–voice|（实验性）人声编码模式。|
|–preset type|type : phone, phon+, lw, mw-eu, mw-us, sw, fm, voice, radio, tape, hifi, cd, stereo<br/><br/>使用一些预设的编码参数，可以输入 –preset help获得详细数据。|
 
|\2.固定 Bitrate 模式 (CBR) 选项|
|-h|高品质模式，启动某些可以增进品质的运算，但是会降低一些速度（建议使用）。<br/>这个选项在 VBR 压缩模式中是预设开启的。|
|-f|快速模式，但会降低品质（预设）。<br/>杂音成型 (Noise Shaping) 将会被关闭，但是心理音响模型还是会被用来计算 Bit 分配和 Pre-Echo 侦测。|
|-b bitrate|指定压缩的 Bitrate。可以使用的 Bitrate 如下：<br/><br/>MPEG Layer 1 (32K, 44.1K, 48K): 32 40 48 56 64 80 96 112 128 160 192 224 256 320<br/>MPEG Layer 2 (16K, 22.05K, 24K): 8 16 24 32 40 48 56 64 80 96 112 128 144 160<br/>默认值 MPEG1 为 128Kbps，MPEG2 为 80Kbps。<br/>不管做哪一种设定，为了节省空间，被判断为静音的部分，会自动采用最小的 Bitrate。|
 
table{width:100%}.
|\2.平均 Bitrate 模式 (ABR) 选项|
|–abr bitrate|不指定品质，而指定想要的平均 Bitrate，LAME 会自动使用不同的 frame 大小。 bitrate许可的范围是 4-310，可以使用这个范围内的任一个整数。<br/>同时可以使用 -b 和 -B 指定变动范围的上下限，像是 lame –abr 123 -b 64 -B 192 a.wav a.mp3 将会限制可以使用的 frame 大小范围在 64-192 Kbits 之间。<br/><br/>使用 -B 是不建议的。 一个 128Kbps 的 MP3 资料流，因为可以使用 Bit Reservoir，所以有一些 frame 其实可以使用到与 320Kbps frame 相同大小的空间。 VBR 并不使用 Bit Reservoir，因此需要允许使用 320Kbps 大小的 frame 才能达到与 CBR 相同的弹性。|
 
|\2.可变 Bitrate 模式 (VBR) 选项|
|-v|使用 VBR 模式。|
|–vbr-old|使用旧的 VBR 压缩算法。|
|–vbr-new|使用新的 VBR 压缩算法（预设）。|
|-V quality|指定 VBR 的压缩品质，范围为 0-9 （数字越小品质越高），默认值为 4。|
|-b bitrate|指定 Bitrate 变动的下限，预设为 32。 Kbps|
|-B bitrate|指定 Bitrate 变动的上限，预设为 320。 Kbps|
|-F|强迫遵守 -b 的限制，适合无法译码低 Bitrate 的 MP3 随身听使用。<br>不启动这一个选项，当输入为模拟静音（强度低于 ATH）时，将会忽略最低 Bitrate 的限制，使用压缩格式中的最低 Bitrate。|
|-t|不写入 Xing VBR 档头，会造成时间总长显示不正确以及搜寻功能失效等问题。|
|–nohist|压缩档案时，不显示 VBR Bitrate 使用率图表。|
 
|\2.MP3 文件头/数据流选项|
|-c|标记档案为有版权 (copyrighted)。|
|-o|标记档案为非原始文件 (non-original)。|
|-p|错误保护，会在每个 frame 占用 16bit 进行 CRC 编码（可能减损音质）。|
|–nores|不使用 Bit Reservoir 功能（会严重减损 CBR 音质）。|
 
|\2.滤波器选项|
|-k|保留住所有频率。<br/> 压缩时，为了把数据流量保留给最重要的频段，提高压缩出来的品质，LAME 会自动随着所选用的 Bitrate 指定高低通滤波器的数值。 如果把频宽设定的比默认值还要大的话，可能会造成无法预期的鸣响以及失真，小心使用！|
|–lowpass freq|设定低通滤波器的起始点为 freq<br/>高于这个频率的声音会被截除。 KHz|
|–lowpass-width freq|设定低通滤波器的宽度，预设为 15% 的 –lowpass 频率。|
|–highpass freq|设定高通滤波起始点为 freq<br/>低于这个频率的声音会被截除。 KHz|
|–highpass-width freq|设定高通滤波器的宽度，预设为 15% 的 –highpass 频率。|
|–resample freq|输出档案的取样频率。|
|–cwlimit freq|计算音色到指定的频率。|

LAME 内建设定 (–preset) 参考表

table{width:80%}.
| |phone|phon+|lw|mw-eu|mw-us|sw|fm|voice|radio|tape|hifi|cd|studio|
|–resample|8|11|11|11|16|11|32|24| |
|–highpass|0.125|0.1| |
|–lowpass|3.4|4|4|4|7.5|4|15|12|15|18|18| |
|–lowpass-width|0|0|0|0|0|0|0|0|0|0.9|0.9| |
|–noshort|yes|yes|no|no|no|no|no|yes|no|no|no|no|no|
| |-mm|-mm|-mm|-mm|-mm|-mm|-mj|-mm|-mj|-mj|-mj|-ms|-ms|
| |-h|-h|-h|
|-b|16|24|24|24|40|24|112|56|128|128|160|192|256|

|\14.VBR 模式时增加的参数：|
|-V|6|4|3|3|3|3|3|4|3|3|2|1|0|
|-b|8|16|16|16|24|16|80|40|96|112|128|192|160|
|-B|24|32|56|56|112|56|256|112|256|256|320|320|320|

举例：如果输入 lame –preset hifi a.wav，则效果跟 lame –lowpass 18 –lowpass-width 0.9 -mj -h -b 160完全相同。
