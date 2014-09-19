---
layout: default
title: Online meeting notes, 18 September 2014
subtitle: Meeting notes
---

## Online meeting notes, 18 September 2014


Attending: About 14 people, including Michael Newton (host), Don Syme, Steve Taylor, Anh-Dung Phan, Jack Fox and others from Tachyus, Mathias Brandewinder.

We used a Google Hangout on Air for the meeting.  It took about 30min to get going due to technical glitches.

Don gave a summary of some of the great things that have been happening over the last year. We talked about the mission statement for the group which everyone seemed happy with.  Don later updated the mission statement to include a mention of “best-practice engineering” based on discussion in the group about F# community projects, packages and quality.

We then walked through the sections of the existing remit doc and chatted about the state of play in each area.


### Compiler, Core Library, Compiler Service

Current pain points:

- [#r is not consistent on Mono/Linux/OSX](https://github.com/fsharp/fsharp/issues/251)

- [fslang.uservoice.com](http://fslang.uservoice.com) is not a great site for making langauge/library suggestions

- There are a [too many places to make suggestions and report issues](http://fsharp.org/guides/engineering/issues)

### Package Management

Current pain points:

- nuget is not distributed and available on Linux

- nuget has a painful command line experience for NuGet

- nuget is too tied to visual studio (the IDE tooling modified project files etc.) 

- the F# REPL doesn’t have “get package” experience

### Continuous Integration

Current positives:

- Travis being widely used, is taking the community in the right direction

Current pain points:

- Tricky to set up parallel builds in Travis

- Need a blog post on “how to do it” 

### Libraries and Frameworks

Current pain points:

- need precompiled/blessed packages that become a standard across a platform (cuts both ways - “blessed” packages get out-of-date)

- Immutable.Collections.FSharp is needed?

- Quality of some FSharp.* packages on nuget is poor and/or experimental

- There are too many packages usng the FSharp.* name, especially two-word package names like FSharp.XYZ

### Documentation

Pain points:

- FSharp.Formatting needs more robust error handling (throw in the compiler services doesn’t give good error reporting)

### Interoperability

We agreed that “language interop” type providers like a Python type provider are out of scope for the group.  The R Provider, for example, is covered mostly by the data science working group.


### Containers and VM Images

A new section was added on the “Containers and VM Images”, based on the suggestion that we could make available pre-baked Docker and Vagrant containers and/or VM images.

### Distributed Compute Frameworks

A new small section was added mentioning Distributed Compute Frameworks after the suggestion that the group should keep an eye on this space.  Don gave a brief recap of Akka.NET, Orleans, MBrace and Dryad/Naiad.

