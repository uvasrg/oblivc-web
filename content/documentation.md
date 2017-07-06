+++
title = "Obliv-C - API Documentation"
draft = false
date = "2017-07-06T16:05:58-04:00"
notoc = "True"
nodate = "True"
nopaging = "True"
+++

## About this Documentation

The below documentation of Obliv-C protocols, functions, qualifiers, and other
aspects of the language is by no means comprehensive for the Obliv-C language. 
It is included for the sole purpose of making the Obliv-C API simpler for users 
to understand in order to write programs in Obliv-C successfully, and uses 
an informal tone to achieve this purpose. See
this [language primer](http://goo.gl/TXzxD0) for information not covered in this documentation or in
the [tutorial](../tutorial).

## General Usage

### Use of tname

Valid substitutions for `tname` are bool, char, int, short, long, or long long,
depending on what data type you need. `<Tname>` means to substitute in a valid
type with the first character of the type capitalized. As of July 2017, the `obliv` qualifier is
not valid for float or double. Use fixed point arithmetic as a workaround for
these data types. However, [floating point support is
forthcoming](https://github.com/samee/obliv-c/pull/30).


### The obliv qualifier

The `obliv` qualifier should be declared on any variable that is supposed to
depend on unknown inputs in any way; `obliv` qualified values implement secure
encryption on the variable, and can only be declared in an Obliv-C file (.oc).
Supported data types for the `obliv` qualifier are bool, int, char, short, long,
and long long. Non-obliv values can be implicitly converted into obliv, but
obliv values cannot be implicitly converted to non-obliv. Doing this requires
`revealObliv<Tname>()` to be called.

### Where to begin

Take a look through the [tutorial](../tutorial) to get started. In order to start running your
Obliv-C program between two parties, ensure that the following Obliv-C library
functions are called in order:
 
1. int protocolAcceptTcp2P(ProtocolDesc\* pd, const char\* port)
2. int protocolConnectTcp2P(ProtocolDesc\* pd, const char\* server, const char\* port)
3. void setCurrentParty(ProtocolDesc\* pd, int party)
4. void execYaoProtocol(ProtocolDesc\* pd, protocol_run start, void\* arg)
5. Any Obliv-C functions needed in your .oc file(s)
6. void cleanupProtocol(ProtocolDesc\* pd)

## C Functions

### void cleanupProtocol(ProtocolDesc\* pd)

C file; requires `#include <obliv.h>`. Call this function in your C file after
execYaoProtocol() has been called. This function will clean up the ProtocolDesc.

### void execYaoProtocol(ProtocolDesc\* pd, protocol_run start, void\* arg)

C file; requires `#include <obliv.h>`. Call this function in your C file's main()
function to execute the Yao protocol. Supply the starting function name (without
parentheses) in the Obliv-C file as the parameter for `protocol_run start`. Supply
any arguments for the Obliv-C starting function in `void* arg`; pass in a struct
to supply multiple parameters. Before calling this function, establish a
connection between parties with protocolAcceptTcp2P() and
protocolConnectTcp2P(), and call setCurrentParty().

### int protocolAcceptTcp2P(ProtocolDesc\* pd, const char\* port)

C file; requires `#include <obliv.h>`. The first party calls this function to
listen for a second party on the supplied port parameter. Use conditional logic
to ensure only the first party calls this function.

### int protocolConnectTcp2P(ProtocolDesc\* pd, const char\* server, const char\* port)

C file; requires `#include <obliv.h>`. The second party should call this function
after the first party calls protocolAcceptTcp2P(); use conditional logic to
ensure this. Supply either 'localhost', the IP address, or the DNS name of the
first party to the `const char* server` parameter.

### void setCurrentParty(ProtocolDesc\* pd, int party)

C file; requires `#include <obliv.h>`. Sets the proper parties into the
ProtocolDesc. Call this before calling execYaoProtocol().

## Obliv-C Functions

### obliv tname feedObliv<Tname>(tname v, int party)

Obliv-C file; requires `#include <obliv.oh>`. This function returns an `obliv`
qualified `tname v` from the specified party to both parties. Floating point
numbers are not currently supported by the `obliv` qualifier. 

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

### name ocBroadcast<Tname>(tname v, int source)

Obliv-C file; requires `#include <obliv.oh>`. Returns v from the specified source
(party). This function allows non-obliv data to be transferred across parties,
similar to `feedObliv<Tname>()`.

### bool revealObliv<Tname>(tname\* dest, obliv tname src, int party)

Obliv-C file; requires `#include <obliv.oh>`. Return value true means `dest` was
written to. `party` indicates which party will receive the revealed obliv value;
enter 0 for all parties to receive the revealed value. It is typical for this
function to be called towards the end of a Yao protocol.
