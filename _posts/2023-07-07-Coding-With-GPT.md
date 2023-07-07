---
layout: post
title:  "[EXPERIMENT] Why AI won't replace programmers"
---

Since the release of ChatGPT, I've been very interested in AI models like GPT 3.5 and GPT 4. I was wondering if I could make ChatGPT create an entire coding project like a plugin or app. Recently, I got a chance to try it out.

For one of my Unity projects, I needed the latest Google Play Billing Library (v6.0.1) for in app products. Unity's IAP solution only implemented the basics of v5. So, I thought, why not let ChatGPT create an Android native plugin which implements the latest version of the Google Play Billing Library? I could maybe even learn some Kotlin while doing so.

I started by asked ChatGPT exactly what I wanted. No luck, "the code will be too long". I told it to create a Kotlin class to contain the native code. It made a simple Kotlin class. Then I took it through each feature which I needed to add to the plugin, and voila, I got a big Kotlin class which should... do something... Wait, what's that?! Deprecated functions?!

Yes, with the knowledge cutoff of ChatGPT, it was not able to get all the new features of Billing v6.0.1 and created the Kotlin script using a few "deprecated" functions. I had to fix these manually.

Because of the knowledge cut-off problem, I got an OpenAI Plus subscription and got access to GPT-4 and some internet-searching plugins. Still, no luck. It gets the correct documentation pages for each feature but does not seem to remember the contents of the pages.

Now, I am convinced that AI will not replace programmers any time soon. The knowledge cut-off is a big issue, especially for these types of tasks. I have stopped working on this experiment because of this. If anyone is interested in the code, the link to the GitHub repository is down below.

[![Play Billing v6 For Unity](https://img.shields.io/badge/Play_Billing_v6_For_Unity-black?style=for-the-badge&logo=github&color=FFFFFF&logoColor=000000)](https://github.com/Uralstech/Play-Billing-v6-For-Unity)