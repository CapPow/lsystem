
# Installation #

Copy lsystem directory over to
  _F:\SteamLibrary\steamapps\common\Blender\2.79\scripts\addons_
or wherever your blender installation is.

In blender go to File->User Preferences->Add-ons and enable
 "Add Mesh: LSystem" then press the "Save User Settings" button.

# Usage #

 Add a mesh via Add->Mesh->LSystem. Change the settings in the LSystem panel to get something halway decent.

## Turtle interpretation of symbols ##

| Symbol | Interpretation                           | Example |
|--------|------------------------------------------|---------|
| F      | Move forward and produce an edge ( a branch segment ) |
| f      | Move forward without producing an edge   |
| +      | Turn left                                |
| -      | Turn right                               |
| /      | Pitch up                                 |
| \      | Pitch down                               |
| <      | Roll left                                |
| >      | Roll right                               |
| $      | Rotate upright                           |
| [      | Start a branch (push state)              |
| ]      | Complete a branch (pop state)            |
| #,%    | Fatten or slink the radius of the branch |
| ¤      | Set radius                               |
| {      | Start a new blender object               |
| }      | End current blender object               |
| ~      | Copy an existing blender object (requires the name of the object to be copied as a parameter) |
| p      | Change pens, requires a value (see table below). | p(subsurf) |
| m      | Set material, requires the name of the material. Note that the material applies to an entire blender object. If you set the material multiple times for the same object the material value will simply be overwritten. | m(Green)
| s      | scale                                    |

F,+,-,/,\,<,>,!,@,#,% use the configured default values in settings panel but this
can also be specified directly in the axiom and the production rules. For example
+(90) would indicate a 90 degree turn to the left.

### Pens ###

| Name   |             |
|--------|-------------|
| pol    | Produces a single polygon |
| edge   | Produces a single edge between two vertices | 
| skin   | Same as edge but applies a skin modifier automatically. This allows the l-system to set the skin radius. |
| subsurf| Same as skin but with a surface subdivision modifier also. |
| curve  | A bezier curve. |
| line   | Produces a quad |
| cylXXX | Produces a cylinder with XXX number of vertices (must be 3 or higher). For example _cyl4_ produces a cylinder with 4 vertices |
 
## Production Rules ##

Production rules consist of three fields: 

  1. A module either a single character 
     module or a single character followed by parenthesis and parameters names (ie A(x,y)).
     The second field is the condition that has to be true if rule is applicable.
  2. This field is only relevant if the module has parameters. Conditions are 
     expressed as boolean expressions (ie eq(x,0) which would mean the rule applies when x is 0)
  3. The third field is the result. This what the module in the first field will 
     be replaced with if the condition is true. Mathematical expressions can be used
     here with parameters if they occur in the module field (see below for supported functions). 

Example:
```
  A(x,y): gt(x,1) -> A(mul(x,y),div(x,y))F(x)+(rand(0,y))
```

### Mathematical functions ###
| Name      |      | Example |
|-----------|------|---------|
| rand(x,y) | random number between x,y | rand(0,90) |
| add(x,y)  | addition | add(1,4) = 1+4 |
| sub(x,y)  | substraction | sub(2,3) = 2-3 |
| mul(x,y)  | multiply | mul(2,3) = 2*3 |
| div(x,y)  | divide   | div(4,6) = 4/6 |
| pow(x,y)  | x to the power of y | pow(2,3) = 2^3 |
| log(x)    | natural logarithm of x | log(12) |
| log(x,y)  | logarithm of x to the base of y | log(4,2) |
| sqrt(x)   | square root of x | |
| sin(x)    | sine of x | |
| cos(x)    | cosine of x | |
| tan(x)    | tangent of x | |
| eq(x,y)   | is equal | eq(1,0) (false) |
| gt(x,y)   | is x greater than y | gt(1,2) (false) |
| lt(x,y)   | is x less than y | lt(1,2) (true) |
| get(x)    | get property x, currently only the current number of iterations is available represented by _i_ | get(i)

### Stochastic rules ###

If there are several rules that match the same input one of the matching rules will be
selected at random.

### Running from a Script ###

```
import lsystem.lsystem
import lsystem.exec
axiom = lsystem.lsystem.ProductionRule("", "X")
rule = lsystem.lsystem.ProductionRule("X", "F[+X][-X]")
objects = lsystem.exec.execute(bpy.context, axiom, [rule])
```

# Examples #

## Sierpinski Gasket ##

See figure 1.10 b in [Algorithmic Beauty of Plants](http://algorithmicbotany.org/papers/abop/abop.pdf) on page 11.

![screenshot](https://github.com/krljg/lsystem/blob/master/examples/sierpinski_gasket.png)

## Fractal Plant ##

See figure 1.24 f in [Algorithmic Beauty of Plants](http://algorithmicbotany.org/papers/abop/abop.pdf) on page 25.

![screenshot](https://github.com/krljg/lsystem/blob/master/examples/fractal_plant.png)

## Monopodial Tree-like Structure ##

See figure 2.6 a in [Algorithmic Beauty of Plants](http://algorithmicbotany.org/papers/abop/abop.pdf) on page 56.

As the rules aren't visible in the picture, here they are:

```
A(l,w) -> ¤(w)F(l)[\(45)B(mul(l,0.6),mul(w,0.707))]>(137.5)A(mul(l,0.9),mul(w,0.707))
B(l,w) -> ¤(w)F(l)[-(45)C(mul(l,0.6),mul(w,0.707))]C(mul(l,0.9),mul(w,0.707))
C(l,w) -> ¤(w)F(l)[+(45)B(mul(l,0.6),mul(w,0.707))]B(mul(l,0.9),mul(w,0.707))
```

![screenshot](https://github.com/krljg/lsystem/blob/master/examples/monopodial_treelike_structure.png)

# See Also #

https://en.wikipedia.org/wiki/L-system

https://www.reddit.com/r/proceduralgeneration/comments/5771ea/making_fractal_trees_in_blender/

https://16bpp.net/blog/post/making-fractal-trees-in-blender

https://github.com/ento/blender-lsystem-addon

http://michelanders.blogspot.se/p/creating-blender-26-python-add-on.html

http://algorithmicbotany.org/papers/#abop

http://algorithmicbotany.org/papers/abop/abop.pdf

http://archive.org/stream/BrainfillingCurves-AFractalBestiary/BrainFilling#page/n0/mode/2up