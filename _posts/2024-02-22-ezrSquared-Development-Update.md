---
title:  "A development update for ezr²"
tags: [Project,Open source,CSharp]
gh-repo: Uralstech/ezrSquared
gh-badge: [star, watch, fork, follow]
readtime: true
---

What's been going on with ezr²?

### What I've Been Doing

The interpreter. I've been making the interpreter. And fixing bugs. A lot of bugs.

Okay - That's not completely `true`. I have added a ***ton*** of features.

This dev update is to showcase all these features. They will be released under the [***ezrSquared-re branch***](https://github.com/Uralstech/ezrSquared/tree/ezrSquared-re) when they are completed.

## Upcoming features for ezrSquared-re

### A Big One - Numbers With No Limits!
YES!!! Integers now implement C#'s *BigInteger*, so you don't have to worry about the size of your numbers!

Floating points numbers now implement C#'s *double* now. So, higher range for floats!

### Another Big One - Accessibility Modifiers.

Yes! I have added accessibility modifiers to ezr²! The accessibility is taken care of during runtime. Whenever your access or set a variable, the context will check for the validity of the operation depending on the accessibility modifiers.

The syntax goes like this:
```ezrSquared
@ A global variable, which already existed in ezr².
global var_a : 342

@ A CONSTANT variable? I guess its just a constant.
constant const_b: "Hello ezr²!"

@ A PRIVATE variable!
private var_c: "You can't touch this"

@ A STATIC variable!
static var_d: 27
```

And of course, you can mix and match them all!
(but you cannot have `global` and `private` together, duh)

The same applies when defining functions and object-types!

Static variables are assigned to the object-type's (or class's) context, which is seperate from the context of an instance of the class.

### Continuing From The Previous - Define Blocks!

Tired of having to type `private` or `static` again and again? Put them in a define block!

Syntax:
```ezrSquared
define private
    private_var_a: 34
    private_var_b: 43
    ...
end
```

`define` blocks accept the `global`, `private`, `static` and `constant` keywords, and you can have them together!

```ezrSquared
define static constant
    static_constant_a: 653
    static_constant_b: "sdf"
end
```

### Additions To Object-Types (Classes)!

Now, you can have readonly or static classes!

In a readonly class, no outside code can modify variables inside the class. Code in the class is still free to modify these variables.

```ezrSquared
readonly object readonly_object do
    ...
end
```

In a static class, well, all variables, function and nested object-types are static! And, you cannot create an instance of the class.

```ezrSquared
static object static_object do
    ...
end
```

### Keyword-Arguments For Functions!

You can now provide infinite keyword-ed arguments to your functions/object initializers! They work just like in any other language.

```ezrSquared
function function_a with more as keyword_args do
    ...
end
```

In the above case, `keyword_args` would be a `dictionary`.

### A Whole Lot Of C# Wrappers!

I've added better C# Source Wrappers, for CSAELS, and automatic C# Wrappers, which will wrap C# classes, functions, properties, fields, etc.
at runtime! The wrappers are also able to convert ezr² objects into primitives and vice-versa.

This will be used in `include` expressions (which have not been implemented yet) to import C# libraries and DLLs which may not have been intended
to be imported into ezr², like *System* namespace libraries or *Newtonsoft.Json*, for example.

### Throwable Errors!

Runtime errors are now actual objects! (Thanks to the aforementioned automatic C# Wrappers)

You can create and handle errors at runtime, just like any other object! You can even throw errors at runtime using the built-in `throw_error` function!

### More Customization For Classes!

You can now override all these properties of your class:

```
is_equal (a = b)
is_inequal (a ! b)
is_less_than (a < b)
is_greater_than (a > b)
is_less_than_or_equal (a <= b)
is_greater_than_or_equal (a >= b)

addition (a + b)
subtraction (a - b)
multiplication (a * b)
division (a / b)
modulo (a % b)
power (a ^ b)

negation (-a)
affirmation (+a)

bitwise_or (a | b)
bitwise_xor (a \ b)
bitwise_and (a & b)
bitwise_left_shift (a << b)
bitwise_right_shift (a >> b)
bitwise_negation (~a)

contains (a in b)
does_not_contain (a not in b) (NEW!)

inversion (invert b)

evaluate_boolean (this is called to evaluate the object to a boolean, like how 1 evalues to true or how nothing evaluates to false)

call_received (this is called when the object is called like a function - NOT when the class is called to create the object)

strict_equals (used for strict equality checking)

get_hash_code (used for getting the object's hash code)

to_string (used to get a string representation of the object, like when it is passed to the 'show' function)
```

The above changes were just most important ones! There are many more, smaller improvements that are going to come to ezr².

## Breaking changes.

Yes. There are breaking changes.

### Syntax Change For The Try-Error Expression (Now Try-Catch)

Yes. I have changed the syntax of Try-Error expressions to Try-Catch.

### Special Functions Are Gone

They are now replaced with functions with pre-set names.

For example, to implement the addition operation to your class, you now do this:
```ezrSquared
object an_object do
    function addition with other do
        return "BOO!"
    end
end
```

### Keywords Are Now Case-Insensitive

Yeah. This can be both a positive and a negative. You can now write code more like sentences, by capitalizing letters in keywords, but, code
that may use variable names like `Step` or `Item` will break (although you should not be naming variables like that anyways since ezr² follows
**snake_case**).

## One Last Addition

I have added - A LOT OF BUGS! Yaaay?

Yes. There are lots of bugs in the 'new' ezr². That's the main reason its been taking so long.

## Conclusion

Well, so that's it for this ezr² update. I will be working on ezr², slowly. Maybe I'll post more dev updates along the way, too.

Thanks for reading this long post.