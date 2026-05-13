---
layout: post
title:  "it's been HOW long?!"
tags: [Open source]
readtime: true
---

Has it really been... EIGHT MONTHS????
<!--more-->

yeah... so like i wanted to keep the blog updated like every month but, welll, here we are.

most of my posts have been about 'something', like a project, or a topic, or an update about a project, but since this isn't really about any specific topic, im not sure what i should write here.

i guess i'll do a review of what's been going on since the last post.

## [UXR.QuestCamera](https://github.com/Uralstech/UXR.QuestCamera)

it's gotten 2 rewrites since november, with version 4.0.0 just released hours ago.
imho it's actually been one of the most interesting projects i've worked on to date.

like how ezrSquared really made me think about how programming languages work and helped me learn more about C#, this helped me learn more about kotlin, the broader android 'ecosystem', compute shaders, C++, OpenGL and the JNI.

it's really been quite a mixed bag and i think i love it for that.

## [UXR.QuestMeshing](https://github.com/Uralstech/UXR.QuestMeshing)

this is another project i like a lot. similar to UXR.QuestCamera, this project rose from the need of a new feature in hAI! MR. we wanted an AR environment, which wouldn't require going through Meta's Space Setup, while still providing interactivity with the avatars. they should be able to sit down, lie down, follow you, all without needing a scan from Meta.

