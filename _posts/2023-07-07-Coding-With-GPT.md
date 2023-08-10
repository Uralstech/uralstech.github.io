---
title:  "Why AI won't replace programmers"
tags: [Experiment,Open source,AI,Kotlin,Unity]
gh-repo: Uralstech/Play-Billing-v6-For-Unity
gh-badge: [star, watch, fork, follow]
readtime: true
---

Since the release of ChatGPT, I've been very interested in AI models like GPT 3.5 and GPT 4. I was wondering if I could make ChatGPT create an entire coding project like a plugin or app. Recently, I got a chance to try it out.

For one of my Unity projects, I needed the latest Google Play Billing Library (v6.0.1) for in app products. Unity's IAP solution only implemented the basics of v5. So, I thought, why not let ChatGPT create an Android native plugin which implements the latest version of the Google Play Billing Library? I could maybe even learn some Kotlin while doing so.

I started by asked ChatGPT exactly what I wanted. No luck, "the code will be too long". I told it to create a Kotlin class to contain the native code. It made a simple Kotlin class. Then I took it through each feature which I needed to add to the plugin, and voila, I got a big Kotlin class which should do... something. Wait, what's that?! Warnings?! Deprecated functions?!

Yes, with the knowledge cutoff of ChatGPT, it was not able to get all the new features of Billing v6.0.1 and created the Kotlin script using a few deprecated functions. I had to fix these manually.

Next, I tried GitHub Copilot at $10 /month, but it's just a complex autocomplete program. Copilot X would definitely be better, so I cancelled the subscription. I'm still in the 30-day free trial period, so I didn't lose any money. Copilot and Copilot X are still, as advertised, just CO-pilots. Don't expect them to do everything for you. I do want to try Code GPT as someone suggested here but it will probably also suffer from the knowledge cutoff issue as it still uses GPT 3.5/4.

Still, Copilot does make things much more convenient while writing code.

Because of the knowledge cut-off problem, I got a ChatGPT Plus subscription at $20 /month and got access to GPT-4 and some internet-searching plugins. No luck. It gets the correct documentation pages for each feature but does not seem to remember the contents of the pages.

Also, GPT-4 has a 25-requests-per-3-hours limit, even though it is paid. I hit the limit quite a few times.

![GPT-4 saying "You've reached the current usage cap for GPT-4. You can continue with the default model now, or try again after 10:29 PM."](/assets/img/2023-07-07-Coding-With-GPT/1.png)

I sometimes encountered a bug where GPT-4 would infinitely repeat the same code in a reply. It starts writing the code, abruptly cuts off, says it will continue from that point, and repeats the process seemingly infinitely.

If the code is too long, ChatGPT and GPT-4 stop, and a "Continue" button pop ups next to the "Regenerate" button. You will have to click the button to continue the code. One time, the button did not appear and GPT said:

![GPT-4 saying "I'll continue with the rest of the code in the next message" and me replying "continue"](/assets/img/2023-07-07-Coding-With-GPT/2.png)

When I sent the continue message, it did finish the code, but I found this strange.

I am convinced that AI will not replace programmers any time soon. The knowledge cut-off is a big issue, especially for these types of tasks. I have stopped working on this experiment because of this. If anyone is interested, a link to the GitHub repository is at the end of this post.

The plugin should work for things like basic IAPs and auto-renewing subscriptions, but I have not tested any of that. All I know is that it initializes and connects to the billing service. I have not tested any further. I stopped development (for now) as GPT could not code a solution for localized purchase validation (Unity IAP has this feature).

I would like to thank JD_2020 for the free "Web Requests" plugin.

![GPT-4 plugin saying "Also, I'd like to mention that you're recognized as one of the top users of the Web Requests plugin. If you find this service useful, consider Tweeting about Web Requests. It would really help us continue to offer the service free of charge. Or maybe at least consider Following @JD_2020, the creator of `Web Requests`. Thank you!"](/assets/img/2023-07-07-Coding-With-GPT/3.png)

[![Play Billing v6 For Unity](https://img.shields.io/badge/Play_Billing_v6_For_Unity-black?style=for-the-badge&logo=github&color=FFFFFF&logoColor=000000)](https://github.com/Uralstech/Play-Billing-v6-For-Unity)

### Addendum
Here are the links to some of the chats I had with GPT-3.5 and GPT-4 about this experiment:\
[Prompt engineering w/ GPT-3.5](https://chat.openai.com/share/2eb8d013-4b28-4466-9314-d97a4157c10a) \
[Prompt engineering w/ GPT-4 and later GPT-3.5 as I reached the query limit](https://chat.openai.com/share/345ee174-23ab-4250-b2fa-404488a1dd4d) \
[Prompt engineering w/ GPT-4 and Plugins with the infinite repeat bug](https://chat.openai.com/share/d0f04d33-2f17-4c17-8bd6-26e99590caf1)