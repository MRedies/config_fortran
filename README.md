# config_fortran

A configuration file parser for Fortran. The intended usage is as follows:

1. You create your configuration variables, by providing a default value and
   a description.
2. You read in a text file in which new values are specified for (some of) the
   variables.
3. You use the updated values in your program, so that there is no need to recompile.

Variables can be of type integer, real, logical/bool, or string, and they can
also be an array of such types.

## Example

Suppose you want to use a grid of size `n_grid`, then you could do:

    integer     :: n_grid
    type(CFG_t) :: my_cfg

    call CFG_add(my_cfg, "grid_size", 1024, "Size of the grid")
    call CFG_read_file(my_cfg, "my_input_file.txt")
    call CFG_get(my_cfg, "grid_size", n_grid)

Here, the default grid size will be 1024. If the file `my_input_file.txt` contains a line

    grid_size = 512

the actual grid size used in your program will be 512. See `test_m_config.f90`
for an more extensive example of the usage. If you have a sufficiently recent `gfortran` compiler, you can run the test with

    $ make
    $ ./test_m_config

## Configuration file syntax

There are three types of lines:

1. Blank lines, or lines only containing a comment (`# ...`), which are ignored.
2. Lines indicating the start of a category: `[category_name]`
3. Lines with an `=`-sign. If they are part of a user-defined category, they
   should start with an indent.

An example of a configuration file is shown below

    age = 29
    name = John

    [weather]
        temperature = 25.2
        humidity = 23.5

    happy = .true.

Note that `temperature` and `humidity` are indented, and that `happy` is not,
which means that `happy` is not part of weather (it is in the default unnamed
category). To place variables in a category, you add them like Fortran types:

    call CFG_add(my_cfg, "weather%temperature", 25.0_dp, "The temperature")

Variables can also be arrays:

    name_of_variable = value1 [value2 value3 ...] # Optional comment

The extra values `[value2 value3 ...]` are omitted for a scalar variable. You
can create variables of varying array size, by specifying `dynamic_size=.true.`
when creating a config variable:

    call CFG_add(my_cfg, "numbers", [1, 2], "Comment", dynamic_size=.true.)

## Requirements

A modern Fortran compiler that supports Fortran 2008. The included `Makefile` was written for `gfortran`.

## TODO

* Write tests

## Alternatives

* [libconfig](http://www.hyperrealm.com/libconfig/) (C/C++)
* [config4*](http://www.config4star.org/) (C/C++)
* [KRACKEN](http://www.urbanjost.altervista.org/LIBRARY/libCLI/arguments/src2015/krackenhelp.html) (Fortran argument parser)
* [FLAP](https://github.com/szaghi/FLAP) (Fortran 2003+ argument parser)
* [FiNeR](https://github.com/szaghi/FiNeR) (Fortran 2003+ config file parser)
