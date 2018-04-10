+++
title = "Obliv-C - API Documentation"
draft = false
date = "2017-07-06T16:05:58-04:00"
notoc = "True"
nodate = "True"
nopaging = "True"
+++

## About this Documentation

This documention is provided to make the Obliv-C API simpler for users to understand in order to write programs in Obliv-C successfully, and uses  an informal tone to achieve this purpose. For getting started with Obliv-C, we recommend first using the [tutorial](../tutorial).

> (This documentation is generated from https://github.com/uvasrg/oblivc-web/edit/master/content/documentation.md; please submit an Issue or PR if you have suggestions on improving it.)

## General Usage

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

### Where to begin

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
