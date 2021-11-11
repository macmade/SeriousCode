SeriousCode
===========

[![Issues](http://img.shields.io/github/issues/macmade/SeriousCode.svg?logo=github)](https://github.com/macmade/SeriousCode/issues)
![Status](https://img.shields.io/badge/status-legacy-red.svg?logo=git)
![License](https://img.shields.io/badge/license-mit-brightgreen.svg?logo=open-source-initiative)  
[![Contact](https://img.shields.io/badge/follow-@macmade-blue.svg?logo=twitter&style=social)](https://twitter.com/macmade)
[![Sponsor](https://img.shields.io/badge/sponsor-macmade-pink.svg?logo=github-sponsors&style=social)](https://github.com/sponsors/macmade)

About:
------

**Clang warning enforcement**

This header file enforces Clang warnings to be turned-on for specific flags (almost everyone, at least each one I was able to find from the LLVM/Clang sources).

When including it, every little code mistake, possibly undefined behavior, every pedantic warning, etc., will turn in a compiler fatal error.

You can use it with IDEs using Clang (ie. Xcode) to ease the warning/error flag definition.
As it enforces the flags to be turned on at compile-time, you don't need to set the in your IDE.

I personally use it for all my production code, and I think every developer should at least be aware of those possible issues.

It's time to get serious.
If your code does not compile with that, maybe it's time for you to learn a bit more about your programming language.

### Note about `-Weverything`

When I originally wrote this project, as a Mac and iOS developer, the `-Weverything` flag was not supported on Apple's implementation of Clang.  
This is why I developed this header file.

I still prefer it over `-Weverything`, as I can then fine-tune the warning flags I want.

### Further reading

I actually [posted a question](http://programmers.stackexchange.com/questions/122608/clang-warning-flags-for-objective-c-development) about this on StackOverflow, and I got an outstanding answer by Chandler Carruth, one of the Clang developers.

The question was:

> As a C & Objective-C programmer, I'm a bit paranoid with the compiler warning flags.
> I usually try to find a complete list of warning flags for the compiler I use, and turn most of them on, unless I have a really good reason not to turn it on.
> 
> I personally think this may actually improve coding skills, as well as potential code portability, prevent some issues, as it forces you to be aware of every little detail, potential implementation and architecture issues, and so on...
> 
> It's also in my opinion a good every day learning tool, even if you're an experienced programmer.
> 
> For the subjective part of this question, I'm interested in hearing other developers (mainly C, Objective-C and C++) about this topic.
> Do you actually care about stuff like pedantic warnings, etc? And if yes or no, why?
> 
> Now about Objective-C, I recently completely switched to the LLVM toolchain (with Clang), instead of GCC.
> 
> On my production code, I usually set this warning flags (explicitly, even if some of them may be covered by -Wall):
> 
> -Wall
> -Wbad-function-cast
> -Wcast-align
> -Wconversion
> -Wdeclaration-after-statement
> -Wdeprecated-implementations
> -Wextra
> -Wfloat-equal
> -Wformat=2
> -Wformat-nonliteral
> -Wfour-char-constants
> -Wimplicit-atomic-properties
> -Wmissing-braces
> -Wmissing-declarations
> -Wmissing-field-initializers
> -Wmissing-format-attribute
> -Wmissing-noreturn
> -Wmissing-prototypes
> -Wnested-externs
> -Wnewline-eof
> -Wold-style-definition
> -Woverlength-strings
> -Wparentheses
> -Wpointer-arith
> -Wredundant-decls
> -Wreturn-type
> -Wsequence-point
> -Wshadow
> -Wshorten-64-to-32
> -Wsign-compare
> -Wsign-conversion
> -Wstrict-prototypes
> -Wstrict-selector-match
> -Wswitch
> -Wswitch-default
> -Wswitch-enum
> -Wundeclared-selector
> -Wuninitialized
> -Wunknown-pragmas
> -Wunreachable-code
> -Wunused-function
> -Wunused-label
> -Wunused-parameter
> -Wunused-value
> -Wunused-variable
> -Wwrite-strings
> 
> I'm interested in hearing what other developers have to say about this.
> 
> For instance, do you think I missed a particular flag for Clang (Objective-C), and why?
> Or do you think a particular flag is not useful (or not wanted at all), and why?
> 
> EDIT
> 
> To clarify the question, note that -Wall only provides a few basic warnings.
> They are actually a lot more warning flags, not covered by -Wall, hence the question, and the list I provide.

Chandler Carruth's answer was:

> For context, I'm a Clang developer working at Google. At Google, we've rolled Clang's diagnostics out to (essentially) all of our C++ developers, and we treat Clang's warnings as errors as well. As both a Clang developer and one of the larger users of Clang's diagnostics I'll try to shed some light on these flags and how they can be used. Note that everything I'm describing is generically applicable to Clang, and not specific to C, C++, or Objective-C.
> 
> TL;DR Version: Please use -Wall and -Werror at a minimum on any new code you are developing. We (the compiler developers) add warnings here for good reasons: they find bugs. If you find a warning that catches bugs for you, turn it on as well. Try -Wextra for a bunch of good candidates here. If one of them is too noisy for you to use profitably, file a bug. If you write code that contains an "obvious" bug but the compiler didn't warn about it, file a bug.
> 
> Now for the long version. First some background on warning flag groupings. There are a lot of "groupings" of warnings in Clang (and to a limited extent in GCC). Some that are relevant to this discussion:
> 
> On-by-default: These warnings are always on unless you explicitly disable them.
> -Wall: These are warnings that the developers have high confidence in both their value and a low false-positive rate.
> -Wextra: These are warnings that are believed to be valuable and sound (i.e., they aren't buggy), but they may have high false-positive rates or common philosophical objections.
> -Weverything: This is an insane group that literally enables every warning in Clang. Don't use this on your code. It is intended strictly for Clang developers or for exploring what warnings exist.
> There are two primary criteria mentioned above which guide where warnings go in Clang, and let's clarify what these really mean. The first is the potential value of a particular occurrence of the warning. This is the expected benefit to the user (developer) when the warning fires and correctly identifies an issue with the code.
> 
> The second criteria is the idea of false-positive reports. These are situations where the warning fires on code, but the potential problem being cited does not in fact occur due to the context or some other constraint of the program. The code warned about is actually behaving correctly. These are especially bad when the warning was never intended to fire on that code pattern. Instead, it is a deficiency in the warning's implementation that causes it to fire there.
> 
> For Clang warnings, the value is required to be in terms of correctness, not in terms of style, taste, or coding conventions. This limits the set of warnings available, precluding oft-requested warnings such as warning whenever {}s are not used around the body of an if statement. Clang is also very intolerant of false-positives. Unlike most other compilers it will use an incredible variety of information sources to prune false positives including the exact spelling of the construct, presence or absence of extra '()', casts, or even preprocessor macros!
> 
> Now let's take some real-world example warnings from Clang, and look at how they are categorized. First, a default-on warning:
> 
>     % nl x.cc
>          1  class C { const int x; };
>     
>     % clang -fsyntax-only x.cc
>     x.cc:1:7: warning: class 'C' does not declare any constructor to initialize its non-modifiable members
>     class C { const int x; };
>           ^
>     x.cc:1:21: note: const member 'x' will never be initialized
>     class C { const int x; };
>                         ^
>     1 warning generated.
>
> Here no flag was required to get this warning. The rationale is that this is code is never really correct, giving the warning high value, and the warning only fires on code that Clang can prove falls into this bucket, giving it a zero false-positive rate.
> 
>     % nl x2.cc
>          1  int f(int x_) {
>          2    int x = x;
>          3    return x;
>          4  }
>     
>     % clang -fsyntax-only -Wall x2.cc
>     x2.cc:2:11: warning: variable 'x' is uninitialized when used within its own initialization [-Wuninitialized]
>       int x = x;
>           ~   ^
>     1 warning generated.
>
> Clang requires the -Wall flag for this warning. The reason is that there is a non-trivial amount of code out there which has used (for good or ill) the code pattern we are warning about to intentionally produce an uninitialized value. Philosophically, I see no point in this, but many others disagree and the reality of this difference in opinion is what drives the warning under the -Wall flag. It still has very high value and a very low false-positive rate, but on some codebases it is a non-starter.
> 
>     % nl x3.cc
>          1  void g(int x);
>          2  void f(int arr[], unsigned int size) {
>          3    for (int i = 0; i < size; ++i)
>          4      g(arr[i]);
>          5  }
>     
>     % clang -fsyntax-only -Wextra x3.cc
>     x3.cc:3:21: warning: comparison of integers of different signs: 'int' and 'unsigned int' [-Wsign-compare]
>       for (int i = 0; i < size; ++i)
>                       ~ ^ ~~~~
>     1 warning generated.
>
> This warning requires the -Wextra flag. The reason is that there are very large codebases where mis-matched sign on comparisons is extremely common. While this warning does find some bugs, the probability of the code being a bug when the user writes it is fairly low on average. The result is an extremely high false-positive rate. However, when there is a bug in a program due to the strange promotion rules, it is often extremely subtle making this warning when it flags a bug have relatively high value. As a consequence, Clang provides it and exposes it under a flag.
> 
> Typically, warnings don't live long outside of the -Wextra flag. Clang tries very hard to not implement warnings which do not see regular use and testing. The additional warnings turned on by -Weverything are usually warnings under active development or with active bugs. Either they will be fixed and placed under appropriate flags, or they should be removed.
> 
> Now that we have an understanding of how these things work with Clang, let's try to get back to the original question: what warnings should you turn on for your development? The answer is, unfortunately, that it depends. Consider the following questions to help determine what warnings work best for your situation.
> 
> Do you have control over all of your code, or is some of it external?
> What are your goals? Catching bugs, or writing better code?
> What is your false-positive tolerance? Are you willing to write extra code to silence warnings on a regular basis?
> First and foremost, if you don't control the code, don't try turning extra warnings on there. Be prepared to turn some off. There is a lot of bad code in the world, and you may not be able to fix all of it. That is OK. Work to find a way to focus your efforts on the code you control.
> 
> Next, figure out what you want out of your warnings. This is different for different people. Clang will try to warn without any options on egregious bugs, or code patterns for which we have long historical precedent indicating the bug rate is extremely high. By enabling -Wall you're going to get a much more aggressive set of warnings targeted at catching the most common mistakes that Clang developers have observed in C++ code. But with both of these the false-positive rate should remain quite low.
> 
> Finally, if you're perfectly willing to silence *false-positive*s at every turn, go for -Wextra. File bugs if you notice warnings which are catching a lot of real bugs, but which have silly or pointless false positives. We're constantly working to find ways to bring more and more of the bug-finding logic present in -Wextra into -Wall where we can avoid the false-positives.
> 
> Many will find that none of these options is just-right for them. At Google, we've turned some warnings in -Wall off due to a lot of existing code that violated the warning. We've also turned some warnings on explicitly, even though they aren't enabled by -Wall, because they have a particularly high value to us. Your mileage will vary, but will likely vary in similar ways. It can often be much better to enable a few key warnings rather than all of -Wextra.
> 
> I would encourage everyone to turn on -Wall for any non-legacy code. For new code, the warnings here are almost always valuable, and really make the experience of developing code better. Conversely, I would encourage everyone to not enable flags beyond -Wextra. If you find a Clang warning that -Wextra doesn't include but which proves at all valuable to you, simply file a bug and we can likely put it under -Wextra. Whether you explicitly enable some subset of the warnings in -Wextra will depend heavily on your code, your coding style, and whether maintaining that list is easier than fixing everything uncovered by -Wextra.
> 
> Of the OP's list of warnings (which included both -Wall and -Wextra) only the following warnings are not covered by those two groups (or turned on by default). The first group emphasize why over-reliance on explicit warning flags can be bad: none of these are even implemented in Clang! They're accepted on the command line only for GCC compatibility.
> 
> * -Wbad-function-cast
> * -Wdeclaration-after-statement
> * -Wmissing-format-attribute
> * -Wmissing-noreturn
> * -Wnested-externs
> * -Wnewline-eof
> * -Wold-style-definition
> * -Wredundant-decls
> * -Wsequence-point
> * -Wstrict-prototypes
> * -Wswitch-default
>
> The next bucket of unnecessary warnings in the original list are ones which are redundant with others in that list:
> 
> * -Wformat-nonliteral -- Subset of -Wformat=2
> * -Wshorten-64-to-32 -- Subset of -Wconversion
> * -Wsign-conversion -- Subset of -Wconversion
>
> There are also a selection of warnings which are more categorically different. These deal with language dialect variants rather than with buggy or non-buggy code. With the exception of -Wwrite-strings, these all are warnings for language extensions provided by Clang. Whether Clang warns about their use depends on the prevalence of the extension. Clang aims for GCC compatibility, and so in many cases it eases that with implicit language extensions that are in wide use. -Wwrite-strings, as commented on the OP, is a compatibility flag from GCC that actually changes the program semantics. I deeply regret this flag, but we have to support it due to the legacy it has now.
> 
> * -Wfour-char-constants
> * -Wpointer-arith
> * -Wwrite-strings
>
> The remaining options which are actually enabling potentially interesting warnings are these:
> 
> * -Wcast-align
> * -Wconversion
> * -Wfloat-equal
> * -Wformat=2
> * -Wimplicit-atomic-properties
> * -Wmissing-declarations
> * -Wmissing-prototypes
> * -Woverlength-strings
> * -Wshadow
> * -Wstrict-selector-match
> * -Wundeclared-selector
> * -Wunreachable-code
>
> The reason that these aren't in -Wall or -Wextra isn't always clear. For many of these, they are actually based on GCC warnings (-Wconversion, -Wshadow, etc.) and as such Clang tries to mimic GCC's behavior. We're slowly breaking some of these down into more fine-grain and useful warnings. Those then have a higher probability of making it into one of the top-level warning groups. That said, to pick on one warning, -Wconversion is so broad that it will likely remain its own "top level" category for the foreseeable future. Some other warnings which GCC has but which have low value and high false-positive rates may be relegated to a similar no-man's-land.
> 
> Other reasons why these aren't in one of the larger buckets include simple bugs, very significant false-positive problems, and in-development warnings. I'm going to look into filing bugs for the ones I can identify. They should all eventually migrate into a proper large bucket flag or be removed from Clang.
> 
> I hope this clarifies the warning situation with Clang and provides some insight for those trying to pick a set of warnings for their use, or their company's use.

### 

License
-------

SeriousCode is released under the terms of the MIT License.

Repository Infos
----------------

    Owner:          Jean-David Gadina - XS-Labs
    Web:            www.xs-labs.com
    Blog:           www.noxeos.com
    Twitter:        @macmade
    GitHub:         github.com/macmade
    LinkedIn:       ch.linkedin.com/in/macmade/
    StackOverflow:  stackoverflow.com/users/182676/macmade
