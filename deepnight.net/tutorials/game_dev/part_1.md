## Introduction

<style type="text/css">
div.f0{
    font-size: 0;
}
img {
    object-fit: cover;
}
</style>

<div class="f0">
    <img src="https://raw.githubusercontent.com/chunqian/chunqian.github.io/main/deepnight.net/assets/img/tutorials/MasterArt_1080.jpg" width="25%" height="200px" />
    <img src="https://raw.githubusercontent.com/chunqian/chunqian.github.io/main/deepnight.net/assets/img/tutorials/2982-shot1.jpg-eq-900-500.jpg" width="25%" height="200px" />
    <img src="https://raw.githubusercontent.com/chunqian/chunqian.github.io/main/deepnight.net/assets/img/tutorials/2982-shot1.png-eq-900-500.jpg.png" width="25%" height="200px" />
</div>

<img src="https://raw.githubusercontent.com/chunqian/chunqian.github.io/main/deepnight.net/assets/img/tutorials/2982-shot4.jpg-eq-900-500.jpg" width="75%" />

Writing a 2D platformer engine can be tricky if you don’t really know where you’re going. Using a clean and simple base is essential. You know the KiSS principle ? Keep It Short and Simple : that’s the way I do it.

Most of my games are based on a similar canvas, be it a 2D platformer or a top-down game. Even [Dead Cells](https://deepnight.net/games/motion-twin/dead-cells/) uses this exact base engine. By the way, it’s interesting to note that a platformer is **nothing more** than a top-down engine with gravity applied to the player on every frame.

In this article, I will use the [Haxe](http://haxe.org/) language: if you don’t know it yet, it’s an amazing language that can compile to many targets, including Flash, C, or iOS/Android. However, the principles here are very generic and simple, meaning that you can easily adapt to any other language.

I use a simple, lightweight, **Entity** class which does all the basics and I extend it. Pretty classic, but there are a few tricks.

Here is a simple version of this class:

```haxe
class Entity {
    // Graphical object
    public var sprite : YourSpriteClass;

    // Base coordinates
    public var cx : Int;
    public var cy : Int;
    public var xr : Float;
    public var yr : Float;

    // Resulting coordinates
    public var xx : Float;
    public var yy : Float;

    // Movements
    public var dx : Float;
    public var dy : Float;

    public function new() {
        //...
    }

    public function update() {
        //...
    }
}
```

## Coordinates system

First thing, I use a coordinate system focused on ease of use.

I usually have a grid based logic: the level, for example, is a grid of empty cells (where the player can walk) and wall cells.

Therefore, `cx,cy` are the grid coordinates. `xr,yr` are ratios (0 to 1.0) that represent the position inside a grid cell. Finally, `xx,yy` are resulting coordinates from `cx,cy + xr,yr`.

Thinking with this system makes lots of things much easier. For example, checking collisions on the right side of an entity is trivial: just read `cx+1,cy` coordinate. You can also use the xr value to check if the Entity is on the right side of its cell.

**We will consider from now on that with have a method `hasCollision(cx,cy)` in our class that returns `true` if their his a collision at a given coordinate, `false` otherwise**.

```haxe
if( hasCollision(cx+1,cy) && xr>=0.7 ) {    
  xr = 0.7; // cap xr
  // ...
}
```

The `xx,yy` coordinates are only updated **at the end of the update loop**.

**Note**: sometimes, updating sprite.x and sprite.y has a small cost: lots of things are updated internally when you change these values. That means each time you modify them, matrices are updated, objects are rendered..etc. So you probably don’t want to work on `sprite.x` directly, that’s the reason I always use an intermediary: `xx`.

It also makes cross platform dev easier as the Entity class is more about logic than graphics.

```haxe
// assuming the cell size of your grid system is 16px
xx = Std.int( (cx+xr) * 16 );
yy = Std.int( (cy+yr) * 16 );
sprite.x = xx;
sprite.y = yy;
```

Also, sometimes you will need to initialize `cx,cy` and `xr,yr` based on a `xx,yy` coordinate :

```haxe
public function setCoordinates(x,y) {   
  xx = x;   
  yy = y;   
  cx = Std.int(xx/16);  
  cy = Std.int(yy/16);  
  xr = (xx-cx*16) / 16; 
  yr = (yy-cy*16) / 16;
}
```

## X movements

On every frame, the value dx is added to `xr`.

If `xr` becomes greater than 1 or lower than 0 (ie. the Entity is beyond the bounds of its current cell), the `cx` coordinate is updated accordingly.

```haxe
while( xr>1 ) { xr --;  cx ++;}
while( xr<0 ) { xr ++;  cx --;}
```

You should always apply friction to `dx`, to smoothly cap its value (much better results than a simple `if`).

```haxe
dx *= 0.96;
```

In your main loop, when the appropriate event is fired (key press or anything), you can simply change `dx` to move your entity accordingly.

```haxe
// hero being an Entity
hero.dx = 0.1;
// or
hero.dx += 0.05;
```

## X collisions

Checking and managing collisions is pretty simple:

```haxe
if( hasCollision(cx+1,cy) && xr>=0.7 ) {
  xr = 0.7;
  dx = 0; // stop movement
}
if( hasCollision(cx-1,cy) && xr<=0.3 ) {
  xr = 0.3;
  dx = 0;
}
```

## X complete !

Here is the complete source code for X management. Couldn’t be simpler :)

```haxe
xr+=dx;
dx*=0.96;
if( hasCollision(cx+1,cy) && xr>=0.7 ) {
  xr = 0.7;
  dx = 0;
}
if( hasCollision(cx-1,cy) && xr<=0.3 ) {
  xr = 0.3;
  dx = 0;
}
```

## What about Y?

Mostly copy and paste. There could be a few differences though, depending on the kind of game you’re making. For example, in a platformer, you may want the `yr` value to cap at 0.5 instead of 0.7 when a collision is detected underneath Entity feet.

```haxe
yr+=dy;
dy+=0.05;
dy*=0.96;

if( hasCollision(cx,cy-1) && yr<=0.3 ) {
  dy = 0;
  yr = 0.3;
}
if( hasCollision(cx,cy+1) && yr>=0.5 ) {
  dy = 0;
  yr = 0.5;
}

while( yr>1 ) { cy++; yr--;}
while( yr<0 ) { cy--; yr++;}
```

Don’t hesitate to leave a comment if you have any question :)

Read the [second part of this article](https://deepnight.net/tutorials/a-simple-platformer-engine-part-2-collisions/).
