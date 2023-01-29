---
layout: post
title: A Little Dive Into Webgpu
categories: [webgpu, webgaming]
description: to get a better rendering performance for webgaming
keywords: webgpu, webgaming, rendering
---

## Introduction

webgpu is a new api for webgaming, it is still in development, but it is already available in chrome canary. It is a low level api, so it is not easy to use, but it is very powerful. It is a good choice for people who want to get a better rendering performance for webgaming.

## first line of code

We can use webgpu in a canvas element, just like this:
```js
const canvas = document.querySelector('canvas');
const context = canvas.getContext('webgpu');
```

## 3D rendering pipeline

Rendering pipeline is a series of steps to render a 3D scene.
The steps in pipeline are:
1. vertex shader
2. primitive assembly
3. rasterization
4. fragment shader
5. Color blending

## what is shader

Shader is a program that runs on GPU, it is written in GLSL, which is a subset of C language. It is used to process data in parallel.

## Texture on 3D rendering

Texture is a 2D image, it can be used in 3D rendering pipeline.
Texture can be a picture or a video.

## Light on 3D rendering

Light is a source of light, it can be used in 3D rendering pipeline
There are 3 types of light:
1. directional light
2. point light
3. spot light