the solution was to create a scan of the environment in real-time! the project is heavily inspired by [Julian Triveri's Lasertag](https://anaglyph.dev/), which is completely [open-source](https://github.com/anaglyphs/lasertag)!

the project doesn't actually contain many public apis. it is mainly two scripts (and a compute shader), one which preprocesses frames from the Depth API, and acts as a replacement for Meta's EnvironmentDepthManager (as it doesn't expose information needed for the next script), and a script which populates a TSDF volume with the depth data and generates a mesh out of it. it can also create and bake a meshcollider from said mesh, and also update the scene's navmesh at runtime.

i decided to use surface nets to generate the mesh as it created simpler meshes by default when compared to an algorithm like marching cubes. the implementation is inspired by [this repo by Quoc-Minh Ton-That](https://github.com/Q-Minh/naive-surface-nets).

## [UAI.LiteRTLM](https://github.com/Uralstech/UAI.LiteRTLM)

[LiteRT-LM](https://github.com/google-ai-edge/LiteRT-LM) is a framework for running LLMs packaged in the LiteRT format (previously known as TFLite), made by Google. it's quite good. actually, it's really good, especially as of recently.
i think it may eventually lead to local LLMs being used by android apps, unlike whatever google is/was doing with [gemini nano](https://developers.google.com/ml-kit/genai#prompt-device).

i'm even able to run gemma 4 on my quest 3 ***while also running hAI! in VR!*** isn't that crazy? on top of that, i'm recording the entire thing. it still manages to run quite fast.

<blockquote class="twitter-tweet" data-media-max-width="560" data-lang="en" data-dnt="true"><p lang="en" dir="ltr">HUGE KUDOS TO THE LITERT-LM TEAM! v0.11.0, released 4 hours ago, with Multi-token Prediction support for Gemma 4 allows it to run fast on a Quest on the GPU with almost no framerate reduction, even when running alongside full VR apps!<a href="https://t.co/nLVbEjOPiw">https://t.co/nLVbEjOPiw</a> <a href="https://t.co/ylrI1dX9Gd">pic.twitter.com/ylrI1dX9Gd</a></p>&mdash; Uday (@uralstechCTO) <a href="https://twitter.com/uralstechCTO/status/2051774996939501932?ref_src=twsrc%5Etfw">May 5, 2026</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

now, i have to add that this is still really early stuff. my own experience with converting a model to LiteRT, then packaging it for LiteRT-LM, didn't [yield the best results](https://uralstech.github.io/Qwen-KCC-On-Device-Pipeline/site/conversion.html#inference-observations-in-litert-lm), in fact, it was barely coherent. of course, it could have been something i messed up, but i only followed the steps shown in [their repo](https://github.com/google-ai-edge/litert-torch/).
another example is how they seemingly still [haven't open sourced](https://github.com/google-ai-edge/litert-torch/issues/753) the conversion pipeline for gemma 3n.

anyways, i think their foundation is really strong, and they're doing great work. so obviously, i had to make it work in unity :3

the project is still stuck on LiteRT-LM v0.8.0, so this video is really outdated in terms of performance, but still:

<blockquote class="twitter-tweet" data-media-max-width="720" data-lang="en" data-dnt="true"><p lang="en" dir="ltr">I&#39;ve just released UAI.LiteRTLM! It wraps Google&#39;s LiteRT-LM Kotlin Android library for use in Unity. With LiteRT-LM, Gemma 3n E2B can run on my Quest 3&#39;s GPU with just ~3 seconds to first generated token! (1/4) <a href="https://twitter.com/hashtag/XR?src=hash&amp;ref_src=twsrc%5Etfw">#XR</a> <a href="https://twitter.com/hashtag/AI?src=hash&amp;ref_src=twsrc%5Etfw">#AI</a> <a href="https://twitter.com/hashtag/Unity?src=hash&amp;ref_src=twsrc%5Etfw">#Unity</a> <a href="https://twitter.com/hashtag/Unity3D?src=hash&amp;ref_src=twsrc%5Etfw">#Unity3D</a> <a href="https://twitter.com/hashtag/MetaQuest?src=hash&amp;ref_src=twsrc%5Etfw">#MetaQuest</a> <a href="https://twitter.com/hashtag/XRDev?src=hash&amp;ref_src=twsrc%5Etfw">#XRDev</a><a href="https://t.co/Z8lz8CK0Ml">https://t.co/Z8lz8CK0Ml</a> <a href="https://t.co/GlmsP8PPFx">pic.twitter.com/GlmsP8PPFx</a></p>&mdash; Uday (@uralstechCTO) <a href="https://twitter.com/uralstechCTO/status/2002186209993658491?ref_src=twsrc%5Etfw">December 20, 2025</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

i have to admit though, the current implementation of the package is quite lazy. litert-lm provides a multiplatform C++ api, but i chose to wrap the kotlin wrapper of the C++ api since it was easier. i "plan" to actually work on it... uh... later.

## [The hAI! MR Model Sideloader](https://github.com/Uralstech/hAI-Friend-MR-Model-Sideloader)

there's not much to say about this, essentially, readyplayerme shut down -> we needed to replace it in hAI! -> why don't we allow users to add whatever avatar they want? -> you can now sideload VRM avatars into hAI! using a PC app which transfers the avatar using wifi to your headset. i consider it a neat project but it's really rushed since i had like 1 month to remove rpm from hAI!.

## [AvLoader](https://github.com/Uralstech/AvLoader)

this is actually related to the Model Sideloader, at least a little bit. before i started working on support for VRM models in hAI!, i decided i wanted a unified api to load (3d) models of different formats and capabilities. that's how AvLoader came to be, and i'm quite proud of it.
in fact, while working on it, i even found [a bug in UniVRM!](https://github.com/vrm-c/UniVRM/pull/2768)

AvLoader provides what is essentially a fallback-based pipeline, or "flow", for loading avatars.

you build a flow by first defining which data loaders it should use. the package has two built-in loaders, one which loads from a folder/files, and one which loads from URLs, using UnityWebRequest. users can also create their own loaders, for example, hAI! has a custom loader to download backed-up avatars from Firebase Storage. loaders not only load the binary model, but also a metadata file and optional thumbnails of the avatar.

next, you declare the importers you want to use. the package provides 3, one which uses Unity's glTFast to load glTF models, one which uses UniGLTF instead, and one for VRM models, using UniVRM. again, users can build their own importers if needed. i actually wanted to add fbx support, but didn't find any actively maintained Unity packages/plugins for loading fbx models at runtime.

next, you can optionally also add post-processors, both sync and async. the package provides many built in ones, like for assigning an animator controller and avatar, for overriding materials or shaders, and even one which caches models in device storage (useful if you downloaded it from a URL)! there's even conditional post-processors which run other post-processors based on lambda functions!

finally, there's also "capabilities". these are interfaces that expose functionality in imported models "guaranteed" by the importer. that is, they are features created by the importer. the package provides support for UniVRM's LookAt and Expressions, and glTF blendshapes through capabilities.

## [UGemini](https://github.com/Uralstech/UGemini) (and [UAI.Abstraction](https://github.com/Uralstech/UAI.Abstraction))

they're officially dead. i first archived ugemini as 1- i don't use it anymore, and 2- google released their [GenAI SDK](https://googleapis.github.io/dotnet-genai/) for .NET and the [Firebase AI Logic SDK](https://firebase.google.com/docs/ai-logic) for Unity specifically, which accomplish what ugemini was trying to do.

<blockquote class="twitter-tweet" data-media-max-width="720" data-lang="en" data-dnt="true" data-theme="dark"><p lang="en" dir="ltr">if you are looking to build client side integrations. I recommend using the Firebase AI logic with App check to protect the key. Unity sdk support was released last year and it works with XR devices as well.</p>&mdash; Thevi Sundaralingam (@Thevi_S) <a href="https://twitter.com/Thevi_S/status/2026303891495911538?ref_src=twsrc%5Etfw">February 24, 2026</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

uai.abstraction is pretty funny. i basically lost all need for it *really* quickly. the commit history of the repo lasts literally *two days* (excluding my archive commits).
with ugemini was archived, uai.abstraction lost half of whatever utility it had, so i decided to officially archive it.

## [AIDE-Chip Surrogates](https://github.com/Uralstech/AIDE-Chip-Surrogates)

this is actually not at all related to unity, or even C#, it's an ml project made in python (and partly in c). tldr; the project aims to provide a pipeline to train small, lightweight ml models for predicting the performance (as ipc, instructions per cycle, or cache miss rate) of a given benchmark for a given architecture. the proof-of-concept was trained on a dataset of 15,000 gem5 simulation results, for 6 benchmarks, for a single-core RISC-V processor.

since this is quite different from my other projects, i recommend checking out the repo and linked resources to know more. the code, dataset and trained models are all publicly available.

---

well, that's what i've been up to on github since the last post. i did exclude one project from having its own entry, ["Qwen KCC On-Device Pipeline"](https://github.com/Uralstech/Qwen-KCC-On-Device-Pipeline), but more so because it didn't really meet my expectations to deserve a mention, mainly due to the LiteRT-LM conversion issue i talked about.

i felt like i should talk about the games i've played recently-ish, so here goes.

## Celeste

i completed all c-sides in celeste, and got the moonberry! i even made my steam profile public: <https://steamcommunity.com/id/SoullessTomb86/>

celeste is such a great game like it's genuinely imo one of THE top 3 games of all time. the soundtrack is unmatched, and the gameplay is so good it makes me forget i died 20k+ times. i cannot even describe what farewell felt like,,, it's just something special. anyways here's me doing old site in 2 minutes 32 seconds

<blockquote class="twitter-tweet" data-media-max-width="720" data-lang="en" data-dnt="true" data-theme="dark"><p lang="en" dir="ltr">celeste old site 2:32.201 &quot;&quot;&quot;speedrun&quot;&quot;&quot; <a href="https://t.co/0kfUW0fU0K">pic.twitter.com/0kfUW0fU0K</a></p>&mdash; Uday (@uralstechCTO) <a href="https://twitter.com/uralstechCTO/status/2018438362944901248?ref_src=twsrc%5Etfw">February 2, 2026</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

## Deadlock

oooh my god. this game. it's so good. i'm so bad at it. but it is so fun i can't be mad. i main silver btw, awoo.

i think? i've been improving recently? i got my first mvp in a match with actual humans. so that's something...

<blockquote class="twitter-tweet" data-media-max-width="720" data-lang="en" data-dnt="true" data-theme="dark"><p lang="en" dir="ltr">OMG OMG OMG MY FIRST MVP!!!!!!!!!!!!!!!!!!!!!!!!! <a href="https://t.co/9umBlUMqvc">pic.twitter.com/9umBlUMqvc</a></p>&mdash; Uday (@uralstechCTO) <a href="https://twitter.com/uralstechCTO/status/2053257869894746330?ref_src=twsrc%5Etfw">May 9, 2026</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

i'll probably keep posting more about deadlock on my x/twitter, idk. i think i've yapped enough for now. thanks for reading!


also, lmao the official x publish website generates embeds which still use the twitter.com link