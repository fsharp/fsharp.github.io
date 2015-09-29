---
layout: default
title: F# Compiler Guide
subtitle: This technical guide discusses the F# Compiler.  
---

# F# Compiler Technical Guide (draft)

This guide discusses the F# Compiler source code and implementation from a technical point of view.  
For details on contributing to the F# compiler and core library, please see 
[Contributing to the F# Language and Compiler](http://fsharp.github.io/2014/06/18/fsharp-contributions.html).

This guide can be read in conjunction with either the
[Microsoft/visualfsharp](http://github.com/Microsoft/visualfsharp) repository, which is an upstream 
repository to [the open edition cross-platform packaging repository]((http://github.com/fsharp/fsharp)
and  [the F# Compiler Service]((http://github.com/fsharp/FSharp.Compiler.Service) 
maintained by [the F# Software Foundation](http://fsharp.org).

This guide is work-in-progress. Please help improve this guide by [editing it and submitting a
pull-request](https://github.com/fsharp/fsharp.github.io/blob/master/_posts/2015-09-29-fsharp-compiler-guide.md). 
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

* Tokens, see pars.fsy, lex.fsl, lexhelp.fs{i} and related files

* Abstract Syntax Tree (AST), see ast.fs, the untyped syntax tree resulting from parsing

* Typed Abstract Syntax Tree (TAST), see tast.fs and related files, the typed, bound syntax tree including both 
  type/module definitions and their backing expressions, resulting from type checking
  and the subject of successive phases of optimization and representation change.

* Type checking context/state, see for example TcState in CompileOps.fs and its constituent parts.  
  A set of tables representing the available names, assemblies etc. in scope during type checking, plus
  associated information.

* Abstract IL, the output of code generation

* .NET Binary (with added "pickled" F# Metadata resource), the final output of fsc.exe

* Incremental .NET reflection assembly emit, the incremental output of fsi.exe

* Corresonding structures in the FSharp.Compiler.Service API

## Key Phases

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
  [QuotationTranslator.fsi](https://github.com/fsharp/FSharp.Compiler.Service/blob/master/src/fsharp/QuotationTranslator.fsi)/
  [fs](https://github.com/fsharp/FSharp.Compiler.Service/blob/master/src/fsharp/QuotationTranslator.fs) and
  [QuotationPickler.fsi](https://github.com/fsharp/FSharp.Compiler.Service/blob/master/src/fsharp/QuotationPickler.fsi)/
  [fs](https://github.com/fsharp/FSharp.Compiler.Service/blob/master/src/fsharp/QuotationPickler.fs).
  Generates the stored information for F# quotation nodes, generated from the TAST expression structures of the
  F# compiler. Quotations are ultimately stored as binary data plus some added type references. "ReflectedDefinition" quotations
  are collected and stored in a single blob.

* _Optimization phases_, primarily the "Optimize" (peephole/inlining) and "Top Level Representation" (lambda lifting) phases,
  see 
  [Optimizer.fsi](https://github.com/fsharp/FSharp.Compiler.Service/blob/master/src/fsharp/Optimizer.fsi)/
  [Optimizer.fs](https://github.com/fsharp/FSharp.Compiler.Service/blob/master/src/fsharp/Optimizer.fs) and
  [InnerLambdasToTopLevelFuncs.fsi](https://github.com/fsharp/FSharp.Compiler.Service/blob/master/src/fsharp/InnerLambdasToTopLevelFuncs.fsi)/
  [fs](https://github.com/fsharp/FSharp.Compiler.Service/blob/master/src/fsharp/InnerLambdasToTopLevelFuncs.fs) and
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
  [Symbols.fs](https://github.com/fsharp/FSharp.Compiler.Service/blob/master/src/fsharp/vs/Symbols.fs)/[fsi](https://github.com/fsharp/FSharp.Compiler.Service/blob/master/src/fsharp/vs/Symbols.fsi), 
  [service.fs](https://github.com/fsharp/FSharp.Compiler.Service/blob/master/src/fsharp/vs/service.fs)/[fsi](https://github.com/fsharp/FSharp.Compiler.Service/blob/master/src/fsharp/vs/service.fsi) 
  and related files.

* _The F# Interactive Shell_, see [fsi.fs](https://github.com/Microsoft/visualfsharp/blob/master/src/fsharp/fsi/fsi.fs) as a tool, and its presentation as an API  in fsi.fsi in FSharp.Compiler.Service.

* _The F# Compiler Shell_, see fsc.fs and fscmain.fs


## Geneated Code Performance 

TBD - will discuss various aspects of generated code and the parts of the compiler responsible.

## Compiler Performance 

TBD - discusses topics related to compiler performance including phase costs, data structures, GC settings etc.

## Compiler Memory Usage

TBD - discusses topics related to compiler and compiler service memory usage including data structures.

## Didn't find what you want?

Please [add an issue](https://github.com/fsharp/fsharp.github.io/issues) outlining what technical information you were looking 
for.

### About Us

The F# Core Engineering Group is a working group associated with [The F# Software Foundation](http://fsharp.org).

Visit our [website](http://fsharp.github.io) and please continue all the great work on core F# engineering!

<br />
 
_First Published: 29 September 2015_

_The F# Core Engineering group_


