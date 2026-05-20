# TouchDesigner GLSL Vertex Displacement

A TouchDesigner project using a GLSL MAT to displace 3D sphere geometry in real-time from a single input texture.

![TouchDesigner Network](touchdesigner-network.png)

## How It Works

A single GLSL MAT controls both the shape of the sphere and its surface lighting. One texture drives both effects through its separate channels.

The vertex shader reads the alpha channel of the input texture and pushes each vertex outward along its normal. Brighter alpha values produce more displacement. The fragment shader reads the RGB channels of the same texture and treats them as a normal map, adding surface lighting detail that follows the displaced geometry.

Swapping the input texture changes both the geometry and the lighting response in real-time.

## Usage

Open the `.toe` file in TouchDesigner and connect any image to the `GLSL MAT` input. The alpha channel drives vertex displacement and the RGB channels drive the normal map.

---

## GLSL Reference Notes

Notes compiled while building this project.

### Graphics Pipeline

The two programmable stages in the graphics pipeline are the vertex shader and the fragment shader.

| Shader Type | File Extension | Purpose |
| --- | --- | --- |
| **Vertex Shader** | `.vert` | Processes each vertex (position, normals, UVs). |
| **Fragment Shader** | `.frag` | Processes each pixel (color, lighting). |

In TouchDesigner, the **GLSL TOP** runs only a fragment shader. The **GLSL MAT** runs both a vertex shader and a fragment shader, which is why it can modify geometry.

### Vertex Shader Attributes

The vertex shader receives these built-in attributes from TouchDesigner geometry:

| Name | Type | Size | Description |
| --- | --- | --- | --- |
| `P` | `vec3` | 3 | Incoming vertex position. |
| `N` | `vec3` | 3 | Surface normal vector for the vertex. |
| `uv` | `vec3` | 3 | Texture coordinates (UVs). |
| `Cd` | `vec4` | 4 | Vertex color (RGBA). |

Vertex shaders work in Normalized Device Coordinates (NDC). The visible space runs from -1.0 to 1.0 on each axis, with (0,0) at the center.

```
Vertex Shader Coordinates	

(-1.0,1.0)  ┏╍╍╍╍╍╍╍┳╍╍╍╍╍╍╍┓ (1.0,1.0)
		    ╏               ╏  
		    ╏               ╏
		    ╏	   x,y	    ╏
		  x ┣   (0.0,0.0)   ┫
		    ╏               ╏
		    ╏               ╏ 
		    ╏               ╏
(-1.0,-1.0) ┗╍╍╍╍╍╍╍┻╍╍╍╍╍╍╍┛ (1.0,-1.0)	
 			        y
```

### Fragment Shader Coordinates

The fragment shader works in UV coordinates, normalized from 0.0 to 1.0, with (0,0) at the bottom-left corner.

```
Fragment (Pixel) Shader Coordinates	

(0.0,1.0) ┏╍╍╍╍╍╍╍┳╍╍╍╍╍╍╍┓ (1.0,1.0)
		  ╏               ╏  
		  ╏               ╏
		  ╏	   x,y	  ╏
		x ┣   (0.5,0.5)   ┫
		  ╏               ╏
		  ╏               ╏ 
		  ╏               ╏
(0.0,0.0) ┗╍╍╍╍╍╍╍┻╍╍╍╍╍╍╍┛ (1.0,0.0)	
 			      y
```

### GLSL in TouchDesigner

A few common uses:

- Generating 2D and 3D generative visuals
- Modifying geometry directly on the GPU
- Building compositing and effects pipelines
- Generating dynamic textures for interactive work

## Links

- [Project Write-up](https://stevenmbenton.com/glsl-vertex-displacement/)
