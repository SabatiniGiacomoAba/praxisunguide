---
title: "Tutorial 3 - Audio-reactive visuals"
date: 2020-06-06T16:30:59+02:00
draft: true
tags: ["sound", "midi", "audio-reactive", "audio"]
categories: ["basics"]
authors: ["Giacomo Sabatini"]

---
Hello everybody! We're getting to the final steps of this PraxisLIVE basic tutorial series! Today we're about to see the "core" of this software: how to create audio on PraxisLIVE? How to make interact Processing visuals with an audio input?

Let's open as usual our project, but this time by also creating a new file (Audio Patch). Once done, you'll see, like on video one when we created it, only the output node.  

{{<figure src="/imgs/3_Audio/audio_first.png" alt="" >}}

As we learned from previous tutorials, code by itself hasn't the cognition of time. So we need to create a **clock** node (audio -> clock). In the middle we must add a synth (a midi-sound generator). I'll choose from "audio: custom" a "synth-duo-osc" node (you can choose whatever you want from there). Between **clock** and **duo-osc** we need an iterator, that will be triggered by the _out_ socket signal and will send its _values_ to the _note_ socket through its _out_ one (like we did on the first tutorial to make camera capturing start). In _values_ we must insert the nodes to make it start. Here's an example taken from here:

{{<youtube_custom id="lK94qu1iObo">}}

{{< highlight html>}}
 a3 - g3 e2 - "c#2" - a2 X d3 X a3 b3 "c#4" d4
 {{</highlight>}}

You should have something like this:

{{<figure src="/imgs/3_Audio/audio_basic_set.png" alt="" >}}

When done, hit play above: if you choose **duo_osc** as I did, you should hear some weird sound (like old videogames) playing some notes. This is by default the synth sound, and here we must get a little into sound design matter. Synths basically produces this kind of sounds:

{{<figure src="https://i.pinimg.com/originals/04/68/6f/04686f729631a9d522e6c04576838504.gif" alt="" >}}

By default, **duo_osc** _waveform-1_ is set on Saw: you can choose also Square, or if you want to hear them both, you can set it on _waveform-2_. As you saw above, by mixing waves you can obtain more complex sounds. To go deeper into this matter, you could try this setup:

{{<figure src="/imgs/3_Audio/audio_mixer.png" alt="" >}}

In the mixer raise in levels or you won't hear anything. By setting different values, you could get interesting results. Just try!
There are other sockets that can modify the outcome sound:
- Attack: It's the passage between 0 and the sound maximum peak: a low value makes it very sharp, a high one instead makes it very soft like a flute note;
- Decay: This parameter states how much the sounds decades from the peak;
- Sustain: It's how much the level reached after Decay is mantained;
- Release: It's how long the passage from sustain level to 0 is.

{{<figure src="https://i.pinimg.com/originals/04/68/6f/04686f729631a9d522e6c04576838504.gif" alt="" >}}

Try to make the bass part! What you have to do is:
- to add an **order** node between the **clock** and the iterator;
- duplicate the iterator and edit values;
- choose a synth;
- plug it to the mixer and raise volume;

Just try to calibrate sounds in a way that all of them can be distinguished. You can also modify them by adding some effects like the **Overdrive** (audio:fx -> Overdrive). Before jumping into audio-reactive part, I would like to show a bunch of things: **osc** node and **player**. **osc** is known as the oscillator, that creates the sound waves I mentioned before, included Sine ones. Now, here you can't play notes by digiting notes but frequencies, which is quite the same thing (of course now you have a wider range) and it could be even funnier to animate. For now, let's just use the Iterator (with an extra order node) and, instead of using notes (a3, d3, and so on) we'll use (440, 520, and so on (they do not correspond to the previously written notes, those are just random numbers)). In the following pic, you may see that I plugged in the osc to in-4l and in-5l:this is because I wanted to trick the fact that there's no "panning" feature in that mixer, so by adjusting those two values I can simulate pan (example: in-4l = 0.5; in-5r = 0.3 means that sound will be in middle-left).

{{<figure src="/imgs/3_Audio/audio_osc.png" alt="" >}}

Next, the **player** node. This can play songs, but also tiny samples, like drums. Download one (I suggest you from [FreeSound.org](https://freesound.org/home/)) and move it to your resources folder (_your_username_/PraxisLIVE Projects/1 - Our First Project/resources). Once done, go to the player mode, and set on _samples_ node your file. Create a **start-trigger** node, plug it to _play_ socket and do the same with its two out sockets (l and r) to the mixer. What is peculiar about chosing a sample is that you can change its frequency just as we did with the **osc** node: we'll have to add a **filter** between the player and the mixer (plug ins to outs). Remember that you can create a sequence by plugging in an Iterator (or this time you could try a Random node and set its range from 0 to 20000) that must be plugged to **order**.

{{<figure src="/imgs/3_Audio/audio_player.png" alt="" >}}

Now we're ready to make our video audio-reactive! What we need is to send a value to the video patch. This time we'll send the volume of a specific track (in my case the basses edited with the Overdrive). Just add this two nodes like in this picture:

{{<figure src="/imgs/3_Audio/audio_send.png" alt="" >}}

For a moment, let's just head back to video, because we need to create the socket that will receive the data. There are essentially two ways to do it:
- create the socket into the node where the variable is required (like the Processing sketch);
- send it to a generic node with a value socket(e.g. "Variable", "Iterator" and so on).

In my case I want to make it directly on our 3d sketch: so I'll edit the code as we saw in the last episode, add a double called "audio_level". Since we're there, let's create the mechanism that will scale the cube according to audio_level value. Under draw() let's write this code:

       {{< highlight html>}}
       scale(map(audio_level, 0, 0.3, 0.0, 2.0));
        {{</highlight>}}

Now, scale can be also managed like this:
scale(x,y);
scale(x,y,z);

But... what is map()? In our case, since when testing this code I noticed that maximum value is 0.3, the scale effect wasn't that visible, so to improve it I "mapped" the value from the range of 0.0 and 0.3 to the bigger one 0.0 2.0. So 0.25 from the previous range now shall be 1.6 periodical.

If you play, of course cube will be invisible because it has 0 scale. Let's head back to audio patch. Click on send and edit address value: set it to video -> p3d (our processing sketch) -> audio_level.
Now you can hit play and enjoy the result! Of course every variable can be connected (you might want to try for example with the hue). For now, I won't explain how to grab other audio parameters, because I have to test them before, but since I'm planning to do an extra episode after the conclusion, I'll surely insert this.

Well, I think we've learned quite a lot of things, just remember that the great and very difficult sketches are "nothing more" (I convey with you that it' easy to say, but...) than a more sophisticated use of the basics (I've stolen this catch from Richard Williams). In the 4 and "last" episode we'll be exploring the GUI patch world: think of DJs, with those consoles with so many buttons, each that immediately changes a parameter, changes sound and so on. Infact, this will be necessary to change when doing our performance at an event, to make easier to change on the fly the things we care most to make both visual and sound more interesting.
Hope you enjoyed it! See you at the next episode!
