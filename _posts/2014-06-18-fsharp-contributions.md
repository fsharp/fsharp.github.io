---
layout: default
title: Contributing to the F# Language and Compiler
subtitle: How Your Contributions to the F# Language, Compiler and Core Library Are Delivered Cross-Platform
---

Contributing to the F# Language and Compiler
============================================

> How Your Contributions to the F# Language, Compiler and Core Library Are Delivered Cross-Platform.

In this post, we outline how we, the [F# Core Engineering Group][fsg] (a technical working group of 
[The F# Software Foundation][fsf]) – are collaborating with the [Visual F# Tools][fst] team at 
Microsoft, Xamarin, Mono and other contributors to F# to help deliver your contributions to the 
F# Language, Compiler and Library across multiple platforms.

In particular, we want to explain more about:

 1.	How you can contribute to the F# Language, Compiler and Core Library 
 2.	Why we ask you to contribute via the [Visual F# Tools][fst] open source repository 
 3.	How your contributions will flow through to other F# tooling worldwide, on many different platforms
 4.	How the core engineering changes may be improved in the future

Background: The F# Core Engineering Group
-------------------------------------

First, some background. The F# Core Engineering group is a technical working group made up of founding members of the 
FSSF, plus other contributors to the core F# compiler and tooling.  Our role is to enable work on 
the core of the F# open source tools, especially in a cross-platform setting. This includes any work on:

 -	The open edition of the [F# compiler](https://github.com/fsharp/fsharp/) and core library 
 -	Central F# open source libraries such as [FSharp.Data](http://fsharp.github.io/FSharp.Data/) and 
    [FSharp.Charting](http://fsharp.github.io/FSharp.Charting/)
 -	Central F# tooling infrastructure such as [FSharp.Compiler.Service](http://fsharp.github.io/FSharp.Compiler.Service/) 
    and [FSharp.Formatting](http://tpetricek.github.io/FSharp.Formatting/)
 -	Central F# tools such as the F# bindings for [MonoDevelop, Xamarin Studio, Emacs](https://github.com/fsharp/fsharpbinding)
    and the [Visual F# Power Tools](http://fsprojects.github.io/VisualFSharpPowerTools/)

You can browse our [core repos](https://github.com/fsharp) and the other 
[community projects](http://fsharp.org/community/projects/). As you can see, there is a huge 
amount going on in F# open source tooling, libraries and related initiatives! We encourage you 
bothto  use and contribute to these initiatives.

That said, there are some things we don't do:

 1.	**We don't package F# for use on Windows.**  This is done by the Visual F# Tools team at Microsoft.  
 2.	**We don't package F# for use on OSX.** This is done by the Mono team at Xamarin, based on tag numbers we send them.
 3.	**We don't package F# for use on Linux.** This is done by the Debian packaging group and other Linux packaging teams.
 4.	**We don't package the F# core library for use on Android or iOS.** This is done by the Xamarin.Android and Xamarin.iOS teams at Xamarin, based on tag numbers we send them.

So, instead of packaging things, our main job is work cooperatively with Microsoft, Xamarin 
and others to ensure F# is made available to users on all major platforms. 
This is a key part of the mission of the F# Software Foundation.

How Your Contributions Flow Cross-Platform 
------------------------------------------

Below, you can see a diagram of how contributions to the F# Language, Compiler and Core Library 
flow through the core F# open source ecosystem (click on the image for a full version):

<a href="/img/repos.png"><img src="/img/repos-small.png" /></a>

Note that:

 1. Contributions to the F# language/compiler/library/tools go first to the Visual F# Tools team 
    Git repository at [github.com/Microsoft/visualfsharp](https://github.com/Microsoft/visualfsharp). 
    
    This is a fully open source, Git-enabled repository that is aligned with the other repositories 
    in the diagram.  The repository is maintained by Microsoft who provide the QA and issue tracking
    for this repository, a valuable service to the F# community.

 2.	These contributions are then integrated into [github.com/fsharp/fsharp](http://github.com/fsharp/fsharp) 
    and [github.com/fsharp/FSharp.Compiler.Service](http://github.com/fsharp/FSharp.Compiler.Service).  
    
    The first of these contains a few small changes for using F# on Linux, Mac and other platforms, as well as some packaging scripts.

    The second of these contains additional changes for turning the F# compiler code into a compiler service component with a clean API.

As shown in the diagram, your contributions will eventually be merged into two important downstream Git-enabled repos, both on github. In particular, your contributions will flow into the 
    [FSharp.Compiler.Service](https://github.com/fsharp/FSharp.Compiler.Service) repo and its 
    [nuget package releases](http://www.nuget.org/packages/FSharp.Compiler.Service/). 

A lot of interesting tooling uses this nuget package, including:

 * the [Visual F# Power Tools](http://fsprojects.github.io/VisualFSharpPowerTools/),
 * the [Xamarin Studio tooling for F#](http://developer.xamarin.com/guides/cross-platform/fsharp/), 
 * [CloudSharper](http://cloudsharper.com/), 
 * [Tsunami](http://tsunami.io/), 
 * the [iPython Notebook](http://nbviewer.ipython.org/github/BayardRock/IfSharp/blob/master/Feature Notebook.ipynb) tooling for F#, 
 * and even the [Emacs MELPA package](https://github.com/fsharp/fsharpbinding/blob/master/emacs/README.md) for F#.

There is a lot of F# tooling in the world – both open source and commercial!  The good news is that 
_all the F# tooling is ultimately derived from one professionally-tested codebase_ – the Visual F# Tools 
curated by the Visual F# team at Microsoft, which you can contribute to.   This helps ensure 
the unity of both the F# language and its main implementations.

Why We Ask You To Contribute via the Visual F# Tools Repo
---------------------------------------------------------

In the above diagram, we ask you to submit your contributions to the F# Language, Compiler and Core 
Library via the Visual F# Tools open source Git-enabled repo.


We do this because:

 1. Contributing to that repo ensures that the main packaging of F# on Windows (the Visual F# Tools) includes your contributions, and ensures that the F# language does not ultimately diverge.

 2. Microsoft provide high-quality code review, automated testing  and general oversight for these contributions

 3. Microsoft are very active in soliciting contributions and helping people to contribute. Follow their twitter account for updates, as well as the RSS feed from the repository.
 
 4. Don Syme, the current [Benevolent Dictator For Life](http://en.wikipedia.org/wiki/Benevolent_dictator_for_life) 
    for the F# Language, works at Microsoft Research and collaborates with the Visual F# Tools team in curating 
    this repository.
 
 5. Microsoft curate and "triage" the open issues list for the F# Compiler, Language and Core Library, based on bug reports they receive.

Overall, Microsoft make huge contributions to F# and the goals of the F# Software Foundation 
through providing and curating this repository.  Because of this, we embrace what they are doing 
and ask you to work with them when making your own contributions.

Where To Contribute
-------------------

If you are using Windows and would like to contribute to the F# Language, Compiler or Core 
Library, you should fork [github.com/Microsoft/visualfsharp](https://github.com/Microsoft/visualfsharp) and contribute directly there. 

At the time of writing, the Visual F# Tools team repository is not yet a cross-platform repository.  
If you are using Linux or Mac and would like to contribute to the F# Language, Compiler or Core Library, 
please follow the instructions at [github.com/fsharp/fsharp](http://github.com/fsharp/fsharp).  

Looking Ahead
-------------

In the future, there are several ways we and the other teams involved are thinking of adjusting our processes:

 1. The Visual F# Tools Team at Microsoft are interested in incorporating the FSharp.Compiler.Services project into the F# Compiler codebase itself.  This would take quite a lot of work but would put the compiler services at the heart of nearly all F# tooling.

 2. The Visual F# Tools Team at Microsoft interested in having the Visual Studio tooling for F# being more closely aligned, either by occasionally integrating drops of the Visual F# Power Tools into the Visual F# Tools, or through other means,

 3. At the time of writing, the Visual F# Tools team repository is not yet a cross-platform repository.   However they are now accepting cross-platform contributions and hopefully they will eventually support cross-platform development too. 

 4. We have suggested to the Visual F# Tools team that they move the F# compiler and language across to the open edition repo on GitHub (but leave the F# Visual Studio-specific Tools in 'visualfsharp'), but they are not yet in a position to do this.

Summary
-------

The F# Tooling ecosystem is full of energy and activity.  Two major commercial contributors are Microsoft and Xamarin, and tools are also available in 100% open source through MonoDevelop, Emacs and others.  We encourage you to contribute to the F# Compiler, Language and Core Library via the Visual F# Tools repository, and also to contribute to the many other great projects in the F# community ecosystem.  We also encourage you to join our group and help us coordinate and grow the F# tooling ecosystem.

Tomas Petricek, for the F# Core Engineering Group, with assistance from Don Syme



<br />
 
_Published: 27 May 2014_  

_Tomas Petricek_  
_(on behalf of the F# Core Engineering group)_

 [fsg]: http://fsharp.github.io/
 [fsf]: http://fsharp.org
 [fst]: http://blogs.msdn.com/b/fsharpteam
