---
title: "Tutorial 4 - One GUI to rule them all"
date: 2020-06-15T10:23:52+02:00
draft: true
tags: ["gui", "patches", "buttons", "sliders", "input"]
categories: ["basics"]
authors: ["Giacomo Sabatini"]
---

Hello there! We got to the final chapter of our PraxisLIVE basic series (excluding the extra chapter)! Today we'll explore the GUI patch, which you might find very useful if you are going to show your work at a live event. The title, a semi-quote from "The Lord of the Rings", sums what GUI do: it allows you to control live parameters all in one screen very quickly, without switching between all the nodes.

Let's open our project and create a GUI patch (New file -> GUI). Once done, click on "Edit" from the GUI patch tool bar. Once highlighted, select Palette (right side of the screen).

The functions that we're going to plug are:

Audio patch:

- **Overdrive**'s _drive_;
- **Mixer** levels;
- **player**'s play (the one that contains the drums);
- **iterator-1**'s values (the notes played by **pm-synth** and **synth-base** under it);

Video patch:

- **variable-1** values (the higher value to reach);
- **p3d** scale-to-reach (is a parameter that I created off-screen; it defines maximum scale value of our cube);


Let's start by adding a __panel__ (drag it on the middle of the screen). On properties, set Label as "Audio Management" and set layout as follows:

{{< highlight html>}}
cell 0 1, width 500, height 700
// "cell" is a parameter to assign the content at specific columns and rows.
 {{</highlight>}}

- Drag in another __panel__ inside the previous one and set Label as "Mixer";
- still another panel inside Mixer with label "Layer 1";
- drag a __v-slider__ named "Master" and a __button__ called "Mute";
- Repeat those last two steps other 5 times (you must have 6 layers all left-aligned).

In this way you made an interfaced Mixer that now we're going to connect to the one in audio patch. To plug those sliders to their respective parameters, go to the element properties (like Layer 1 master slider), click on the three dots near to Bindings and choose the audio property like we did with the **send** node on audio patch. Well, the rest of the button can be made in the same way:

- for the notes you can create a "textfield";
- for Overdrive - drive just use a slider;
- Video Management requires a separated panel;
- to manage hue use an "h-rangeslider" (set minimum binding to video -> variable 2 -> value; maximum binding instead video -> variable 1 -> value). In this way, you'll be able to manage the whole range;
-  for scale to reach you can choose between a slider or a textfield;

I added also an "xy-pad" that allows me to move the cube (Create on p3d script X and Y sockets, substitute translate(width/2, height/2) with translate(X,Y); then set binding-x to X, y to Y, min-x = 0, max-x = 800, min-y = 0, max-y = 600. I suggest you to set controller's width and height keeping sketch ratio).

If you literally followed what said above, you should have something like this:

{{<figure src="/imgs/4_OneGUItoRuleThemAll/final-gui.png" alt="" >}}

Well, this tutorial has been quite briefer than usual, since it's not that complicated to explain the basics of it. Maybe on advanced series we'll explore it better, understanding how we can achieve even weird results or how to design that board to fulfill our needs. Thank you very much for getting until here, I hope that it has been funny and that I've been able to make you interest about this software and the advanced multimedia world. Remember that I will publish a bonus episode with all the tips and tricks that I might have missed all the way long. I'll leave you the download link of the whole project in the new Resources Section that it will be soon available. See you soon!
