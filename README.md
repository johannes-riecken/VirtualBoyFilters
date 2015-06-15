# VirtualBoyFilters
Filters to beautify videos or screenshots of monochrome Virtual Boy games

Virtual Boy videos look better with colors! In the following I describe how to convert a video of a Virtual Boy game using the command line tools FFmpeg and ImageMagick. 

## Requirements
[FFmpeg](www.ffmpeg.org) for converting the video into pictures of single frames and converting the modified frames back to a video.
[ImageMagick](www.imagemagick.org) for applying a filter to every video frame.
A file colors.png (sample in this repository), which contains the colors that will be used for translating grayscale values to colors. 

## Steps
Install FFmpeg and ImageMagick and add both to PATH. 
Download colors.png to the same folder as the video (referred to as `video.mp4` in commands) and open a command line in that folder.
Depending on your command line interpreter, you will have to escape parentheses with a backslash. 
If necessary shorten the video's duration (here the cut begins after 1 minute 30 seconds and runs for 10 seconds.
```
ffmpeg -ss 0:1:30 -i video.mp4 -t 0:0:10 short.mp4
```
Extract the video's sound.
```
ffmpeg -i short.mp4 -f mp3 sound.mp3
```
Extract the video's frames.
```
mkdir frames/
ffmpeg -i short.mp4 frames/%04d.png
mkdir out/
```
Apply filter to frames. (Alternative 1 for short videos or PCs with much free RAM. Modify 1280x720 to match the image dimensions).
```
convert frames/*.png null: ( -clone 0--1 -blur 12x12 -append colors.png -clut -crop 1280x720 +repage ) -compose multiply -layers composite +adjoin out/%04d.png
```
Apply filter to frames. (Alternative 2 if Alternative 1 is too slow; for Windows Command Line).
```
for %f in frames/*.png do convert %f ( +clone -blur 12x12 colors.png -clut ) -compose multiply -composite out/%f
```
Convert the modified frames back to a video with sound.
```
ffmpeg -i out/%04d.png -i sound.mp3 -o out.mp4
```

## Caveats
Applying the filter can be slow. Ways to speed it up:
- Leave out the "-blur 12x12" part or use smaller blur parameters. 
- Only have a few frame images in the frames/ folder at a time. I tried it with 300 frames without issues.
Conversion needs much disk space, because pictures of every video frame need to be written to the hard drive first. 

## References
colors.png created using a technique by [Fred Weinhaus](http://fmwconcepts.com/imagemagick/pseudocolor/index.php)
