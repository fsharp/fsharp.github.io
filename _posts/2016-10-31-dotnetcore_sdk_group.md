---
layout: default
title: Developer group about F# in .NET Core and .NET Core SDK
subtitle: Group focused to improve the F# story on .NET Core and integration in the .NET Core SDK
---

# Towards a .NET Core Development Group
{:.no_toc}

We propose to have a group focused to improve the F# story on .NET Core and integration 
in the .NET Core SDK.

Add yourself in https://goo.gl/7esjsF or join #dotnetcore channel in FSF slack for info.


## Summary
{:.no_toc}

The goal is to improve the experience for:

- New users
- Existing projects in ecosystem who want to add .NET Core support

Because the goal is about the whole experience, lots of different areas/projects are interested.

It's not about using .NET Core, but about contributing to so F# will be awesome to use on the .NET Core and .NET Core SDK.

## Want to help?
{:.no_toc}

Everyone can start contribute by him/herself already, everything is open source.

But the topic is new and there were also various changes in the previous months (netstandard/project.json),
so it's a bit difficult to get the current status, where start, where is going.

To help new contributors, we'll do some quick session focused on contribuing about:

- Foundamentals:
  - Topics like .net core/coreclr/netstandard/msbuild/sdk/ide
  - How the sdk and f# integration works, from zero to final executable/package

- Overview of areas who require help
  - Like compiler/ide/tooling/docs/testing/libraries/etc
  - Everyone has their own agenda and definition of fun

- Quickstart how to hack dotnet sdk about f# integration
  - from clone repo -> hack -> check if works.
  - Same for core project too

- Weekly chat about progress

## Features and roadmap
{:.no_toc}

This continue the works done in the past.

For example .NET Core Sdk preview2 already support F# ootb for most 
use cases, and some projects in ecosystem already added .NET Core (or .NET Standard ) support.

But working <> awesome to use.

The lists are just to give an idea of the todo.
Some features are already implemented, some not, some are partial.

- `Basics` list of features needed for basic usage.
- `Additionals` are nice to have, some already works in preview2 (like docker)

### Basics
{:.no_toc}

- Support for .NET Core SDK preview3 (next version)
- MSBUILD based, fsproj
  - Sdk+compiler from nuget package
- Supported OS: same as .NET Core Sdk
- Must be possibile to use normal workflow from sdk
  - restore/build/run/pack/publish/test
  - Support for multi framework targeting (crossgen)
- Should be possibile to use it in parallel to old project system
- Works and helps the ecosystem
  - Paket
  - FAKE
  - Fable
  - Ionide
- IDE Support: VS Code/FSAC/VS
  - Intellisense
  - Debugging
  - Manually edit of project file works
- Documentation, for example
  - Migration from old-fsproj (maybe with a tool too)
  - From zero to standalone console app in ide
  - From zero to library as nuget package (multiple frameworks)
- CI, travis/appveyor
- Should support other frameworks/profiles: Mono, .NET, PCL
- Help community projects already supporting preview2, like
  - Suave
  - Argu
- F# Interactive (fsi)
- Templates
  - normal use case
  - integrated in expected templates generators
    - dotnet new
    - Forge
    - ProjectScaffolding
- Single FSharp.Core package with multiple target frameworks.

### Additionals
{:.no_toc}

- As normal as possibile, to easier interoperate with C# on same solution
  - Reference C# project
  - Be referenced by C# project
- CI testing of different os
- Use the new features of the sdk where useful
  - like sdk tools to remove the need of bootstrapping (`dotnet reveal`)
- Docker examples (both linux and windows)
- Mono, Xamarin, IOS, etc
- List f# project who support or doesnt support netstandard1.6
- Type Providers: require `netstandard2.0`
