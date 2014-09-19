---
layout: default
title: Online meeting notes, 02 July 2013
subtitle: Meeting notes
---

## Online meeting notes, 02 July 2013


After a few glitches, ~10 of us joined the online session today to discuss the goals, remit and activities doc for the CoreEng WG.
 
However at least 3 people couldn’t participate because of some kind of problems with Google+. Still the chat session was lively and we probably got more done than other techniques. Some people had added comments to the doc before the session.
 
Notes from the chat session (we didn’t take a full copy of the chat log):
 
1. People generally like the group remit  doc and it sets the right scope for the group
 
2.Probably the thorniest issue is libraries.
 
  a. Don said he felt the F# community needed to get to the point that it could roll out libraries as good and trusted as FSharp.Core.
 
  b. There was discussion about process. Would FSSF design libraries? Would it “bless” libraries? Rick emphasized that putting out FSSF-blessed libraries is a “recipe for disaster” unless our process, quality and design is good enough.
 
  c. People suggested we draw on experiences from Python, Dojo and .NET library processes.
 
  d. There are a lot of ideas floating around about how the WG and FSSF can facilitate high-quality libraries. See the on-going comment threads in the document for discussion about this.
 
3. We used RProvider and MatlabProvider as two examples of components which are "getting there" but where we need improvement:

  a. better docs
  b. better getting-started material
  c. better feedback (Rick said he’s had no feedback on the Matlab provider).
 
4. Dave pointed out how fsharpbinding really needs more contributors - there's a lot to do
 
5. Don asked for contributors to dev guides on fsharp.org. A draft guide for Mac/Linux/Cross-Platform is up but more work is needed.
 
6. Dave pointed out opportunities re LightTable and wants to integrate F# into that.
 
7. Don sang the praises of Vagrant for using Linux VMs from a Windows machine, and will send around material [see below]
 
8. People said positive things about ExtCore, and perhaps that could be brought more into the center of the picture in the library space
 
9. Don talked about how 60% of F# newcomers come through http://fsharp.org, and that that is the primary place we can inform people about F# and its associated tools.
 
10.  We talked through the RProvider as an example: a GitHub repo page doesn't cut it as a "landing page" for newcomers, e.g. for the RProvider. One solution is to make a github pages page for your project. Also, the RProvider has a nuget package, which wasn’t well known and is not discoverable.
 
11. There was a lot of interest in having FSharp.Charting be cross-platform
 
    a. The plan is to have FSharp.Charting.OxyPlot.* etc. for cross-platform versions of the same design pattern.

    b. Robin is interested in contributing to this.

    c. Sergey asked whether FSharp.Charting could have an RProvider backend.
 
12. We discussed documentation tools for projects.
 
    a. Sample generation.	Don says “ I think FSharp.Formatting is fine - and much, much better than nothing - I think it's what I'd encourage people to use right now. But yes, it would be great to go even further, and draw inspiration from other communities too.“
    
    b. Documentation generation. Don says “we have a hole w.r.t. API doc generation". 	Dave says http://fogus.github.io/marginalia/ does a good job for Clojure. He says FSharp.Formatting almost does this now, see this issue FSharp.Formatting request
 
Action items
 
- @Don says I'll send around a summary to the group.

- @Antonio says “I can help on Windows Dev Guide for fsharp.org”

- @Antonio says Andrea Canciani is willing to contribute (maybe to F# binding?)

- @Don says he’ll send around resources on Vagrant including the standard VagrantFile for a VM that has F# 3.0 and Mono 3.0 installed.

- @Rick says it's possible to make the matlab provider cross-platform, but he needs wrappers for matlab dynamic libraries that will be supported in mono. And he has no clue how to go about it.

- @Robin says I will take “FSharp.Charting Cross Platform” to start.

- @Don will send around a sample of using OxyPlot for cross-platform charting with PDFs

- @Everyone agreed to send Rick feedback on the Matlab type provider (install a trial edition of matlab)

Participating founders (as of July 2013)

- Dave Thomas
- Fabio Galuppo		
- Usher, John (RTIO)	
- Don Syme		
- Carsten König		
- Dmitry Slutsky		
- Michael Newton		
- Andrew Cherry		
- Sergey Tihon		
- Jack Fox
- Antonio Cisternino		
- Richard Minerich		
- Robin Neatherway		
- Steffen Forkmann		
- Anh-Dung Phan		
- Intellifactory		
- Adil Akhter		
- Tao Liu			
- Jonathan Leonard		
