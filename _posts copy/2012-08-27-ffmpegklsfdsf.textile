---
title: "ffmpeg"
layout: post
category: Apple
---

#include <stdio.h>
#include <stdlib.h>
extern "C"{//
#include "avcodec.h"
#include "avformat.h"
}

http://www.wunderradio.com/code.html

int main(char arg,char *argv[])
{
    char *filename ="02.swf";
    
    av_register_all();//注册所有可解码类型
    AVFormatContext *pInFmtCtx=NULL;//文件格式
    AVCodecContext *pInCodecCtx=NULL;//编码格式
    if (av_open_input_file(&pInFmtCtx,filename,NULL, 0, NULL)!=0)//获取文件格式
        printf("av_open_input_file error\n");
    if(av_find_stream_info(pInFmtCtx) < 0)//获取文件内音视频流的信息
        printf("av_find_stream_info error\n");
    
    unsigned int j;
    // Find the first audio stream

    int    audioStream = -1;
    for(j=0; j<pInFmtCtx->nb_streams; j++)//找到音频对应的stream
        if(pInFmtCtx->streams[j]->codec->codec_type==CODEC_TYPE_AUDIO)
        {
            audioStream=j;
            break;
        }
        if(audioStream==-1)
        {
            printf("input file has no audio stream\n");
            return 0; // Didn't find a audio stream

        }
        printf("audio stream num: %d\n",audioStream);
        pInCodecCtx = pInFmtCtx->streams[audioStream]->codec;//音频的编码上下文
        AVCodec *pInCodec=NULL;
        /* FILE *file3 = fopen("codec_private_data_size.txt","w");
        for(int i = 0; i <200000; i++)
        {
        pInCodec = avcodec_find_decoder((CodecID)i);
        if (pInCodec!=NULL)
        {
        fprintf(file3,"%s %d\n",pInCodec->name,pInCodec->priv_data_size );
        }
        }
        fclose(file3);
        system("pause");
        */
        pInCodec = avcodec_find_decoder(pInCodecCtx->codec_id);//根据编码ID找到用于解码的结构体
        if(pInCodec==NULL)
        {
            printf("error no Codec found\n");
            return -1 ; // Codec not found
        }


        //使用test的代替pInCodecCtx也可以完成解码，可以看出只要获取以下几个重要信息就可以实现解码和重采样
        AVCodecContext *test = avcodec_alloc_context();
        test->bit_rate = pInCodecCtx->bit_rate;//重采样用
        test->sample_rate = pInCodecCtx->sample_rate;//重采样用
        test->channels = pInCodecCtx->channels;//重采样用
        test->extradata = pInCodecCtx->extradata;//若有则必有
        test->extradata_size = pInCodecCtx->extradata_size;//若有则必要
        test->codec_type = CODEC_TYPE_AUDIO;//不必要
        test->block_align = pInCodecCtx->block_align ;//必要

        
        if(avcodec_open(test, pInCodec)<0)//将两者结合以便在下面的解码函数中调用pInCodec中的对应解码函数
        {
            printf("error avcodec_open failed.\n");
            return -1; // Could not open codec

        }
        
        if(avcodec_open(pInCodecCtx, pInCodec)<0)
        {
            printf("error avcodec_open failed.\n");
            return -1; // Could not open codec

        }
        
        static AVPacket packet;
        
        printf(" bit_rate = %d \r\n", pInCodecCtx->bit_rate);
        printf(" sample_rate = %d \r\n", pInCodecCtx->sample_rate);
        printf(" channels = %d \r\n", pInCodecCtx->channels);
        printf(" code_name = %s \r\n", pInCodecCtx->codec->name);
        //printf("extra data size: %d :data%x %x %x %x\n",pInCodecCtx->extradata_size,pInCodecCtx->extradata[0]
        //     ,pInCodecCtx->extradata[1],pInCodecCtx->extradata[2],pInCodecCtx->extradata[3]);
        printf(" block_align = %d\n",pInCodecCtx->block_align);
        
        //system("pause");
        //////////////////////////////////////////////////////////////////////////

        uint8_t *pktdata;
        int pktsize;
        int out_size = AVCODEC_MAX_AUDIO_FRAME_SIZE*100;
        uint8_t * inbuf = (uint8_t *)malloc(out_size);
        FILE* pcm,*packetinfo;
        packetinfo = fopen("packetinfo.txt","w");
        pcm = fopen("result.pcm","wb");
        long start = clock();
        while(av_read_frame(pInFmtCtx, &packet)>=0)//pInFmtCtx中调用对应格式的packet获取函数
        {
            //fprintf(packetinfo," packet { pts=%d; dts =%d;%x,%x,%x,%x; size=%d; stream_index=%d; pos=%d;}\n",

            // packet.pts,packet.dts,packet.data[0],packet.data[1],packet.data[2],packet.data[3],packet.size,packet.stream_index,packet.pos);

            if(packet.stream_index==audioStream)//如果是音频
            {
                pktdata = packet.data;
                pktsize = packet.size;
                while(pktsize>0)
                {
                    //fprintf(packetinfo,"packet data:%x %x %x %x %x\n",pktdata[0],pktdata[1],pktdata[2],pktdata[3],pktdata[4]);

                    //fprintf(packetinfo,"packet size:%d\n\n",pktsize);

                    out_size = AVCODEC_MAX_AUDIO_FRAME_SIZE*100;
                    //解码
                    int len = avcodec_decode_audio2(pInCodecCtx,(short*)inbuf,&out_size,pktdata,pktsize);
                    if (len<0)
                    {
                        printf("Error while decoding.\n");
                        break;
                    }
                    if(out_size>0)
                    {
                        fwrite(inbuf,1,out_size,pcm);//pcm记录
                        fflush(pcm);
                    }
                    pktsize -= len;
                    pktdata += len;
                }
            }
            av_free_packet(&packet);
        }
        long end = clock();
        printf("cost time :%f\n",double(end-start)/(double)CLOCKS_PER_SEC);
        free(inbuf);
        fclose(pcm);
        fclose(packetinfo);
        if (pInCodecCtx!=NULL)
        {
            avcodec_close(pInCodecCtx);
        }
        if (test!=NULL)
        {
            avcodec_close(test);
        }
        av_free(test);
        av_close_input_file(pInFmtCtx);
        return 0;
}

 

一个英文版的例子（有讲解）
ffmpeg的一些使用例子
mpeg and SDL Tutorial
ffmpeg编译相关
ffmpeg工作组(中文) 

转自：

http://blog.chinaunix.net/u3/108358/showart_2132123.html