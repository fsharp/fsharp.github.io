---
layout: default
title: Recommended Guidelines for F# Projects, Packages and Namespaces
subtitle: Naming, Engineering and General Advice
---

Recommended Guidelines for F# Projects, Packages and Namespaces (DRAFT, not yet [published](http://fsharp.github.io)) 
============================================

> Naming, Engineering and General Advice


One of the goals of the F# Open Engineering group is to help ensure best engineering practices in public-facing F# components and packages.

In this post we outline some current recommended guidelines for naming and engineering for F# projects, packages and namespaces.
We particularly focus on open projects such as those in the [F# Community Projects](http://fsharp.org/community/projects/) list or
[packages in Nuget for an "FSharp" search](http://www.nuget.org/packages?q=fsharp).

These are recommendations we make as a working group based on our remit as part of the F# Software Foundation. 
They are not hard-and-fast rules, and are offered for discussion amongst
F# developers. If you would like to discuss these guidelines or suggest modifications, 
please [submit a pull request or issue to GitHub](https://github.com/fsharp/fsharp.github.io/tree/master/_posts).

First, as a group we recognize that:

1. We recognize that FSharp.* projects should be high-quality or trending rapidly in that direction.
   Any packages and code under the FSharp.* namespace should be of sufficient quality to be considered 
   ready for production use by people in the wider F# community. 

2. We recognize the need to avoid pollution of the FSharp.* namespace, particularly by unfinished projects.

3. We recognize the need to encourage incubation and experimentation using FSharp.* project, package and namespace names. 
   Important F# components such as [FSharp.Data](http://fsharp.github.io/FSharp.Data/) have developed in this way.

4. We recognize that packages and projects using FSharp.* naming should generally be cross-platform,
   unless explicitly qualified by some platform-specific moniker (e.g. "Windows", "Android", "Gtk" or "Linux").
   Code in the FSharp.* namespace should build, run and pass tests across multiple platforms.
   
5. We want to continue to encourage and scale the positive and productive spirit with which the F# community operates by sharing
   information about how to make successful, long-lasting and broad-reach components in a collaborative way.

In light of these, we cover some specific recommendedations with regard to naming, quality and general advice below.
They are written to augment the existing [F# Component Design Guidelines](http://fsharp.org/specs/component-design-guidelines/).
Some of these recommendations may eventually be added to those guidelines.

<br />

## Naming Guidelines

<br />

### Guideline: Do make the purpose of your project or package clear in its name

When starting a project, it can be all-to-easy to choose a name that is too general, for 
example "FSharp.Helpers".  As you clarify what your project is about, make sure you adjust the name of your project 
accordingly.


For example, a project such as "FSharp.Cloud" is unclear in purpose. Is this for uploading music to Apple's iCloud? Or for
Amazon AWS?  Or Microsoft Azure? Instead, use a name such as "FSharp.Azure.Scripting" or "FSharp.Amazon.Scripting" if your
package is in fact a scripting library for a particularly cloud infrastructure provide.

Alternatively, you may end up dropping the use of an "FSharp" name for your project or package. see below, and choosing
a more product-like name.

<br />

### Guideline: Consider an "FSharp.*" name for things intended primarily for F# developers

Many libraries, package or tools are designed explicitly for the use of F# programmers.
These may be valid candidates for an "FSharp.*" name, if other criteria are met. 

For example, "FSharp.Data" or "FSharp.Data.SqlCommandProvider" are in this category.

<br />

### Guideline: Avoid an "FSharp.*" name for things not intended exclusively for F# developers

Some projects, packages and tools use F# as an implementation or scripting language but are intended for broader use.
If your audience is larger than the worldwide community of F# developers, then strongly consider using a
name which doesn't mention F#.  

For example, 

- [FAKE](http://fsharp.github.io/FAKE/) is a general build tool which happens to use F# as a scripting language. It has achieved
  widespread adoption, partly because the name is well chosen to appeal to a broad audience.

- [Deedle](http://bluemountaincapital.github.io/Deedle/) package
  is branded as "An exploratory data library for .NET" and includes a C#-facing API (implemented in F#). 
  It was successfully renamed from "FSharp.Data.DataFrame" to broaden its reach, and at the time of writinng [a Google
  search for "C# DataFrame"](http://www.google.co.uk/#q=c%23+dataframe) brings Deedle as the first hit.

Likewise, some projects are product-like or are actual products, often with reach beyond the worldwide community of F# developers
or enabling new development experiences. These should normally use a name that doesn't begin with "FSharp", though may mention F#
if useful.  Examples are [MathDotNet](http://www.mathdotnet.com/), 
[Akka.NET](http://akkadotnet.github.io/), 
[Alea.cuBase](https://www.quantalea.net/products/introduction/), 
[WebSharper](http://websharper.com/), 
[CloudSharper](http://cloudsharper.com/) and
[FCell](http://fcell.io/). Some of these are F#-specific, some are general .NET tools with an F# angle, some are products, some
are open source, but all benefit from their own branding.


<br />

### Guideline: Avoid using two word "FSharp.XYZ" names for projects and packages 

We recommend that new public-facing projects do not use a two-word qualified FSharp.* name except in extremely rare circumstances.

For example, projects with names such as 
FSharp.Numerics, FSharp.Distribution, FSharp.BigData or FSharp.Web are named too broadly. We are uncomfortable with seeing 
anyone use unqualified two-word FSharp.* names for projects except in rare circumstances such as FSharp.Data.

If your project already uses such a name, then strongly consider renaming it.  One exception is [FSharp.Data](http://fsharp.github.io/FSharp.Data/)
which is sufficiently broad and canonical to use a two-word name.

<br />

### Guideline: Do use existing second-level namespaces where appropriate

.NET and F# components are placed in second-level namespaces such as "FSharp.Control", "FSharp.Data", "FSharp.Net", and so on.  
Use the following prefixes where possible, rather than inventing new ones:

-   FSharp.Control: Functionality related to control flow, such as asynchronous programming, message passing, 
event-based programming, reactive programming, and similar
-   FSharp.Data: Types related to data access, data schema, and similar
-   FSharp.Text: Text processing, formating, printing, or similar functionality
-   FSharp.Net: Types relating to networking
-   FSharp.Compiler: Functionality relating to compilation of F#
-   FSharp.Core: Use sparingly.  Typically required for help types required by incubation of compiler features

For example, a library like "FSharp.Actor" might be better renamed to "FSharp.Control.Actor". Similarly, "FSharp.Reactive"
is better renamed to "FSharp.Control.Reactive".  Likewise, a type provider 
for a data source or schema format XYZ should normally be placed in "FSharp.Data", e.g. "FSharp.Data.XYZ".

<br />

### Guideline: Consider using "Experimental" in project/package names in early stages

We encourage incubation of candidates for the FSharp.* namespace.  One way to do this is to allow some moderation, e.g. allow free use of names like

-	FSharp.Data.Experimental.XYZ
-	FSharp.Linq.Experimental.XYZ
-	FSharp.Core.Experimental.XYZ

The use of "Experimental" is is useful and recommended in early stages.  Renaming projects and packages is fairly well
supported by GitHub and existing package managers, combined with global-search-and-replace.


<br />

### Guideline: Clearly label project status prior to release

Projects, packages and tools using the FSharp.* top level namespace should clearly and prominently label their status 
in all documentation during the project's early phases of development. Projects should use version numbers below 1.0 
to denote pre-release status, and clearly label their status as "Alpha" when the project is still lacking features 
or functionality required for widespread usage, and "Beta" prior to adequate usage and testing to be considered a mature package.

<br />

### Guideline: Do seek review of your project, package and namespace names

The F# community love to help and give good advice.  Seek advice via the [#fsharp tag on Twitter](https://twitter.com/hashtag/fsharp?src=hash)
or other community forums.

<br />

## Engineering Guidelines

<br />

### Guideline: Apply good software engineering practice before publicizing your project

Before you publicize an open, public-facing project, it pays to get all the basics in place to allow
people to collaborate with you.  These are, minimially:  

- *Naming*.  Get the naming of your project right. Make its purpose clear. 

- *README* and *Road Map*.  Add a clear, simple README to your project. Make its purpose clear. Add a road map too.

- *Packaging*.  Make a package for your library, component or tool. often this will be a nuget package, 
  but if it is some other kind of tool then make and publish the appropriate package. For example,
  the [Emacs mode for F#](http://fsharp.github.io/fsharpbinding/) is published as a MELPA package.
  Document how the package gets published.

- *Testing*.  Ensure your project has tests that build, run and pass out-of-the-box.

- *Documentation*.  Ensure your project has good documentation. If on GitHub, consider publishing your 
  documentation via GitHub pages. Strongly consider using documentation generation tools and templates used by the [F# Project Scaffolding](https://github.com/fsprojects/ProjectScaffold)

- *Tutorials*.  Ensure your project has tutorials as part of its documentation. Templates and examples are available in the [F# Project Scaffolding](https://github.com/fsprojects/ProjectScaffold)

- *Vidoes*.  If you've given a talk on your project, add a link to the video. It's a great way to introduce yourself to potential contributors.

- *Continuous Integration*.  Add continuous integration build and testing to your project, for example using AppVeyor or Travis.  For AppVeyor, this is done by addding `appveyor.yml` ([example](https://github.com/fsprojects/ProjectScaffold/blob/master/appveyor.yml)) and 
  registering your project.  For Travis, you add a `.travis.yml` ([example](https://github.com/fsprojects/ProjectScaffold/blob/master/.travis.yml)) and register the project.
  Travis provides OSX and Linux machines - OSX machines are used if the language is set to "objective-c".

- *Reach a Known State*.  Document all known issues.  Consider labelling somme of them as "up-for-grabs". Don't leave undocumented minefields in your package or tool.

- *Cross-platform*.  If working on Windows, ensure your project uses a cross-platform build-and-test using Travis if possible.

- *PCL if possible*.  If writing a library, make your component a PCL portable component where possible, targeting the broadest possible profile such as "Profile 7" or "Profile 259".


If you don't do these things, people are unlikely to want to collaborate with you on your project.


<br />

### Guideline: Consider using the [F# Project Scaffolding](https://github.com/fsprojects/ProjectScaffold) 

The [F# Project Scaffolding](https://github.com/fsprojects/ProjectScaffold) gives a structure for creating new projects
which can be very useful for documentation, packaging, testing, tutorials, CI and more.

Please contribute fixes and improvement to the scaffolding to make it easier for others to use.

<br />

### Guideline: Evolving code and new features should be carried out on a feature branch, and merged in only when ready

Do not develop features in your "master" branch or published nuget packages.  Instead, use a feature branch, or a fork,
or a new project, or a component marked Experimental (or similar).

<br />

## General Guidelines

<br />

### Guideline: Consider putting open incubation projects in [The F# Community Incubation Space](http://github.com/fsprojects)

The F# Open Engineering group manages [The F# Community Incubation Space](http://github.com/fsprojects) for community
incubation projects.  To get your project added or removed from this space, [add an issue to the admin section](https://github.com/fsprojects/FsProjectsAdmin/) 
of this space.

<br />

### Guideline: Do recruit additional contributors and maintainers of your project.

Please contribute fixes and improvement to the project scaffolding to make it easier for others to use.  If you add your
project to [The F# Community Incubation Space](http://github.com/fsprojects) then the owners of tha space will 
also automatically be able to perform some maintenance tasks on your repository, including accepting pull requests
or curating issues.


<br />

### Guideline: Do use a nice logo for your package or tool.

Many F# community projects include nice logos.  If necessary, ask for help to design a new logo in a matching style.
For example, see the [FSharp.Data](https://twitter.com/FSharpData)  library.

<br />

### Guideline: Do add your project to the [F# Community Projects](http://fsharp.org/community/projects/) list

We recommend you consider adding your project the [F# Community Projects](http://fsharp.org/community/projects/) list. You can
do this by submitting a pull request on GitHub as described on the page.

<br />

### Guideline: Do consider creating a Twitter account for your package or tool

A Twitter account for a project can increase its visibility, especially in early stages, and set it
on a track to be an independent, long-lived technology with multiple contributors.  For
example, see the [FSharp.Data](https://twitter.com/FSharpData) or [F# for Open Editors](https://twitter.com/fsharpbinding) 
on Twitter.

If you tweet, use the [#fsharp tag on Twitter](https://twitter.com/hashtag/fsharp?src=hash).

<br />

### Guideline: Consider making your project "independent" of you or your company

Projects, packages and tools are often branded in a way to make them less directly dependent on a particular person or company,
relying on people who fill roles rather than being permanently tied to an individual. 

For example, the [Akka.NET](http://akkadotnet.github.io/) project started life as a project called "Pigeon" in 
the GitHub account of one major contributor.  It is now branded as an "independent" project. 

Branding your project as independent (or indirectly-dependent) can increase
confidence, help attract additional contributors, give new opportunities (for example to start a consulting company
around the technology), and above all can help with transitions as people move on and off the project.  Effectively,
a layer of indirection is placed between the projects and the contributors.  Behind the scenes, the same
contributors may be involved, especially initially, but the long term advantages are very large.

This approach is frequently used for open source packages and tools initially started by individuals. 
Note that detaching yourself from your project in this way can be both challenging and liberating.

This approach can also be used for products or open-source projects where companies are major contributors. 
This can be a difficult choice: the company may (or may not) want its name attached to the open-source project in a strong way.
For example, [Deedle](http://bluemountaincapital.github.io/Deedle/) is under the [BlueMountain Capital](http://bluemountaincapital.github.io/) 
GitHub account.


<br />
<br />
<br />

### About Us

The F# Open Engineering Group is a technical group associated with [The F# Software Foundation](http://fsharp.org).

We manage the cross-platform and open-source [F# Compiler and Components](https://github.com/fsharp) repositories 
and the [F# Community Project Incubation Space](https://github.com/fsprojects).

Visit our [website](http://fsharp.github.io) and please continue all the great work on core F# engineering!

<br />
 
_First Published: 19 September 2014_  

_The F# Open Engineering group_
