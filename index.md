---
layout: default
title: The F# Core Engineering Group
---

<br />

## About Us

The F# Core Engineering Group is a technical group associated with
[The F# Software Foundation](http://fsharp.org).
We manage the cross-platform and open-source [F# Compiler and Components](https://github.com/fsharp) repositories. 


The group works cooperatively with major industrial and community contributors to F# to 
facilitate open-source contributions to the F# core components. For example:

* We run the [F# Language Design and Core Library RFC Process](http://fsharp.github.io/2016/09/26/fsharp-rfc-process.html)

* We help maintain the "Use F#" guides such as [Use F# on Windows](http://fsharp.org/use/windows), [Use F# on Linux](http://fsharp.org/use/linux) and [Use F# on OSX](http://fsharp.org/use/osx).

* We work cooperatively with the [Visual F# Tools team](http://blogs.msdn.com/b/fsharpteam) to 
  [facilitate contributions](http://fsharp.github.io/2014/06/18/fsharp-contributions.html) to
  the F# language, compiler and library via the [Visual F# Tools repository](https://github.com/Microsoft/visualfsharp)

* We cooperate with the [Xamarin](http://xamarin.com) team to see F# packaged as part of the Xamarin tools for [OSX](http://fsharp.org/use/mac), 
  [Android](http://fsharp.org/use/android) and [iOS](http://fsharp.org/use/ios).

* We cooperate with the [Mono team](http://www.mono-project.com/) to see F# packaged as part of the standard Mono distributions.

* We cooperate with the [Debian packaging team](http://packages.qa.debian.org/f/fsharp.html) to see F# packaged on Debian.


## Goals and Remit

See  our [Goals and Remit](http://fsharp.github.io/2013/07/09/group-remit.html) document.

## Get Involved 

To get involved:

* [Contribute to the F# compiler and core library](http://fsharp.github.io/2014/06/18/fsharp-contributions.html)

* [Contribute to discussions](https://github.com/fsharp/fsharp.github.io/issues)

* [Contribute to guides and blog entries on this site](https://github.com/fsharp/fsharp.github.io/tree/master/_posts)

* [Contribute to guides on fsharp.org](https://github.com/fsharp/fsfoundation/tree/gh-pages/guides)

* [Contribute to other core repositories](http://github.com/fsharp) and [F# community incubation projects](http://github.com/fsprojects)

* [Join our Google Group](http://groups.google.com/group/fsharp-opensource)

* [Join our private discussion list (low volume)](mailto:fsharp@fsharp.org)

Our mission is to maintain the excellent quality of the core F# implementation across these platforms,
and to extend the tooling available for F# across your favorite platforms.


<a id="bloglist" > &nbsp; </a>
<br />

## Guides and Posts


<div>
{% for post in site.posts %}

    <br />
    <h2>
      <a href="{{ site.baseurl }}{{ post.url }}">
        {{ post.title }} 
      </a> 
    </h2>

    <blockquote><p> {{ post.subtitle }} 
                    <!-- <time datetime="{{ post.date | date: '%Y-%m-%d' }}">({{ post.date | date: "%-d %B %Y" }})</time>   -->
                </p>
    </blockquote>

{% endfor %}

</div>

##  Contact Us

Most group discussion happens in other forums. To contact the group, please either:

* [Ask questions on StackOverflow](http://stackoverflow.com/tags/f%23/info)
* [Post to our Google Group](http://groups.google.com/group/fsharp-opensource)
* [Propose or discuss an F# language feature](http://fslang.uservoice.com) - please check for duplicates first
* [Contribute an issue to the F# compiler and library](http://fsharp.github.io/2014/06/18/fsharp-contributions.html)
* [Contribute an issue to other core repositories](http://github.com/fsharp)
* [Contribute an issue to other F# community incubation projects](http://github.com/fsprojects)

If using the Xamarin, Microsoft or Intellifactory professional tooling for F#, you should use
the support contacts for those products.  They may also refer you to the above forums for some issues.

The convenors of the F# Core Engineering Group are Michael Newtown, Dave Thomas, Tomas Petricek and Don Syme.
You can contact them directly, though it is better to use one of the public forums above.
