# Introduction

Clasp is an implementation of Common Lisp primarily designed for compatibility with C++-language programs and libraries.

Note that Clasp is a work in progress, as is this manual.

## Conformance

Clasp conforms with the requirements of ANSI INCITS 226-1994 (R2004) with some exceptions, listed below in the "Lisp" section. Any deviation from the standard not listed there is a bug, and should be reported (see "Contributing").

## Distribution

At present, the developers do not make Clasp binaries available. Clasp is open source, and available on-line at https://github.com/clasp-developers/clasp.

## Building

TODO

## Contributing

The developers welcome bug reports and patches. Both should ideally be mediated through the GitHub interface at https://github.com/clasp-developers/clasp.

Development for Clasp is mostly carried out on Freenode's #clasp channel.

## Credits

Clasp is the project of Dr. Christian Schafmeister. Additional contributions have been made by Alex Wood, Karsten Poeck, and many other contributors as seen in the Git history.

Clasp's source code is derived substantially from that of Embeddable Common Lisp. Code from SBCL and SICL has been incorporated as well. Most notably, the compiler is SICL's Cleavir compiler with some minor customizations.

# Starting and Stopping

TODO

# Lisp

As mentioned, Clasp is an implementation of ANSI Common Lisp. The following subsections detail intentional deviations from the standard, some extensions to standard functionality, and notes on optimizing Lisp programs to run well within Clasp. (Extensions not closely related to standard systems are explained in their own sections below.)

## Evaluation and Compilation

Clasp expands compiler macros essentially unconditionally. That is, provided the operator is not declared `notinline`, the compiler will attempt to expand compiler macros. If compiler macroexpansion signals, this signal will be recorded and reported by the compiler. If compiler macroexpansion errors out, the compiler will abandon expansion and use the unexpanded form, while noting the error for display.

[We have a symbol macro function accessor, but I don't think it will work for non-global symbol macros, so it can't exactly be documented]

## Types and Classes

`typep` calls can be efficiently compiled only if the second argument, the type specifier, is constant. Clasp's compiler will process the type specifier and generate code to check for objects of that type directly, avoiding the usual runtime cost of interpreting the type.

Types defined by `deftype` use a "type expander" function analogous to a macro expander function. A type expander is a function of two arguments, a type specifier and an environment. When called on an appropriate specifier and environment, it computes and returns another type specifier. Type expanders are accessible through the `ext:type-expander` accessor.

### Disjointness

Unless otherwise specified, types Clasp defines as extensions can be considered to be in a disjointness relationship with other types, as in CLHS 4.2.2 "Type Relationships". That is, if Clasp defines a type `foo`, you can assume that `foo` is not a subtype of `hash-table`, or `cons`, or so on, and vice versa, unless it is explicitly stated to be. But just as in 4.2.2, Clasp extension types may be subtypes of `structure-object` or `standard-object` without this being explicitly noted here.

## Data and Control Flow

