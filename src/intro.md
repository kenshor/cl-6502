## High-Level Emulation by Example

I started working on **cl-6502** to develop a better mental model of assembly
and how CPUs work. The project has evolved into a highly correct, concise 6502
emulator. Its nearest neighbors, and inspirations, are
[lib6502](http://piumarta.com/software/lib6502/) and
[py65](https://github.com/mnaberez/py65) which also make for pretty good
reading if you prefer Python or C to Lisp. :)

## The MOS 6502

The [MOS 6502](http://en.wikipedia.org/wiki/MOS_Technology_6502), as noted in the README,
is famous for its usage in:

* the [Apple II](http://en.wikipedia.org/wiki/Apple_II_series),
* the [Nintendo](http://en.wikipedia.org/wiki/Nintendo_Entertainment_System),
* the [Commodore 64](http://en.wikipedia.org/wiki/Commodore_64),
* the [BBC Micro](http://en.wikipedia.org/wiki/BBC_Micro),
* Bender, the Terminator, and elsewhere.

It was, therefore, a large component of the microcomputer boom in the 1980s and
helped usher in the Modern PC era we enjoy today. The 6502 also comes from a
time when assembly programming was necessary for fast, responsive programs.
Consequently, the assembly language was designed with the intent that it would
be used by humans at least as often as it would be generated by a compiler for
a higher-level language. This makes it ideal for education purposes especially
relative to the complex x86 assembly that is prevalent today.

## Explicit Goals

**cl-6502** does have some explicit technical goals:

* Code size excluding tests should be < 800 loc. (**0.9.5**: 762 lines)
* Able to run at 8 mhz or faster using a single core on an Intel Core CPU. (**0.9.5**: ~50 mhz)
* Cycle-accurate emulation suitable for use in a full NES emulator.
* Readable as a 6502 introduction for someone with or without a copy of [CLHS](http://www.lispworks.com/documentation/HyperSpec/).

## A Word on Performance

Performance is **not** an explicit goal of the **cl-6502** project. Currently,
it is *roughly* 15x slower than lib6502 and 100x faster than py65. This has less
to do with the choice of language than with my naivete about emulation techniques
and the emphasis on readable, safe code. For example, **cl-6502** raises an
exception upon encountering illegal opcodes while lib6502 prints a message and
continues.

## Why Lisp?

Common Lisp was chosen for several reasons:

* It is the language the [primary author](http://redlinernotes.com/) is most familiar with.
* It has strong support for [metaprogramming](http://lists.warhead.org.uk/pipermail/iwe/2005-July/000130.html).
* It supports several [paradigms](http://en.wikipedia.org/wiki/Programming_paradigm) and has good facilities to describe low-level behavior.
* It has [native code compilers](http://www.sbcl.org/) capable of generating fast code.

## Emulation Basics

Emulation isn't particularly different from any other kind of programming. You
need to store the state of the hardware you're emulating and provide functions
to transform that state based on user input to produce correct results.
In **cl-6502**'s case, that means:

* a CPU object,
* functions for assembly instructions and interrupts the CPU can execute,
* some RAM (represented as an array of bytes) to store the emulated program in,
* functions to make the CPU run a single instruction or run until the program halts.

More complete coverage of emulation, including whole-system emulation,
can be found in Victor Moya del Barrio's excellent
[Study of the Techniques for Emulation Programming](http://personals.ac.upc.edu/vmoya/docs/emuprog.pdf).

## Design Decisions

These are the current major design decisions in **cl-6502**:

* Compile-time macros for status register handling and addressing mode dispatch.
* Storage of opcode metadata and opcode lambdas in separate arrays.
* A naive interpreter loop rather than Context-Threaded Dispatch, JIT, etc.
* Ignore decimal mode support in ADC/SBC since the NES didn't have it.

If you get stumped by a macro, I'd encourage you to macroexpand one of its callsites. Don't be afraid to skim something and come back to it later, either.

With all that out of the way, let's dive in!
