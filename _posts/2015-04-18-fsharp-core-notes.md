---
layout: default
title: Notes on FSharp.Core
---

# Notes and Guidelines on FSharp.Core

This technical guide discusses the FSharp.Core library.  Please help improve this guide by [editing it and submitting a pull-request](https://github.com/fsharp/fsharp.github.io/blob/master/_posts/2015-04-18-fsharp-core-notes.md). 

Some highlights:

* [Do not bundle FSharp.Core with a library](#do-not-bundle-fsharp.core-with-a-library)
* [Do bundle FSharp.Core with an application](#do-deploy-fsharp.core-as-part-of-your-application)
* [FSharp.Core is binary compatible](#fsharp.core-is-binary-compatible)
* [FSharp.Core version numbers](#fsharp.core-version-numbers)
* [C# projects referencing F# projects need an FSharp.Core reference](#a-c#-project-referencing-an-f#-dll-or-nuget-package-may-need-to-also-have-a-reference-to-fsharp.core.dll)


### Application v. Library v. Script

Each project is either an *application* or a *library*.   Examples of applications are:

* Application: a `.exe` project or a `.dll` project that is a test project, addin, website, or an app.

* Libraries are just ordinary `.dll` commponents (excluding those above which are applications). 

* Scripts are just `.fsx` files, possibly referring to other files using `#load`.

### Do *not* bundle FSharp.Core with a library 

Do _not_ include a copy of FSharp.Core with your library or package.  If you do, you will create havoc for users of
your library.

The decision about which `FSharp.Core` a library binds to is up to the application hosting of the library.
The library and/or library package can place constraints on this, but it doesn't decide it.

Especially, do _not_ include FSharp.Core in the ``lib`` folder of a nuget package.


### Do deploy FSharp.Core as part of your application

For applications, FSharp.Core is normally part of the application itself (so-called "xcopy deploy" of FSharp.Core).  

To achieve this, you normally use ``<Private>true</Private>`` in your project file. In  Visual Studio this is equivalent to setting the `CopyLocal` property to `true` properties for the `FSharp.Core` reference.

FSharp.Core.dll will normally appear in the `bin` output folder for your application. For example:

```
    Directory of ...\ConsoleApplication3\bin\Debug
    
    18/04/2015  13:20             5,632 ConsoleApplication3.exe
    18/04/2015  13:20               187 ConsoleApplication3.exe.config
    14/10/2014  12:12         1,400,472 FSharp.Core.dll
```

### Do *not* assume FSharp.Core is in the GAC

In compiled applications, you should never assume that 
FSharp.Core is in the GAC ("Global Assembly Cache").  Instead, you should 
deploy the appropriate FSharp.Core as part of your application.  

### Do not assume any specific version of FSharp.Core is in the GAC, even if it is on your machine

Once again, do not rely on FSharp.Core being in the GAC.  For F# executable components (see above), 
use `Private=True` for your executable components.

On some installations of F#, some versions of FSharp.Core are added to the GAC.  Do not rely on these
being present in production code being deployed off your machine.

There _are_ some exceptions to this.

* Standard installations of F# tools on Linux and Mac on  Mono also install the latest FSharp.Core into the
  GAC. THey also add machine-wide binding redirects for that component. That means that, for those machines, the latest innstalled FSharp.Core will be used by applications.
  
* Some installations of F# on Windows Visual Studio do install FSharp.Core into the GAC. 

  - VS2010 installs FSharp.Core 4.0.0.0 only
  - VS2012 installs FSharp.Core 4.3.0.0 only
  - VS2013 installs FSharp.Core 4.3.1.0 only
  - VS2015 installs FSharp.Core 4.4.0.0 only

  If you can assume a particular version of Visual Studio (e.g. in a dev/test situation), then you may
  be able to assume FSharp.Core is in the GAC.  

But it is best to avoid this assumption and instead make sure an appropriate FSharp.Core
is deployed as part of your application.  To do this, use ``<Private>true</Private>`` for the FSharp.Core reference
in your project file (see below). In the Visual Studio IDE this is equivalent to setting the `CopyLocal` property to `true`  properties for the `FSharp.Core`  eference.

### FSharp.Core is binary compatible

FSharp.Core is binary compatible across versions of the F# language. For example, FSharp.Core `4.0.0.0` (F# 2.0) is binary compatible with  `4.3.0.0` (F# 3.0), `4.3.1.0` (F# 3.1), `4.4.0.0` (F# 4.0) and so on.

Likewise, FSharp.Core is binary compatibile from "portable" profiles to actual runtime implementations. For example, FSharp.Core  `3.78.3.1` (a portable profiles for F# 3.1, see the table below) is binary compatible
with the runtime implementation assembly `4.3.1.0` (F# 3.1) and `4.4.0.0` (F# 4.0).

Binary compatibility means that a component built for X can instead bind to Y at runtime.
It doesn't mean that Y behaves the same as X (some bug fixes may have been made, and Y may have more functionality than X).

### Libraries target lower versions of FSharp.Core

F# ecosystem libraries should generally target the *lowest language version* and the *earliest, most portable* version of FSharp.Core feasible.

If your library is part of an ecosystem, it should target the lowest version of FSharp.Core that
is necessary for the functionality of the library.  For example, consider targeting portable profile 259 (which can be used on many platforms), or `4.3.0.0` or `4.3.1.0`.  

This will make your library usable in more situations by more people, and you will get fewer requests to 
recompile your library for older platforms, and you will be happier.

Sometimes you will have to choose between targeting the *earliest* version of F# possible, and the *most portable* profile. 
For example, profile 259 only became available for F# 3.1.  In this case you just have to 
either target F# 3.0 (and a less portable profile such as 47) *or* target F# 3.1 (and the highly portable profile 259).


### Applications target higher versions of FSHarp.Core

F# applications should generally use the *highest* language version and the most *platform-specific* version of FSharp.Core.

Generally, when wrtiting an application, you want to use the highest version of FSharp.Core available for the platform
you are targeting.

If your application in being developed by people using multipl versions of F# tooling (commmon
in open source working) you may need to target a lower version of the language and a correspondingly earlier version
of FSharp.Core.

For personal libraries, or libraries that are effectively part of an application, the choice is yours.


### Use Binding Redirects for Applications

If applications use library components that reference an earlier FSharp.Core, then they may need binding redirects
to specify that those libraries should bind to the actual FSharp.Core used as part of the application.

An `app.config` is used to specify binding redirects. It is normal to redirect all lower versions of FSharp.Core to 
the version actually being used.  For example, to redirect all versions of FSharp.Core up to 4.3.0.0 to use 4.3.1.0:

```
        <dependentAssembly>
          <assemblyIdentity name="FSharp.Core" publicKeyToken="b03f5f7f11d50a3a" culture="neutral" />
          <bindingRedirect oldVersion="0.0.0.0-4.3.0.0" newVersion="4.3.1.0" />
        </dependentAssembly>
```

This is located in a section of the app.config like this:

```
    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
        ...
        <runtime>
          <assemblyBinding xmlns="urn:schemas-microsoft-com:asm.v1">
          ...
          </assemblyBinding>
        </runtime>
    </configuration>
```

Application project files should normally also specify `AutoGenerateBindingRedirects` which allows tooling to 
help keep the binding redirects up-to-date, see below.

### FSharp.Core in F# Interactive

F# Interactive (`fsi.exe`) always references and uses the FSharp.Core of the corresponding toolchain, as follows:

- F# 3.0 --> 4.3.0.0
- F# 3.1 --> 4.3.1.0
- F# 4.0 --> 4.4.4.0

F# Interactive can load PCL assemblies that reference compatible FSharp.Core

### FSharp.Core and static linking

The ILMerge tool and the F# compiler both allow static linking of assemblies including static linking of FSharp.Core.
This can be useful to build a single standalone file for a tool.

However, these options must be used with caution. 

* Only use this option for applications, not libraries. If it's not a .EXE (or a library that is effectively an application) then don't even try using this option.

* Prior to F# 4.0, static linking could not be used for F# assemblies which use quotation literals.  Even for F# 4.0
  static linking can only be used for F# assemblies compiled with F# 4.0 and referencing at least FSharp.Core 4.4.0.0
  (or a corresponding portable profile for FSharp.Core).

Searching on stackoverflow reveals further guidance on this topic.


### FSharp.Core in components using FSharp.Compiler.Service

If your application of component uses FSharp.Compiler.Service, 
see [this guide](http://fsharp.github.io/FSharp.Compiler.Service/corelib.html). This scenario is more compilcated
because FSharp.Core is used both to run your script or application, and is referenced during compilation.

Likewise, if you have a script or library using FSharp.Formatting, then beware that that is using FSharp.Compiler.Service.
For scripts that is normally OK because they are processed using F# Interactive, and the default FSharp.Core is used.
If you have an application using FSharp.Formatting as a component then see the guide linked above.


### Entries in Project Files

By default, Visual Studio, Xamarin Studio and other tools generate well-formed F# `.fsproj` project files which
reference FSharp.Core in an appropriate way for the purpose that the component serves.  However, when using F# in 
advanced ways (and especially in cross-platform and portable scenarios) it can be useful to understand the
appropriate project file entries.  Additionally, in some F# projects the project file may have been edited by hand.  
In these cases it becomes essential to ensure you are referencing FSharp.Core properly.

### Use ``TargetFSharpCoreVersion`` in all F# projects

It is normal for all F# components to define the property TargetFSharpCoreVersion:

```
    <TargetFSharpCoreVersion>4.3.0.0</TargetFSharpCoreVersion>
```

or

```
    <TargetFSharpCoreVersion>4.3.1.0</TargetFSharpCoreVersion>
```

Later in the file `FSharp.Core` will be mentioned in the actual `FSharp.Core` reference, e.g.
```
    ... Version=$(TargetFSharpCoreVersion), Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a">
```
or other variations on this.

### Use ``AutoGenerateBindingRedirects=true`` in applications

It is normal for applications to use AutoGenerateBindingRedirects in their `.fsproj` file:

```
    <AutoGenerateBindingRedirects>true</AutoGenerateBindingRedirects>
```

This helps keep your binding redirects up-to-date when using Visual Studio and other tools that understand this property.

### Use ``Private=True`` when referencing FSharp.Core in applications

Applications should use `Private=true` in their FSharp.Core reference. 
This is means `FSharp.Core.dll` is copied to the target directory and can be found at runtime, see above.

Libraries do not need to use this.

### A C# project referencing an F# DLL or nuget package may need to also have a reference to FSharp.Core.dll

A C# project referencing an F# DLL or nuget package may need to also have a reference to FSharp.Core.dll.  This
reference must currently be managed explicitly unless you refer to the nuget package for FSharp.Core (see below).
Using the appropriate reference text below is recommended.


###  Examples of how to reference FSharp.Core

* *.NET 4.x*. For F# components restricted to run on .NET 4.x, it is normal to reference non-portable FSharp.Core using the following (adjust the FSharp.Core version number appripriately):

```
    <PropertyGroup>
       ...
       <TargetFSharpCoreVersion>4.3.1.0</TargetFSharpCoreVersion>
       ...
    </PropertyGroup>
    
    <Reference Include="FSharp.Core, Version=$(TargetFSharpCoreVersion), Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a">
      <Private>True</Private>
    </Reference>
```

* *PCL libraries*. For F# portable PCL library components, it is normal to use the following text in the project file:

```
    <PropertyGroup>
       ...
       <TargetFSharpCoreVersion>3.78.3.1</TargetFSharpCoreVersion>
       ...
    </PropertyGroup>

    <Reference Include="FSharp.Core">
         <Name>FSharp.Core</Name>
         <AssemblyName>FSharp.Core.dll</AssemblyName>
         <HintPath>$(MSBuildExtensionsPath32)\..\Reference Assemblies\Microsoft\FSharp\.NETCore\$(TargetFSharpCoreVersion)\FSharp.Core.dll</HintPath>
    </Reference>
```

* *Legacy PCL libraries*. "Legacy" portable projects (profile 47) use `.NETPortable` instead of `.NETCore`:


```
    <PropertyGroup>
       ...
       <TargetFSharpCoreVersion>2.3.5.1</TargetFSharpCoreVersion>
       ...
    </PropertyGroup>

    <Reference Include="FSharp.Core">
         <Name>FSharp.Core</Name>
         <AssemblyName>FSharp.Core.dll</AssemblyName>
         <HintPath>$(MSBuildExtensionsPath32)\..\Reference Assemblies\Microsoft\FSharp\.NETPortable\$(TargetFSharpCoreVersion)\FSharp.Core.dll</HintPath>
    </Reference>
```

For components  created with earlier F# tooling (e.g. Visual Studio 2012 or before), project files may use different reference text.
These should generally be adjusted to use the formulations above, this may be done automatically by some tooling.

### FSharp.Core in Xamarin apps

FSharp.Core is referenced as a Private/CopyLocal component in Xamarin apps for mobile devices.  This reference is done via 
the nuget package for FSharp.Core, see below.

### The FSharp.Core nuget package

FSharp.Core is also available [as a NuGet package](http://www.nuget.org/packages/FSharp.Core). 
It is not yet normal to reference FSharp.Core via this package, though it is
used by Xamarin application templates and by some F# library packages (as a transitive nuget dependency). 

At the time of writing, the relevant versions of the nuget packages were as follows (check the package versions and descriptions 
for latest information).

* [The nuget package for FSharp.Core for F# 3.0 (4.3.0.0)](http://www.nuget.org/packages/FSharp.Core/3.0.2)

* [The nuget package for FSharp.Core for F# 3.1 (4.3.1.0)](http://www.nuget.org/packages/FSharp.Core/3.1.2.1)

Notes:

* The nuget package includes all of the FSharp.Core redistributables from Visual F#. In addition, they include 
assemblies for MonoAndroid and MonoTouch built from [The F# Open Edition Repository](http://github.com/fsharp/fsharp)
and other  extra "builds" of FSharp.Core for Xamarin-related portable profiles.

* If you make the FSharp.Core nuget package a dependency of your own nuget package, you will induce a 
transitive dependency that nuget package forany users of your package,. This forces them to manage 
their FSharp.Core dependency more manually via nuget commands rather than via Visual Studio or other IDE
tooling.  While useful in some situations, this should be done with caution.

### FSharp.Core version numbers

Main .NET Framework DLLs (used at runtime for applications on .NET 4.x):

|         |                 |   Version  |
|:-------:|:---------------:|:-----------|
| F# 2.0  | .NET 4.0+       |   4.0.0.0  |
| F# 3.0  | .NET 4.0+       |   4.3.0.0  |
| F# 3.1  | .NET 4.0+       |   4.3.1.0  |
| F# 4.0  | .NET 4.5+       |   4.4.0.0  |

Portable PCL profiles (used at compile-time for portable libraries, can also be used at runtime when testing or as part of Windows Phone/Store apps):

|         |   4.0 |  4.5 | WinStore  |  WP8  | WP8.1  | iOS/Android  | Profile |  Version   | Moniker |
|:-------:|:-----:|:----:|:---------:|:-------:|:----:|:------------:|:-------:|:-----------|:--------|
| F# 3.0  |   x   |  x   |      x    |         |     |  x        | Profile47   |  2.3.5.0   | portable-net45+sl5+netcore45+MonoAndroid1+MonoTouch1 |
| F# 3.1  |   x   |  x   |      x    |         |     |  x        | Profile47   |  2.3.5.1   | portable-net45+sl5+netcore45+MonoAndroid1+MonoTouch1 |
|         |       |  x   |      x    |         |     |  x        | Profile7    |  3.3.1.0   | portable-net45+netcore45+MonoAndroid1+MonoTouch1 |
|         |       |  x   |      x    |    x    |     |  x        | Profile78   |  3.78.3.1  | portable-net45+netcore45+wp8+MonoAndroid1+MonoTouch1 |
|         |       |  x   |      x    |    x    | x   |  x        | Profile259  |  3.259.3.1 | portable-net45+netcore45+wpa81+wp8+MonoAndroid1+MonoTouch1 |
| F# 4.0  |   x   |  x   |      x    |         |     |  x        | Profile47   |  3.47.4.0  | portable-net45+sl5+netcore45+MonoAndroid1+MonoTouch1 |
|         |       |  x   |      x    |         |     |  x        | Profile7    |  3.7.4.0   | portable-net45+netcore45+MonoAndroid1+MonoTouch1 |
|         |       |  x   |      x    |    x    |     |  x        | Profile78   |  3.78.4.0  | portable-net45+netcore45+wp8+MonoAndroid1+MonoTouch1 |
|         |       |  x   |      x    |    x    | x   |  x        | Profile259  |  3.259.4.0 | portable-net45+netcore45+wpa81+wp8+MonoAndroid1+MonoTouch1 |

Mobile and other platform DLLs  (used at compile-time and runtime for applications, provided by Xamarin)

|         | Platform              |   Version  |
|:-------:|:----------------------|:------------|
| F# 3.1  | MonoTouch, MonoDroid  |   2.3.98.1  |
|         | XamarinMac Mobile     |   2.3.99.1  |
|         | XamarinMac Full       |   2.3.100.1  |
| F# 4.0  | MonoTouch, MonoDroid  |   3.98.4.0  |
|         | XamarinMac Mobile     |   3.99.4.0  |
|         | XamarinMac Full       |   3.100.4.0  |

.NET 2.0/3.5 DLLs (used at compile-time or runtime for applications, do not support F# 3.1+ constructs)

|         |   Version  |
|:-------:|:---------:|
| F# 2.0  |   2.0.0.0  |
| F# 3.0  |   2.3.0.0  |



### About Us

The F# Core Engineering Group is a technical group associated with [The F# Software Foundation](http://fsharp.org).

We manage the cross-platform and open-source [F# Compiler and Components](https://github.com/fsharp) repositories 
and the [F# Community Project Incubation Space](https://github.com/fsprojects).

Visit our [website](http://fsharp.github.io) and please continue all the great work on core F# engineering!

<br />
 
_First Published: 18 April 2015_

_The F# Core Engineering group_

