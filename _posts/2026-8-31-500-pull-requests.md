---
layout: post
title:  "five hundred pull requests"
tags: [Project,Open source,Unity]
readtime: true
---

i named this post on the 3rd and im not changing it now
<!--more-->

okay, icl but i find this format of talking about stuff really hard to maintain.
i *want* to do this, but i just dread sitting down and just straight writing tons
of paragraphs. but i want to keep going. so i will.

my last post was on the 18th of last month, and a lot has happened since then.

to address the title, i decided it was time to sign all my (maintained) packages.
this isn't a big deal, it's something unity introduced in 6.3, but i just decided the
warning for unsigned packages in the package manager was too annoying to not address.
tldr; i had to set up an action to create the signed unitypackage for all of my packages.
i also updated the code of conduct to contributor covenant 3.0 in each repo and automated
the github pages release and pdf generation with the action.

next, meta released version 205 of their unity sdk, which fixed unity 6.5 support.
so, i updated everything to 6.5, which has some new apis to handle fepm (fast enter
play mode), which will be enabled by default in unity 6.6. the biggest fepm violation
was in utils.singleton, as it stores static instances of monobehaviors. i also went through
all the code in all my packages to check for more fepm violations, and managed to find some
unrelated bugs in avloader and ushare.

alongside all this, two pretty big things happened.

first, due to performance issues in hAI! which were traced back to uxr.questmeshing,
i was able to remove *all* lag spikes caused by its navmesh generation. i also cleaned it
up as a whole and improved the mesh generation shader quite a bit. i am, personally,
very proud of uxr.questmeshing v1.3.0 and v1.4.0.

second, litert-lm v0.14.0 released, with android support for the python api.
the python api was backed by a c api. this meant i could finally get uai.litertlm
working on all platforms with the c api, instead of it being a c# wrapper for the
android/kotlin package which in itself is a wrapper for the c++ litert-lm library.

so, i completely rewrote uai.litertlm to make it work with the c api. i built
and packaged the litert-lm c library for uai.litertlm for android, macos, ios,
the ios arm64 simulator, and windows, which are all the platforms supported by both
unity and litert-lm, except for linux.

now i've got a really solid api for running llms on android. but how would i get the actual
llm files in the first place? i could include it in the apk, or package it as an obb for
distribution. but what if local inference was *optional* in this theoretical app?
that'd be a lot of wasted space for an optional feature. what if i wanted to serve
multiple models? what if i wanted the user to be able to choose which model to download?
and so on.

now yes, unity has unitywebrequests. but it only works while the app is running, and
having said app open for half an hour just for downloading the llm is not very convenient
for the user.

android provides the downloadmanager api to start large downloads which will run in the
background, even with the app closed. it also pauses and resumes downloads in case of
connection loss, low battery, etc. [now it works in unity!](https://github.com/Uralstech/UDownloadManager)
ios also has a similar service, and i do plan to support it *eventually*.

and that's everything that happened in july after my last post.

during august, i released uxr.questmeshing v1.5.0, which added some qol features and
improved exception handling. afterwards, most of my commits went to uai.litertlm,
including getting it released on nuget, making it my second ever nuget package.

one thing i noticed while looking around the *huge* litert-lm repo, is that they were
working on a framework to get generative, litert-compiled tts models running on the same
platforms as litert-lm, called "omni". it exposes an api completely different from
litert-lm, and uses raw litert models for inference. i had been keeping an eye on it for
a while, but in august, they finally got it working, with qwen 3 tts. presumably, they had
their own version of the compiled model, but seemed to be based on [this one](https://huggingface.co/litert-community/Qwen3-TTS-12Hz-0.6B-Base)
from hugging face. so, i forked the litert-lm repo and started working on getting the model
from hugging face working with the omni api.

i've published all the code and edited weights on github and huggingface. chatgpt actually
helped quite a bit with the model compilation/conversion scripts, so i only needed one patch
in the actual c++ code for the omni api, in addition to the c wrapper i made to make it work
with c# and unity.

<blockquote class="twitter-tweet" data-media-max-width="750" data-lang="en" data-dnt="true" data-theme="dark">got voice cloning working, with my own voice! (it doesn&#39;t really sound like me but... close enough) <a href="https://x.com/hashtag/LiteRT?src=hash&amp;ref_src=twsrc%5Etfw">#LiteRT</a> <a href="https://x.com/hashtag/LiteRTLM?src=hash&amp;ref_src=twsrc%5Etfw">#LiteRTLM</a> <a href="https://x.com/hashtag/Omni?src=hash&amp;ref_src=twsrc%5Etfw">#Omni</a> <a href="https://x.com/hashtag/Unity?src=hash&amp;ref_src=twsrc%5Etfw">#Unity</a> <a href="https://x.com/hashtag/MetaQuest?src=hash&amp;ref_src=twsrc%5Etfw">#MetaQuest</a> <a href="https://x.com/hashtag/AI?src=hash&amp;ref_src=twsrc%5Etfw">#AI</a> <a href="https://x.com/hashtag/LocalLLM?src=hash&amp;ref_src=twsrc%5Etfw">#LocalLLM</a> <a href="https://t.co/ywfdi3YQVD">https://t.co/ywfdi3YQVD</a> <a href="https://t.co/hDjXk3qFX1">pic.twitter.com/hDjXk3qFX1</a></p>&mdash; Uday (@uralstechCTO) <a href="https://x.com/uralstechCTO/status/2088102707710267717?ref_src=twsrc%5Etfw">August 14, 2026</a></blockquote> <script async src="https://platform.x.com/widgets.js" charset="utf-8"></script>

and... yeah. that's it for this month. thank you for reading this post.




i'm not adding a deadlock snippet this time because matchmaking is trash rn