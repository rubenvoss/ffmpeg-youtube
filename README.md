# .bashrc:
```
#converting mac m1 screen recording to youtube format, uses all 8 cpus
# usage: ffmpeg-youtube input.mov output.mp4
ffmpeg-youtube() {
  ffmpeg -i "$1" -c:v libx264 -preset slow -crf 18 -c:a aac -ar 48000 -ac 2 -b:a 320k -profile:v high -level 4.0 -bf 2 -coder 1 -pix_fmt yuv420p -b:v 15M -threads 8 -cpu-used 0 -r 60 "$2"
}
```



Recommended encoding settings for YouTube in FFMpeg – ScribbleGhost
5–6 minutes

When you upload a video to YouTube, YouTube will convert that video to an appropriate video format for streaming.

This can take some time, and you really have no control over how YouTube converts your video. If you have the hardware for it, you can convert the video yourself before uploading it. This way you might save some time waiting for the video to get ready and you will have more control over the visual quality of the video.

YouTube has an official encoding guide, which if you follow correctly, will result in the video not having to be converted by YouTube once you upload it.

We can use the handy command-line-based tool FFMpeg to follow these recommendations.

Here is an example for a 1080p SDR video at 60 frames per second:

ffmpeg -i input.ext -c:v libx264 -preset slow -crf 18 -c:a aac -ar 48000 -ac 2 -b:a 320k -profile:v high -level 4.0 -bf 2 -coder 1 -pix_fmt yuv420p -b:v 10M -threads 4 -cpu-used 0 -r 60 output.mp4

Let’s break this down a little.
Container

output.mp4

Youtube recommends using MP4 as the video container type. So we will use output.mp4
Audio codec

-c:a aac -ar 48000 -ac 2 -b:a 320k

Youtube recommends using AAC-LC so we can write -c:a aac to convert the audio stream to the “Advanced Audio Coding” codec. This offers better quality than MP3 but is still a lossy format though.

You will also need to use either -ar 48000 which sets the sampling frequency/sample rate to 48kHz, or -ar 96000 kHz which sets the sample rate to 96khz. Trust me when I say that there is absolutely no reason to use 96kHz. Just use 48 kHz, it won’t reduce the audio quality.

Videos are almost always in 48kHz unless you got some high-quality-super-duper-lossless audio going on.

The AAC codec for FFMpeg will by default set the audio bitrate to 128kbps unless else is specified. 128kbps is rather low quality so we should set the bit rate to a minimum of -b:a 320k This is really not required from Youtube, and it doesn’t make sense to limit the audio bitrate, since storage is not an issue here. But since the FFMpeg AAC codec limits the bit rate to 128kbps by default, you should probably set this.

If your video has surround sound you might consider converting the audio to stereo, since most YouTube watchers are not going to have surround speakers. You can downmix surround audio to stereo with: -ac 2 or -ac 5.1.
Video codec

Youtube wants us to use MPEG-4 Part 10, Advanced Video Coding (MPEG-4 AVC) as the video codec, also simply known as H.264.

-c:v libx264 will set the encoder to x264.

As an optional choice, we can also set the encoding speed. A preset deciding how long we want to wait for the encoding. -preset slow is a preset that provides slower, but better better compression. A rule of thumb is to use the slowest preset that you have the patience for. Available presets are: ultrafast, superfast, veryfast, faster, fast, medium, slow, slower, veryslow or placebo. You should never use placebo as it will be extremely slow while adding virtually no visual difference at all.

Moving on to the quality of the video. This is strictly not a Youtube recommendation, but it’s nice to have control over the visual quality of the video. We can set the quality of the video changing the CRF value. CRF stands for “Constant Rate Factor”. The range of the scale is 0-51: where 0 is lossless (best quality), 23 is the default, and 51 is the worst possible. You would probably want to set this to anywhere from -crf 18 to -crf 28. Consider 18 to be visually lossless, or at least or nearly so.

Next Youtube recommends us to use a H.264 profile of “High -level 4.0”. That can be written as -profile:v high in FFMpeg.

-bf 2 Sets 2 consecutive B frames. FFMPEG’s default is 16.

-coder 1 sets the entropy encoder to CABAC. By default, CABAC is the entropy encoder used by x264.

-pix_fmt yuv420p Sets the aspect ratio to 16:9.

-b:v 10M Sets the bitrate to 10Mbps. This is good if the footage is 24,25 or 30 frames per second. Set this to 15M if the video is 48, 50 or 60 fps. More recommendations here.

-threads 4 Enables FFMpeg to use 4 threads for multi-core CPUs. Set this to whatever is appropriate for you.

-cpu-used 0 Most CPU intensive, but best fastest. Uses all cores.

-r 60 This is not required by Youtube but optional. The current maximum supported frame rate on Youtube is 60fps. You can upload at whatever FPS you want, but if you want to downsample the content yourself you can use this. It might save you some time waiting for Youtube to encode the video.
