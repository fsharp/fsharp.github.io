---
layout: default
title: Notes and Guidance on FSharp.Core
subtitle: This technical guide discusses the FSharp.Core library.  
---

# Notes and Guidance on FSharp.Core

This technical guide discusses the FSharp.Core library.  Please help improve this guide by [editing it and submitting a pull-request](https://github.com/fsharp/fsharp.github.io/blob/master/_posts/2015-04-18-fsharp-core-notes.md). 

## Highlights

* [Do not bundle FSharp.Core with a library](#do-not-bundle-fsharp-core-with-a-library)  
* [Do bundle FSharp.Core with an application](#do-deploy-fsharp-core-as-part-of-your-application)  
* [FSharp.Core is binary compatible](#fsharp-core-is-binary-compatible)  
* [C# projects referencing F# projects need an FSharp.Core reference](#a-c-project-referencing-an-f-dll-or-nuget-package-may-need-to-also-have-a-reference-to-fsharp-core-dll)  
* [The FSharp.Core NuGet Package](#the-fsharp-core-nuget-package)  
* [FSharp.Core version numbers](#fsharp-core-version-numbers)  

## Contents
{:.no_toc}

* Will be replaced with the ToC, excluding the "Contents" header
{:toc}

## General Guidance 

### Application v. Library v. Script

Each project is either an *application* or a *library*.

* Examples of application are `.exe` project or a `.dll` project that is a test project, addin, website, or an app.

* Libraries are just ordinary `.dll` components (excluding those above which are applications). 

* Scripts are not projects, just `.fsx` files, possibly referring to other files using `#load` and Libraries using `#r`

### Do *not* bundle FSharp.Core with a library 

Do _not_ include a copy of FSharp.Core with your library or package.  If you do, you will create havoc for users of
your library.

The decision about which `FSharp.Core` a library binds to is up to the application hosting of the library.
The library and/or library package can place constraints on this, but it doesn't decide it.

Especially, do _not_ include FSharp.Core in the ``lib`` folder of a NuGet package.


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

### Do *not* assume any specific version of FSharp.Core is in the GAC, even if it is on your machine

Once again, do not rely on FSharp.Core being in the GAC.  For applications (see above), 
use ``<Private>true</Private>`` for the FSharp.Core reference in the project file (see below). In the Visual Studio IDE this is equivalent to setting the `CopyLocal` property to `true`  properties for the `FSharp.Core` reference.

On some installations of F#, some versions of FSharp.Core are added to the GAC.  Do not rely on these
being present in production code being deployed off your machine.

There _are_ some exceptions to this.

* Standard installations of F# tools on Linux and Mac on  Mono also install the latest FSharp.Core into the
  GAC. They also add machine-wide binding redirects for that component. That means that, for those machines, the latest installed FSharp.Core will be used by applications.
  
* Some installations of F# on Windows Visual Studio do install FSharp.Core into the GAC. 

  - VS2010 installs FSharp.Core 4.0.0.0 only
  - VS2012 installs FSharp.Core 4.3.0.0 only
  - VS2013 installs FSharp.Core 4.3.1.0 only
  - VS2015 installs FSharp.Core 4.4.0.0 only

  If you can assume a particular version of Visual Studio (e.g. in a dev/test situation), then you may
  be able to assume FSharp.Core is in the GAC.  

But it is best to avoid this assumption and instead make sure an appropriate FSharp.Core
is deployed as part of your application.  

### What to do for error "Could not load file or assembly FSharp.Core, Version=4.0.0.0" or similar

The normal way to resolve this is to  deploy FSharp.Core as part of your application, see above. See also [this stackoverflow question](http://stackoverflow.com/questions/24801302/could-not-load-file-or-assembly-fsharp-core-version-4-0-0-0-azure-web-role) and many other similar answers on the web.

Earlier versions of F# (Visual F# 2.0) had a "runtime redistributable" you could install on target machines.  This model is now no longer used and instead you deploy FSharp.Core as part of your application.

### FSharp.Core is binary compatible

FSharp.Core is binary compatible across versions of the F# language. For example, FSharp.Core `4.0.0.0` (F# 2.0) is binary compatible with  `4.3.0.0` (F# 3.0), `4.3.1.0` (F# 3.1), `4.4.0.0` (F# 4.0) and so on.

Likewise, FSharp.Core is binary compatible from "portable" profiles to actual runtime implementations. For example, FSharp.Core  `3.78.3.1` (a portable profiles for F# 3.1, see the table below) is binary compatible
with the runtime implementation assembly `4.3.1.0` (F# 3.1) and `4.4.0.0` (F# 4.0).

Binary compatibility means that a component built for X can instead bind to Y at runtime.
It doesn't mean that Y behaves the same as X (some bug fixes may have been made, and Y may have more functionality than X).

### Libraries target lower versions of FSharp.Core

F# ecosystem libraries should generally target the *earliest, most portable* version of FSharp.Core feasible.

If your library is part of an ecosystem, it can be helpful to target the _earliest, most widespread language version_ 
and the _earliest_ (4.0+) and _most portable_ profiles of the .NET Framework feasible.

In some cases you might also like to attempt to make your component into a PCL Portable component.
This is covered below.

For personal libraries, or libraries that are effectively part of an application, the choice is yours, just target
the latest language version and the framework you're using in your application.



### Applications target higher versions of FSharp.Core

F# applications should generally use the *highest* language version and the most *platform-specific* version of FSharp.Core.

Generally, when writing an application, you want to use the highest version of FSharp.Core available for the platform
you are targeting.

If your application in being developed by people using multiple versions of F# tooling (common
in open source working) you may need to target a lower version of the language and a correspondingly earlier version
of FSharp.Core.



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
            <dependentAssembly>
              <assemblyIdentity name="FSharp.Core" publicKeyToken="b03f5f7f11d50a3a" culture="neutral" />
              <bindingRedirect oldVersion="0.0.0.0-4.3.0.0" newVersion="4.3.1.0" />
            </dependentAssembly>
          </assemblyBinding>
        </runtime>
    </configuration>
```

Application project files should normally also specify `AutoGenerateBindingRedirects` which allows tooling to 
help keep the binding redirects up-to-date, see below.

### FSharp.Core in F# Interactive

F# Interactive (`fsi.exe`) always references and uses the FSharp.Core of the corresponding tool chain, as follows:

- F# 3.0 --> 4.3.0.0
- F# 3.1 --> 4.3.1.0
- F# 4.0 --> 4.4.0.0

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
see [this guide](http://fsharp.github.io/FSharp.Compiler.Service/corelib.html). This scenario is more complicated
because FSharp.Core is used both to run your script or application, and is referenced during compilation.

Likewise, if you have a script or library using FSharp.Formatting, then beware that is using FSharp.Compiler.Service.
For scripts that is normally OK because they are processed using F# Interactive, and the default FSharp.Core is used.
If you have an application using FSharp.Formatting as a component then see the guide linked above.

### Making PCL-portable libraries

PCL portable libraries target a subset of .NET functionality and can be used in many different
circumstances. F# PCL portable libraries target a special FSharp.Core which is binary compatible with the final FSharp.Core used for an application (see above). The FSharp.Core numbers for PCL profiles are listed later in this guide.

In some cases you might like to make your own libraries that are PCL Portable components.
Below is a table of the recommended profiles to target depending on the minimal version of F# you can 
assume your team or other users of your library are using.

| Version | Framework       |   Recommended PCL Profile  | 
|:-------:|:---------------:|:-----------|
| F# 2.0  | .NET 4.0+       |   n/a  |   
| F# 3.0  | .NET 4.0+       |  Profile47 (net45+sl5+netcore45+MonoAndroid1+MonoTouch1) | 
| F# 3.1  | .NET 4.0+       |  Profile47 (net45+sl5+netcore45+MonoAndroid1+MonoTouch1) | 
| F# 3.1.2  | .NET 4.0+       | Profile47 (net45+sl5+netcore45+MonoAndroid1+MonoTouch1) |
| F# 4.0  | .NET 4.5+       |  Profile259 (portable-net45+netcore45+wpa81+wp8+MonoAndroid1+MonoTouch1) or Profile47 |

From the above, it can be seen that targeting F# 3.0 or 3.1 and Profile47 is a good choice for open source
libraries.  (In Visual Studio 2013, this choice is labeled as "Portable Library (.NET 4.0, Windows Store, Silverlight 5.0)" though the generated libraries can also be used with  Xamarin Android  and Xamarin iOS.)

To convert an existing project to a portable profile, it may well be easiest to  edit the project file by hand.
See the project file fragments later in this guide.

See [this bug](http://visualfsharp.codeplex.com/workitem/144) for why 
profiles 7,78,259 are not recommended when using F# 3.1, even though the tooling allows their creation and consumption.


## `FSharp.Core` Entries in Project Files

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

### A C# project referencing an F# DLL or NuGet package may need to also have a reference to FSharp.Core.dll

A C# project referencing an F# DLL or NuGet package may need to also have a reference to FSharp.Core.dll.  This
reference must currently be managed explicitly unless you refer to the NuGet package for FSharp.Core (see below).
Using the appropriate reference text below is recommended.


###  Examples of how project files reference FSharp.Core and the F# .targets file


* *.NET 4.0, 4.5, 4.5.1, 4.5.2 etc.*. For F# components restricted to run on .NET 4.x, it is normal to reference non-portable FSharp.Core using the following (adjust the FSharp.Core version number appropriately):

```
    <PropertyGroup>
       ...
       <TargetFrameworkVersion>v4.5</TargetFrameworkVersion>
       <TargetFSharpCoreVersion>4.3.1.0</TargetFSharpCoreVersion>
       ...
    </PropertyGroup>
    ...
    <ItemGroup>
       ...
      <Reference Include="FSharp.Core, Version=$(TargetFSharpCoreVersion), Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a">
        <Private>True</Private>
      </Reference>
       ...
    </ItemGroup>
    ...
    <PropertyGroup>
        <FSharpTargetsPath>$(MSBuildExtensionsPath32)\Microsoft\VisualStudio\v$(VisualStudioVersion)\FSharp\Microsoft.FSharp.Targets</FSharpTargetsPath>
    </PropertyGroup>
    <Import Project="$(FSharpTargetsPath)" Condition="Exists('$(FSharpTargetsPath)')" />
  </Choose>
```

* *PCL libraries*. For F# portable PCL library components, it is normal to use the following text in the project file:

```
    <PropertyGroup>
       ...
       <TargetFrameworkVersion>v4.5</TargetFrameworkVersion>
       <TargetFrameworkProfile>Profile78</TargetFrameworkProfile>
       <TargetProfile>netcore</TargetProfile>
       <TargetFSharpCoreVersion>3.78.3.1</TargetFSharpCoreVersion>
       ...
    </PropertyGroup>

    <ItemGroup>
       ...
      <Reference Include="FSharp.Core">
         <Name>FSharp.Core</Name>
         <AssemblyName>FSharp.Core.dll</AssemblyName>
         <HintPath>$(MSBuildExtensionsPath32)\..\Reference Assemblies\Microsoft\FSharp\.NETCore\$(TargetFSharpCoreVersion)\FSharp.Core.dll</HintPath>
      </Reference>
       ...
    </ItemGroup>
    ...
    <Import Project="$(MSBuildExtensionsPath32)\Microsoft\VisualStudio\v$(VisualStudioVersion)\FSharp\Microsoft.Portable.FSharp.Targets" />

```

* *Legacy PCL libraries*. "Legacy" portable projects (profile 47) use `.NETPortable` instead of `.NETCore`:


```
    <PropertyGroup>
       ...
       <TargetFrameworkVersion>v4.0</TargetFrameworkVersion>
       <TargetFrameworkProfile>Profile47</TargetFrameworkProfile>
       <TargetFSharpCoreVersion>2.3.5.1</TargetFSharpCoreVersion>
       ...
    </PropertyGroup>

    <ItemGroup>
      ...
      <Reference Include="FSharp.Core">
         <Name>FSharp.Core</Name>
         <AssemblyName>FSharp.Core.dll</AssemblyName>
         <HintPath>$(MSBuildExtensionsPath32)\..\Reference Assemblies\Microsoft\FSharp\.NETPortable\$(TargetFSharpCoreVersion)\FSharp.Core.dll</HintPath>
      </Reference>
      ...
    </ItemGroup>
    ...
    <PropertyGroup>
        <FSharpTargetsPath>$(MSBuildExtensionsPath32)\Microsoft\VisualStudio\v$(VisualStudioVersion)\FSharp\Microsoft.Portable.FSharp.Targets</FSharpTargetsPath>
    </PropertyGroup>
    <Import Project="$(FSharpTargetsPath)" />
```

For components  created with earlier F# tooling (e.g. Visual Studio 2012 or before), project files may use different reference text. These should generally be adjusted to use the formulations above, this may be done automatically by some tooling.

### FSharp.Core in Xamarin apps

FSharp.Core is referenced as a Private/CopyLocal component in Xamarin apps for mobile devices.  This reference is handled via 
the normal Xamarin framework install locations i.e `/Library/Frameworks/Xamarin.iOS.framework/Versions/Current/lib/mono/Xamarin.iOS/FSharp.Core.dll` for Xamarin.iOS.


### Missing FSharp.Core References in .fsproj Project Files

This section deals with how .fsproj project files that are missing an FSharp.Core reference are handled by different tooling.

*Visual Studio*

If there is no `FSharp.Core` reference  in your fsproj, Visual Studio will assume an environment of "latest", i.e. whatever version it's currently running against. The generated compiler command line has no `FSharp.Core` ref in it. The compiler will make the same fallback decision (assume reference to whatever version it is running) in order to determine the FSharp.Core version it will use as target. So for F# 4.0 it will be as if you referenced `FSharp.Core, Version=4.4.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a`.

But that's where it gets tricky - the compiler actually has to resolve that version information to an assembly location. On Windows it will use MSBuild to do that resolution. With a regular install of the VF# tools on Windows, a reg key is written at `HKLM\SOFTWARE\Wow6432Node\Microsoft\.NETFramework\v4.0.30319\AssemblyFoldersEx\F# 4.0 Core Assemblies` which points to the appropriate `%ProgramFiles(x86)%\Reference Assemblies\...` location which should be used for compile-time resolution.  MSBuild looks at AssemblyFoldersEx when determining candidate resolution paths (that's a general MSBuild extensibility mechanism). If you whack that key, then `FSharp.Core` winds up being resolved from the GAC, and that fails due to missing optdata/sigdata.

So... for Visual Studio, the latest version of `FSharp.Core` can be obtained by inspecting the currently-executing code in Visual Studio, e.g. `fsi.exe`. But finding a suitable copy that sits next to optdata/sigdata requires outside assistance - either a reg key, a call to MSBuild or some other pointer to get you to `...\Reference Assemblies\Microsoft\FSharp\.NETFramework\v4.0\4.4.0.0\FSharp.Core.dll`.


## The FSharp.Core NuGet package

FSharp.Core is also available [as a NuGet package](http://www.nuget.org/packages/FSharp.Core). 
It is not yet normal to reference FSharp.Core via this package, though it is used by some F# library packages (as a transitive NuGet dependency). 

At the time of writing, the relevant versions of the NuGet packages were as follows (check the package versions and descriptions 
for latest information).

* [The NuGet package for FSharp.Core for F# 3.0 (4.3.0.0)](http://www.nuget.org/packages/FSharp.Core/3.0.2)

* [The NuGet package for FSharp.Core for F# 3.1 (4.3.1.0)](https://www.nuget.org/packages/FSharp.Core/3.1.2.5)

* [The NuGet package for FSharp.Core for F# 4.0 (4.4.0.0)](https://www.nuget.org/packages/FSharp.Core/4.0.0.1) 

Notes:

* The NuGet package includes all of the FSharp.Core redistributables from Visual F#. In addition, they include 
assemblies for MonoAndroid and MonoTouch built from [The F# Open Edition Repository](http://github.com/fsharp/fsharp)
and other  extra "builds" of FSharp.Core for Xamarin-related portable profiles.

* If you make the FSharp.Core NuGet package a dependency of your own NuGet package, you will induce a 
transitive dependency that NuGet package for any users of your package,. This forces them to manage 
their FSharp.Core dependency more manually via NuGet commands rather than via Visual Studio or other IDE
tooling.  While useful in some situations, this should be done with caution.

## FSharp.Core version numbers

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

Version numbering system for recent and future releases 

| Target Framework | F# 3.0 / VS 2012 | F# 3.1 / VS 2013 | 3.1.1, 3.1.2 updates |  F# 4.0 / VS 2015 |  F# X.Y / VS vFuture |
|:----------------:|:----------------:|:----------------:|:--------------------:|:-----------------:|:--------------------:|
|      .NET 2      | 2.3.0.0          | 2.3.0.0 [frozen] |                      | 2.3.0.0 [frozen]  | 2.3.0.0 [frozen]     |
|      .NET 4      | 4.3.0.0          | 4.3.1.0          |                      | 4.4.0.0           | 4.X.Y.0              |
|    Portable 47   | 2.3.5.0          | 2.3.5.1          |                      | 3.47.4.0          | 3.47.X.Y             |
|    Portable 7    |                  | 3.3.1.0          |                      | 3.7.4.0           | 3.7.X.Y              |
|    Portable 78   |                  |                  | 3.78.3.1             | 3.78.4.0          | 3.78.X.Y             |
|    Portable 259  |                  |                  | 3.259.3.1            | 3.259.4.0         | 3.259.X.Y            |
|    Portable N    |                  |                  |                      |                   | 3.N.X.Y              |


### About Us

The F# Core Engineering Group is a working group associated with [The F# Software Foundation](http://fsharp.org).

We manage the cross-platform and open-source [F# Compiler and Components](https://github.com/fsharp) repositories 
and the [F# Community Project Incubation Space](https://github.com/fsprojects).

Visit our [website](http://fsharp.github.io) and please continue all the great work on core F# engineering!

<br />
 
_First Published: 18 April 2015_

_The F# Core Engineering group_

