# Recitation 2 #

## Makefiles ##

Make is a UNIX utility that follows a blueprint you create for compiling
programs. Calling `make` will automatically search your current directory for a
file called "Makefile" and use it to call various compiler commands according to
the rules outlined therein. 

### Jae's myadd Makefile ###

Take Jae's Makefile piece by piece. It can be found in this git repository as
`sample-makefile`

```make
CC  = gcc
CXX = g++
```

Make has a some pre-configured rules for how to compile programs. For example it
knows how to specify files as arguments to a compiler. However, you should tell
it what compiler to use for C files and C++ files. Here, we set the
special make variables CC and CXX to gcc, the C-compiler, and g++, the c++
compiler.

```make
INCLUDES =

CFLAGS   = -g -Wall $(INCLUDES)
CXXFLAGS = -g -Wall $(INCLUDES)
```

Here we define our own variable, INCLUDES, which we can use for directories that
we wish to include at the compilation step. An example value for INCLUDES could
be `-I/home/jae/cs3157` which would tell the compiler to look in
/home/jae/cs3157 during the compilation step for missing header files and other
sorts of relevant files.

After defining INCLUDES, we define the flags that we want each compiler to be
run with. In this case we include the `-g` flag for debugging and `-Wall` flag
to display all warnings. Lastly, we reference our variable INCLUDES to add those
flags as well.

```make
LDFLAGS = -g
```

LDFLAGS are the flags that are appended to the compiler when using it for
linking. In this case we just want the debugging info to be included.

```make
LDLIBS =
```

LDLIBS will automatically be appended to the linker commands. These are flags
like `-lm` and function similarly to our INCLUDES variable but are added at a
different step. `m` denotes the math library.

That's about it for our variable declarations. The next step is to define
compile order and dependencies. The very first "target" or rule in your makefile
gets built when you type `make` in this case the first target is:

```make
main: main.o myadd.o
```

Note that we did not specify the linking rule, because make follows an implied
linking rule:

    $(CC) $(LDFLAGS) <all-dependent-.o-files> $(LDLIBS)

Also note that make assumes that main depends on main.o, so we could omit it:

    main: myadd.o 

Basically what this rule says is make should produce an executable called "main"
by linking myadd.o and main.o. This declares main.o and myadd.o as dependencies
of main, meaning that if any of the dependencies (or their dependencies) change 
between the last time this target was run, it should re-run the outdated targets
as well as this one.

The next target we declare is main.o:

```make
main.o: main.c myadd.h
```

This says that main.o depends on main.c (assumed) as well as myadd.h. See last
week's recitation notes to understand why main.o depends on myadd.h. We could
omit main.c as follows:

  main.o: myadd.h

Either way, we do not specify a rule here because make assumes the implicit
rule:

    $(CC) -c $(CFLAGS) <the-.c-file>

Lastly, we specify the target for myadd.o:

```make
myadd.o: myadd.c myadd.h
```

We'll include two phony targets. We tell make that they're "phony" so that it
doesn't attempt to use implicit rules or try to compile them. The first target
we make is "clean" which should remove all intermediate files. *Always include a
clean* so that `make clean` can be used to remove intermediate files like object
files, compiled code, etc. This should return your directory to just its source
code that can generate all the other files. *Be careful:* Using `rm -f` will not
prompt you to remove files. This is customary for `make clean` but it also means
if you make a mistake in designing your rule it could remove files that you
didn't want to. There is no "trash" in UNIX - they'll be gone forever.

Lastly, we define a phony "all" target that just depends on the main and clean
targets. This will always remove all intermediate files and compiled files,
forcing make to recompile everything when main is called.

```make
.PHONY: clean
clean:
        rm -f *.o a.out core main

.PHONY: all
all: clean main
```


The default action of certain signals is to cause a process to terminate and produce a core dump file, a disk file containing an image of the process's memory at the time of termination. This image can be used in a debugger (e.g., gdb(1)) to inspect the state of the program at the time that it terminated.



### Exercise ###

1. I go through the following steps on clic and get the following output.

```unix
˜/cs3157/lab1$ ls
    main.c  Makefile  myadd.c  myadd.h  README.txt

˜/cs3157/lab1$ make 
    gcc -g -Wall    -c -o main.o main.c
    gcc -g -Wall    -c -o myadd.o myadd.c
    gcc -g  main.o myadd.o   -o main

˜/cs3157/lab1$ touch myadd.h 

˜/cs3157/lab1$ make 
    gcc -g -Wall    -c -o main.o main.c
    gcc -g -Wall    -c -o myadd.o myadd.c
    gcc -g  main.o myadd.o   -o main
```

The Makefile in that same directory is shown here, with the build rules
removed.  Fill in the missing lines, specifying targets and
dependencies that are consistent with the CLIC lab session shown
above.

Fill in only targets and dependencies.  That is, do not write the gcc
commands that come below the target/dependency lines.  Rely on the
implicit rules that make knows by default.

```make
    CC  = gcc
    CXX = g++
    INCLUDES =
    CFLAGS   = -g -Wall $(INCLUDES)
    CXXFLAGS = -g -Wall $(INCLUDES)
    LDFLAGS = -g
    LDLIBS =

    # Fill in the missing targets and dependencies here

    .PHONY: clean
    clean:
            rm -f *.o *˜ a.out core main
```












