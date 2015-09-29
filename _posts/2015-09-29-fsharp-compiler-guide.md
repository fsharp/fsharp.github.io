---
layout: default
title: F# Compiler Technical Overview
subtitle: This technical guide discusses the F# Compiler.
---

# F# Compiler Technical Overview

## Contents
* [Introduction](#introduction)
* [Overview](#overview)
* [Key Data Formats and Representations](#key-data-formats-and-representations)
* [Key Compiler Phases](#key-compiler-phases)
* [Compiler Startup Performance](#compiler-startup-performance)
* [Compiler Memory Usage](#compiler-memory-usage)
* [Bootstrapping](#bootstrapping)

## Introduction

This guide discusses the F# Compiler source code and implementation from a technical point of view.  
See also [Contributing to the F# Language, Compiler and Core Library](http://fsharp.github.io/2014/06/18/fsharp-contributions.html).

This guide can be read in conjunction with any or all of:

* The [Microsoft/visualfsharp](http://github.com/Microsoft/visualfsharp) repository (where most changes should be submitted)
* The [F# Software Foundation cross-platform packaging](http://github.com/fsharp/fsharp) repository
* The [F# Compiler Service](http://github.com/fsharp/FSharp.Compiler.Service) repository

These all include the same core code, and the relationship between them is described [here](http://fsharp.github.io/2014/06/18/fsharp-contributions.html).

Please help improve this guide by [editing it and submitting a
pull-request](https://github.com/fsharp/fsharp.github.io/edit/master/_posts/2015-09-29-fsharp-compiler-guide.md). 
If you have specific topics that you would like to see addressed in this guide, 
please [add an issue](https://github.com/fsharp/fsharp.github.io/issues).

## Overview 

The F# compiler repositories are used to produce a range of different artefacts.  For the purposes of this
guide, the important ones are:

* The [FSharp.Compiler.Service](https://www.nuget.org/packages/FSharp.Compiler.Service) nuget package and dll, 
  shipped as an integrated component in most  F# editor and scripting tools.

* fsc.exe, fsi.exe and FSharp.Compiler.dll, the core binaries of the Visual F# tools for Windows, by Microsoft.  These tools also
  include FSharp.LanguageService.Compiler.dll, a special trimeed-down build of the core of the F# compiler.

* fsharpc, fsharpi, fsc.exe, fsi.exe, FSharp.Compiler.dll, the core binaries of the cross-platform open edition packages for F#, included in both Linux packages and Xamarin tooling for F#.

* FSharp.Core.dll, see [the separate guide on versions and related matters](http://fsharp.github.io/2015/04/18/fsharp-core-notes.html).  Not covered in this guide except where there
  is an interaction with the F# compiler core.

In all these cases these distributions of F# include the core of the F# compiler:

* The [fsharp](https://github.com/Microsoft/visualfsharp/tree/master/src/fsharp) directory contains the core compiler logic

* The [absil](https://github.com/Microsoft/visualfsharp/tree/master/src/absil) directory contains the Abstract IL library which the F# compiler uses

## Key Data Formats and Representations 

The following are the key data formats and internal data representations of the F# compiler code in its various configurations:

* _Input source files_  Read as Unicode text, or binary for referenced assemblies.

* _Input command line arguments_  See [CompileOptions.fs](https://github.com/Microsoft/visualfsharp/blob/master/src/fsharp/CompileOptions.fs) for the full  code implementating the arguments table.  Command line arguments are also accepted by the F# Compiler Service API in project speficiations, and as optional input to F# Interactive.

* _Tokens_, see [pars.fsy](https://github.com/Microsoft/visualfsharp/blob/master/src/fsharp/pars.fsy), [lex.fsl](https://github.com/Microsoft/visualfsharp/blob/master/src/fsharp/lex.fsl), [lexhelp.fs](https://github.com/Microsoft/visualfsharp/blob/master/src/fsharp/lexhelp.fs) and related files.

* _Abstract Syntax Tree (AST)_, see [ast.fs](https://github.com/Microsoft/visualfsharp/blob/master/src/fsharp/ast.fs), the untyped syntax tree resulting from parsing

* _Typed Abstract Syntax Tree (TAST)_, see [tast.fs](https://github.com/Microsoft/visualfsharp/blob/master/src/fsharp/tast.fs) and related files. The typed, bound syntax tree including both 
  type/module definitions and their backing expressions, resulting from type checking
  and the subject of successive phases of optimization and representation change.

* _Type checking context/state_, see for example [TcState in CompileOps.fs](https://github.com/Microsoft/visualfsharp/blob/master/src/fsharp/CompileOps.fs#L5071) and its constituent parts, particularly TcEnv in [TypeChecker.fs](https://github.com/Microsoft/visualfsharp/blob/master/src/fsharp/TypeChecker.fs) and NameResolutionEnv in [NameResolution.fs](https://github.com/Microsoft/visualfsharp/blob/master/src/fsharp/NameResolution.fs).
  A set of tables representing the available names, assemblies etc. in scope during type checking, plus
  associated information.

* _Abstract IL_, the output of code generation, then used for binary generation, and the input format when reading .NET assemblies, see [ILModuleDef in il.fs](https://github.com/Microsoft/visualfsharp/blob/master/src/absil/il.fsi#L1598)

* _The .NET Binary format_ (with added "pickled" F# Metadata resource), the final output of fsc.exe, see the ECMA 335 specification and the [ilread.fs](https://github.com/Microsoft/visualfsharp/blob/master/src/absil/ilread.fs) and [ilwrite.fs](https://github.com/Microsoft/visualfsharp/blob/master/src/absil/ilwrite.fs) binary reader/generator implementations.  The added F# metadata is stored in a binary resource, see [TastPickle.fs](https://github.com/Microsoft/visualfsharp/blob/master/src/fsharp/TastPickle.fs).

* _The incrementally emited .NET reflection assembly,_ the incremental output of fsi.exe. See [ilreflect.fs](https://github.com/Microsoft/visualfsharp/blob/master/src/absil/ilreflect.fs)

* The incremental project build engine state in [IncrementalBuild.fsi](https://github.com/fsharp/FSharp.Compiler.Service/tree/master/src/fsharp/vs/IncrementalBuild.fsi)/[IncrementalBuild.fs](https://github.com/fsharp/FSharp.Compiler.Service/tree/master/src/fsharp/vs/IncrementalBuild.fs) in the FSharp.Compiler.Service API. (note: An older version of this functionality persists internally in the Microsoft\visualfsharp repository for the Visual F# Tools)

* The corresonding APIs wrapping and accessing these structures in [the public-facing FSharp.Compiler.Service API](https://github.com/fsharp/FSharp.Compiler.Service/tree/master/src/fsharp/vs) (note: An older version of this functionality persists internally in the Microsoft\visualfsharp repository for the Visual F# Tools)

* The [F# Compiler Service Operations Queue](https://fsharp.github.io/FSharp.Compiler.Service/queue.html), covered in the compiler service documentation (note: An older version of this functionality persists internally in the Microsoft\visualfsharp repository for the Visual F# Tools)

* The [F# Compiler Service Caches](https://fsharp.github.io/FSharp.Compiler.Service/caches.html), covered in the compiler service documentation (note: An older version of this functionality persists internally in the Microsoft\visualfsharp repository for the Visual F# Tools)

## Key Compiler Phases

The following are the key phases and high-level logical operations of the F# compiler code in its various configurations:

* _Basic Lexing._  Produces a token stream.

* _White-space sensitive lexing_.  Accepts and produces a token stream.

* _Parsing_. Accepts a token stream and produces an AST.

* _Resolving references with MSBuild_ (only really used in F# Interactive), See ReferenceResolution.fs/fsi.  
  Accepts command-line arguments and produces information about assembly references.

* _Importing referenced .NET binaries as Abstract IL and TAST data structures_, see import.fs/fsi.
  Accepts information about IL references and produces a TAST node for each referenced assembly, 
  including information about its type definitions (and type forwarders if any).

* _Importing referenced F# binaries and optimization information as TAST data structures_, see pickle.fs/fsi.
  Accepts binary data and produces  TAST nodes for each referenced assembly, 
  including information about its type/module/function/member definitions.

* _Sequentially type checking files_, see
  [TypeChecker.fsi](https://github.com/Microsoft/visualfsharp/blob/master/src/fsharp/TypeChecker.fsi)/
  [fs](https://github.com/Microsoft/visualfsharp/blob/master/src/fsharp/TypeChecker.fs).
  Accepts an AST plus a type checking context/state and produces new TAST nodes
  incorporated into an updated type checking state, plus additional TAST Expression nodes used during code generation.

* _Pattern match compilation_, see
  [PatternMatchCompilation.fsi](https://github.com/Microsoft/visualfsharp/blob/master/src/fsharp/PatternMatchCompilation.fsi)/
  [fs](https://github.com/Microsoft/visualfsharp/blob/master/src/fsharp/PatternMatchCompilation.fs).
  Accepts a subset of checked TAST nodes representing F# pattern matching and produces TAST expressions implementing
  the pattern matching.  Called during type checking as each construct involving pattern matching is processed.

* _Constraint solving_, see
  [ConstraintSolver.fsi](https://github.com/Microsoft/visualfsharp/blob/master/src/fsharp/ConstraintSolver.fsi)/
  [fs](https://github.com/Microsoft/visualfsharp/blob/master/src/fsharp/ConstraintSolver.fs).
  A constraint solver state is maintained during type checking of a single file, and constraints are progressively
  asserted (i.e. added to this state).  Fresh inference variables are generated and variables are eliminated (solved).
  Variables are also generalized at various language constructs, or explicitly declared, making them "rigid".
  Called during type checking as each construct is processed.

* _Post-inference type checks_, see
  [PostInferenceChecks.fsi](https://github.com/Microsoft/visualfsharp/blob/master/src/fsharp/PostInferenceChecks.fsi)/
  [fs](https://github.com/Microsoft/visualfsharp/blob/master/src/fsharp/PostInferenceChecks.fs).
  Called at the end of type checking/inference for each file.
  A range of checks that can only be enforced after type checking on a file is complete.

* _Quotation generation_, see
  [QuotationTranslator.fsi](https://github.com/Microsoft/visualfsharp/blob/master/src/fsharp/QuotationTranslator.fsi)/[fs](https://github.com/Microsoft/visualfsharp/blob/master/src/fsharp/QuotationTranslator.fs) and
  [QuotationPickler.fsi](https://github.com/Microsoft/visualfsharp/blob/master/src/fsharp/QuotationPickler.fsi)/[fs](https://github.com/Microsoft/visualfsharp/blob/master/src/fsharp/QuotationPickler.fs).
  Generates the stored information for F# quotation nodes, generated from the TAST expression structures of the
  F# compiler. Quotations are ultimately stored as binary data plus some added type references. "ReflectedDefinition" quotations
  are collected and stored in a single blob.

* _Optimization phases_, primarily the "Optimize" (peephole/inlining) and "Top Level Representation" (lambda lifting) phases,
  see 
  [Optimizer.fsi](https://github.com/Microsoft/visualfsharp/blob/master/src/fsharp/Optimizer.fsi)/[Optimizer.fs](https://github.com/Microsoft/visualfsharp/blob/master/src/fsharp/Optimizer.fs) and
  [InnerLambdasToTopLevelFuncs.fsi](https://github.com/Microsoft/visualfsharp/blob/master/src/fsharp/InnerLambdasToTopLevelFuncs.fsi)/[fs](https://github.com/Microsoft/visualfsharp/blob/master/src/fsharp/InnerLambdasToTopLevelFuncs.fs) and
  [LowerCallsAndSeqs.fs](https://github.com/Microsoft/visualfsharp/blob/master/src/fsharp/LowerCallsAndSeqs.fs).
  Each of these takes TAST nodes for types andexpressions and either modifies the ndoes in place or produces new TAST nodes.  
  These phases are orchestrated in [CompileOptions.fs](https://github.com/Microsoft/visualfsharp/blob/master/src/fsharp/CompileOptions.fs)

* _Abstract IL code generation_, see 
  [IlxGen.fsi](https://github.com/Microsoft/visualfsharp/blob/master/src/absil/IlxGen.fsi)/[IlxGen.fs](https://github.com/Microsoft/visualfsharp/blob/master/src/absil/IlxGen.fs)
  Accepts TAST nodes and produces Abstract IL nodes.

* _Abstract IL rewriting_, see 
  [EraseClosures.fs](https://github.com/Microsoft/visualfsharp/blob/master/src/absil/EraseClosures.fs) and
  [EraseUnions.fs](https://github.com/Microsoft/visualfsharp/blob/master/src/absil/EraseUnions.fs).
  Eliminates some constructs by rewriting Abstract IL nodes.
  
* _Abstract IL to .NET binary_, see 
  [ilwrite.fsi](https://github.com/Microsoft/visualfsharp/blob/master/src/absil/ilwrite.fsi)/[ilwrite.fs](https://github.com/Microsoft/visualfsharp/blob/master/src/absil/ilwrite.fs). 

* _Abstract IL to .NET Reflection-Emit_, see 
  [ilreflect.fs](https://github.com/Microsoft/visualfsharp/blob/master/src/absil/ilreflect.fs). 

* _TAST information presented as a compiler service API_, see 
  [Symbols.fsi](https://github.com/Microsoft/visualfsharp/blob/master/src/fsharp/vs/Symbols.fsi)/[fs](https://github.com/Microsoft/visualfsharp/blob/master/src/fsharp/vs/Symbols.fs), 
  [service.fsi](https://github.com/Microsoft/visualfsharp/blob/master/src/fsharp/vs/service.fsi)/[fs](https://github.com/Microsoft/visualfsharp/blob/master/src/fsharp/vs/service.fs) 
  and related files.

* _The F# Interactive Shell_, see [fsi.fs](https://github.com/Microsoft/visualfsharp/blob/master/src/fsharp/fsi/fsi.fs) as a tool, and its presentation as an API  in fsi.fsi in FSharp.Compiler.Service.

* _The F# Compiler Shell_, see [fsc.fs](https://github.com/Microsoft/visualfsharp/blob/master/src/fsharp/fsi/fsc.fs) and [fscmain.fs](https://github.com/Microsoft/visualfsharp/blob/master/src/fsharp/fsi/fscmain.fs).


## Compiler Startup Performance

Compiler startup performance is a key factor affecting happiness of F# users.  If the compiler took 10sec to start up,
then far fewer people would use F#.

On all platforms, the following factos affect startup performance:

* Time to load compiler binaries.  This depends on the size of the generated binaries, whether they are pre-compiled (e.g. using NGEN), and the way the .NET implementation loads them.

* Time to open referenced assemblies (e.g. mscorlib.dll, FSharp.Core.dll) and analyze them for the types and namespaces defined.  This depends particularly on whether this is correctly done in an on-demand way.  

* Time to process "open" declarations are the top of each file.   Processing these declarations have been observed to take 
  time in some cases of  F# commpilation.

* Factors specific to the specific files being compiled.

On Windows, compiler startup performance tends to be greatly improved through the use of NGEN.  NGEN is run on ``fsc.exe`` and ``fsi.exe`` for installations of the Visual F# Tools.  

On Mono/Linux/OSX, compiler startup performance is less good but is not too bad.  Some improvements can be achieved
using AOT (Mono's equivalent of NGEN).

> Note: If you are building tools using the ``FSharp.Compiler.Service`` nuget package, NGEN is not automatically run on that DLL, and the startup timees of the tool you are building may be degraded. If possible, you should arrange for NGEN to be run when your tool is installed.

## Compiler Memory Usage

Overall memory usage is a primary determinant of the usability of the F# compiler and instances of
the F# compiler serivce.  Overly high memory usage results in poor throughput (particularly due to increased GC times)
and low user interface responsivity in tools such as Visual Studio or other editing environments.

### Key scenarios for memory usage

Overall memory usage depends considerably on scenario,phase and configuration. Some key scenarios are:
* Overall memory usage of an instance of Visual Studio or another editing environment when editing F# projects
* Overall memory usage of the Visual F# Power Tools in Visual Studio when editing and refactoring F# projects
* Memory usage and throughput of the F# compiler fsc.exe
* Memory usage and throughput of the F# Interactive dynamic scripting compiler fsi.exe

Analyzing memory usage of the F# Compiler and instances of the F# Compiler Service can be done using tools such
as the Visual Studio Managed Memory analysis.  For example:

### Analyzing memory usage

To analyze memory usage of the Visual F# Tools in Visual Studio (with or without the Visual F# Power Tools) 

1.  Take a process minidump of the devenv.exe process in Task Manager
2.  Open that .dmp file in another instance of Visual Studio and click on "Debug Managed Memory"

You can also compare to a baseline generated without using the Visual F# Tools.

This buckets memory usage by type and lets you analyze the roots keeping those objects alive.  
At the time of writing, these were some of the top types consuming managed memory were as follows. In some
cases these have been bucketed:

| Type                           |   Approx %  |  Category | Cause  |
|:------------------------------:|:-----------:|:---------:|:---------:|
| ``MemChannel``                 |  ~20%       | TAST Abs/IL | In-memory representations of referenced DLLs. "System" DLLs are read using a memory-mapped file. |
| ``ValData``                 	 | ~12%         |  TAST   | per-value data, one oject for each F# value declared (or imported in optimization expressions)  |
| + others  | | | |
| ``EntityData``                 | ~12%         | TAST | various types for per-type-or-module-or-namespace-definition data, for each F# type declared, or F# or .NET type imported |
| ``TyconAugmentation``          | | | |
|  ``PublicPath``                | | | |
|  ``Lazy<ModuleOrNamespaceType>`` | | | |
| ``Func<ModuleOrNamespaceType>`` | | | |
| + others  | | | |
| ``ILTypeDefs``, ``ILTypeDef``  | ~9%  | TAST/AbsIL | various types and delayed thinks for reading IL type definitions from .NET assemblies |
| ``Tuple<ILScopeRef, ILAttributes, Lazy<ILTypeDef>>`` | | | |
| ``Lazy<Tuple<ILScopeRef, ILAttributes, Lazy<ILTypeDef>>`` |  |  |  | 
| ``Func<Tuple<ILScopeRef, ILAttributes, Lazy<ILTypeDef>>`` |   | |  | 
| ``Import+lazyModuleOrNamespaceTypeForNestedTypes@400``   | | | |
| ``Lazy<FSharpList<ILAttribute>>`` | ~6%  | TAST/AbsIL | various types for delayed thunks for reading lists of attributes about .NET assemblies |
| ``ILBinaryReader+seekReadCustomAttrs@2627`` | | | |
| ``Func<FSharpList<ILAttribute>>`` 	| | | |
| ``Attrib``	     | | | |
| ``Dictionary<Int32, String>``	 | ~3%         | TAST/AbsIL   | various tables including those used in readong binaries |
| ``Dictionary<String, String>`` | ~2%  | TAST/AbsIL | memoization tables for strings  |
| ``EntityRef``               	 | ~2%  | TAST | nodes representing references to entities, pointing to an EntityData |
| ``Ident``                      | ~1.5%    | TAST | identifiers - a range and a reference to a string
| ``TType_fun``                  | ~1.5%    | TAST | node for function types, especially in imported metadata |
| ``TType_app``                  | ~1.5%    | TAST | node for constructed types like ``list<int>`` |
| ``FSharpList<TType>``          | ~1.5%    | TAST | lists of types, usually in tuple and type applications |
| ``TyparData``                  | ~1.5%    | TAST | data about type inference variables and gneric parameters |
| ``ValLinkagePartialKey``	     | ~1%  | TAST | data indicating how one assembly references a value/method/member in another |
| ``ILTypeRef``	     | ~1%  | TAST/AbsIL | type references in AbstractIL metadata |
| ``XmlDoc``	     | ~1%  | AST | documentation strings |
| + a long tail of other types, most of which probably belong in the above general categories, increasing the totals to 100%  | | | |

Looking at the above analysis, the conclusions at the time of writing are

1. A considerable chunk of memory is used for in-memory copies of some referenced DLLs. In practice, this is difficult to
   avoid, as similar memory is also used even if using memory-mapped DLLs.

2. Nearly all long-lived memory is related to the TAST nodes of the F# compiler data structure, or the related Abstract IL nodes for .NET asemblies being imported.

3. The Abstract IL data structures for delayed-readaing of .NET type definitions use memory inefficiently

4. The Abstract IL data structures for delayed-readaing of .NET attributes use memory relatively inefficiently

5. There is a considerable "long tail" of memory usage when categorized by type

An alternative way to look at the data is at which F# types are being used inefficiently in long-stored objects.
For example: 

| F# Core Type                  |   Approx %  |  
|:------------------------------:|:-----------:|
| ``FSharpList<...>``                 |  ~4%       | 
| ``FSharpOption<...>``                 |  ~1.5%       | 
| ``Tuple<...>``                 |  ~1.5%       | 
| ``FSharpMap<...>``                 |  ~1%       | 
| ``FSharpSet<...>``                 |  ~0% (negligible)       | 

There are micro savings available here if you hunt carefully.

## Geneated Code Performance 

TBD - will discuss various aspects of generated code and the parts of the compiler responsible.

## Compiler Throughput Performance

TBD - discusses topics related to compiler performance including phase costs, data structures, GC settings etc.


## Bootstrapping

The [Microsoft/visualfsharp](http://github.com/Microsoft/visualfsharp) and [fsharp/fsharp](http://github.com/fsharp/fsharp) 
repositories are bootstrapped.  That is, an existing F# compiler is used to build a "proto" compiler from the current source
code.  That "proto" compiler is then used to compile itself, producing a "final" compiler.  This ensures the final compiler is compiled with all relevant optimizations and fixes.

The [FSharp.Compiler.Service] component is not bootstrapped and is simpy compiled with an existing F# compiler to produce
a .NET 4.x component.  In  practice this is sufficient given the overall stability of the codebases.

## Didn't find what you want?

Please [add an issue](https://github.com/fsharp/fsharp.github.io/issues) outlining what technical information you were looking 
for.

### About Us

The F# Core Engineering Group is a working group associated with [The F# Software Foundation](http://fsharp.org).

Visit our [website](http://fsharp.github.io) and please continue all the great work on core F# engineering!

<br />
 
_First Published: 29 September 2015_

_The F# Core Engineering group_


