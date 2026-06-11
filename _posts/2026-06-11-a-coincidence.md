---
layout: post
title:  "a coincidence"
tags: [Project,Open source,Unity]
readtime: true
---

why's there a rewrite of UShare?
<!--more-->

around half an hour ago, i merged [UShare](https://github.com/uralstech/UShare)'s unstable branch into master.
as i had mentioned in the last post, ushare was "basically feature complete". and, well, it was.

i was just going to start a new Android/iOS project, and before getting started, just wanted to confirm all
the packages i wanted to use were still working and up to date. i started with [UMoth](https://github.com/Uralstech/UMoth/),
and updated its gradle and agp versions, as i had also mentioned in the last post. right after committing the post,
i moved on to updating ushare. i did the same thing i did for umoth and just updated its dependencies, and moved onto testing it.

coincidentally, my quest 3 was already plugged into my mac, and since it's an android device, i decided to test ushare on it.
now, [hAI! MR](https://www.meta.com/experiences/hai-friend-mr/6902823533148269/) already uses ushare, so i KNOW it works on the quest.
the only problem was that hAI! only used it to share text, and i hadn't tried sharing any images or files using it.

this time, i did. in a simple, 2D, sideloaded app.

the system share sheet opened up fine, and it recognized that i wanted to share an image.
i chose whatsapp, chose a contact, and: "something went wrong".

turns out that after selecting the share target in the share sheet, my app regained focus and triggered ushare's automatic file deletion,
so the image i wanted to share got deleted before whatsapp could send it.

i then started out by removing the auto deletion code from ShareSheetManager. then, i started looking for ways to find out when the share
operation has been completed. iOS does this easily, as just a delegate passed to `UIActivityViewController`. android, on the other hand,
requires you to set up a broadcastreceiver, which also has to be declared in the app's manifest. so i added support for that, which
resulted in a rewrite of the kotlin plugin. i also saw that i could improve the iOS (swift) plugin, and since it was comparatively simple,
it basically got rewritten too. in this process, i found out:

1. the android broadcast receiver doesn't fire for all targets (like copy to clipboard)
2. neither the iOS delegate nor the android broadcast receiver indicate if the target app has consumed the file

i then decided that since the files being deleted by ushare were already in the system assigned cache directory, the app is probably fine
deleting them in subsequent launches, instead of immediately after the share event. but now there's an issue. ushare doesn't tell callers
the names of the files it created. also, why does ShareData() implicitly use a completely different share method on iOS if the content
being shared is exclusively made up of images? isn't that hidden logic? for these reasons, i chose to rewrite ShareSheetManager, too.
and with that, the entire package was rewritten. i decided to make a lightweight, mostly-backwards-compatible "ShareSheetManager",
which exposes the exact same public methods it did before, but using the new ShareSheetManagerV2 under the hood.

and now it's out! UShare v2.0.0. i hope i don't find more bugs in it. or in my other packages. we'll see, i guess.


also i've changed my mind on billy, mina is much better. probably in the top two ritualists. after silver, of course.