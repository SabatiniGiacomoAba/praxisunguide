---
title: "Tutorial 2 - Let's start coding!"
date: 2020-05-29T09:44:26+02:00
draft: true
tags: ["second", "coding", "processing", "3D"]
categories: ["basics"]
authors: ["Giacomo Sabatini"]

---
Welcome to this second episode of the basic tutorial about PraxisLIVE! Today we'll be starting coding with Processing, tasting a little bit what it's capable of!

Let's re-open our last project: you should have it displayed on the left. Then click on the gear near to the play button to **Build** the project. Once done, you should see again your video file opened.

First of all, we shall edit the **animator** node to make it loop

Since I noticed that blur is not that visible as effect, we'll be also change some nodes:
- add an **fx-hsv** node (under video:gl-custom) between **composite** and **screen**;
- plug the **animator** node to _hue_;
- create a new **variable** node: set value to 0 and plug _out_ to **animator**'s value and _trigger_ to **start-trigger**'s _out_; this will reset the value node everytime you'll play the project;

You should end with something like this:

{{<figure src="/imgs/2_LetsStartCoding/1-video_animation_hsv.png" alt="" >}}

The new animation will change the hue of the webcam output.
Now let's right-click over the **animator** node and select "Edit code". A new tab will appear and you shall see the node's code. For the ones who are new to coding it will look like a non-sense and scary text, maybe useful to summon weird demons or whatever: I understand that feeling, since for me at the beginning it was the same, but it will be everything clear on the next steps.

Here's a breakdown of the code:

This is a socket, with the _type_ specified as Number (it means that only numerical values are allowed).
{{< highlight html>}}
@P(1) @Type.Number @Transient @OnChange("toChanged")
 double to;
{{</highlight>}}

A "void" calls a part of the script that can be called when triggered and it will execute only once per-call. "update" void runs instead when the whole script is called and always keep running.

{{< highlight html>}}
public void update() {
       if (value.isAnimating()) {

           out.send(d(value));
           active = true;


       } else if (active) {
           out.send(to);
           active = false;
       }
   }

{{</highlight>}}

What we need is to create a new socket for a peculiar kind of variable: a boolean. It can have only two kind of values: **true** or **false**.

{{< highlight html>}}
@P(4) boolean loop;

{{</highlight>}}

Then we need to create an **if/else** statement: this is used to put a condition to make part of code executed. If you give a look to the code above, you can see that there are an **if** and its condition between round parenthesis and an **else** with another block of code. In that example, the code can be explained like this:
**IF** value changes, then send it to the _out_ socket and keep **active** bool true; if not (**else**), keep last value sent and active is false.


In our case, **if** we toggle on (translated in code language, if loop is **true**) the loop checkbox, then animation will loop, **else** will execute only one time as default.

To make the loop happen after the first transition, we need to make the animation go back and forth. To make this, we'll create a
double variable called _toKeeper_. We'll also create a new void named **setup**. This is a default Processing void that runs only once when script is called for the first time. In our if/else we must insert another one that will perform like this:
- if **to** has reached **toKeeper** value, resets it to 0;
- if instead is 0, **to** becomes **toKeeper**.



