# Video Playback

This error is from vlc. I got it when trying to play a video file `.mp4`.

```
[00007fc9f4c0cc90] main decoder error: Codec `h264' (H264 - MPEG-4 AVC (part 10)) is not supported.
```

This error is raised when libraries used to decode video formats are missing. Fix it by installing the needed packages:

```sh
sudo pacman -S vlc-plugin-ffmpeg
```
