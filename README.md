# midi-to-simple-metal-gcode
Convert a MIDI music file to gcode instructions compatible with the Ultimaker 2. The conversion code was obtained from where this repository was [forked from](https://github.com/michthom/MIDI-to-CNC).

The concept behind how a 3D printer can generate musical tunes is explained [here](http://zeroinnovations.com/how-to-play-the-imperial-march-on-a-3d-printer/).

##Demo videos on Youtube
To be added...

The Gcode for these are placed inside ```gcode_files``` directory.

##How to convert a midi file for the Ultimaker 2?

Not all midi files can be supported or be converted properly. If my understanding is correct, only 3 notes can be played concurrently at any one time as there are only 3 axes motors available. The code will randomly pick any three notes if there are too many concurrent note playbacks. 

###1. Generate the Gcode file
```bash
git clone https://github.com/yeokm1/midi-to-ultimaker2-gcode.git
cd midi-to-ultimaker2-gcode
python mid2cnc.py -infile midi_files/national_anthem_singapore.mid -outfile gcode_files/singapore_national_anthem.gcode -machine custom -units metric -ppu 80 80 3200 -safemin 25 25 25 -safemax 170 170 170 -axes X -verbose
```
Replace the relevant paths with paths to your input and output file. I have set the bed size quite small although the Ultimaker 2 can go much larger to prevent going to near the edges.  
The "-verbose" argument is optional.

To know what each argument means, check out the original [readme file](README).

I have limited the motor movement to only the X axis as the sounds from the other motors seem to be muffled. You can go ahead and experiment by removing/modifying the axes argument.

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
M107
G0 F7200 X25 Y25 Z170
G92 X0 Y0 Z0
```

The generated Gcode file seems a little "screwed" at the start because they set the origin at the current position then attempt to move to it (current position) which results in no movement. I removed the other stuff as they have no impact on the musical output.

###3. "Print" the Gcode
Copy the Gcode file to the SD card and just let the Ultimaker 2 handle the printing.

##Dependencies
1. Python 2

##Resources
1. [Singapore National Anthem Midi file](http://www.midiworld.com/download/4159)
2. [Imperial March on 3D Printer](http://zeroinnovations.com/3dprinting/how-to-play-the-imperial-march-on-a-3d-printer.html)
3. [Fringe Midi video](http://www.youtube.com/watch?v=oOMQ1LWBasw)
4. [Portal 2 Still Alive Score](http://sebastianwolff.info/blog/2008/12/still-alive-sheet-music/)
