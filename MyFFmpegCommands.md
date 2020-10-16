# My ffmpeg commands 


I've created this file as a Quick Help Guide for me in the day-2-day use of FFmpeg while preparing, transcoding, decoding LCEVC clips for a number of different evaluations.
Note that, as of now, these commands require a particular version of FFmpeg that can be obtained from me.

## help for lcevc 

`ffmpeg --help encoder=lcevc_h264`

`ffmpeg --help decoder=lcevc_h264`

`ffmpeg --help encoder=lcevc_hevc`

`ffmpeg --help decoder=lcevc_hevc`

## Encoding


To encode with debug surfaces, simply add this to the command line \
` -eil_params "debug_write_surfaces=1"`


## Decoding

Decode a file to YUV \
`ffmpeg -vcodec lcevc_h264 -i d:\Disney\Muppets_sticking_residuals3.mp4 -f rawvideo d:\Disney\Muppets_sticking_residuals3.yuv`

Decode to null \
`ffmpeg -y -vcodec lcevc_h264 -disable_dithering 1 -i d:\Disney\Beauty_lcevc_h264.mp4 -f null -`

## Transcoding

Transcoding 264 \
`ffmpeg -y -i d:\Disney\Beauty_h264.mp4 -c:v libx264 -preset medium -b:v 2m ..\testOutput.mp4`

and if you want to resize the input video: \
`ffmpeg -y -i ..\Netflix_BarScene_DVLABS_MEZZ.mp4 -s 1920x1080 -c:v libx264 -preset medium -b:v 2m ..\testOutput.mp4`


Transcoding lcevc \
`ffmpeg -y -vcodec lcevc_h264 -disable_dithering 1 -i d:\Disney\Beauty_lcevc_h264.mp4 -c:v libx264 -preset medium -b:v 2m ..\testOutput.mp4`


## Cropping Video

`ffmpeg -f rawvideo -s 4096x2160 -r 60 -pix_fmt yuv420p10le -i "D:\Sources\UHD\AoM\Netflix_ToddlerFountain_4096x2160_60fps_10bit_420.yuv" -filter:v "crop=3840:2160:128:0" -r 60 -pix_fmt yuv420p10le -vcodec rawvideo "D:\Sources\UHD\AoM\Netflix_ToddlerFountain_3840x2160_60fps_10bit_420.yuv"`


## Rescaling

`ffmpeg -i input.yuv -vf scale=960:540 -c:v rawvideo -pix_fmt yuv420p out.yuv`

## Creating an RTMP stream

`LD_LIBRARY_PATH=. taskset -c 0-5 ./ffmpeg -re -fflags +genpts -stream_loop -1 -i BBB.mp4 -c:v lcevc_h264 -base_encoder x264 -threads 6 -r 30 -g 60 -c:a copy -s 1920x1080 -b:v 3M -bufsize 2000k -eil_params "encoding_debug_residuals=1" -f flv rtmp://rtmp.london.v-nova.com/v-nova/BBB_residuals`

`ffmpeg -re -fflags +genpts -stream_loop -1 -i <input file>.mp4 -c:v copy -c:a copy -bufsize 2000k -f flv "rtmp://rtmp.us-east-1.v-nova.com/v-nova/Twitch"`

## Peforming metric evaluation

### PSNR
Calculating PSNR with FFmpeg:

`ffmpeg -i [Input1] -i [Input2] -lavfi psnr -f null -`

`ffmpeg -i [Input1] -i [Input2] -filter_complex psnr -f null -`



## Customer specific

### Disney
Here are some examples of command lines that we used to generate clips for Disney

This is a command for a 540p output as used by Derek with mostly defaults:\
`ffmpeg -i d:\Disney\Muppets.mov -pix_fmt yuv420p -vf scale=w=-2:h=540 -c:v lcevc_h264 -base_encoder x264 -threads 8 -g 48 -b:v 400k -eil_params "dc_dithering_type=uniform;dc_dithering_strength=4;preset=veryslow;rc_pcrf_window_type=rolling" d:\Disney\Muppets_sticking_residuals3.mp4`

This command is for a 480p output handcrafted by rick\
`ffmpeg.exe -y  -i C:\EncoderLab\sources\muppets.mov -c:v lcevc_h264 -base_encoder x264 -an -pix_fmt yuv420p -s 856x480 -c:v lcevc_h264 -base_encoder x264 -threads 8 -g 48 -b:v 400k -eil_params "dc_dithering_type=uniform;dc_dithering_strength=4;preset=veryslow;rc_pcrf_window_type=rolling;deterministic=1;m_strength1=0.7;m_strength2=0.9;scaling_mode_level0=2D;encoding_downsample_luma=lanczos" muppets-derek-rick19-480p-400k.mp4`