---
layout: default
title: The F# Open Engineering Group
subtitle: How Your Contributions to the F# Language, Compiler and Core Library Are Delivered Cross-Platform
---

<br />

About Us
========

The F# Open Engineering Group is a technical group associated with
[The F# Software Foundation](http://fsharp.org).
We manage the cross-platform and open-source [F# Compiler and Components](https://github.com/fsharp) repositories 
and the [F# Community Project Incubation Space](https://github.com/fsprojects).
The group also helps to facilitate open-source contributions to the F#
Language via the [Visual F# Language and Tools](https://visualfsharp.codeplex.com/) repository.

* [Join our Google Group](http://groups.google.com/group/fsharp-opensource)
* [Discuss F# language directions](http://fslang.uservoice.com)
* [Contribute to the F# compiler and library](http://fsharp.github.io/blog/2014/fsharp-contributions.html)
* [Contribute to other core repositories](http://github.com/fsharp)
* [Contribute to F# community incubation projects](http://github.com/fsprojects)

Please help us in our mission to maintain the excellence of these projects and
to bring F# to your favorite platforms.


<a id="bloglist" > &nbsp; </a>
<br />

Blog
====


<div>
{% for post in site.posts %}

    <br />
    <h2>
      <a href="{{ site.baseurl }}{{ post.url }}">
        {{ post.title }} 
      </a> 
    </h2>

    <blockquote><p> {{ post.subtitle }} 
                    <time datetime="{{ post.date | date: '%Y-%m-%d' }}">({{ post.date | date: "%-d %B %Y" }})</time>                        </p>
    </blockquote>

{% endfor %}

</div>

Contact Us
==========

Most group discussion happens in other forums. To contact the group, please either:

* [Ask questions on StackOverflow](http://stackoverflow.com/tags/f%23/info)
* [Post to our Google Group](http://groups.google.com/group/fsharp-opensource)
* [Propose or discsus an F# language feature](http://fslang.uservoice.com) - please check for duplicates first
* [Contribute an issue to the F# compiler and library](http://fsharp.github.io/blog/2014/fsharp-contributions.html)
* [Contribute an issue to other core repositories](http://github.com/fsharp)
* [Contribute an issue to F# community incubation projects](http://github.com/fsprojects)

If using the Xamarin, Microsoft or Intellifactory professional tooling for F#, you should use
the support contacts for those products.  They may also refer you to the above forums for some issues.

The convenors of the F# Open Engineering Group are Michael Newtown, Dave Thomas, Tomas Petricek and Don Syme.
You can contact them directly, though it is better to use one of the public forums above.
