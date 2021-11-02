<!-- [< Back](..) -->

Game-Loop Behaviour
===================

DOME handles the logistics of operating a game loop, so you only have to worry about what happens during your update loop.

The game loop is broken into three stages:
 * Input and Asynchronous Event Handling
 * Update
 * Draw

The default game-loop can be described as having a "fixed timestep with variable rendering".

This means that the the Update Phase runs with a fixed granularity of 16.6ms but the Input and Draw phases are processed at every opportunity. 
Therefore, you can rely on your game having deterministic behaviour as every update is the same time-slice, and DOME should gracefully handle a variety of machines of differing power. If the draw and input phases take too long, DOME may run your update code multiple times to "catch up".
In addition, DOME can ask your game to render at arbitrary times between ticks. It provides your `Game` with a `draw(alpha)` call, where `alpha`  is a value between 0.0 and 1.0, a percentage of how far between the last tick and the next you are executing. You can use this to extrapolate the game state from the previous tick to "now".

DOME also starts with VSync enabled by default, so it will wait under the VBlank before executing the next frame's game loop.

## Options

There is no way for DOME to detect if it is running on a system which supports VSync, so you can disable it programmatically using the `Window.vsync` setting in the [dome](/modules/dome) module.

It's possible that you spot a single-frame stutter while the game is running at a high framerate. This is due to slight errors in frame time counting, and so DOME catches up unnecessarily. You can disable the catchup behaviour using `Window.lockstep = true`.



