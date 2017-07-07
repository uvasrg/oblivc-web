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
$ sudo apt-get install ocaml libgcrypt20-dev ocaml-findlib
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
$ CC=/opt/local/bin/gcc-mp-5 
$ CPP=/opt/local/bin/cpp-mp-5 
$ LIBRARY_PATH=/opt/local/lib 
$ ./configure && make
```

You're all set! The compiler is a GCC wrapper script found in
   `bin/oblivcc`.  Example programs are in `test/oblivc`.

## Understanding Obliv-C Programs

Here, we explain how an Obliv-C program works in four steps. For the
purposes of this tutorial, we will be using a sample linear regression
implementation in Obliv-C, [found
here](https://github.com/havron/obliv-c/tree/linReg/tutorial/olinReg). This
example is for instructional purposes, and does not take advantage of
all features of Obliv-C (notably, it does not use the built-in support
for floating point numbers now provided by Obliv-C).

### 1. Connecting parties, storing arguments, and executing Yao's protocol

#### Storing Arguments to a Struct

Navigate to `main()` in 
[linReg.c](https://github.com/havron/obliv-c/blob/linReg/tutorial/olinReg/linReg.c); 
notice the `#include <obliv.h>` header needed to call
Obliv-C's built in functions. `ProtocolDesc pd` is initialized, which is used as
an argument for several essential functions. After storing commandline arguments
(hostname, TCP port, party, and data filename) to `protocolIO io` (a struct
defined in
[linReg.h](https://github.com/havron/obliv-c/blob/linReg/tutorial/olinReg/linReg.h)), 
a connection must be established between the two parties.
_Using the struct is a convenient way to access and manipulate data between your
C and Obliv-C files._ 

#### Connecting Parties

```
// code ...
const char *remote_host = strtok(argv[1], ":"); // parse hostname from cmdline
const char *port = strtok(NULL, ":"); // parse port name from cmdline
ProtocolDesc pd;
// code ...
if(argv[2][0] == '1') { // if the cmdline argument for party is 1
  if(protocolAcceptTcp2P(&pd,port)!=0) { 
    log_err("TCP accept from %s failed\n", remote_host);
    exit(1);
  }
} else { // if the commandline argument for party is not 1
  if(protocolConnectTcp2P(&pd,remote_host,port)!=0) {
    log_err("TCP connect to %s failed\n", remote_host);
    exit(1);
  }
}
// code ...
currentParty = (argv[2][0]=='1'?1:2);
setCurrentParty(&pd, currentParty);
```

The first party calls `protocolAcceptTcp2P()` to listen for a connection (use of `log_err()` is specific to [dbg.h](https://github.com/havron/obliv-c/blob/linReg/tutorial/olinReg/dbg.h)
and is not necessary to include in your own files for the purpose of making an Obliv-C 
program. The important takeaway from the above code snippet is that the accept and connect 
functions were called by the appropriate parties). Then, the second party calls 
`protocolConnectTcp2P()`, supplying the hostname from the commandline argument and 
connecting to the first party. After a connection is made, `setCurrentParty()`
is called, which allows the `ProtocolDesc pd` to keep track of parties. 

#### Executing Yao's Protocol

```c
// Execute Yao protocol and cleanup
execYaoProtocol(&pd, linReg, &io); // starts 'linReg()'
cleanupProtocol(&pd);
```
Now, `execYaoProtocol()` is called; this function begins linReg.oc's code at the supplied function name: linReg().

### 2. Loading local data and sharing obliv qualified data

Loading local data and obtaining protocolIO struct values in Obliv-C: 
Navigate to [linReg.oc](https://github.com/havron/obliv-c/blob/linReg/tutorial/olinReg/linReg.oc)
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
[linReg.h](https://github.com/havron/obliv-c/blob/linReg/tutorial/olinReg/linReg.h). 
The `load_data()` function from 
[linReg.c](https://github.com/havron/obliv-c/blob/linReg/tutorial/olinReg/linReg.c)
 is called, with the int arrays created in the Obliv-C file being passed in as parameters. 
_It is important to note that calls can be made to C file functions even while being 
in Yao's protocol and running code in an Obliv-C file_.
 The majority of the `load_data()` function is comprised of regular C procedures, 
with the exception of this code snippet:

```c
int aint = a * SCALE;
assert(APPROX((double) DESCALE(aint), a));
if (party == 1) {
  *(*x + io->n - 1) =  aint; // messy, but needed for dereferencing 
} else if (party == 2) {
  *(*y + io->n - 1) =  aint;
}
```

As of July 2017, the `obliv` qualifier that we will need for the data to 
be shared with the other party does not accept the floating point number (`a`)
 that is being read in from the data file. Support for floating point numbers
 [is forthcoming](https://github.com/samee/obliv-c/pull/30), and will render this
part of the tutorial irrelevant (simply use the `obliv float` data type once it arrives). 
For now, the value stored in the int array is a scaled value of the float, 
such that the radix point is eliminated. Because of the scaling that is used on 
the numbers, data values of over 32,000 cannot be used as they cause integer 
overflow (a solution would be to use long ints or long long ints). 
The `assert()` statement checks for whether the data value exceeds 32,000 by 
comparing its approximate value with its actual value. After the value is scaled 
and checked for exceeding its boundary, it is stored in the appropriate array based 
on whether it comes from party 1's local file or party 2's. Although the int arrays 
x and y were declared for both parties, only party 1 will have stored data to its own 
local x int array, and vice versa (if you need to share the local data values 
non-obliviously, look at `ocBroadcast<Tname>()` in the [documentation](../documentation)). 

#### Sharing `obliv` data

 Now that the data for each party is stored locally in scaled int arrays, the 
data must be made `obliv` qualified and then shared with the other party 
as private data:

```c
// code ...
check_input_count(io);
  
obliv int *ox = malloc(sizeof(obliv int) * io->n);
obliv int *oy = malloc(sizeof(obliv int) * io->n);
  
toObliv(io, ox, x, 1);
toObliv(io, oy, y, 2);
```
Two new int arrays that are `obliv` qualified are created, with size n 
known since it was stored to `io->n` after loading the local data 
(notice the call to `check_input_count()` which ensures both parties sent 
the same amount of data using the `ocBroadcastInt()` function). Then, `toObliv()`
 is called:

```c
// code ...
void toObliv(protocolIO *io, obliv int *oa, int *a, int party) {
  int i, res;
  for(i = 0; i < io->n; i++) {
    oa[i] = feedOblivInt(a[i], party);
  }
}
```

`feedOblivInt()` (see [documentation](../documentation)) allows both parties to 
share their data over their network connection as `obliv` qualified values. 
The loop in `toObliv()` goes through each value in each party's respective array.
You may notice that while both parties call `toObliv()` twice, the party from which the 
local int array is selected is hardcoded into the function call, so as to prevent party 1 
from trying to convert its own y array data (which are all 0 because nothing was ever stored 
to them) into `obliv` values, and vice versa.

### 3. Computing linear regression and using fixed point math

#### Fixed point math usage

See [forthcoming floating point support](https://github.com/samee/obliv-c/pull/30)!

As mentioned in step 2, the data values that have been shared by each 
party as `obliv` qualified values were first scaled to be integers from their 
original float values when being read in from their data files using bit shifting 
(see [linReg.h](https://github.com/havron/obliv-c/blob/linReg/tutorial/olinReg/linReg.h) 
for `#define SCALE` and `#define DESCALE()`). This changes how multiplication and 
division can be done on scaled values; see `fixed_div()` and `fixed_mul()` 
in [linReg.oc](https://github.com/havron/obliv-c/blob/linReg/tutorial/olinReg/linReg.oc)
 to look at the fixes needed (essentially, temporary obliv long long ints had to be used 
in order to handle the large numbers that result from multiplying the already 
very high scaled ints, and then adjusting the scale appropriately). 

#### Computing the function of two parties (linear regression)

Our data has been shared obliviously into two arrays, and we are now ready to 
perform our joint function on our private data (linear regression):

```c
// code ...
int n = io->n;
obliv int sumx = sum(ox, n); // sum of x
obliv int sumxx = dotProd(ox, ox, n); // sum of each x squared
obliv int sumy = sum(oy, n); // sum of y
obliv int sumyy = dotProd(oy, oy, n); // sum of each y squared
obliv int sumxy = dotProd(ox, oy, n); // sum of each x * y

// Compute least-squares best fit straight line
om = fixed_div(n * sumxy - fixed_mul(sumx, sumy), n * sumxx - osqr(sumx)); // slope
ob = fixed_div(fixed_mul(sumy, sumxx) - fixed_mul(sumx, sumxy), n * sumxx - osqr(sumx)); // y-intercept

obliv int ocov = (n*sumxy - fixed_mul(sumx, sumy));
obliv int ostddevs = fixed_mul((n*sumxx) - osqr(sumx), (n*sumyy) - osqr(sumy));
obliv int orsqr = fixed_div(osqr(ocov), ostddevs); // sqrt(revealed rsqr) = r
```

First, notice the `sum()` and `dotProd()` functions called, which allow us to 
obtain summations that we need in order to compute our three results: om (obliv slope), 
ob (obliv y-intercept), and orsqr (obliv correlation squared). Then we can use those 
summations to calculate our results; note that all multiplication and division is 
adjusted for fixed point math. With the results now captured as `obliv int`s, 
we are ready to reveal them to the users and cleanup our protocol.

### 4. Revealing results, cleaning up

#### Revealing Results

With our values for om, ob, and orsqr obtained, we can reveal them:

```c
revealOblivInt(&io->rsqr, orsqr, 0);
revealOblivInt(&io->m, om, 0);
revealOblivInt(&io->b, ob, 0);
```

See the [documentation](../documentation) for `revealOblivInt()`. 
The first parameter is the location where the non-obliv result value is 
stored (`protocolIO io` is used here, as it is our struct for accessing data on 
either our C or Obliv-C files). The second parameter is our `obliv` results that will 
have their `obliv` qualifier removed when they are stored to the destination (`io` struct).
 Lastly, '0' specifies that all parties will receive the result, instead of '1' 
for party 1 or '2' for party 2 receiving the results. 

#### Cleaning up, recording runtime information

Having stored the results as non-obliv ints in the io struct, we are now ready 
to leave Yao's protocol and print our results to the Terminal. 
After freeing our int arrays and obliv int arrays, `linReg()` is finished, 
and as result `execYaoProtocol()` from 
[linReg.c](https://github.com/havron/obliv-c/blob/linReg/tutorial/olinReg/linReg.c)
is finished.

```c
execYaoProtocol(&pd, linReg, &io); // starts 'linReg()'
cleanupProtocol(&pd);
// code ...
/* WARNING (July 2017): yaoGateCount() is now defined 
    and must be implemented in linReg.oc and not here */
int gates = yaoGateCount();
// code ...
log_info("Slope   \tm = %15.6e\n", (double) DESCALE(io.m)); // print slope
log_info("y-intercept\tb = %15.6e\n", (double) DESCALE(io.b)); // print y-intercept
log_info("Correlation\tr = %15.6e\n", sqrt((double) DESCALE(io.rsqr))); // print correlation
```

As noted in step 1, `log_info()` is specific to 
[dbg.h](https://github.com/havron/obliv-c/blob/linReg/tutorial/olinReg/dbg.h)
 and is not necessary to include in your own files for the purpose of making an 
Obliv-C program (`printf()` works just fine). First, `cleanupProtocol()` is called;
 see the documentation for this function, it is necessary to be called just as `free()`
 needs to be called after `malloc()` on a given variable. A useful built in function 
to Obliv-C is `yaoGateCount()`, which measures the amount of gates needed for 
executing Yao's protocol. After printing some of this information, we are ready to 
print our results from the linear regression. Notice that `DESCALE()` is needed to 
scale the results back down to floats, and `sqrt()` is called on `rsqr()` to obtain 
r (there is currently no supported `osqrt()` function in Obliv-C, making it necessary 
to perform `sqrt()` outside of Yao's protocol. Although this is considered performing 
a computation on values whose reveal was intended to be final, no intermediate information 
can be reverse engineered as a result of calling `sqrt()` outside of Yao's protocol).

## Conclusion

We hope that using the walkthrough tutorial of the linear regression
implementation in Obliv-C will be a useful guide in starting to write
your own Obliv-C programs!

Please let us know if you have any suggestions for improving Obliv-C
or this tutorial.  We also welcome issues and pull requests to the
[Obliv-C repository](https://github.com/samee/obliv-c), and hearing
about your experiences building applications with Obliv-C.
