# sixvid

<img src="README.md.d/sixvid.gif" align="right" width="33%">

Simple script for animated GIF viewing using sixels

## What is sixvid?

Sixvid is a looping GIF viewer for sixel terminals written in bash. It
was something hackerb9 wrote a long time ago and may never be properly
finished. It does have some nice features, though.

## Usage

**sixvid** [ *-e* | *-s* | *-S* ]  [ *-b* ] &lt;filename>

```bash
$ sixvid nyantocat.gif
```

### Keys

| Key | Action |
|:-:|:-:|
| Space | Quit |
| p | Pause |
| [, ] | Slower, faster FPS |
| Backspace | Reset FPS |

Other keys
| Key | Action |
|:-:|:-:|
| b | Benchmarking (draw as fast as possible) |
| ^L | Redraw screen <br/>(in case of scrolling glitches)|
| M | use mimeopen to launch external viewer |
| q, Esc | Quit, but with different return code<br/>(for wrapper scripts) |


### Command line options

| Short | Long | Meaning |
|:-|:-|:-|
| -b | --benchmark | Run as fast as possible, setting goal FPS to infinity |
| -e | --no-shrink<br/>--exact | Show image exactly, no color/geometry reduction regardless of ssh or serial connection |
| -s | --shrink<br/>--ssh | Force shrinking color/geometry<br/>(pretend to be over ssh) |
| -S | --serial | Force shrinking color/geometry<br/>(pretend to be over a serial connection) |


## Niceties

* Can be used as a rough benchmark of relative sixel performance for
  various terminal emulators on the same machine. (See
  [**Benchmarking**](#benchmarking-with-sixvids-fps) below)

* Decodes video and converts to sixel in background processes.

* Shows status of background jobs and loops on as much video as has
  been decoded so far.

* Dynamically adjusts delay loop to approximate correct FPS.

* Attempts to shrinks geometry/color depth when on slow ssh/serial connection.

## Requirements

* A sixel capable terminal (e.g., `xterm -ti vt340`)

* ImageMagick

* ffmpeg (only needed for video formats ImageMagick does not innately know)

## Limitations

A "limitation" is a bug we have declared to be "by design" because we
can imagine no better way the world could be. All bugs we reject as
"by design" are in truth limitations, not of our software, but us.

* Does not attempt to play audio. This is unlikely to happen until
  machines are so fast it's easy for a shell script to write 44,100
  16-bit stereo samples per second to an audio device.

* Takes an exorbitant amount of /tmp space as it unpacks every frame
  as sixels. Gzipping the sixels may help, at the cost of CPU, but
  there is no obvious solution to this problem.

* Large videos on slow CPUs might not finish rendering to sixel before
  the animation loops back to the start. The most obvious alternative,
  stuttering along as the CPU decodes each frame, is boring to watch
  while waiting for the decoding to finish. The second most obvious
  alternative, waiting until the entire video is decoded, is a
  non-starter as the video may be very long and the user might only
  need to see the first few seconds before they want to quit. 

## Bugs, bugs, bugs

* If first guess of weightestimate — milliseconds to transfer and
  display a single sixel frame — is far off the mark, then it can take
  a few seconds for the animation to play at the correct FPS.

* For large videos, probably should render to 1-bit sixel as quickly
  as possible and then replace it with higher depth once all the
  frames are done.

* Should use DECSDM so that large images don't cause scrolling, but
  we're in the awkward, teenage years of understanding DECSDM.
  (Viz.: dankamongmen's [DECSDM is a poorly-understood hellscape"](https://github.com/dankamongmen/notcurses/issues/1782)).

* Uses number of sixel bytes being sent to guess at framerate, but
  some terminals may be slower at rendering a large image than one
  with more colors, given the same filesize.


## Todo

* Utilize transparent GIF techniques to optimize sixel output.

* Currently, we just have the initial delay hardcoded, but we ought to
  try sending a single frame to get a better estimate.

* Average the benchmark FPS over time and also show std deviation.
  (Some terminals are inconsistent in their speed).

* If a terminal supports double-buffering, as the VT340 does, use it.

* Someday, go back to using DECSDM (sixel display mode).

## Benchmarking with sixvid's FPS

You can use `sixvid` to give you a rough idea of how fast the various
sixel interpreters in terminal emulators function relative to each
other on your machine. Hit the letter `b` while viewing (or use the
`-b` command line flag) to turn on benchmarking mode, which attempts
to send the image as fast as possible.

Because the FPS number depends greatly on your own machine, it is not
a pure measurement of an terminal emulator's speed. You cannot say one
terminal emulator is faster if the numbers were calculated on
different machines.

With that caveat, the sixvid FPS numbers are good enough to at least
give you a sense of relative speed of terminal emulators.

As of August 2021, here are the speeds on hackerb9's box using the
[nyantocat.gif](../blobs/main/nyantocat.gif) image.

```bash
sixvid --benchmark nyantocat.gif
```

|Terminal|Frames per Second|Notes|
|:-:|:-:|:-|
|foot 1.6.4 | 169 FPS||
|mlterm 3.9.0 | 240 FPS||
|XTerm(366) | 223 FPS||
|yaft 0.28+<br/>(git 2018-11-14) | 230 FPS|Linux framebuffer version|
