+++
title = "Obliv-C Tutorial"
notoc = "True"
nodate = "True"
nopaging = "True"
+++

Obliv-C is a simple GCC wrapper that makes it easy to embed secure
computation protocols inside regular C programs. This tutorial will
explain how to set up and build your first Obliv-C program using a
privacy-preserving linear regression application as an example.

## Installation

#### 1. Install Dependencies

Before setting up Obliv-C, several other modules need to be installed. Here are the commands to do the installations, depending on your platform.

Ubuntu: 
```c
$ sudo apt install ocaml libgcrypt20-dev ocaml-findlib
```

Fedora: 

```c
$ sudo dnf install glibc-devel.i686 ocaml ocaml-ocamldoc ocaml-findlib ocaml-findlib-devel libgcrypt libgcrypt-devel perl-ExtUtils-MakeMaker perl-Data-Dumper
```

Mac OS (with Macports): 

```c
$ sudo port install gcc5 ocaml ocaml-findlib libgcrypt +devel
```

#### 2. Cloning Obliv-C

Clone (git) the [Obliv-C source repository](https://github.com/samee/obliv-c):

```html
$ git clone https://github.com/samee/obliv-c
```

#### 3. Build Obliv-C

Linux: 

```c
$ ./configure && make
```

Mac OS:

First, you need to install [MacPorts](https://guide.macports.org/chunked/installing.macports.html).  Then,

```html
$ CC=/opt/local/bin/gcc-mp-5 CPP=/opt/local/bin/cpp-mp-5 LIBRARY_PATH=/opt/local/lib ./configure && make
```

You're all set! The compiler is a GCC wrapper script found in
   `bin/oblivcc`.  Example programs are in `test/oblivc`.

## Understanding Obliv-C Programs

Here, we explain how an Obliv-C program works in four steps. For the
purposes of this tutorial, we will be using a sample linear regression
implementation in Obliv-C, [found
here](https://github.com/samee/obliv-c/tree/obliv-c/test/oblivc/linreg). This
example is for instructional purposes, and does not take advantage of
all features of Obliv-C. (However, it does use the built-in support
for floating point numbers now provided by Obliv-C).

### 1. Connecting parties, storing arguments, and executing Yao's protocol

#### Storing Arguments to a Struct

Navigate to `main()` in 
[linreg.c](https://github.com/samee/obliv-c/blob/obliv-c/test/oblivc/linreg/linreg.c); 
notice the `#include <obliv.h>` header needed to call
Obliv-C's built in functions. `ProtocolDesc pd` is initialized, which is used as
an argument for several essential functions. After storing commandline arguments
(hostname, TCP port, party, and data filename) to `protocolIO io` (a struct
defined in
[linreg.h](https://github.com/samee/obliv-c/blob/obliv-c/test/oblivc/linreg/linreg.h)), 
a connection must be established between the two parties.
_Using the struct is a convenient way to access and manipulate data between your
C and Obliv-C files._ 

#### Connecting Parties

```c
// code ...
const char *remote_host = strtok(argv[1], ":"); // parse hostname from cmdline
const char *port = strtok(NULL, ":"); // parse port name from cmdline
ProtocolDesc pd;
protocolIO io;
// code ...
log_info("Connecting to %s on port %s ...\n", remote_host, port);
if(argv[2][0] == '1') {
   if(protocolAcceptTcp2P(&pd,port)!=0) {
      log_err("TCP accept from %s failed\n", remote_host);
      exit(1);
   }
} else {
   if(protocolConnectTcp2P(&pd,remote_host,port)!=0) {
      log_err("TCP connect to %s failed\n", remote_host);
      exit(1);
   }
}
// code ...
cp = (argv[2][0]=='1'? 1 : 2);
setCurrentParty(&pd, cp); // only checks for a '1'
io.src = argv[3]; // filename
```

The first party calls `protocolAcceptTcp2P()` to listen for a connection (use of `log_err()` is specific to [dbg.h](https://github.com/samee/obliv-c/blob/obliv-c/test/oblivc/linreg/dbg.h)
and is not necessary to include in your own files for the purpose of making an Obliv-C 
program. The important takeaway from the above code snippet is that the accept and connect 
functions were called by the appropriate parties). Then, the second party calls 
`protocolConnectTcp2P()`, supplying the hostname from the commandline argument and 
connecting to the first party. After a connection is made, `setCurrentParty()`
is called, which allows the `ProtocolDesc pd` to keep track of parties. 

#### Executing Yao's Protocol

```c
// Execute Yao protocol and cleanup
execYaoProtocol(&pd, linReg, &io);
cleanupProtocol(&pd);
```
Now, `execYaoProtocol()` is called; this function begins linReg.oc's code at the supplied function name: linReg().

### 2. Loading local data and sharing obliv qualified data

Loading local data and obtaining protocolIO struct values in Obliv-C: 
Navigate to [linreg.oc](https://github.com/samee/obliv-c/blob/obliv-c/test/oblivc/linreg/linreg.oc)
and look at the `void linReg()` function; this function was called by 
`execYaoProtocol()` at the end of step 1.

```c
protocolIO *io = (protocolIO*) args;

int *x = malloc(sizeof(int) * ALLOC);
int *y = malloc(sizeof(int) * ALLOC);
// code ...
load_data(io, &x, &y, ocCurrentParty());
```

Notice that the struct used for accessing variables across C and Obliv-C files (`protocolIO io`)
 is obtained from the call to `linReg()` by `execYaoProtocol()`. 
To load the data points (the private input) from local files for each party, two 
int arrays were created with `ALLOC` being an initial size defined in 
[linreg.h](https://github.com/samee/obliv-c/blob/obliv-c/test/oblivc/linreg/linreg.h). 
The `load_data()` function from 
[linreg.c](https://github.com/samee/obliv-c/blob/obliv-c/test/oblivc/linreg/linreg.c)
 is called, with the int arrays created in the Obliv-C file being passed in as parameters. 
_It is important to note that calls can be made to C file functions even while being 
in Yao's protocol and running code in an Obliv-C file_.
 The majority of the `load_data()` function is comprised of regular C procedures, 
with the exception of this code snippet:

```c
float a_float = (float) a;
if (party == 1) {
   *(*x + io->n - 1) =  a_float; // messy, but needed for dereferencing 
} else if (party == 2) {
   *(*y + io->n - 1) =  a_float;
}
```

#### Sharing `obliv` data

Now that the data for each party is stored locally in float arrays, the 
data must be made `obliv` qualified and then shared with the other party 
as private data:

```c
// code ...
check_input_count(io);

obliv float *ox = malloc(sizeof(obliv float) * io->n);
obliv float *oy = malloc(sizeof(obliv float) * io->n);
    
toObliv(io, ox, x, 1);
toObliv(io, oy, y, 2);
```
Two new float arrays that are `obliv` qualified are created, with size n 
known since it was stored to `io->n` after loading the local data 
(notice the call to `check_input_count()` which ensures both parties sent 
the same amount of data using the `ocBroadcastFloat()` function). Then, `toObliv()`
 is called:

```c
// code ...
void toObliv(protocolIO *io, obliv float *oa, float *a, int party) 
{
    for(int i = 0; i < io->n; i++) {
        oa[i] = feedOblivFloat(a[i], party);
    }
}
```

`feedOblivFloat()` (see [documentation](../documentation)) allows both parties to 
share their data over their network connection as `obliv` qualified values. 
The loop in `toObliv()` goes through each value in each party's respective array.
You may notice that while both parties call `toObliv()` twice, the party from which the 
local float array is selected is hardcoded into the function call, so as to prevent party 1 
from trying to convert its own y array data (which are all 0 because nothing was ever stored 
to them) into `obliv` values, and vice versa.

### 3. Computing linear regression

#### Computing the function of two parties (linear regression)

Our data has been shared obliviously into two arrays, and we are now ready to 
perform our joint function on our private data (linear regression):

```c
// code ...
int n = io->n;
obliv float sumx = sum(ox, n); // sum of x
obliv float sumxx = dotProd(ox, ox, n); // sum of each x squared
obliv float sumy = sum(oy, n); // sum of y
obliv float sumyy = dotProd(oy, oy, n); // sum of each y squared
obliv float sumxy = dotProd(ox, oy, n); // sum of each x * y

// Compute least-squares best fit straight line
om = (n * sumxy - (sumx * sumy)) / (n * sumxx - osqr(sumx)); // slope
ob = ((sumy * sumxx) - (sumx * sumxy)) / (n * sumxx - osqr(sumx)); // y-intercept

obliv float ocov = (n * sumxy) - (sumx * sumy);
obliv float ostddevs = ((n * sumxx) - osqr(sumx)) * ((n * sumyy) - osqr(sumy));
obliv float orsqr = osqr(ocov) * ostddevs; // sqrt(revealed rsqr) = r
```

First, notice the `sum()` and `dotProd()` functions called, which allow us to 
obtain summations that we need in order to compute our three results: om (obliv slope), 
ob (obliv y-intercept), and orsqr (obliv correlation squared). We can then use these 
summations to calculate our results and reveal them to the users.

### 4. Revealing results, cleaning up

#### Revealing Results

With our values for om, ob, and orsqr obtained, we can reveal them:

```c
revealOblivFloat(&io->rsqr, orsqr, 0);
revealOblivFloat(&io->m, om, 0);
revealOblivFloat(&io->b, ob, 0);
```

See the [documentation](../documentation) for `revealOblivFloat()`. 
The first parameter is the location where the non-obliv result value is 
stored (`protocolIO io` is used here, as it is our struct for accessing data on 
either our C or Obliv-C files). The second parameter is our `obliv` results that will 
have their `obliv` qualifier removed when they are stored to the destination (`io` struct).
 Lastly, '0' specifies that all parties will receive the result, instead of '1' 
for party 1 or '2' for party 2 receiving the results. 

#### Cleaning up, recording runtime information

Having stored the results as non-obliv floats in the io struct, we are now ready 
to leave Yao's protocol and print our results to the Terminal. 
After freeing our float arrays and obliv float arrays, `linReg()` finishes, 
and as a result `execYaoProtocol()`, from 
[linreg.c](https://github.com/samee/obliv-c/blob/obliv-c/test/oblivc/linreg/linreg.c),
completes execution as well.

```c
// Execute Yao's protocol and cleanup
execYaoProtocol(&pd, linReg, &io);
cleanupProtocol(&pd);
// code ...

log_info("Slope   \tm = %15.6e\n", io.m); // print slope
log_info("y-intercept\tb = %15.6e\n", io.b); // print y-intercept
log_info("Correlation\tr = %15.6e\n", sqrt(io.rsqr)); // print correlation
```

To conclude, `cleanupProtocol()` is called;
see the documentation for this function, as it is necessary and serves a similar purpose to use of `free()`
after calling `malloc()` on a given variable. 
(Another useful built in function 
to Obliv-C is `yaoGateCount()`, which measures the amount of gates needed for 
executing Yao's protocol). 
Finally, we are ready to print our results from the linear regression.
Note: as noted in step 1, `log_info()` is specific to 
[dbg.h](https://github.com/samee/obliv-c/blob/obliv-c/test/oblivc/linreg/dbg.h)
and it is not necessary to include this function in your own Obliv-C programs (`printf()` works just fine). 


## Conclusion

We hope that this tutorial of how to implement linear regression
 in Obliv-C will be a useful guide for starting to write
your own Obliv-C programs!

Please let us know if you have any suggestions for improving Obliv-C
or this tutorial.  We also welcome issues and pull requests to the
[Obliv-C repository](https://github.com/samee/obliv-c) and deeply appreciate hearing
about your experiences building applications with Obliv-C.
