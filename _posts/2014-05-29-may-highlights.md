---
layout: default
title: Some Recent F# Core Engineering Highlights
subtitle: An Update on the F# Compiler Services, Visual F# Power Tools and more
---

Some Recent F# Core Engineering Highlights
==========================================

In this blog post, we'll have a quick
look at the recent work done by the community that falls within the remit of the Core 
Engineering group – there is a lot going on! 

F# Compiler Service
-------------------

F# Compiler Service (`FSharp.Compiler.Service.dll`) is a recent community project which 
can be used for hosting the F# compiler. This is a fundamental project that enables building
other open components on top of the F# compiler and has already enabled much of the 
features in [F# Power Tools](http://fsprojects.github.io/VisualFSharpPowerTools/), 
[F# Formatting](http://tpetricek.github.io/FSharp.Formatting/) and other libraries.

The F# Compiler Service currently exposes a number of core compiler APIs including:

 * **F# language tools for editors** including the F# tokenizer, ability to process
   parsed AST of F# code, editor services for auto-completion and tools for resolving
   symbols and other information about F# code.

 * **Hosting of F# interactive and compiler** allows calling F# interactive and the 
   F# compiler as .NET libraries. This way, you can compile and evaluate F# code on
   the fly.

There is a lot more that you can do. For more information:

 * See [the F# Compiler Service documentation](http://fsharp.github.io/FSharp.Compiler.Service/)
 * Get the [library via NuGet](https://www.nuget.org/packages/FSharp.Compiler.Service) and play with it!
 * Contribute [to the project on GitHub](https://github.com/fsharp/FSharp.Compiler.Service)

Cross-platform and Open Tools
-----------------------------

The core [F# open-source repository](https://github.com/fsharp/fsharp) has been 
getting ready for the upcoming development of F# 4.0. The "master" branch is now
fully integrated with the open-source release of F# 3.1 and everything is now
building automatically:

 * Head (branch `master`), Mono 3.x, OSX + some unit tests (Travis) <a href="https://travis-ci.org/fsharp/fsharp/branches"><img src="https://camo.githubusercontent.com/b816bca9234348b84b7cc0051c3af184d4c81c67/68747470733a2f2f7472617669732d63692e6f72672f6673686172702f6673686172702e706e673f6272616e63683d6d6173746572" alt="Build Status" data-canonical-src="https://travis-ci.org/fsharp/fsharp.png?branch=master" style="max-width:100%;"></a>
 * F# 3.1 (branch `fsharp_31`), Mono 3.x, OSX + some unit tests (Travis) <a href="https://travis-ci.org/fsharp/fsharp/branches"><img src="https://camo.githubusercontent.com/b5cbca4def11bb46820e0bba90c9b3fa4b0d5174/68747470733a2f2f7472617669732d63692e6f72672f6673686172702f6673686172702e706e673f6272616e63683d6673686172705f3331" alt="Build Status" data-canonical-src="https://travis-ci.org/fsharp/fsharp.png?branch=fsharp_31" style="max-width:100%;"></a>
 * F# 3.0 (branch `fsharp_30`), Mono 3.x, OSX + some unit tests (Travis) <a href="https://travis-ci.org/fsharp/fsharp/branches"><img src="https://camo.githubusercontent.com/f84c15eb8bc65ad99e30a7cf927ebd373db5675c/68747470733a2f2f7472617669732d63692e6f72672f6673686172702f6673686172702e706e673f6272616e63683d6673686172705f3330" alt="Build Status" data-canonical-src="https://travis-ci.org/fsharp/fsharp.png?branch=fsharp_30" style="max-width:100%;"></a>

Much of the F# compiler test suite can now be run on OSX and Linux. This flushed out 
about 5 bugs in Mono, nearly all of which have already been fixed. The test are also
run automatically as part of the Travis build, so if you're contributing, you do not
have to worry about breaking things!

The [F# bindings for Xamarin Studio and Emacs](https://github.com/fsharp/fsharpbinding) 
have had some nice work by Robin and Dave where the identifier parsing code (used for
auto-complete and tool tips) is now shared between both the Emacs addin and Xamarin Studio 
addin. This has resulted in a number of fixes to intellisense.

Although it is still in its early days, there has also been some work on 
[integrating F# with Vim](https://github.com/timrobinson/fsharp-vim) and
[Sublime text](https://github.com/fsharp/fsharpbinding/tree/master/sublimetext). If you're
missing your favorite editor here, be sure to contribute!


Visual Studio F# Power Tools
----------------------------

In the recent few months, amazing work has been done on Visual F# Power Tools.
This is an open source project that aims to bring useful F# Visual Studio extensions 
into a single home. The project has been rapidly developing and currently supports the 
following features:

 * Automatically generating XML documentation comments
 * Formatting document and selection (integrating the [Fantomas](https://github.com/dungpa/fantomas) project) 
 * Navigation features including navigation bar, navigate to command and more
 * Improved syntax highlighting and depth colorization
 * Rename refactoring, automatic implementation of interfaces and more

To get involved with the project:

 * To contribute [see the project on GitHub](https://github.com/fsprojects/VisualFSharpPowerTools)
 * To install [go to Visual Studio gallery](http://visualstudiogallery.msdn.microsoft.com/136b942e-9f2c-4c0b-8bac-86d774189cff)
 * For more information, [see the project documentation](http://fsprojects.github.io/VisualFSharpPowerTools/)
 * To discuss potential features and fix bugs [go to the GitHub issues](https://github.com/fsprojects/VisualFSharpPowerTools/issues?page=1&state=open)


<br />
 
_Published: 27 May 2014_  

_Tomas Petricek_  
_(on behalf of the F# Core Engineering group)_