{{< highlight html>}}
public void update() {

  @P(1) @Type.Number @Transient @OnChange("toChanged")
  double to;
  @P(2) @Type.Number @OnChange("valueChanged")
  Property value;
  @P(3) @Type.Number(min = 0, max = 60, def = 0)
  double time;
  @P(4) boolean loop;
   double toKeeper; //useful to keep the initial to value with setup void, which will run only once at script call.

  @Out(1) Output out;
  boolean active;


  public void setup()
  {
      toKeeper = to;
  }
  @Override
  public void update() {

      if (value.isAnimating()) {
          out.send(d(value));//the value inside the parenthesis is called function argument.
          active = true;
      } else if (active) {

          if(loop)
          {
            if(to == toKeeper)
            {
                to = 0.0;
            }
            else if (to == 0.0)
            {
              to = toKeeper;
            }
            out.send(to);
            active = true;
            toChanged();
          }
          else
          {
          out.send(to);
          active = false;
          }
      }
  }

{{</highlight>}}

If you will hit play you shall see your result. Now, the very interesting part of today's episode: we're going to map our video in a 3D rotating cube. To do that, let's create a clean p3d node (p2d is for 2d sketches). (it's under video:gl). You will notice that it has only an out socket: this is because the node is essentially empty (there are just two voids: **setup** and **draw**. Draw is another main Processing function that keeps executing the written code at every frame, so it's quite similar to update to be simple). In order to receive the image, we must add an IN socket as we did before where our texture node will be plugged in. Write this after Class Declaration.
The part in which variables are stored before voids is called **script head**. Keep it in mind because I'll call it this way from now on.

{{< highlight html>}}
@In(0)
org.praxislive.video.pgl.code.userapi.PImage in;
{{</highlight>}}

"org.praxislive.video.pgl.code.userapi" it's a library specification: it's asked by Praxis itself because PImage belong to two different libraries, so in this way you specify exactly which one is. Under it, create a new double that will be used to rotate our cube.

{{< highlight html>}}
double r=0;
{{</highlight>}}

Let's move to **draw()** void. **draw()** works like this:
First must be written the instructions that the targeted shape has to follow (like **translate** or **rotate**), then the shape itself. Let's write them:

{{< highlight html>}}
public void draw() {
       translate(width/2,height/2);//this translates the Shape at the middle of the sketch.
       rotateY(r); //it rotates the Shape along Y axis at r
       r=r+0.02; //this adds a 0.02 to r every time draw is executed.
       beginShape(QUADS); //Shape is initialized
       endShape();//The Shape begun above is complete.
}
{{</highlight>}}

You can read what each line does as notes (//), which are not read by Praxis. Sometimes is useful also for debuging or testing. Now it comes a very tedious part, but once you'll got the hand of it, it will be just a boring procedure. Some of you that already knows a little Processing could say "Hey, why don't use just "box" instead of that long code". Well, it's because we won't then be able to apply the texture as we want. Infact **beginShape()** allows you to create any kind oh shape (cylinders, pyramids, octahedron, or even your own inventions) and works with vertices. To make a side, you'll have to create each vertex and establish where it is.
Two verteces following each other make a line, so be careful at the order. For example, this one is correct, because it follows a clockwise order:

{{<figure src="/imgs/2_LetsStartCoding/Shape_Vertex.png" alt="" >}}

Now look at what happens if we shift x3 and x4:
{{<figure src="/imgs/2_LetsStartCoding/Shape_Vertex_2.png" alt="" >}}

Vertex in our case has 5 arguments: x, y, z, u, v.

Since we're working on a 3D space, we must include z value. u and v instead are useful to map the texture to the cube faces (who's familiar with 3D modelling knows them as UV maps). So we must write four instructions per face (a cube has 6 faces), each separated by an empty line (or the script will think it's a whole face). Here's then the final code.
{{< highlight html>}}
public void draw() {
       translate(width/2,height/2);//this translates the Shape at the middle of the sketch.
       rotateY(r); //it rotates the Shape along Y axis at r
       r=r+0.02; //this adds a 0.02 to r every time draw is executed.
       beginShape(QUADS); //Shape is initialized
       texture(in); //it plugs in our texture;
      vertex(-100,  100,  100, 0, height);
      vertex( 100,  100,  100, height, height); //height is the sketches height; in this way our webcam output will cover the faces of the cube. Note: also UV must follow the same rule you used to create the face.
      vertex( 100, -100,  100, height, 0);
      vertex(-100, -100,  100, 0,0);


      vertex( 100,  100,  100, 0, height);
      vertex( 100,  100, -100, height, height);
      vertex( 100, -100, -100, height, 0);
      vertex( 100, -100,  100, 0,0);

      vertex( 100,  100, -100, 0, height);
      vertex(-100,  100, -100, height, height);
      vertex(-100, -100, -100, height, 0);
      vertex( 100, -100, -100, 0,0);

      vertex(-100,  100, -100, 0, height);
      vertex(-100,  100,  100, height, height);
      vertex(-100, -100,  100, height, 0);
      vertex(-100, -100, -100, 0,0);

      vertex(-100,  100, -100, 0, height);
      vertex( 100,  100, -100, height, height);
      vertex( 100,  100,  100, height, 0);
      vertex(-100,  100,  100, 0,0);

      vertex(-100, -100, -100, 0, height);
      vertex( 100, -100, -100, height, height);
      vertex( 100, -100,  100, height, 0);
      vertex(-100, -100,  100, 0,0);
       endShape();//The Shape begun above is complete.
}
{{</highlight>}}

Now our Sketch is done! Return to video and plug **p3d** to **screen** and **hsv**. You can join the results of this very long tutorial!

{{<figure src="/imgs/2_LetsStartCoding/2-Final.png" alt="" >}}

Thank you very much if you got until here, next tutorial will be about audio. We're going infact to:
- create a midi comp;
- plug it to video and create an interaction between them;

Hope that it wasn't too long and you learned something cool!
See you at next episode!
