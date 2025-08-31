---
layout: post
title:  "Native Sign-In and Sharing for Mobile in Unity!"
tags: [Project,Open source,Unity]
readtime: true
---

Hey everyone! Check out my new Unity packages for Android and iOS!
This was also my first time writing FOSS Swift code, and I had a blast doing it.

## UMoth

UMoth (**Mo**bile au**th**) implements native plugins to implement Google Sign In for Android 6+ (API Level 23) and Sign in With Apple for iOS 14.0+. It uses the respective native APIs on each platform to accomplish this ([`CredentialManager`](https://developers.google.com/identity/android-credential-manager) + [`GetGoogleIdOption`](https://developers.google.com/identity/android-credential-manager/android/reference/kotlin/com/google/android/libraries/identity/googleid/GetGoogleIdOption) on Android and [`ASAuthorizationAppleIDProvider`](https://developer.apple.com/documentation/authenticationservices/asauthorizationappleidprovider) on iOS).

It's really easy to use, the sign in operation is only one function call for each platform, and exposes all the attributes of the native credential objects to Unity. There's also clear documentation on how you can integrate it with Firebase!

The Unity package, Android Kotlin plugin and iOS Swift framework are completely free and open source on GitHub!

[![GitHub Repository](https://img.shields.io/badge/GitHub_Repository-black?style=for-the-badge&logo=github&color=FFFFFF&logoColor=000000)](https://github.com/Uralstech/UMoth)
[![Documentation](https://img.shields.io/badge/Documentation-black?style=for-the-badge&logo=github&color=FFFFFF&logoColor=000000)](https://uralstech.github.io/UMoth/)

## UShare

UShare implements native plugins to implement share sheet support in Unity for Android 6+ and iOS 14.0+. It uses [`FileProvider`](https://developer.android.com/reference/kotlin/androidx/core/content/FileProvider) on Android and [`UIActivityViewController`](https://developer.apple.com/documentation/UIKit/UIActivityViewController) on iOS.

Most methods to invoke the share sheet can be done in one line, and will work for both platforms seamlessly! The plugin allows you to share text, binary data, *anything* with the system share sheet. Sharing text and images have been confirmed to work with apps like WhatsApp, iMessage, the iOS Email and Notes apps, X and Discord on Android, etc. More apps definitely work, but these are the ones I've personally tested.

Just like UMoth, the Unity package, Kotlin plugin, and Swift framework are FOSS, on GitHub!

[![GitHub Repository](https://img.shields.io/badge/GitHub_Repository-black?style=for-the-badge&logo=github&color=FFFFFF&logoColor=000000)](https://github.com/Uralstech/UShare)
[![Documentation](https://img.shields.io/badge/Documentation-black?style=for-the-badge&logo=github&color=FFFFFF&logoColor=000000)](https://uralstech.github.io/UShare)


## A Quick Dev Update

I haven't really been getting much motivation to work on ezr² as of recently, due to my XR work taking importance.
Even then, I feel like I'm already getting restricted by the codebase. I started working on ezr² RE 2 YEARS ago.
I feel like I've learnt much more since then, and that I could definitely do better. It's my passion project, I
don't want any timelines for it and I don't want to be dissatisfied with it. So, there's probably gonna be another
rewrite of ezr². As they say, third time's the charm :3


Regarding FOSS in general, I'll be getting back into XR dev for a while, so there's probably not gonna be many updates
to my GitHub projects for some time.