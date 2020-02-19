# Introduction to Tessellation
> An introduction to tessellation. Python code is provided to run the visualisation.

1. TOC
{:toc}

## What is Tessellation

Tessellation is a feature that converts a low-detailed surface patch to a higher detailed surface patch dynamically on the Graphics Processing Unit (GPU). Using a low resolution model with a few polygons, tessellation makes rendering high levels of detail possible by subdividing each patch into smaller primitives. 

// Add stuff about what is coming in this page


## Benefits

According to [DX11 Tess Docs](https://docs.microsoft.com/en-us/windows/win32/direct3d11/direct3d-11-advanced-stages-tessellation), the benefits are:

1. Lower memory and bandwidth requirements. 
2. Allows continuous or view dependent details to be calculated on the fly.
3. Improves performance by performing expensive computations at lower frequency (doing calculations on a lower-detail model). For instance, calculations for collision detection or soft body dynamics.

A graphics pipeline is a series of functions that transforms inputs (primitive data such as points, lines or triangles) into outputs for rendering. Primitives refer to the atomic or irreducible objects the system can handle. 
To add tessellation, the graphics pipeline requires three new stages. 


## Pipeline Stages

The [DX11 graphics pipeline](https://docs.microsoft.com/en-us/windows/win32/direct3d11/overviews-direct3d-11-graphics-pipeline) consists of a series of stages shown in Figure 1. 

![](../Downloads/d3d11-pipeline-stages.jpg "Figure 1: DX11 graphics pipeline"){:width="90%"}

A description of each stage is summarised in this itemised list:
1. Input-Assembler Stage
> Read primitive data and assemble them into primitives for other stages (e.g. line lists, and triangle strips)
2. Vertex Shader Stage
> Processes the assembled vertices and applies operations such as transformations, skinning, morphing, and per-vertex lighting. > Single input vertex and single output vertex. 
3. Tessellation Stages
> Breaks up a patch of control points into smaller primitives and thus create higher detailed features.
4. Geometry Shader Stage
> Operates on vertices and can generate output vertices
5. Stream-Output Stage
> Continuously output vertex data from geometry shader to buffers
6. Rasterizer Stage
> Converts primitives into a raster image for displaying.
7. Pixel Shader Stage
> Operate on a per-pixel level and can change lighting, etc using the available constant variables, texture data, and others.
8. Output-Merger Stage
> Generates the final rendered pixel colour, determines which pixels are visible and blending pixel colours.

The tessellation stages consistes of three new stages which are:

* Hull-Shader (HS) Stage
> Computes patch constants (such as tessellation factors) and other parameters for the tessellation stage.
> Performs any special transformations on the input patch data.
* Tessellator (Tess) Stage
> A fixed function that partitions a geometry into smaller primitives and outputs u,v coordinates of the vertices and assembly order.
* Domain-Shader (DS) Stage
> Calculates vertex position that corresponds to the each u,v coordinate.

{% include info.html text="[OpenGL](https://www.khronos.org/opengl/wiki/Tessellation) uses the names Tessellation Control Shader, Primitive Generator, and Tessellation Evaulation Shader to refer to the HS, Tess, and DS respectively." %}

{% include info.html text="As an aside, the word "shader" refers to an operation that transforms four input numbers into four output numbers. Historically,  a shader was used to change the brightness of pixels (RGBA values) but now encompasses more general operations and the name has stuck." %}



## Domain
uv coordinates

For a triangle, barycentric coordinates are used to represent a 2D position using three numbers ($\alpha, \beta, \gamma$) with the condition that $\alpha+\beta+\gamma = 1$.




## Tessellation Factors

### Inner Factors
This specifies how the interior is partitioned. When this factor is 


### Outer Factors
This specifies how the outer edges of the output domain are partitioned.


## Partition Type

### Integer

#### Power of 2
Same as integer but the valid tessellation factors are powers of two. That is $2^0,2^1,...,2^5,2^6$ with the maximum tessellation factor of 64.

### Fractional
#### Parity
Where each new vertex appears when the tessellation factor increases is determed by the parity - odd or even. 

#### Odd
New vertices are generated from the corners.

The number of segments on an edge is odd so a tessellation factor of 5 would give five equally spaced segments. If the tessellation factor was 5.1, there would be 


#### Even
New vertices are generated from the midpoint


## Output Domains


### Isoline


### Triangle


### Quadrilateral


![]()


## Visualisation

I have taken the DX11 Spec for tessellation[^1], compiled it and made a python script[^2] to run the C++ code with customisable parameters. 
You can play around with the tessellation factors and output domain type and get a feel for how it all works.
The steps are:
```bash
git clone https://github.com/YiweiMao/tessDX
cd tessDX/
make
```
Then in a python shell, execute the following:
```python
from pytess import *

# for one instance of tessellation
# if len(tfs)==2, isoline. elif len(tfs)==4, tri. elif len(tfs)==6, quad.
Tessellator(partition=PART_INT,outputPrim=OUTPUT_TRIANGLE_CW,tfs=[1,2,3,4]).doTess()

# for interactivity. Change the sliders to see the updated tessellation
interact(showTess,partition=(0,3,1),outputPrim=(0,3,1),outTF0=(1,64,0.1),outTF1=(-1,64,0.1),
                 outTF2=(-1,64,0.01),outTF3=(-1,64,0.1),inTF0=(1,64,0.1),inTF1=(-1,64,0.1))
```
![](../Downloads/tess_interact_eg.png "Tessellation Widget")


## Conclusion



# References

[^1]: https://github.com/microsoft/DirectX-Specs/tree/master/d3d/archive/images/d3d11
[^2]: https://github.com/YiweiMao/tessDX

