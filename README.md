# sixvid

<img src="README.md.d/sixvid.gif" align="right">

Simple script for animated GIF viewing using sixels

## What is sixvid?

Sixvid is a looping GIF viewer for sixel terminals written in bash. It
was something hackerb9 wrote a long time ago and may never be properly
finished. It does have some nice features, though.

## Niceties

* Can be used as a rough benchmark of relative sixel performance for
  various terminal emulators on the same machine. (See
  **Benchmarking** below)

* Decodes video and converts to sixel in background processes.

* Shows status of background jobs and loops on as much video as has
  been decoded.

* Dynamically adjusts delay loop to approximate correct FPS.

* Attempts to shrinks geometry/color depth when on slow ssh/serial connection.

## Limitations

* Does not attempt to play audio. This is unlikely to happen until
  machines are so fast it's easy for a shell script to write 44,100
  16-bit stereo samples per second to an audio device.

* Takes an exorbitant amount of /tmp space as it unpacks every frame
  as sixels.

## Bugs, bugs, bugs

* If first guess of delay is far off the mark, then it can take a few
  seconds for the animation to play at the correct FPS.

* Large videos might not finish rendering to sixel before the
  animation finishes showing everything. 

* For large videos, probably should render to 1-bit sixel as quickly
  as possible and then replace it with higher depth once all the
  frames are done.

* Should use DECSDM so that large images don't cause scrolling, but
  we're in the awkward, teenage years of understanding DECSDM.
  (Viz.: dankamongmen's [DECSDM is a poorly-understood hellscape"](https://github.com/dankamongmen/notcurses/issues/1782)).

* Uses number of bytes being sent to guess at framerate, but xterm may
  be slower at rendering a large image than one with more colors,
  given the same filesize.


## Todo

* Currently, we just have the initial delay hardcoded, but we ought to
  try sending a single frame to get a better estimate.


