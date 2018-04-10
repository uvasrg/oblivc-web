+++
title = "Obliv-C - Documentation"
draft = false
date = "2017-07-06T16:05:58-04:00"
notoc = "True"
nodate = "True"
nopaging = "True"
+++

#### About this Documentation

This documention is provided to make the Obliv-C API simpler for users to understand in order to write programs in Obliv-C successfully, and uses  an informal tone to achieve this purpose. For a more formal introduction to Obliv-C, read [_Obliv-C: A Language for Extensible
Data-Oblivious Computation_](http://eprint.iacr.org/2015/1153.pdf).  For getting started quickly programming with Obliv-C, we recommend using the [tutorial](../tutorial).

> <font size="-1">This documentation is generated from https://github.com/uvasrg/oblivc-web/; please submit an Issue or PR if you have suggestions on improving it.</font>

# Introduction

Obliv-C was designed to provide a convenient bridge between users of secure computation protocols and protocol developers. Programmers who want to use secure computation, but are not experts in cryptography, should be able to use it easily and securely. On the other hand, experts should be able to provide extra primitives as simple library functions without having to invent a full language/compiler from scratch. This is intended to rectify the current situation where a new language needs to be invented every time someone decides to develop a new cryptographic protocol (e.g., ORAM for random access, private set intersection protocols, or protocols for other adversarial models). This way researchers can test new cryptographic techniques without having to be experts in language design/compilers.

The way this language is supposed to be used is as follows: we start with a code written in this Obliv-C, a strict extension of standard C. All parties agree to execute this program in a secure execution protocol, where most inputs are private, and only the output of the computation are to be revealed to all (or some) parties. The intermediate results stay hidden, protected by the underlying cryptographic protocols. 

## Oblivious Types

### The `obliv` qualifier

The `obliv` qualifier should be declared on any variable that is
supposed to depend on unknown inputs in any way; `obliv`-qualified
values implement secure encryption on the variable, and can only be
declared in an Obliv-C file (`.oc`).  

Supported data types for the `obliv` qualifier are `bool`, `int`,
`char`, `short`, `long`, `long long`, and `float`. Non-obliv values
can be implicitly converted into `obliv`, but `obliv` values cannot be
implicitly converted to non-obliv. Doing this requires
`revealObliv<Tname>()` (described below) to be called to indicate that
previously-oblivious data is now being revealed.

## Control Structures

For oblivious execution, program control can never depend on `obliv` values, which are not (semantically) accessible to the execution. In general, we cannot hide the length of time a protocol takes to run, and in the same vein, the number of iterations of a loop is also known to all parties (since they are all executing the protocol). Hence, Obliv-C will not allow an `obliv` variable to be ever used in the condition of any existing control structure (e.g., `for`, `while`, `if`, etc.). 

### Oblivious conditionals: `obliv if`

The new control structure ‘obliv if’ can be used with `obliv` values in the predicate. Its syntax is exactly like the normal if structure:
````
obliv if(...) {
  ...
} else obliv if(...) {
  ...
} else {
  ...
}
````

This control structure allows you to use `obliv` variables in if conditions, and works a lot like normal if blocks. 

However, while it allows you to use obliv variables as a condition, you must realize some obvious differences. For one, during execution, it is not known (to the system executing this program) if the condition is true or not --- that is the whole point of `obliv`-qualified variables. So the blocks will be actually executed _regardless_ of the condition: it doesn’t matter if the condition is true or not. So consider this:

````
obliv int x, y;
...
obliv if (x > 0) y = 4;
````

The body of the `obliv if` will be executed regardless of whether `x` is positive or not. However, none of the parties would know if their execution actually changed the value of `y` or not. If `x` was positive, `y` is now 4 after this is executed. If not, `y` still has the same value as before. But the actual value of `y` is stored encrypted in the circuit, and unknown to the executions. The assignment operation simply has no impact on the semantic value of `y` if the condition is false.

While `obliv if` allows you to use `obliv` variables in a condition, it does come with its own set of restrictions. For example, this is not allowed:
````
int x=0;
obliv int y;
obliv if (y > 0) x = 1; // error x is not obliv
````
In general, inside a given `obliv if` context, the program cannot modify any non-obliv variable that was declared outside the block, since that would betray the value of obliv condition. 

If you find yourself wanting to do this, you probably want `x` to be
an `obliv int` in the first place. (If you really want to do this, you
can escape the type system using `~obliv` to create an unconditional
segment.)

However, this is legal:
````
obliv if (...) {
  for (int i = 0; i < n; i++) { … }
}
````
Even though both `i` and `n` are non-obliv, this is okay, since `i` was declared inside the `obliv if` block and `n` is never modified. The requirement is that any changes made to non-obliv variables while inside an oblivious conditional scope are never visible from the outside.

Control statements like `break`, `continue`, `return`, `goto`, and
case labels, etc. cannot be used to jump across `obliv if` boundaries. Jump-like statements can only move control flow within the same `obliv if` block.

## Functions

Consider the following code:
````
int x;
void func() { x++; }
…
void protocolMain() {
  obliv if (...) 
    func(); // Not valid code!
}
````
This is obviously problematic, since it would violate the restrictions on `obliv if`: we are modifying a global, non-obliv variable `x` inside an oblivious context. 

To accommodate function calls in oblivious contexts, Obliv-C introduces `obliv` functions. We now have two kinds of functions:
````
void func() { … }
void func2() obliv { … }
````
The difference is simple: you are allowed to perform any operation inside a normal (non-obliv) function (`func()`), but it may not be invoked from inside an oblivious context (which includes any `obliv if` block, as well as inside other `obliv` functions). 

On the other hand, `func2` is an `obliv` function --- it can be called from anywhere, but may not modify any non-obliv variables not declared inside the function.

There is one more issue related to calling functions that comes up when passing parameters by reference. Consider this:

```
void ofunc(int* p1, const int* p2) obliv { … }
```

When this function is invoked, there are restrictions on what `p1` can point to. For example, this is problematic:

```
int n, m;
obliv if (...) { ofunc(&n,&m); } // error
obliv if (...) { int i; ofunc(&i,&m); } // ok
```

When compiling, the compiler sees `n` being passed by reference. So it assumes that `ofunc` may be used to modify `n`. The variable `i` is declared inside, so the second invocation is okay, since i can be modified in this scope. Non-obliv variables declared outside becomes something like a `const` variable when entering an oblivious scope. We say “something like a const” because there is a slight difference between C’s notion of `const`, and our notion here of a `frozen` variable.

## `frozen`

For simple variables, `frozen` may be implicitly converted to `const`: `frozen int*` can be used whenever a function really expects `const int*`. The difference, however, shows up in case of structs, where they are not implicitly convertible. Here, `frozen struct foo*` refers to the “deep-const” version of `foo`. 

Consider the following code fragment:
````
struct S { int x, *p; };
void func(const struct S* a, frozen struct S* b) {
  // Neither a->x nor b->x can be modified here, as expected
  a->x = 5;  // error
  b->x = 5;  // error
  // But *b->p is different from *a->p
  *a->p = 5; // ok: a->p has type int *const,
             //   as opposed to const int*
  *b->p = 5; // still error; frozen is recursively applied 
             //   to all pointer fields in a struct/union
}
````
This slight difference is the reason Obliv-C introducing the new keyword `frozen`. 

In general, any non-obliv variable becomes frozen-qualified when entering an oblivious scope. For simpler types, this distinction between `const` and `frozen` does not matter. But for structs, unions, pointer-to-pointers, etc., it does. For example, `int **frozen` is actually the same as `const int *const *const`, which is different from `const int**` or `int **const`.

## Unconditional Segments

This is the one part of Obliv-C that looks very different from anything in C. Essentially, it chalks out a block of code for _unconditional_ execution even though the surrounding code is conditional on some oblivious data.

For example,
````
int n = …;
obliv if (cond) { // line 1
  n = 5; // error: cannot modify outer non-obliv n in obliv scope
  ~obliv(c) {
    n = 5; // valid: but the assignment happens even if c is false
  }
}
````
Since the execution of the `~obliv` block does not depend on any obliv value, it gets rid of all restrictions on frozen variable modification. The restrictions can be later reinstated by using a nested obliv-if structure, if desired. 

However, remember that we are in an oblivious context, and the contents of the `~obliv` block will be executed regardless of the value of the condition. So, `n` will be modified inside the `~obliv` even if the condition is false! 

The value of the parameter `c` in `~obliv(c)` will hold the condition. The `~obliv(varname)` syntax declares a new variable, `varname` of type `obliv bool` that indicate whether the enclosing oblivious condition is true or false. 

This syntax is useful for library developers when they know that there is a more efficient way of executing obliv functions than what one can otherwise write. Many of the standard library functions (for example, ORAM designs) use this feature, so you can find useful examples of its use there. In general, when this is used, it is the responsibility of the programmer to make it look like nothing is being executed even when the condition is false (i.e., its execution should not have any visible non-obliv side effects if cond is false, or such side-effects should be constrained to implementation-internal variables only). On the other hand, neither should the programmer assume that it will always be executed even if the condition is false, since the compiler is allowed to optimize it away.  

The `~obliv` escapes the normal type system for Obliv-C, allowing programmers write code that could make no sense if you are not careful. But, it poses no security risk --- the value of the oblivious condition (and all obliv variables) is not known to the execution and protected by the protocol, so there is no risk of leaking oblivious data by using `~obliv`.


# API Documentation

Take a look through the [tutorial](../tutorial) to get started. In order to start running your
Obliv-C program between two parties, ensure that the following Obliv-C library
functions are called in order:
 
1. `int protocolAcceptTcp2P(ProtocolDesc* pd, const char* port)`
2. `int protocolConnectTcp2P(ProtocolDesc* pd, const char* server, const char* port)`
3. `void setCurrentParty(ProtocolDesc* pd, int party)`
4. `void execYaoProtocol(ProtocolDesc* pd, protocol_run start, void* arg)`
5. Any Obliv-C functions needed in your `.oc` files
6. `void cleanupProtocol(ProtocolDesc* pd)`

## C Functions

These functions should be called in a C file, and require using `#include <obliv.h>`.

### int protocolAcceptTcp2P(ProtocolDesc\* pd, const char\* port)

The first party calls this function to listen for a second party on
the supplied port parameter. Use conditional logic to ensure only the
first party calls this function.

### int protocolConnectTcp2P(ProtocolDesc\* pd, const char\* server, const char\* port)

The second party should call this function after the first party calls
`protocolAcceptTcp2P()`; use conditional logic to ensure this. Supply
either 'localhost', the IP address, or the DNS name of the first party
to the `const char* server` parameter.

### void setCurrentParty(ProtocolDesc\* pd, int party)

Sets the proper parties into the `ProtocolDesc`. Call this before
calling `execYaoProtocol()`.

### void execYaoProtocol(ProtocolDesc\* pd, protocol_run start, void\* arg)

Call this function in your C file's `main()` function to execute Yao's
garbled circuits protocol. Supply the starting function name (without
parentheses) in the Obliv-C file as the parameter for `protocol_run
start`. Supply any arguments for the Obliv-C starting function in
`void* arg`; pass in a struct to supply multiple parameters. Before
calling this function, establish a connection between parties with
`protocolAcceptTcp2P()` and `protocolConnectTcp2P()`, and call
`setCurrentParty()`.

### void cleanupProtocol(ProtocolDesc* pd)

Call this function in your C file after `execYaoProtocol()` has been
called. This function will clean up the `ProtocolDesc` object.

## Obliv-C Functions

These functions should be used in Obliv-C (`.oc`) files. They require `#include <obliv.oh>'.

Valid substitutions for _`tname`_ are `bool`, `char`, `int`, `short`,
`long`, `long long`, or `float`.  The value of _`<Tname>` is the
corresponding type name with its first letter capitalized. (As of
March 2018, the `obliv` qualifier is not supported for
double-precision floating point, however, [single-precision floating
point support is
available](https://github.com/samee/obliv-c/pull/30).)


### obliv _tname_ feedObliv<_Tname_>(_tname_ v, int party)

This function returns an `obliv`-qualified type from the specified
party to both parties.  For example, for `float`, there is:
```
   obliv float feedOblivFloat(float v, int party)
```

Sample code for using this function to feed an int array from the specified
party into an obliv int array to both parties:

```
void toObliv(int n, obliv int *oa, int *a, int party) {
  int i, res;
  for(i = 0; i < n; i++) {
    oa[i] = feedOblivInt(a[i], party);
  }
}
```

### name ocBroadcast<_Tname_>(_tname_ v, int source)

Returns _v_ from the specified source (party). This function allows
non-obliv data to be transferred across parties, similar to
`feedObliv<Tname>()`.

### bool revealObliv<_Tname_>(_tname_\* dest, obliv _tname_ src, int party)

Return value true indicated that `dest` was written to
successfully. `party` indicates which party will receive the revealed
obliv value; use `0` for all parties to receive the revealed value. It
is typical for this function to be called towards the end of a
protocol execution, revealing the output of the function that was
computed security to both parties. 
