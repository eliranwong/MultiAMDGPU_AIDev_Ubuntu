MP4 TO MP4 (MEDIUM)

> ffmpeg -i input.mp4 -b 1000000 output.mp4

M2TS TO MP4

> ffmpeg -i input.m2ts -vcodec libx264 -crf 20 -acodec ac3 -vf "yadif" output.mp4

MP4 TO WEBM (HIGH)

> ffmpeg -i input.mp4 -aq 5 -ac 2 -qmax 25 -threads 2 output.webm

MP4 TO WEBM (MEDIUM)

> ffmpeg -i input.mp4 -aq 5 -ac 2 -qmax 35 -threads 2 output.webm

MP4 TO OGV (HIGH)

> ffmpeg -i input.mp4 -vcodec libtheora -acodec libvorbis -q:v 6 -q:a 5 output.ogv

MP4 TO OGV (MEDIUM)

> ffmpeg -i input.mp4 -vcodec libtheora -acodec libvorbis -q:v 2 -q:a 4 output.ogv

WEBM TO MP4 (https://blog.addpipe.com/converting-webm-to-mp4-with-ffmpeg/)

> ffmpeg -i input.webm output.mp4
