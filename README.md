#The Project

This is an arduino project for automated octave switching.
Run it as a proxy between midi out and midi in (can be done with only one keyboard on keyboards that let you disable local keys, like a Novation UltraNova).

The end result is kind of like manual arpeggiation.  It lets you do
extremely fast (as in, you might possibly outrun the hardware!) scale
and arpeggio runs.  It isn't clock based, and doesn't make guesses.
It only plays notes you actually play, but octave switches to the nearest note to the previous note.  In practice it means that jumps up of a fifth or more shift octave down, and jumps down of a fifth or more shift an octave up.

#How To

The keyboard is a quartertone flat below middle C, and designed exclusively for playing mono voices.

Demonstration Video:

https://www.youtube.com/watch?v=IfWY_6Q8RX4

Tutorial on using the quartertone split:

https://www.youtube.com/watch?v=vimtrlz7cGs&feature=autoshare

To build one, install the Arduino SDK:

http://www.arduino.cc

Buy an Arduino Uno, easily available from electronics stores for as low as $5:

![Arduino Uno](images/arduinouno.jpg)

Buy a MIDI shield for roughly $20.  You typically need to buy them online.  I find them on Amazon (Olimex, LinkSprite).  You only need MIDI IN and OUT for these purposes (you can buy a partial MIDI shield that doesn't have a MIDI THRU, with only IN and OUT if you like):

![LinkSprite MIDI Shield](images/midishield.jpg)
![Olimex MIDI Shield](images/midishield2.jpg)

Stack them together.  Be very careful to align the pins correctly before plugging into USB (ie: the power source), or you can damage the board.  Then use the Arduino SDK to upload octaveRounder.ino into the Arduino boardi over the USB.

As always with MIDI, make sure that the keyboard is set to transmit MIDI out (usually on channel 1).  Then MIDI OUT from that keys controller into the MIDI IN of the MIDI Shield, and MIDI OUT from the Shield into the MIDI IN of the synth.  If your MIDI keys controller has a disable local keys option, use that.  If you can disable local keys, you can use the same device for keys controller and synth.  I use a Novation UltraNova for this purpose:

![Novation UltraNova](images/ultranova.jpg)


#How It Works

The expected behavior of this MIDI filtering pedal can be clearly defined by saying what bytes we expect to come out of the pedal in response to certain bytes going in.  To simplify things, assume that we are going to work with MIDI channel 1 only.  We will talk entirely in terms of hexadecimal numbers when speaking of the protocol.  That means that MIDI:

- turn on notes with a byte 0x90, a note number byte, then a volume byte
- turn off notes with a byte 0x80, note number byte, and a parameter for how hard to turn it off.  this is a rarely used options.
- turn off notes with a byte 0x90, a note number, but a zero volume byte.  this is what most MIDI devices do in practice.
- In our notation, green text with a '?' denotes byte input
- red text with a '!' denotes byte output

A completely transparent pedal would simply emit exactly the bytes that were put into it.  But our filter will at the very least need to alter the note numbers to match up its internal notion of where its octave switch is at.  A simple downward arpeggiate rewrites the notes, and looks like this:

![unittest1](images/unittest1.png)

Note that we see the note going up and going back down.  If our filter works correctly, all notes should eventually be back to zero no matter what we do to the keyboard.
This is an arpeggio going up (note the 0x0c is 12 in hexadecimal - the number of notes in an octave)

![unittest2](images/unittest2.png)