[specialp and symbol-constantp aren't really regular enough to document]

[core:out-of-extent-unwind should perhaps be moved to ext]

`defsetf`, `define-setf-expander` etc. define a "setf expander" function analogous to a macro expander function. A setf expander is a function of two arguments, a place and an environment. When called an appropriate place and environment, the expander computes and returns the values used by `setf`. Setf expanders are accessible through the `ext:setf-expander` accessor.

## Iteration

`loop` supports iteration over general sequences (see below) through a for-as-sequence subclause. This is identical to the subclause in SBCL. The syntax is `being {each | the} {element | elements} {of | in}`. For example, `(loop for x being each element in '(1 2 3) do (print x))`.

## Objects

CLOS, as part of Common Lisp, is fully supported.

### Metaobject Protocol

The Metaobject Protocol, as described in AMOP [reference], is supported. Undocumented deviations from AMOP are bugs and should be reported, as with the CL standard.

Symbols relating to MOP are exported from the "CLOS" package.

### Generic function dispatch efficiency

Clasp uses a new system for generic function dispatch designed by Dr. Robert Strandh. [Paper reference goes here.] Essentially, after a few calls to a generic function, a just-in-time compiler will install a discriminating function for it that can pass control to the correct effective method very efficiently. This means that calls with arguments that all have the same specializers as those of a previous call will in general be more efficient.

For some applications, the specializers a function will be called with are known beforehand, and the runtime overhead of the just-in-time compilation would be unfortunate. Clasp defines an interface to take care of most of the compilation early: The `clos:satiate` function. See it's docstring for more info.

This system is still under development and will be improved further.

### Miscellany

A consequence of the dispatch method described above is that obsolete instances are updated as soon as they are used as an argument to any generic function call - not just to slot accessors. This is allowed by the standard, but may surprise some programmers.

## Structures

## Conditions

## Symbols

## Packages

Clasp supports package-local nicknames, through an interface based on that of SBCL's. A package-local nickname is a nickname for a package that is only active when some other package is in place. For example, if the package "FOO" has "B" as a package-local nickname for package "BAR", then while `*package*` is the `foo` package, the prefix "B:" will be read as if it was "BAR:".

Local nicknames may be specified in `defpackage` through the `(:local-nicknames (nickname package-name)*)` extended options. `nickname` must be a string designator and `package-name` a package designator - both are unevaluated. The functions `ext:package-local-nicknames`, `ext:add-package-local-nickname`, `ext:remove-package-local-nickname`, and `ext:package-locally-nicknamed-by-list` can be used for a more programmatic interface.

## Numbers

## Characters

Clasp supports Unicode by default. `code-char` and `char-code` work with Unicode codepoints.

Type `character` includes all characters in Unicode. Type `base-char` includes only single byte characters, i.e. Basic Latin and Latin-1 Supplement.

## Conses

## Arrays

In Clasp, arrays with no fill-pointer, displacement, or express adjustability are simple (as in `simple-array`), and arrays that have any of these are not. Additionally, Clasp implements multidimensional arrays - even ones that are simple in this sense - as if they were displaced to an underlying one dimensional array. As such, it is most efficient to work with one-dimensional simple arrays directly.

## Strings

## Sequences

### Extensible Sequences

[The extensible sequences protocol described by Chris Rhodes](http://www.doc.gold.ac.uk/~mas01cr/papers/ilc2007/sequences-20070301.pdf) [FIXME: Real citation] is supported. Symbols related to the protocol are external in Clasp's "SEQUENCE" package. This protocol allows programmers to define their own sequence classes that work efficiently with standard Common Lisp functions. It is recommended that programmers consult other resources, such as Dr. Rhodes' paper, for more information on how to use this protocol effectively.

To summarize: Programmers wishing to make a custom sequence class must ensure their class has `cl:sequence` as a superclass. (Note that `sequence` is itself abstract, so if a custom class needs to have e.g. slots, it should also be a subclass of `standard-object` or something like it.) Methods on `elt`, `(setf elt)`, `length` applicable to objects of the class must be defined for any sequence functions to work; an applicable method on `make-sequence-like` must be defined for creation of this sequence to work; and an applicable method on `adjust-sequence` must be defined for destructive operations to work. Standard sequence functions will then operate correctly with these sequences, as will `make-sequence` and `coerce`.

For efficiency, programmers may also define applicable methods on `make-sequence-iterator`, or less efficiently but more simply, on `make-simple-sequence-iterator`, `iterator-step`, `iterator-endp`, `iterator-element`, `(setf iterator-element)`, `iterator-index`, and `iterator-copy`.

Note that because the `sequence:` generic function cognates to `cl:` sequence functions are defined to have the same behavior in almost all cases, Clasp takes the view that they need not be called. For example, a call to `cl:find` with a custom sequence object _may_ result in a call to `sequence:find`, but may not. In other words the cognates are considered optional, and only possibly useful for optimization. This is still in flux. If you think it's a bad idea, contact a maintainer to talk.

As a small extension to the extension, if a custom sequence object does not implement enough of the protocol for a sequence function to complete, it will signal an error of type `sequence:protocol-unimplemented`. The reader `sequence:protocol-unimplemented-operation` can be used to get the name of the operation that failed from these conditions.

## Hash Tables

`make-hash-table` supports additional keyword arguments.

`:weakness` can be used to indicate that the garbage collection may collect individual hash table entries even when the hash table itself is live, in certain circumstances. At present, only weak-key hash tables are supported: when the weakness argument is `:key`, the hash table's reference to the key of a table entry is _weak_, and if there are no non-weak references to a key, it is collectable. See the "Garbage Collection" section below for more information on weak references. If the weakness parameter is passed as `nil`, or not passed, the hash table does not contain weak references.

`:thread-safe` can be used to make hash table access safe across multiple threads. If a thread-safe argument is not passed, or `nil` is passed, the hash table cannot safely be written to or read from multiple threads simultaneously (see "Memory Model", below, for a brief explanation of terminology). If the thread-safe argument is true, the implementation will ensure that accesses can be carried out from multiple threads simultaneously safely. This does impose a small performance penalty, which is why it is not the default.

## Filenames

## Files

## Streams

### Gray streams

The Gray stream interface as described in ANSI committee issue "STREAM-DEFINITION-BY-USER" (readable, e.g., [on Kent Pitman's website](http://www.nhplace.com/kent/CL/Issues/stream-definition-by-user.html)) is supported. Symbols are exported from package "GRAY". We recommend programmers use a multi-implementation compatibility layer such as [trivial-gray-streams](https://common-lisp.net/project/trivial-gray-streams/) rather than use Clasp's implementation directly.

Gray streams allow programmers to define their own stream classes with custom behavior that work with standard Common Lisp functions. It is recommended that programmers consult another resource, such as the trivial-gray-streams documentation, for more information on how to use this interface effectively.

## Printer

When `format`'s control string argument is constant, the compiler will process it early, so that the runtime doesn't have to. This improves runtime speed but increases code size.

## Reader

## System Construction

## Environment

# C++ Interface

TODO

# Foreign Function Interface

Clasp can interact with C programs and libraries through its Foreign Function Interface (FFI). Symbols relating to this interface are external in package "CLASP-FFI". However, it is recommended for most applications that you use a cross-implementation wrapper layer, specifically [CFFI](https://common-lisp.net/project/cffi/).

TODO

# Debugger

TODO

# Multiprocessing

Multiprocessing is supported. Symbols relating to multiprocessing are exported from the "MP" package.

## Processes

A process is a Lisp object representing a distinct thread of execution. Each process evaluates a call to a Lisp function, and exits when that call would finally return values. Processes have names for debugging purposes. Processes are "enabled" if they have begun evaluating, "suspended" if that evaluation has been paused by `process-suspend`, and otherwise "active".

Processes have type `process`. `make-process` creates a new process but does not enable it. `process-enable` enables a process, and `process-run-function` both creates and enables a process. The name of a process can be retrieved with `process-name`. `process-active-p` can be used to query whether a process is active. `process-suspend`, `process-resume`, `interrupt-process`, and `process-kill` interfere with a process's evaluation. `process-join` waits until a process until it completes, and then returns the values its function returned. `all-processes` gets a list of all enabled processes. The variable `*current-process*` is bound in any process to that process. Consult the docstrings of these functions for more information.

## Special variables

Bindings of special variables (by `let`, `progv`, lambda lists, etc.) are thread-local. That is, executing a binding form for a variable will not affect that variable's value in other threads. The global value - from `symbol-value` - is, in contrast, shared between threads.

## Mutexes

A mutex (short for "MUTual EXclusion"), or lock, can be used to control access to a shared resource by multiple processes.

Mutexes have type `mutex`. A mutex is created with `make-lock`, or `make-recursive-mutex` for a recursive mutex. `get-lock` and `giveup-lock` obtain and release exclusion on a mutex, respectively. `mutex-name` retrieves any name of a mutex given at creation.

## Shared Mutexes

TODO

## Condition Variables

TODO

## Memory Model

Clasp does not have a formal memory model. Here is a sketch of one: Two accesses of a place are `concurrent` if they take place in different threads and are not excluded from running simultaneously by locks. Two concurrent accesses `conflict` if at least one is a write. If a conflicting access is `not atomic` the program has undefined behavior (e.g. tearing).

Some accesses are atomic but `unordered`, meaning that there is not necessarily a modification order to the place that is observed by all threads, e.g. one thread may see writes occur in a different order from another thread. Some accesses are `sequentially consistent`, meaning that there is such a globally observable modification order, and furthermore that all sequentially consistent accesses have a globally observable order.

Places may be complex, indeed completely custom. Clasp defines atomicity of some simple places; other places, and more complex modification operations, should hopefully be understandable from those. For example, to setf the `second` of a list, one `cdr` must be read before a `car` is written, and each of these individual accesses is atomic while the overall access is not.

In general Clasp tries to guarantee unordered atomicity, but does not always succeed.

Note that in this context "atomic" does not mean "lock-free".

Places that can be accessed unorderedly are: `car`, `cdr`, `symbol-value`, `symbol-plist`, `symbol-function`/`fdefinition`, `compiler-macro-function`, `ext:setf-expander`, `ext:type-expander`. Access to the elements of simple one-dimensional arrays should be unordered, except for integer element types smaller than `(unsigned-byte 8)`. Access to `standard-object` and `structure-object` slots (regardless of allocation) should also be unordered.

## Compare-and-swap

The `mp:cas` macro can be used to execute an atomic, sequentially consistent compare-and-swap of certain places. See its docstring for more information. `define-cas-expander` and `get-cas-expansion` are available to expand this mechanism analogously to `setf`. Uses of this operator as `atomic-update`, `atomic-incf`, and `atomic-decf` are also defined. Documentation strings for individual places' CAS properties can be obtained as having doctype `mp:cas`, so e.g. `(documentation 'symbol-value 'mp:cas)`.

# Introspection

Information about objects is stored and accessible. This is primarily intended for editor integration, but the functions can be used in any context. It is not recommended that they be used for purposes other than human understanding, however - it can sometimes be dropped or inaccurate. These mechanisms are similar to the standard `documentation` function.

`ext:function-lambda-list` can be used to get the lambda list of a function object, and `ext:compiled-function-name` its name.

`ext:source-location` returns a list of source locations for a symbol or object. Source locations are of type `ext:source-location`; they contain a pathname accessible with `ext:source-location-pathname`, and a file offset (as from `file-position`) accessible with `ext:source-location-offset`.

# Sockets

A low level networking API based on SBCL's (which is in turn based on BSD sockets) is available in package "SB-BSD-SOCKETS".

TODO

# Serve Event

TODO

# Garbage Collection

Symbols related to garbage collection are exported from the "GCTOOLS" package.

The function `garbage-collect` forces a garbage collection.

`finalize` registers a finalizer function for an object. When the object is collected, the function will be called with no arguments. Note that this function should not close over the object, because then the closure will keep that object alive indefinitely.