---
layout: default
title: Core Engineering online meeting notes, 19 November 2018
subtitle: Meeting notes
---

## Core Engineering online meeting notes, 19 November 2018

This online session 18 people joined to discuss the term "Core Engineering".

Questions could be posted during the meeting with prefix "QUESTION:" and as it was an open session also comments could be added with the "COMMENT:" prefix.

The discussion started with a reference to the [google-document](https://docs.google.com/document/d/1wAAR0v1dglXXQThqaMN_iNHi5xSH1boAlP3q-_4kSWI/edit?usp=sharing) to explain the reason why the discussion is happening. Afterwards Don Syme stated the goals for the meeting being to come up with a list of actionable improvements that the community can make to ease development in the core F# repositories and document those in the github repository Microsoft/visualfsharp or elsewhere.

The particular proposed solutions / taks named Proposed Core Engineering Task (PCET) can be found at the end of the document.

The list of questions and ideas of the google document where worked through in the whole meeting starting with:
- Discussions about features take months up to years without decision.
- Some features with many votes are open since years and no decision has been taken on them too
- Some RFC’s for accepted features are such incomplete that it’s not possible to start implementing them
- Expected timeframe for an accepted feature with started implementation isn’t available

Don Syme states he's fine with the current approach of handling language suggestions speaking of not closing suggestions rather instead labeling suggestions with "proably-not". However he agrees that the RFC list needs some love and should be cleaned of such which may be better closed and or rebooted. To address this issue the PCET 1 was created where importantly a RFC may be rebooted if it doesn't get a comment or change within a couple of months. Regular meetings to clean up RFC's have been disagreed to aswell voting about whether a RFC should be closed, the process itself prefered be asnychronous only.

Besides he announced that the office hours will now be regulary hold every monday 5 pm GMT either by himself or other volunteers.

To the state of language suggestions which have been open a long time with many votes Don Syme tells that for particular suggestions (in the discussion Type Classes) a decision won't be taken in near future but people may still provide implementations.

Afterwards the current situation that Don Syme is only deciding whether a language suggestion is accepted took place with the result that in future there won't be a different process to the current including the refreshed statement that Don Syme is the official F# Language Design Lead supported by the FSSF and Microsoft. Carefully also was added that the FSSF in principle is allowed to change this situation through voting however this scenario appears very unlikely. But Don Syme would like to have in future a more active oversight of the design process considering to report every 6 months to the FSSF board.

Furthermore a discussion takes place about whether the design governance or process increase the difficulty of working in the compiler (with different opinions).

For language design certain statements are now fully official:
- The decision on TCs is clear: we won't put a fully-fledged version of TCs into F# while there is any chance that the corresponding feature will be put into C# (quote Don Syme)
- we will seek to evolve the SRTP mecahnism to accurately support some TC-like scenarios but in a fairly conservative way (quote Don Syme)

Further question points which have been adressed afterwards are:
- separation of compiler and tools into different repositories
- democratic voted group of “core compiler engineers” to the so called “Compiler Group”
- regularly timed open chat / discussion with the “Compiler Group” about feature implementation details + compiler architecture design
- List of people with their knowledge in the compiler
- Ask people that have a deep knowledge of f# compiler what needs to be done. Looking at github a first list of people to ask could be: Kevin Ransom, Don Syme, Steffen Forkmann, Vasily Kirichenko
- Provide a beta channel promoted by Microsoft which allows to easily download a custom F# compiler version with a specific feature in beta version

Don Syme agrees on that the various F# compiler repositories should be merged in the future to less confuse people. Phillip Carter added that they are still working on improving the infrastructure of the visualfsharp repository to improve CI times.

Additionaly Don Syme expressed again, that he's much in favour of regular timed compiler related discussions either held by him or a group of volunteers.

Regarding Beta Channels Don Syme gave over to Phillip Carter who tells that the Infrastructure for the .NET SDK isn't ready for such yet. No further particular decision or statement was given for this proposal.

After 1 1/2 hours the time for the meeting was up and the discussion ended.

### Participating members:

- Don Syme
- Jindřich Ivánek
- Krzysztof Cieslak
- Roman Sachse
- Toby Shaw
- Isaac Abraham
- Phillip Carter
- Chet Husk
- Mark Lambert
- Bjørn Bæverfjord
- robert kuzelj
- Dave Thomas
- Oskar Gewalli
- Thomas Boby
- Bryant Huang
- Gerard
- Elliott V. Brown
- Diego Esmerio
- Victor Peter Rouven Müller

### Proposed core engineering tasks:
- Scrub the active RFC list and propose to archive those that haven't seen action for 6-12 months
- Label all suggestions with current status: (“Probably not”, “accepted in principle”, etc.)
- make sure the current status around type classes is accurately documented on the corresponding language suggestions
- The FSSF board require a report from the F# design lead (Don Syme) every 6 months on progress with the F# Language Design Process, and at the same time collect feedback from the community.
- combine all build and automated testing in fsharp/fsharp into Microsoft/visualfsharp including the automatic build of the FSHarp.Compiler.TOols package
- automate the integration of Microsoft/visualfsharp --> fsharp/fsharp so the latter is just a mirror of the former
- move all build, automated testing and issues from fsharp/FSharp.Compiler.Service into Microsoft/visualfsharp and either deprecate the former or set up an automatic mirror
- ncave moves his PR for the fable version of F# compiler from fsharp/FSharp.COmpiler.Service to Microsoft/visualfsharp repo
- regular Monday 5pm BST F# Compiler Office Hours, initially led by Don but hopefully one or two additional people when he's not available
