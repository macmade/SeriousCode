SeriousCode
===========

Copyright (c) 2011 eosgarden - Jean-David Gadina <macmade@eosgarden.com>

**Clang warning enforcment**

About:
------

This header file enforces Clang warnings to bu turned-on for specific flags (almost everyone, at least each one I was able to find).

When including it, every little code mistake, possibly undefined behavior, every pedantic warning, etc., will turn in a compiler fatal error.

You can use it with IDEs using Clang (ie. Xcode) to ease the warning/error flag definition.
As it enforces the flags to be turned on at compile-time, you don't need to set the in your IDE.

I personally use it for all my production code, and I think every developer should at least be aware of those possible issues.

I'ts time to get serious.
If your code does not compile with that, maybe it's time for you to learn a bit more about your programming language.
