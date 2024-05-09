---
title:  "ezr² development update (May 10, 2024)"
tags: [Project,Open source,CSharp]
gh-repo: Uralstech/ezrSquared
gh-badge: [star, watch, fork, follow]
readtime: true
---

Another (brand new) (**the** latest) (fresh from the oven) development update for ezr²!

### What I've Been Doing

Uhhhhh... Well... Stuff? So, I first wanted to make some last-last-last-microsecond changes, yeah? Then, I wanted to make *many* last-last-last-microsecond changes.

Well, then I finished the changes. I thought I'll just try, uuuh, you know, using ezr² for some time? And... Let's just say... I found a a few huge, fat bugs.

The `skip` and `stop` statements returned null when used in a loop! Like, C# null, not `nothing`. So, I fixed that. Then I found out they do the same when you use them *outside*
a loop. So I've fixed that. THEN, I found having empty `return` statements in single-line functions also did the same! Soooooooooooooooooooooooo, I fixed that.

I think the launch maaaaaaaay be a <sub><sup>tiny</sup></sub> <sub><sup><sup>bit</sup></sup></sub> <sub><sup><sup><sup>delayed.</sup></sup></sup></sub>

<sub><sup>It's very late at night here. So that's it for this devlog or update or whatever. Also, I like using the sup and sub tags!</sup></sub>