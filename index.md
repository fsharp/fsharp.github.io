---
layout: default
title: The F# Open Engineering Group
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

<br />

[Contributing to the F# Langauge and Compiler](blog/2014/fsharp-contributions.html)
-----------------------------------------------------------------------------------

> How Your Contributions to the F# Language, Compiler and Core Library Are Delivered Cross-Platform.

<br />
 
[Recent F# Open Engineering Highlights](blog/2014/may-highlights.html)
----------------------------------------------------------------------

> Updates from the F# Open Engineering Group

<br />
 

<section class="archive">
{% for post in site.posts %}

    <h2>
      <a href="{{ site.baseurl }}{{ post.url }}">
        {{ post.title }} 
      </a> 
      <time datetime="{{ post.date | date: '%Y-%m-%d' }}">{{ post.date | date: "%-d %B" }}</time> 
    </h2>

    <blockquote><p> subtitle {{ post.subtitle }} </p></blockquote>
    

{% endfor %}
</section>
