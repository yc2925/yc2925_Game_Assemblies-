# GenPT-Live in TouchDesigner — Monday Documentation Log

**Date:** Monday, September 21, 2026  
**Project:** `GenPT_Native_Feedback.1.toe`  
**Platform:** macOS, TouchDesigner 2025.32280

## 1. TouchDesigner MCP setup

We first checked the TouchDesigner connection through the MCP bridge. The connection was live and reported TouchDesigner running on macOS. We then created a basic test network to confirm that operators could be created, connected, inspected, and previewed through MCP.

The basic test used a generated TOP connected to a Null TOP. This verified the complete workflow:

1. Connect to TouchDesigner.
2. Inspect the operator network.
3. Create and configure operators.
4. Connect TOPs and CHOPs with TouchDesigner Python.
5. Inspect the rendered output and node errors.

## 2. Image and microphone setup

Six reference images were loaded into TouchDesigner with Movie File In TOPs. Each image was normalized to the same output canvas so blends would not change the final aspect ratio.

The laptop microphone was connected through an Audio Device In CHOP. Its signal was analyzed, smoothed, and scaled into a usable level. That level controlled image blending, motion, feedback strength, and other visual parameters.

![Initial audio-reactive image output](/Users/retochen/Documents/GitHub/yc2925_Game_Assemblies-/Images/0921-1.png)

## 3. Translating GenPT-Live ideas without Stable Diffusion

The original GenPT-Live project was used as a conceptual reference. Its useful ideas were adapted into native TouchDesigner operations so the system could run on the Mac without Stream Diffusion or Stable Diffusion.

The main feedback idea was:

```text
current image + previous frame + transformed pixel memory = next frame
```

The TouchDesigner version used these stages:

- `seed` selected the current audio-reactive image.
- `history` held the previous frame.
- `memory_transform` moved the previous frame slightly.
- `memory_decay` softened the stored image.
- `palette_reduce` compressed the image into a thin strip.
- `palette_expand` stretched that strip back across the canvas.
- `merge_memory` and `merge_strips` combined the new image with the stored material.
- `loop_frame` sent the result back into the feedback loop.

![Feedback network showing history, memory, strips, and loop output](/Users/retochen/Desktop/Screenshot%202026-09-21%20at%2010.22.15%E2%80%AFPM.png)

This reproduced the visual logic of GenPT-Live—image memory, transformed fragments, and iterative feedback—using TOPs instead of an AI diffusion model.

## 4. Aspect-ratio and rotation corrections

The source images had different dimensions. This initially caused the output ratio to change as the blends switched. Every fit, blend, feedback, and output stage was then normalized to a fixed landscape canvas.

The working output was set to **1280×720**, later raised to **1920×1080**. The rotation stage was corrected so the image content itself filled the landscape canvas rather than remaining as a narrow vertical column.

The output was checked for full opacity so feedback transforms could not reveal empty transparent pixels.

## 5. Audio-reactive image behavior

The first approach used a timed image cycle, which felt like a slideshow. That approach was removed. The image blend was then driven continuously by the microphone instead of changing through hard image swaps.

The goal became a continuous field of six images: sound changes the balance and movement of the images while all six remain part of the composition.

![Audio-reactive TouchDesigner output with the image and pixel effects visible](/Users/retochen/Desktop/Screenshot%202026-09-21%20at%2010.29.38%E2%80%AFPM.png)

## 6. Stretched-pixel treatment

The output was extended with pixels sampled from the current image itself. The stretched material was used first as edge bars, then as woven rectangular patches inside the image.

The final treatment used a GLSL TOP to:

- sample the current image;
- choose several moving rectangular regions;
- stretch pixels from the same source image into those regions;
- keep each rectangle in a consistent horizontal **5:3 width-to-height ratio**;
- change placement, size, and strength in response to microphone level;
- preserve an opaque alpha channel.

The result is a native TouchDesigner version of GenPT-Live’s pixel-fragment feedback language, with the fragments coming directly from the live image rather than from a diffusion model.

## 7. Final working structure

```text
six image sources
        ↓
audio-reactive blend network
        ↓
warp and native feedback loop
        ↓
source-derived stretched pixel rectangles
        ↓
1920×1080 output
```

The project was inspected repeatedly during the build. The final output was checked for its resolution, opacity, and TouchDesigner node errors. Changes made during this session were kept in the open project unless explicitly saved by the user.

