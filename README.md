# midi-to-simple-metal-gcode
Convert a MIDI music file to gcode instructions compatible with the Printrbot Simple Metal. The conversion code was obtained from where this repository was [forked from](https://github.com/michthom/MIDI-to-CNC).

The concept behind how a 3D printer can generate musical tunes is explained [here](http://zeroinnovations.com/3dprinting/how-to-play-the-imperial-march-on-a-3d-printer.html). That author also uses a Printrbot Simple Metal as well but did not mention his instructions so I created a short tutorial below.

##Demo videos on Youtube
[![](http://img.youtube.com/vi/PI1DXdU53Ps/0.jpg)](https://www.youtube.com/watch?v=PI1DXdU53Ps)

Playing the Singapore National Anthem. 

[![](http://img.youtube.com/vi/rh3QHoTB2Ts/0.jpg)](https://www.youtube.com/watch?v=rh3QHoTB2Ts)

Portal Still Alive.

[![](http://img.youtube.com/vi/en3cRWAqXwg/0.jpg)](https://www.youtube.com/watch?v=en3cRWAqXwg)

Fringe opening theme. The reference piano-video for this can be viewed [here](http://www.youtube.com/watch?v=oOMQ1LWBasw).

The Gcode for these are placed inside ```gcode_files``` directory.

##How to convert a midi file for the Printrbot Simple Metal?

Not all midi files can be supported or be converted properly. If my understanding is correct, only 3 notes can be played concurrently at any one time as there are only 3 axes motors available. The code will randomly pick any three notes if there are too many concurrent note playbacks. 

###1. Generate the Gcode file
```bash
git clone https://github.com/yeokm1/midi-to-simple-metal-gcode.git
cd midi-to-simple-metal-gcode
python mid2cnc.py -infile midi_files/national_anthem_singapore.mid -outfile gcode_files/singapore_national_anthem.gcode -machine custom -units metric -ppu 80 80 2020 -safemin 0 0 0 -safemax 120 120 120 -verbose
```
Replace the relevant paths with paths to your input and output file. I have set the bed size at a conservative 120mm x 120mm x 120mm although the Printrbot Simple Metal can go up to 150mm x 150mm x 150mm.  
The "-verbose" argument is optional.

To know what each argument means, check out the original [readme file](README).

###2. Make some modifications to the generated Gcode file

Replace these lines
```bash
( Input file was xxx.mid )
G21 (Metric FTW)
G90 (Absolute posiitioning)
G92 X0 Y0 Z0 (set origin to current position)
G0 X0 Y0 Z0 F2000.0 (Pointless move to origin to reset feed rate to a sane value)
G04 P0.0000
```
with 
```bash
G0 X0 Y0 Z0 F2000.0
G92 X0 Y0 Z0
```

The generated Gcode file seems a little "screwed" at the start because they set the origin at the current position then attempt to move to it (current position) which results in no movement. I removed the other stuff as they have no impact on the musical output.

###3. "Print" the Gcode
Send the Gcode file to your printer using your favourite 3D-printer control software. I happen to use Repetier-Host but others should work just as well. 

##Dependencies
1. Python 2
2. Repetier-Host (Any other 3D-Printer control software that allows you to send custom Gcode commands should be fine too)

##Resources
1. [Singapore National Anthem Midi file](http://www.midiworld.com/download/4159)
2. [Imperial March on 3D Printer](http://zeroinnovations.com/3dprinting/how-to-play-the-imperial-march-on-a-3d-printer.html)
3. [Fringe Midi video](http://www.youtube.com/watch?v=oOMQ1LWBasw)
4. [Portal 2 Still Alive Score](http://sebastianwolff.info/blog/2008/12/still-alive-sheet-music/)
