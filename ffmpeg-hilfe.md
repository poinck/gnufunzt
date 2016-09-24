## FFMpeg-Hilfe

### Record von Cynergy TV-Karte:

ffmpeg -f video4linux2 -channel 1 -i /dev/video0 -f pulse -i default /tmp/pulse.wav test1716.mkv

**nachträgliches Deinterlace:**
ffmpeg -i test1716.mkv -vf yadif -c:v mjpeg -q 0 -sn -f matroska  test1716_deinterlaced.mkv
	# -r 24 -vsync 1

**zum Überprüfen der Channeleinstellung**
ffplay -f video4linux2 -i /dev/video0 -channel 1


### Rohdaten:

MTS zu MJPEG MKV:
for f in *.MTS; do ffmpeg -i "$f" -filter:v yadif -c:a copy -c:v mjpeg -q 0 -sn -f matroska -r 24 -vsync 1 "$f.mkv"; done

MTS zu WAV:
for f in *.mts; do ffmpeg -i "$f" -c:a pcm_f32le "$f.wav"; done

Recordmydesktop OGV zu MJPEG MKV:
for f in *.ogv; do ffmpeg -i $f -c:a copy -c:v mjpeg -q 0 -sn -f matroska -r 24 -vsync 1 -y $f.mkv; done

GNOME3 WEBM zu MJPEG MKV:
for f in *.webm; do ffmpeg -i "$f" -c:a copy -c:v mjpeg -q 0 -sn -f matroska -r 24 -vsync 1 -y "$f.mkv"; done


### Blender (mehrfach rendern):
blender -b e0xb\ sur.blend -a -o //_frames


### High quality encoding (Youtube+Archiv):

Blender JPG zu x264 MKV:
x264 -v --crf 20 --fps 24 -o ep.000.mkv outframes/%04d.jpg
ffmpeg -r 24 -f image2 -start_number 325 -i _frames/%04d.jpg -r 24 -codec:v libx264 -q:v 20 "interview _final 140726.mkv"
ffmpeg -r 24 -f image2 -start_number 325 -i _frames/%04d.jpg -r 24 -codec:v libx264 -preset slow -crf 18 "interview _final 140726.mkv"

Blender WAV zu THEORA OGG:
oggenc -q8 sound.wav

THEORA OGG + x264 MKV zu x264 MKV + SOUND:
mkvmerge -o gnufunzt_episode_0.mkv ep.000.mkv sound.ogg


### Web:

MKV zu WEB MP4:
ffmpeg -i gnufunzt_episode_0.mkv -strict experimental -s hd480 gnufunzt_episode_0.mp4

MKV zu WEB WEBM:
ffmpeg -i gnufunzt_episode_0.mkv -vcodec libvpx -f webm -qmin 5 -qmax 25 -s hd480 -q:a 6 gnufunzt_episode_0.webm


### Torrent:

MKV zu TORRENT MP4:
ffmpeg -i gnufunzt_episode_0.mkv -strict experimental gnufunzt_episode_0_hd.mp4

MKV zu TORRENT WEBM:
ffmpeg -i gnufunzt_episode_0.mkv -vcodec libvpx -f webm -qmin 5 -qmax 25 -q:a 6 gnufunzt_episode_0_hd.webm

TORRENT:
mktorrent -v -a http://tracker.gnufunzt.de/announce gnufunzt_episode_0_hd.mp4
mktorrent -v -a http://tracker.gnufunzt.de/announce gnufunzt_episode_0_hd.webm

**alle Formate zusammen encoden**
```
export GE=13 ; ffmpeg -i "gnufunzt_episode_$GE.mkv" -strict experimental "gnufunzt_episode_${GE}_hd.mp4" ; ffmpeg -i "gnufunzt_episode_$GE.mkv" -vcodec libvpx -f webm -qmin 5 -qmax 25 -q:a 6 "gnufunzt_episode_${GE}_hd.webm" ; ffmpeg -i "gnufunzt_episode_$GE.mkv" -strict experimental -s hd480 "gnufunzt_episode_$GE.mp4" ; ffmpeg -i "gnufunzt_episode_$GE.mkv" -vcodec libvpx -f webm -qmin 5 -qmax 25 -s hd480 -q:a 6 "gnufunzt_episode_$GE.webm"
```

### PAL 4:3 .mp4, kompatibel zu Philips-Fernseher

Rendern in Blender:
Dimensions: Resolution 720x576, Framerate 24 fps
Output: MPEG
Encoding: Format MPEG-4 (Bitrate 6000), Audio-Codec AC3 (Bitrate 384)


