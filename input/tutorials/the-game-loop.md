Description: The Game Loop
Published: 2018/12/18
Category: Additional Reading
Order: 2
Author: Steven Blom

---

The Game Loop
=
Now that we know what the client and server are, we can discuss another important concept in Minecraft and many other games, which is the game loop.

In order for a game to not just be a still image, we need to continually draw the world to the screen, and for a game to be interactive, we need to interact with the world based on input.

![A Game Loop](/assets/Tutorials/Events_GameLoop.svg)

When you have a server in the mix, things get a little bit more complicated (This is a vast oversimplification).

![A Complex Game Loop](/assets/Tutorials/Events_ComplexGameLoop.svg)

Now we have two game loops, doing different things, at potentially different speeds. In Minecraft, both the client and the server aim to have a complete loop done in under 50 milliseconds (1/20th of a second).

If the client cannot perform it's actions in under 50 milliseconds, then user will typically see the game stuttering and the time between each frame of animation will become noticable.

If the server cannot perform it's actions in under 50 milliseconds, then the user will see delays in the world reacting to their actions. This effect can also be observed when a user's connection to the server is too slow, or the latency is too high.