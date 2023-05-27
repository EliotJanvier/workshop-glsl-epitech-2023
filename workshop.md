# Introduction to GLSL

What is GLSL? Its a programming language made to write shaders. But what are shaders? Glad you asked.

## Shaders: what, why?

When running a graphical application, there is a need to render a number of objects on screen. Given the great number of pixels on modern screens, the traditionnal way of programming quickly meets an obstacle: speed. Indeed, a simple loop, of even threads often struggle do make it quick enough to compute everything to render 3D graphics at a high enough framerate. Although that might be fine for some basic applications such as text editors, this is definitively out of question for games (and this is just an exemple).

Modern machines offer an alternative: instead of coding a loop that will go over each pixel on the screen, one can choose to parallelize the work by using the GPU.
The CPU has a reduced number of cores, but each of those cores has a great computing power. The GPU has it reversed: it has a very high number of very small cores, making it adapted for performing the same small calculation a great number of times.

A Shader is simply an small program that will run in a parallel manner on the GPU.
This program could do anything, not just graphics, but this is what we are going to do today.



## A little bit of vocabulary

Shaders are organized in a rendering pipeline: they are connected one after the other, with the outputs of the previous linked to the input of the next.
There exists two kind of shaders:

- Vertex Shaders: these shaders take coordinates as input. For exemple, it could be the coordinates of the vertices in the geometry of 3D objects. They output transformed coordinates that will be used by the subsequent shaders. For exemple, one application for this could be to transform the coordinates of all vertices in your 3D word into screen coordinates. Each point fed to a vertex shader will spawn a parallel process associated to that shader.
- Fragment shaders: these shaders are responsible for computing and changing the color of the pixels on the screen. They are often chained together to produce complexe effects.

## Basic syntax:

The glsl language is very close to the C language in terms of syntax, but is has some improved handling for vectors and numbers.
For exemple, you can do the following:

```C
vec3 u = vec3(1, 2, 3); // creates a vector with three components
vec2 v = u.xy; // creates a vector with 2 components (1, 2)
```

For more informations, you can go to this link: https://en.wikibooks.org/wiki/GLSL_Programming/Vector_and_Matrix_Operations

You can declare a structure just like in C:

```C
struct Sphere {
    vec3 center;
    float radius;
};
```

Same with for loops, except you can get the length of an array like so:

```C
for (int i = 0; i < 3; ++i) {
    // do smth
}
```
To declare a variable that is the same for all shaders, use the following syntax:

```glsl
uniform float variableName = 3.0f;
```
To declare a variable that will be piped to and from another shader, use the following syntax (respectively):

```glsl
out float result;
in float input;
```
(note that you won't use this today though)

## Lets get our hands dirty!

Since shaders use the GPU, they are quite bothersome to setup. They require to allocate memory on the GPU, copy the data where glsl can find it, compile the shaders before executing them, linking the shaders to the memory you assigned to them... It is a lot of troubles. For those interested, check out this website:
https://learnopengl.com/Getting-started/Shaders
It has everything you need and more.

However today, we will keep it simple. Go to the following website:
https://thebookofshaders.com/edit.php
There, you will find a web editor with some preset variables.

```glsl
// Author:
// Title:

#ifdef GL_ES
precision mediump float; // don't bother, it just sets the precision for the floats
#endif

uniform vec2 u_resolution; // a constant set from outside your code, with the resolution of the screen
uniform vec2 u_mouse; // a constant set with the coordinates of the mouse.
uniform float u_time; // a constant set with the time.

void main() {
    vec2 st = gl_FragCoord.xy/u_resolution.xy;
    st.x *= u_resolution.x/u_resolution.y;

    vec3 color = vec3(0.);
    color = vec3(st.x,st.y,abs(sin(u_time)));

    gl_FragColor = vec4(color,1.0);
}
```

Note that you also have a predefined variable: gl_FragColor, which represents the color of the fragment shader. Can you find another present variable? What does it represent?
Play with it! Can you make a small black circle appear around the mouse?

## Now for the real challenge

Lets try some 3D. What you are going to do now is called raymarching. It is similar to raytracing but the equations are much simpler!
We will try to render a simple sphere.

### The raymarching algorithm

For each pixel on the screen, we cast a ray that goes through it. This is why glsl is so adapted to this algorithm, because we can run the computation for each pixel in a separate process, thus making it very, very fast.
The Raymarching algorithm is quite simple:
For each ray, we find the distance to the closest object in the scene. Then, we advance along the ray by that distance, and repeat until the closest object is close enough. The distance to objects is given by a distance function.

Here are the steps you can use (feel free to do it another way!):
- Set up the scene. Define one or more spheres using an appropriate structure.
- Write the distance function for the sphere. It should return the distance to the surface of the sphere for any point in space.
- Set up your rays. Use the on screen coordinates to compute the direction of the ray. Be careful to the origin of the coordinates! It is not the center of the screen...
- Write the loop to 'march' along a ray. Once you are close enough to the sphere, you can set the color of your pixel!
- If you got there, why not trying to add a light and some shadows?


## To the infinite and beyond!
Now that you tackled this challenge, you have a good grasp of what a shader is. You could try to see how they are used in other software, like Unity for exemple!



