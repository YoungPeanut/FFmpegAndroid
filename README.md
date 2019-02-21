# FFmpegAndroid
android端基于FFmpeg库的使用。<br>
基于ffmpeg3.2.4版本，编译生成libffmpeg.so文件。<br>
添加编译ffmpeg源码的参考脚本<br>
目前音视频相关处理：<br>

- #### 音频剪切、拼接
- #### 音频混音
- #### 音频转码
- #### 音视频合成
- #### 音频抽取
- #### 音频解码播放
- #### 音频编码
- #### 视频抽取
- #### 视频剪切
- #### 视频转码
- #### 视频截图
- #### 视频降噪
- #### 视频抽帧
- #### 视频转GIF动图
- #### 视频添加水印
- #### 视频画面拼接
- #### 视频反序倒播
- #### 视频画中画
- #### 图片合成视频
- #### 视频解码播放
- #### 本地直播推流
- #### 实时直播推流
- #### 音视频解码播放
- #### OpenGL+GPUImage滤镜
- #### FFmpeg的AVFilter滤镜


左边是ffplay客户端拉流播放，中间是web网页播放：

![动态图片](https://github.com/xufuji456/FFmpegAndroid/blob/master/gif/live.gif)

视频添加文字水印（文字白色背景可以改为透明）：

![静态图片](https://github.com/xufuji456/FFmpegAndroid/blob/master/picture/water_mark.png)

视频转成GIF动图：

![动态图片](https://github.com/xufuji456/FFmpegAndroid/blob/master/gif/VideoToGif.gif)

滤镜效果：

![静态图片](https://github.com/xufuji456/FFmpegAndroid/blob/master/picture/filter_balance.png)

![静态图片](https://github.com/xufuji456/FFmpegAndroid/blob/master/picture/filter_sketch.png)

![静态图片](https://github.com/xufuji456/FFmpegAndroid/blob/master/picture/filter_edge.png)

![静态图片](https://github.com/xufuji456/FFmpegAndroid/blob/master/picture/filter_grid.png)

视频画中画：

![静态图片](https://github.com/xufuji456/FFmpegAndroid/blob/master/picture/picture_in_picture.png)

视频画面拼接：

![动态图片](https://github.com/xufuji456/FFmpegAndroid/blob/master/gif/horizontal.gif)

视频倒播：

![动态图片](https://github.com/xufuji456/FFmpegAndroid/blob/master/gif/reverse.gif)

***
<br><br>


##### AudioHandleActivity 音频-命令行
//转码
ffmpeg -i tiger.mp3 transform.aac
//剪裁
ffmpeg -i %s -ss 10 -t 15 %s
//合并，支持MP3、AAC、AMR等，不支持PCM裸流，不支持WAV（PCM裸流加音频头）
ffmpeg -i concat:a.mp3|b.mp3 -acodec copy ab.mp3
//混合
ffmpeg -i a.mp3 -i b.mp3 -filter_complex amix=inputs=2:duration=first -strict -2 ab.acc
//解码播放（AudioTrack）  c调Java
jmethodID audio_track_method =
    (*env)->GetMethodID(env,player_class,"createAudioTrack","(II)Landroid/media/AudioTrack;");
//解码播放（OpenSL ES）
(*engineEngine)->CreateAudioPlayer(engineEngine, &bqPlayerObject, &audioSrc, &audioSnk,
                                                3, ids, req);
//音频编码,可编码成WAV、AAC。如果需要编码成MP3、AMR，ffmpeg需要重新编译，把MP3、AMR库enable
ffmpeg -f s16le -ar 8000 -ac 1 -i in.pcm out.wav   //采样率8000 声道1
//PCM裸流音频文件合并
直接FileOutputStream 把 a.pcm b.pcm 写到ab.pcm

##### VideoHandleActivity  视频处理
//视频转码:mp4转flv、wmv, 或者flv、wmv转Mp4
ffmpeg -i %s -vcodec copy -acodec copy %s
//视频剪切
ffmpeg -i %s -ss %d -t %d %s
//视频合并

//视频截图
ffmpeg -i %s -f image2 -t 0.001 -s 1080x720 screenShot.jpg
//视频添加水印
ffmpeg -i %s -i launcher.png -filter_complex overlay=0:0 %s
//视频转成gif
ffmpeg -i %s -ss 30 -t 5 -s 320x240 -f gif %s
//屏幕录制
ffmpeg -vcodec mpeg4 -b 1000 -r 10 -g 300 -vd x11:0,0 -s %s -t %d %s
//图片合成视频
ffmpeg -f image2 -r 1 -i %s img#d.jpg -vcodec mpeg4 %s
//视频解码播放
videoPlayer.play(filePath, surfaceHolder.getSurface());
    //注册所有组件
    av_register_all();
    //分配上下文
    AVFormatContext * pFormatCtx = avformat_alloc_context();
    //打开视频文件
    if(avformat_open_input(&pFormatCtx, file_name, NULL, NULL)!=0)
    //获取视频流索引 videoStream = i
    pFormatCtx->streams[i]->codec->codec_type == AVMEDIA_TYPE_VIDEO
    //获取解码器上下文
    AVCodecContext  * pCodecCtx = pFormatCtx->streams[videoStream]->codec;
    //获取解码器
    AVCodec * pCodec = avcodec_find_decoder(pCodecCtx->codec_id);
    // 把AVPacket解码成AVFrame 此时一帧帧图片还涉及格式转换， YUV420P -> RGBA
    avcodec_decode_video2(pCodecCtx, pFrame, &frameFinished, &packet);
    // window与帧的步幅同步
    ANativeWindow* nativeWindow = ANativeWindow_fromSurface(env, surface);
    memcpy(dst + h * dstStride, src + h * srcStride, (size_t) srcStride);
    // 然后 ANativeWindow_unlockAndPost(nativeWindow)
//视频画面拼接:分辨率、时长、封装格式不一致时，先把视频源转为一致
ffmpeg -i %s -i %s -filter_complex hstack %s   hstack:水平拼接
//视频反序倒播
ffmpeg -i %s -filter_complex [0:v]reverse[v] -map [v] %s
//视频降噪
ffmpeg -i %s -nr 500 %s
//视频转图片
ffmpeg -i %s -ss %s -t %s -r %s %s
//两个视频合成画中画
ffmpeg -i %s -i %s -filter_complex overlay=%d:%d %s

##### MediaHandleActivity  Muxing 合成 提取
//音视频合成  视频文件有音频,先把纯视频文件抽取出来
ffmpeg -i %s -i %s -t %d %s
//提取视频
ffmpeg -i %s -vcodec copy -an %s
//提取音频
ffmpeg -i %s -acodec copy -vn %s

##### MediaPlayerActivity  音视频播放
与上面videoPlayer.play(filePath, surfaceHolder.getSurface())一样都是ffmpeg的播放，但这里定义了MediaPlayer结构体。

初始化：上下文，surface，编解码器，AVPacket生产者消费者线程队列
解码视频
解码音频
音视频同步

##### PushActivity  使用ffmpeg推流直播 flv

##### LiveActivity  实时推流直播:AAC音频编码、H264视频编码、RTMP推流

##### FilterActivity  加滤镜
    //滤镜数组
    private String[] filters = new String[]{
            "lutyuv='u=128:v=128'",
            "hue='h=60:s=-3'",
            "lutrgb='r=0:g=0'",
            "edgedetect=low=0.1:high=0.4",
            "boxblur=2:1",
            "drawgrid=w=iw/3:h=ih/3:t=2:c=white@0.5",
            "colorbalance=bs=0.3",
            "drawbox=x=100:y=100:w=100:h=100:color=red@0.5'",
            "vflip",
            "unsharp"
    };
    private String[] txtArray = new String[]{
            "素描",
            "鲜明",//hue
            "暖蓝",
            "边缘",
            "模糊",
            "九宫格",
            "均衡",
            "矩形",
            "翻转",//vflip上下翻转,hflip是左右翻转
            "锐化"
    };



