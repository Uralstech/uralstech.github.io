---
title:  "ezr² development update (Feb 25, 2024)"
tags: [Project,Open source,CSharp]
gh-repo: Uralstech/ezrSquared
gh-badge: [star, watch, fork, follow]
readtime: true
---

Another development update for ezr²!

### What I've Been Doing

I have successfully finished working on `static` definitions! But, I have some bad news about breaking changes.

## Breaking changes.

### Class definitions.

Yep, there is a new way to define classes now.

```ezrSquared
object something do
    ...
end
```

The above example may not look different to the existing way, but when you add parameters, you get:

```ezrSquared
object something_2 with b, c do
    constant function initialize do
        this.b: b
        this.c: c
    end
end
```

Yeah.

The function ***must*** be `constant` and, yes, it is now required if you have parameters for your classes.

Even with that, I am not sure this will stick around. I plan to add something like this:

```ezrSquared
object something_3 do
    constant function initialize with b, c do
        this.b: b
        this.c: c
    end
end
```

## Conclusion

Well, so that's it for this ezr² update. It was a short one!