---
layout: post
title:  "Note: Using Graphics.ConvertTexture"
tags: [Unity, Note]
---

A note on using Unity's Graphics.ConvertTexture.
<!--more-->

---

## Graphics.ConvertTexture()

`Graphics.ConvertTexture` in Unity can be used to change the format of/resizing `Texture2D`s. But there's one tiny problem.

### The issue

The converted texture is stored in the GPU. If you try to use `GetPixels` or `GetPixels32` on the texture in your script, it will return a completely grey texture. You have to copy the texture into the CPU. And, unlike what the [***Unity documentation says***](https://docs.unity3d.com/ScriptReference/Graphics.ConvertTexture.html), there is no function called `Texture2D.RequestIntoNativeArray`.

### The fix

Use `AsyncGPUReadback.RequestIntoNativeArray`. This is what I'm using to resize a texture:
```csharp
// Don't forget to define texture, newWidth and newHeight.

Texture2D scaled = new(newWidth, newHeight);

// Convert the texture.
Graphics.ConvertTexture(texture, scaled);

// Create an array to store the texture.
NativeArray<float> nativeArray = new(newWidth * newHeight * sizeof(float), Allocator.Persistent);

// Start the operation to read the texture from the GPU.
AsyncGPUReadbackRequest request = AsyncGPUReadback.RequestIntoNativeArray(ref nativeArray, scaled);

// Wait till it's done.
while (!request.done)
    await Task.Yield();

// Check for errors.
if (request.hasError)
{
    // Dispose the array.
    nativeArray.Dispose();
    return null;
}

// Load the texture.
scaled.LoadRawTextureData(nativeArray);

// Dispose the array.
nativeArray.Dispose();

return scaled;
```

I have no idea if what I'm doing here is performant, but it works.