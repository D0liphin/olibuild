# olibuild
contains `olibuildcpp` and `olibuildc` which are both makefiles for general (small) C++ and C projects respectively. If you want, add more rules!

This will only contain documentation for `olibuildc`, but they're literaly the same thing with different defaults so it doesn't matter.

## Setting Up an Olibuild Project

First, create a directory for your project, then `cd` into it.

```plaintext
$ mkdir HelloWorld
$ cd HelloWorld
```

Assuming this is a C project, you will now need to put a copy of `olibuildc` into this folder  
You can create a simple script in `/bin` that will do this, or store `olibuildc` in a hidden folder like `~/.make/`  
Using the second option:  

```plaintext
$ cp ~/.make/olibuildc .
$ mv olibuildc Makefile
```

- Then, just run `make init`

```plaintext
$ make init
*** Initialised olibuild project! \(^-^)/ *** 
$ tree
.
├── include
├── Makefile
├── src
│   └── main.c
└── tests

3 directories, 2 files
```

- This contains a helloworld project by default, see `./src/main.c`

```c
#include <stdio.h>

int main() {
    printf("Hello, world!\n");
    return 0;
}
```

## `make help`

Displays help information about olibuildc

## `make build`

Olibuilder builds this project.  
When you build a project, it will create some binaries in `./target`

```plaintext
$ make build
gcc -std=c99 -Wall -Wextra -g -I . -I ./include -MMD -MP -c -o ./target/src/main.c.o src/main.c
gcc -o ./target/main ./target/src/main.c.o
*** done! \(^-^)/ ***
$ tree
.
├── include
├── Makefile
├── src
│   └── main.c
├── target
│   ├── main
│   └── src
│       ├── main.c.d
│       └── main.c.o
└── tests

5 directories, 5 files
```

`./target/main` is the main binary file. You can run it now with `./target/main`, or you can just run

## `make run`

as a shortcut.

These files are created so that we can recompile more quickly. But, they take up a lot of space! Get rid of them with 

## `make clean`

And olibuilder will clean up your program for you

```plaintext
$ make clean
*** All cleaned up, boss! (￣ー￣)ゞ ***
```

Look how well behaved he is!

## `make print-src`

You can print all the files being used by olibuild with `make print-src`

```plaintext
$ make print-src
o files      =./target/src/main.c.o
source files = ./src/main.c
d files      = ./target/src/main.c.d
tests        = 
```

You'll see we have `tests =` at the bottom there, that moves us on to 

## Tests!

The testing module is very limited, but for large projects, this tool doesn't make sense anyway, so use something else! (or expand the test commands and commit it to this repo! :))  

Anyway, let's create some more files... My file structure looks like this now:

```plaintext
$ tree
.
├── include
│   └── add.h
├── Makefile
├── src
│   ├── add.c
│   └── main.c
└── tests
    ├── test_add_1_3.c
    └── test_add_4_2.c

3 directories, 6 files
```

The entire project can be found in this git repo, but the idea is basically that `./include/add.h` defines a function `int add(int, int)`, `./src/add.c` implements this function and we have a couple tests in `./tests` that use the function and print out some info.  
For example, `./tests/test_add_1_3.c` looks like this:

```c
#include <stdio.h>
#include "../include/add.h"

int main() {
	printf("add(1, 3) = %d\n", add(1, 3));
	return 0;
}
```

Now, let's build our tests and run execute them!

```plaintext
$ make build-tests 
gcc -std=c99 -Wall -Wextra -g -I . -I ./include -MMD -MP -c -o ./target/src/add.c.o src/add.c
gcc -std=c99 -Wall -Wextra -g -I . -I ./include -MMD -MP -c -o ./target/tests/test_add_1_3.c.o tests/test_add_1_3.c
gcc -std=c99 -Wall -Wextra -g -I . -I ./include -MMD -MP -c -o ./target/tests/test_add_4_2.c.o tests/test_add_4_2.c
olibuild: building test: ./tests/test_add_1_3.c 
olibuild: building test: ./tests/test_add_4_2.c 

$ make run-tests

olibuild: running test /tests/test_add_1_3
======================================================
add(1, 3) = 4
======================================================

olibuild: running test /tests/test_add_4_2
======================================================
add(4, 2) = 6
======================================================
```

How handy!

If you use the default configuration (which you should) you can clean tests only with

```plaintext
$ sudo rm -r ./target/tests/
```

If we don't want to run all the tests, we can change the the tests we run, by specifying the `RUN_TESTS` variable in the `Makefile`

```Makefile
RUN_TESTS=$(wildcard ./tests/*)
```

This means that we should run all the source files (anything with an extension in `$(SRC_EXTS)`) in `./tests`.
If we want to just run `test_add_4_2`, we can change this line to

```Makefile
RUN_TESTS=./tests/test_add_4_2.c
```

You could also separate your tests into different folder groups, and use a wildcard to select from a specific folder... up to you!

## Modifying the Directory Structure

By default, this build tool only allows you to place source files in `./`, `./include` and `./src/`. 
You can change the directories we look in for source files by adding a directory to the `SRC` variable, and you can change the directory we look for header files by adding a directory to `INCLUDE`.

## Modifying the Source File Extension List

If for some reason you want to use files other than `.c` in olibuildc or something other than `.cxx`, `.cpp` or `.cc` in olibuildcpp, just add an extension to `SRC_EXTS`.

## Modifying the Entry Point

The file `ENTRY_POINT` (by default  `./src/main.c` is ignored when compiling tests).

## That's it!

Thansk for checking out olibuild :)
