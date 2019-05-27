---
layout: default
title:  Q & A
---

## Why was Ten created?
I'll be honest, I wrote Ten because it seemed like a fun project;
I was curious about how other scripting languages were implemented,
and wondered if I could create my own.  That's it.

But that doesn't mean Ten is just a 'toy language', it boasts some
powerful features not present in other languages targeting the same
niche (think Lua, Wren, etc.) and has some pretty neat aesthetics
designed for minimalism and consistency.

## Where should Ten be used?
There's no such thing as a one-size-fits-all programming language, or
if there is then I've never heard of it; and it certainly isn't Ten.

Ten takes the most essential features from modern scripting languages,
adds a few useful optimizations, and wraps it all up in a cool looking
functional-procedural syntax; all in a few thousand lines of standards
compliant C99 code.

Ten's portability and small memory footprint make it an ideal option not
only as an extension language for other applications; but as a scripting
language for resource constrained devices, like those found in many
IoT products or DIY projects.

The language's minimal and consistent syntax also make it a decent
option for new coders as the learning curve will be relatively shallow.

## How small is Ten?
At the time of this writing the core Ten implementation compiles to
the following sizes for the indicated platforms via GCC.

|    Platforms    |            Size         |
|:---------------:|:-----------------------:|
|   amd64-linux   |                   200Kb |
|   arm-none      |                   171Kb |
|   wasm          |                    27Kb |

Note that these sizes can be reduced somewhat by setting the `-Os`
optimization flag to optimize for size rather than performance.  This
reduces the `amd64-linux` build by about 30Kb.

## How fast is Ten?
Though not particularly fast as far as scripting languages go, Ten's
virtual machine is implemented as a bytecode interpreter, which makes
its performance comparable to Lua, Python, and other modern scripting
languages.  The limited benchmarking I've done shows Ten to be slightly
slower than the reference Lua implementation, though it also uses
significantly less memory than Lua.

I'm hoping that the performance gap between Ten and Lua will narrow as
Ten has a chance to mature and more optimizations are added to the
implementation.

## How efficient is Ten?
I haven't gotten around to doing any exact measurements regarding Ten's
memory use (separate from the memory used by its host process), but the
limited benchmarks I've done show that Ten is generally pretty efficient
with memory; most benchmarks show it to use significantly less memory
than the reference Lua implementation, with a memory footprint up to 5x
smaller than Lua's in one of the more extreme cases.
