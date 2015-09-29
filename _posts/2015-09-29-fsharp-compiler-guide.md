---
layout: default
title: F# Compiler Guide
subtitle: This technical guide discusses the F# Compiler.  
---

# F# Compiler Technical Guide (draft)

This guide discusses the F# Compiler source code and implementation from a technical point of view.  
For details on contributing to the F# compiler and core library, please see 
[Contributing to the F# Language and Compiler](http://fsharp.github.io/2014/06/18/fsharp-contributions.html).

This guide can be read in conjunction with either 

* The [Microsoft/visualfsharp](http://github.com/Microsoft/visualfsharp) repository
* The [F# Software Foundation cross-platform packaging repository](http://github.com/fsharp/fsharp)
* The [F# Compiler Service](http://github.com/fsharp/FSharp.Compiler.Service), a repository of [the F# Software Foundation](http://fsharp.org)

This guide is work-in-progress. Please help improve this guide by [editing it and submitting a
pull-request](https://github.com/fsharp/fsharp.github.io/edit/master/_posts/2015-09-29-fsharp-compiler-guide.md). 
If you have specific topics that you would like to see addressed in this guide, 
please [add an issue](https://github.com/fsharp/fsharp.github.io/issues).

# Overview

The F# compiler repositories are used to produce a range of different artefacts.  For the purposes of this
guide, the foreemost amongst these are:

* FSharp.Compiler.Service.dll, part of [the corresponding nuget package](https://www.nuget.org/packages/FSharp.Compiler.Service) 
  and shipped as an integrated component in a range of F# editor and scripting tooling.

* fsc.exe, fsi.exe and FSharp.Compiler.dll, the core binaries of the Visual F# tools for Windows.  These tools also
  include FSharp.LanguageService.Compiler.dll, a special trimeed-down build of the core of the F# compiler.

* fsharpc, fsharpi, fsc.exe, fsi.exe, FSharp.Compiler.dll, the core binaries of the cross-platform open edition packages for F#.

* FSharp.Core.dll, see [the separate guide on versions and related matters](http://fsharp.github.io/2015/04/18/fsharp-core-notes.html).  Not covered in this guide except where there
  is an interaction with the F# compiler core.

In all these cases these distributions of F# include the core of the F# compiler:

* The [fsharp](https://github.com/Microsoft/visualfsharp/tree/master/src/fsharp) directory contains the core compiler logic

* The [absil](https://github.com/Microsoft/visualfsharp/tree/master/src/fsharp) directory contains the Abstract IL library which the F# compiler uses

## Key Logical Structures

* _Tokens_, see [pars.fsy](https://github.com/fsharp/FSharp.Compiler.Service/blob/master/src/fsharp/pars.fsy), [lex.fsl](https://github.com/fsharp/FSharp.Compiler.Service/blob/master/src/fsharp/lex.fsl), [lexhelp.fs](https://github.com/fsharp/FSharp.Compiler.Service/blob/master/src/fsharp/lexhelp.fs) and related files.

* _Abstract Syntax Tree (AST)_, see [ast.fs](https://github.com/fsharp/FSharp.Compiler.Service/blob/master/src/fsharp/ast.fs), the untyped syntax tree resulting from parsing

* _Typed Abstract Syntax Tree (TAST)_, see [tast.fs](https://github.com/fsharp/FSharp.Compiler.Service/blob/master/src/fsharp/tast.fs) and related files. The typed, bound syntax tree including both 
  type/module definitions and their backing expressions, resulting from type checking
  and the subject of successive phases of optimization and representation change.

* _Type checking context/state_, see for example [TcState in CompileOps.fs](https://github.com/fsharp/FSharp.Compiler.Service/blob/master/src/fsharp/CompileOps.fs#L5071) and its constituent parts, particularly TcEnv in [TypeChecker.fs](https://github.com/fsharp/FSharp.Compiler.Service/blob/master/src/fsharp/TypeChecker.fs) and NameResolutionEnv in [NameResolution.fs](https://github.com/fsharp/FSharp.Compiler.Service/blob/master/src/fsharp/NameResolution.fs).
  A set of tables representing the available names, assemblies etc. in scope during type checking, plus
  associated information.

* _Abstract IL_, the output of code generation, see [ILModuleDef in il.fs](https://github.com/fsharp/FSharp.Compiler.Service/blob/master/src/absil/il.fsi#L1598)

* _The .NET Binary format_ (with added "pickled" F# Metadata resource), the final output of fsc.exe, see the ECMA 335 specification and the [ilread.fs](https://github.com/fsharp/FSharp.Compiler.Service/blob/master/src/absil/ilread.fs) and [ilwrite.fs](https://github.com/fsharp/FSharp.Compiler.Service/blob/master/src/absil/ilwrite.fs) binary reader/generator implementations.  The added F# metadata is stored in a binary resource, see [pickle.fs](https://github.com/fsharp/FSharp.Compiler.Service/blob/master/src/fsharp/pickle.fs).

* _The incrementally emited .NET reflection assembly,_ the incremental output of fsi.exe. See [ilreflect.fs](https://github.com/fsharp/FSharp.Compiler.Service/blob/master/src/absil/ilreflect.fs)

* The corresonding APIs wrapping and accessing these structures in [the public-facing FSharp.Compiler.Service API](https://github.com/fsharp/FSharp.Compiler.Service/tree/master/src/fsharp/vs)

## Key Phases

* _Input source files_  Read as Unicode text.

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
  [TypeChecker.fsi](https://github.com/fsharp/FSharp.Compiler.Service/blob/master/src/fsharp/TypeChecker.fsi)/
  [fs](https://github.com/fsharp/FSharp.Compiler.Service/blob/master/src/fsharp/TypeChecker.fs).
  Accepts an AST plus a type checking context/state and produces new TAST nodes
  incorporated into an updated type checking state, plus additional TAST Expression nodes used during code generation.

* _Pattern match compilation_, see
  [PatternMatchCompilation.fsi](https://github.com/fsharp/FSharp.Compiler.Service/blob/master/src/fsharp/PatternMatchCompilation.fsi)/
  [fs](https://github.com/fsharp/FSharp.Compiler.Service/blob/master/src/fsharp/PatternMatchCompilation.fs).
  Accepts a subset of checked TAST nodes representing F# pattern matching and produces TAST expressions implementing
  the pattern matching.  Called during type checking as each construct involving pattern matching is processed.

* _Constraint solving_, see
  [ConstraintSolver.fsi](https://github.com/fsharp/FSharp.Compiler.Service/blob/master/src/fsharp/ConstraintSolver.fsi)/
  [fs](https://github.com/fsharp/FSharp.Compiler.Service/blob/master/src/fsharp/ConstraintSolver.fs).
  A constraint solver state is maintained during type checking of a single file, and constraints are progressively
  asserted (i.e. added to this state).  Fresh inference variables are generated and variables are eliminated (solved).
  Variables are also generalized at various language constructs, or explicitly declared, making them "rigid".
  Called during type checking as each construct is processed.

* _Post-inference type checks_, see
  [PostInferenceChecks.fsi](https://github.com/fsharp/FSharp.Compiler.Service/blob/master/src/fsharp/PostInferenceChecks.fsi)/
  [fs](https://github.com/fsharp/FSharp.Compiler.Service/blob/master/src/fsharp/PostInferenceChecks.fs).
  Called at the end of type checking/inference for each file.
  A range of checks that can only be enforced after type checking on a file is complete.

* _Quotation generation_, see
  [QuotationTranslator.fsi](https://github.com/fsharp/FSharp.Compiler.Service/blob/master/src/fsharp/QuotationTranslator.fsi)/[fs](https://github.com/fsharp/FSharp.Compiler.Service/blob/master/src/fsharp/QuotationTranslator.fs) and
  [QuotationPickler.fsi](https://github.com/fsharp/FSharp.Compiler.Service/blob/master/src/fsharp/QuotationPickler.fsi)/[fs](https://github.com/fsharp/FSharp.Compiler.Service/blob/master/src/fsharp/QuotationPickler.fs).
  Generates the stored information for F# quotation nodes, generated from the TAST expression structures of the
  F# compiler. Quotations are ultimately stored as binary data plus some added type references. "ReflectedDefinition" quotations
  are collected and stored in a single blob.

* _Optimization phases_, primarily the "Optimize" (peephole/inlining) and "Top Level Representation" (lambda lifting) phases,
  see 
  [Optimizer.fsi](https://github.com/fsharp/FSharp.Compiler.Service/blob/master/src/fsharp/Optimizer.fsi)/[Optimizer.fs](https://github.com/fsharp/FSharp.Compiler.Service/blob/master/src/fsharp/Optimizer.fs) and
  [InnerLambdasToTopLevelFuncs.fsi](https://github.com/fsharp/FSharp.Compiler.Service/blob/master/src/fsharp/InnerLambdasToTopLevelFuncs.fsi)/[fs](https://github.com/fsharp/FSharp.Compiler.Service/blob/master/src/fsharp/InnerLambdasToTopLevelFuncs.fs) and
  [LowerCallsAndSeqs.fs](https://github.com/fsharp/FSharp.Compiler.Service/blob/master/src/fsharp/LowerCallsAndSeqs.fs).
  Each of these takes TAST nodes for types andexpressions and either modifies the ndoes in place or produces new TAST nodes.  
  These phases are orchestrated in [CompileOptions.fs](https://github.com/fsharp/FSharp.Compiler.Service/blob/master/src/fsharp/CompileOptions.fs)

* _Abstract IL code generation_, see 
  [IlxGen.fsi](https://github.com/fsharp/FSharp.Compiler.Service/blob/master/src/absil/IlxGen.fsi)/[IlxGen.fs](https://github.com/fsharp/FSharp.Compiler.Service/blob/master/src/absil/IlxGen.fs)
  Accepts TAST nodes and produces Abstract IL nodes.

* _Abstract IL rewriting_, see 
  [EraseClosures.fs](https://github.com/fsharp/FSharp.Compiler.Service/blob/master/src/absil/EraseClosures.fs) and
  [EraseUnions.fs](https://github.com/fsharp/FSharp.Compiler.Service/blob/master/src/absil/EraseUnions.fs).
  Eliminates some constructs by rewriting Abstract IL nodes.
  
* _Abstract IL to .NET binary_, see 
  [ilwrite.fsi](https://github.com/fsharp/FSharp.Compiler.Service/blob/master/src/absil/ilwrite.fsi)/[ilwrite.fs](https://github.com/fsharp/FSharp.Compiler.Service/blob/master/src/absil/ilwrite.fs). 

* _Abstract IL to .NET Reflection-Emit_, see 
  [ilreflect.fs](https://github.com/fsharp/FSharp.Compiler.Service/blob/master/src/absil/ilreflect.fs). 

* _TAST information presented as a compiler service API_, see 
  [Symbols.fsi](https://github.com/fsharp/FSharp.Compiler.Service/blob/master/src/fsharp/vs/Symbols.fsi)/[fs](https://github.com/fsharp/FSharp.Compiler.Service/blob/master/src/fsharp/vs/Symbols.fs), 
  [service.fsi](https://github.com/fsharp/FSharp.Compiler.Service/blob/master/src/fsharp/vs/service.fsi)/[fs](https://github.com/fsharp/FSharp.Compiler.Service/blob/master/src/fsharp/vs/service.fs) 
  and related files.

* _The F# Interactive Shell_, see [fsi.fs](https://github.com/Microsoft/visualfsharp/blob/master/src/fsharp/fsi/fsi.fs) as a tool, and its presentation as an API  in fsi.fsi in FSharp.Compiler.Service.

* _The F# Compiler Shell_, see [fsc.fs](https://github.com/Microsoft/visualfsharp/blob/master/src/fsharp/fsi/fsc.fs) and [fscmain.fs](https://github.com/Microsoft/visualfsharp/blob/master/src/fsharp/fsi/fscmain.fs).


## Geneated Code Performance 

TBD - will discuss various aspects of generated code and the parts of the compiler responsible.

## Compiler Throughput Performance

TBD - discusses topics related to compiler performance including phase costs, data structures, GC settings etc.

## Compiler Startup Performance

TBD - discusses topics related to compiler startup performance, on both Windows/.NET and Linux/OSX/Mono.

## Compiler Memory Usage

Overall memory usage is a primary determinant of the usability of the F# compiler and instances of
the F# compiler serivce.  Overly high memory usage results in poor throughput (particularly due to increased GC times)
and low user interface responsivity in tools such as Visual Studio or other editing environments.

Overall memory usage depends considerably on scenario,phase adn configuration. Some key scenarios are:
* Overall memory usage of an instance of Visual Studio or another editing environment when editing F# projects
* Overall memory usage of the Visual F# Power Tools in Visual Studio when editing and refactoring F# projects
* Memory usage and throughput of the F# compiler fsc.exe
* Memory usage and throughput of the F# Interactive dynamic scripting compiler fsi.exe

Analyzing memory usage of the F# Compiler and instances of the F# Compiler Service can be done using tools such
as the Visual Studio Managed Memory analysis.  For example:

* To analyze memory usage of Visual Studio you can take a process minidump of the devenv.exe process in Task Manager, then open that .dmp file in another instance of Visual Studio.

```
Microsoft.FSharp.Compiler.AbstractIL.ILBinaryReader+MemChannel	24	20,035,696	20,035,696
Microsoft.FSharp.Compiler.Tast+ValData	14,456	3,674,796	16,367,960
Dictionary<Int32, String>	61	2,190,612	2,190,612
Microsoft.FSharp.Compiler.Tast+EntityData	16,018	1,890,800	148,685,060
Dictionary<String, String>	422	1,380,636	1,380,936  Microsoft.FSharp.Compiler.AbstractIL.Internal.Library+Tables+memoize@782<String, String>	24
Microsoft.FSharp.Compiler.Tast+EntityRef	75,237	1,203,792	58,333,884
Microsoft.FSharp.Compiler.Ast+Ident	48,158	1,098,292	1,098,292
Microsoft.FSharp.Quotations.ExprConstInfo+ValueOp	20,022	958,924	1,109,212
Microsoft.FSharp.Compiler.Tast+TType+TType_fun	46,048	920,960	3,227,144
Microsoft.FSharp.Collections.FSharpList<Microsoft.FSharp.Compiler.Tast+TType>	54,642	874,272	1,770,444
Lazy<Microsoft.FSharp.Collections.FSharpList<Microsoft.FSharp.Compiler.AbstractIL.IL+ILAttribute>>	24,596	784,648	16,151,812
Microsoft.FSharp.Compiler.Tast+TyparData	17,802	783,288	2,897,324
Func<Tuple<Microsoft.FSharp.Compiler.AbstractIL.IL+ILScopeRef, Microsoft.FSharp.Compiler.AbstractIL.IL+ILAttributes, Lazy<Microsoft.FSharp.Compiler.AbstractIL.IL+ILTypeDef>>>	24,420	781,440	1,855,856
Lazy<Tuple<Microsoft.FSharp.Compiler.AbstractIL.IL+ILScopeRef, Microsoft.FSharp.Compiler.AbstractIL.IL+ILAttributes, Lazy<Microsoft.FSharp.Compiler.AbstractIL.IL+ILTypeDef>>>	24,419	781,408	2,637,232
Microsoft.FSharp.Compiler.AbstractIL.IL+ILTypeDefs	6,719	780,648	4,938,452
Func<Microsoft.FSharp.Collections.FSharpList<Microsoft.FSharp.Compiler.AbstractIL.IL+ILAttribute>>	24,395	780,640	15,036,988
Microsoft.FSharp.Compiler.Tast+TyconAugmentation	16,018	704,792	5,896,576
Microsoft.FSharp.Compiler.AbstractIL.IL+ILTypeDef	6,694	604,956	19,695,560
Microsoft.FSharp.Compiler.Tast+PublicPath	15,993	592,944	592,944
Microsoft.FSharp.Collections.FSharpList<Microsoft.FSharp.Quotations.FSharpExpr>	36,972	591,552	17,080,672
Microsoft.FSharp.Quotations.FSharpExpr	33,804	540,864	23,174,608
Microsoft.FSharp.Compiler.Import+lazyModuleOrNamespaceTypeForNestedTypes@400	12,698	507,920	507,920
Lazy<Microsoft.FSharp.Compiler.Tast+ModuleOrNamespaceType>	16,018	501,008	100,136,884
Microsoft.FSharp.Compiler.Tast+TType+TType_app	24,773	495,460	18,247,412
Tuple<Microsoft.FSharp.Compiler.AbstractIL.IL+ILScopeRef, Microsoft.FSharp.Compiler.AbstractIL.IL+ILAttributes, Lazy<Microsoft.FSharp.Compiler.AbstractIL.IL+ILTypeDef>>	24,419	488,380	488,380
Microsoft.FSharp.Compiler.AbstractIL.ILBinaryReader+seekReadCustomAttrs@2627	24,394	487,880	13,963,620
Microsoft.FSharp.Compiler.Tast+ValLinkagePartialKey	20,072	483,760	706,624
Func<Microsoft.FSharp.Compiler.Tast+ModuleOrNamespaceType>	15,055	481,760	11,734,608
Microsoft.FSharp.Compiler.AbstractIL.IL+ILTypeRef	12,816	458,996	1,291,104
List<Tuple<Boolean, Microsoft.FSharp.Compiler.Tast+ValRef>>	16,018	424,816	504,432
Microsoft.FSharp.Core.FSharpOption<String>	24,638	400,436	400,436
Microsoft.FSharp.Compiler.Ast+XmlDoc	16,312	391,680	391,680
Microsoft.FSharp.Collections.FSharpList<Tuple<Microsoft.FSharp.Collections.FSharpList<String>, Tuple<String, Lazy<Tuple<Microsoft.FSharp.Compiler.AbstractIL.IL+ILScopeRef, Microsoft.FSharp.Compiler.AbstractIL.IL+ILAttributes, Lazy<Microsoft.FSharp.Compiler.AbstractIL.IL+ILTypeDef>>>>>>	24,420	390,720	3,809,360
Tuple<String, Lazy<Tuple<Microsoft.FSharp.Compiler.AbstractIL.IL+ILScopeRef, Microsoft.FSharp.Compiler.AbstractIL.IL+ILAttributes, Lazy<Microsoft.FSharp.Compiler.AbstractIL.IL+ILTypeDef>>>>	24,419	390,704	3,027,936
Tuple<Microsoft.FSharp.Collections.FSharpList<String>, Tuple<String, Lazy<Tuple<Microsoft.FSharp.Compiler.AbstractIL.IL+ILScopeRef, Microsoft.FSharp.Compiler.AbstractIL.IL+ILAttributes, Lazy<Microsoft.FSharp.Compiler.AbstractIL.IL+ILTypeDef>>>>>	24,419	390,704	3,418,640
Microsoft.FSharp.Compiler.Tast+Attrib	9,692	387,680	9,324,424
Microsoft.FSharp.Collections.FSharpList<Microsoft.FSharp.Compiler.Tast+ArgReprInfo>	23,958	383,328	1,964,996
Lazy<Microsoft.FSharp.Compiler.AbstractIL.IL+ILTypeDef>	14,131	371,864	898,412
```



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


