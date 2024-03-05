---
title:  "ezr² development update (Mar 5, 2024)"
tags: [Project,Open source,CSharp]
gh-repo: Uralstech/ezrSquared
gh-badge: [star, watch, fork, follow]
readtime: true
---

Another (newer) development update for ezr²!

### What I've Been Doing

Major bugfix! And I have finished what I said I'd do in the last update.

## Bug fixes

### Dictionaries are fixed!

There was a <sub><sup>teensy-weensy</sup></sub> bug with ezr² dictionaries.

Keys in a dictionary are *supposed* to be immutable, you could add a key and remove it, nothing more, nothing less.

Well...

```
> item a: [1]
[1]
> item b: {a:5}
{[1] : 5}
> a + 6
nothing
> a
[1,6]
> b
{[1, 6] : 5}
```

This bug has existed, well, since the start of ezr² development! Now it's fixed!

#### How?
---

I've added a new interface called `IEzrMutableObject`. Any object of which values can be changed, has to inherit from `IEzrMutableObject`. Objects like `EzrList`, `EzrDictionary`, `EzrCharacterList` and `EzrClassInstance`.

You only have to implement one function, as shown here:
```csharp
    public class EzrList : EzrObject, IEzrMutableObject
    {
        ...
        
        public IMutable<IEzrMutableObject>? DeepCopy(RuntimeResult result)
        {
            List<EzrObjectReference> copy = [];
            for (int i = 0; i < Value.Count; i++)
            {
                EzrObjectReference newReference = new(Value[i].Object, Value[i].AccessibilityModifiers);
                if (newReference.Object is IEzrMutableObject mutableElement)
                {
                    IEzrObject? objectCopy = (IEzrObject?)mutableElement.DeepCopy(result);
                    if (result.ShouldReturn)
                        return null;

                    newReference.UpdateObject(objectCopy!);
                }

                copy.Add(newReference);
            }

            return new EzrList(copy, Context, StartPosition, EndPosition);
        }
    }
```

To facilitate this, the types `Context`, `EzrObjectReference` and `RuntimeEzrObjectDictionary` have also implemented similar functions.

## Breaking changes.

### Class definitions.

So, [*last update*](https://uralstech.github.io/2024/02/25/ezrSquared-Development-Update.html), I said I would implement this syntax for class definitions:

```ezrSquared
object something_3 do
    constant function initialize with b, c do
        this.b: b
        this.c: c
    end
end
```

It's finished now! Yaay!

Also, classes will now be called classes, instead of 'object type'-s. But this is only a change in the source code, not syntax.

## Conclusion

Well, so that's it for this ezr² update. It was a another short one!