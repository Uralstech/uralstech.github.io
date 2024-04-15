---
title:  "Note: Using Graphics.ConvertTexture"
tags: [Unity, Note]
---

A note on using Unity's Graphics.ConvertTexture.

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
Graphics.ConvertTexture(texture, scaled);

NativeArray<float> nativeArray = new(newWidth * newHeight * sizeof(float), Allocator.Persistent);
AsyncGPUReadbackRequest request = AsyncGPUReadback.RequestIntoNativeArray(ref nativeArray, scaled);
while (!request.done)
    await Task.Yield();

if (request.hasError)
{
    nativeArray.Dispose();
    return null;
}

scaled.LoadRawTextureData(nativeArray);
nativeArray.Dispose();
return scaled;
```

I have no idea if what I'm doing here is performant, but it works.