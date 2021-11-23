| HGS Simulations is a product of Aquanty Inc.
| 564 Weber Street North, Unit 2
| Waterloo, Ontario N2L 5C6
| This manual documents HydroGeoSphere (HGS) version 2321.
| Copyright ©2015, Aquanty Inc. All rights reserved.
| This publication is a product of Aquanty Inc. and may not be
  reproduced either commercially or non-commercially by any means
  including but not limited to electronic replication, photocopy, or
  mechanical replication without direct written permission from Aquanty
  Inc.
| Permissions may be sought directly from Aquanty Inc:
| Phone: 1-855-aquanty
| Fax: (519) 279-1081
| Email: info@aquanty.com

|image|

.. _chap:quick_start:

Quick Start Guide
=================

The goal of this guide is to get you up to speed quickly on the basic
operation of **HydroGeoSphere** (HGS). The topics covered include:

-  HGS installation.

-  A brief overview of the key executable files.

-  Running a model.

HGS Installation
----------------

**HydroGeoSphere** consists of a suite of 64-bit applications that
target either the Windows (Windows 7 and newer) or Linux operating
systems. In order to run HGS, a valid HGS license file ``hgs.lic`` must
be present in the installation directory. To obtain a valid HGS license
file, please email the ``hostid.txt`` file located in the installation
directory to ``info@aquanty.com``.

Windows
~~~~~~~

By default, HGS is installed in the directory:

The installation folder contains the key executable files (``grok.exe``,
``phgs.exe``, ``hsplot.exe``, ``hsbatch.exe``) required for simulation
and results post-processing, the ``hostid.txt`` file, and a number of
DLL files. Folders within the installation directory include:

-  *docs*: This folder contains the theory, reference, and verification
   manuals which are important references for model setup.

-  *illustration* and *verification*: These folders contain example
   problems, many of which are discussed in the verification manual.

Note that the installer will attempt to add the installation directory
to your system path. Should it fail to do so, for example, if it does
not have write access, then you will need to update your system path
manually. We recommend that you always run the installer with
administrator privileges if possible.

Linux
~~~~~

In Linux, HGS is installed under the current folder in the directory:

where “RevNum” is the revision number of the installation. The
installation folder contains the key binary files (``grok``, ``hgs``,
``hsplot``, ``hsbatch``), the ``hostid.txt`` file, and the EULA. Folders
within the installation directory include:

-  *docs*: This folder contains the theory, reference, and verification
   manuals which are important references for model setup.

-  *lib*: This folder contains the external dependency library files.

-  *verification*: This folder contains example problems, many of which
   are discussed in the verification manual.

The installer modifies the ``~/.bashrc`` file by adding the new
environment variable ``HGSDIR`` that points to the HGS installation
directory, the HGS installation directory to ``PATH``, and the ``lib``
directory to ``LD_LIBRARY_PATH``. Users are required to either start a
new shell or run ``source ~/.bashrc`` for these changes to be added to
their environment.

Some notes to keep in mind:

-  Users are encouraged to check the contents of their ``~/.bashrc``
   file to ensure their bash environment is set up appropriately for
   their own computing purposes and for running **HydroGeoSphere**.

-  Users on other Linux terminal shells (e.g., zsh, csh, ksh, etc.)
   should maintain their own shell profiles to appropriately set their
   ``HGSDIR``, ``PATH``, and ``LD_LIBRARY_PATH`` environment variables,
   if necessary. Please consult with your local system administrator if
   you are unsure about how to do this for your HPC computing
   environment.

Key Executable Overview
-----------------------

There are three steps required to setup, simulate, and view the results
of a simulation.

#. A data file is prepared for the pre-processor (called **grok**) which
   is then run to generate the input data files for HGS.

#. HGS is run to solve the problem and generate output data files.

#. Depending on the problem, post-processing of the data is completed
   using **HSPLOT**, to convert the data into a Tecplot compatible
   format for visualization and analysis.

Grok (``grok.exe``)
~~~~~~~~~~~~~~~~~~~

The **grok** input file contains all of the information and instructions
required for the HGS simulation. This file name consists of a meaningful
prefix (up to 40 characters) to which the extension ``.grok`` is
appended. For example, if the problem prefix created by the user is
*test*, then the general input file created by the user will be
``test.grok``. **Grok** will attempt to read the problem prefix from the
``batch.pfx`` file, which contains a single line with the prefix name.
If **grok** is unable to find this file, then the user will be prompted
to enter the prefix name at the console. Information contained within
the **grok** file includes mesh definition, model parameterization,
initial conditions, boundary conditions, convergence criteria, and
simulation output criteria. The pre-processor, **grok**, performs its
task in the following order:

#. Read and allocated default array sizes.

#. Read the problem identification information.

#. Read instructions for generating the grid.

#. Perform grid modifications if necessary.

#. Generate default properties for all parameters.

#. Read optional instructions for modifying the default parameters.

#. Write the HGS-compatible data types.

Once the *prefix*\ ``.grok`` file has been built by the user it is
compiled by running ``grok.exe``. A more detailed description of
**grok** and its associated commands are contained in Chapter
`2 <#chap:io_instructions>`__ of this document. We note that ``.grok``
files in the *illustration* and *verification* folders are an excellent
resource for reviewing **grok** structure and the use of
**grok** commands.

HGS (``phgs.exe``)
~~~~~~~~~~~~~~~~~~

After the execution of ``grok.exe``, which writes all the HGS-compatible
data files, ``phgs.exe`` is executed to perform the model simulation.
There is little user involvement at this stage other than the
configuration of the parallel execution details in the file
``parallelindx.dat``.

The file ``parallelindx.dat`` does not exist, ``phgs.exe`` will create
it when it is launched. This file tells ``phgs.exe`` how many processors
to use the simulation. By default ``parallelindx.dat`` is created
assuming the simulation is being performed in serial mode, i.e., one
processor.

::

   __Number_of_CPU
              1
   __Num_Domain_Partitiong
              1
   __Solver_Type
              1
   __Coloring_Input
    F
   __Wrting_Output_Time
     -1.00000000000000
   __Simulation_Restart
              1

To increase the level of parallelization, change the values of
“\__Number_of_CPU” and “\__Num_Domain_Partitiong” (these values should
be the same). When setting these values it is important to make sure you
don’t exceed the number of processors available on your machine. In
general we recommend that at most you use up to two fewer than the total
number available. For example, if your machine has eight processors, we
recommend that you use up to six if you plan on using the machine for
other tasks. Note that when the number of CPUs requested is greater than
one, the solver type must be changed to 2.

The following example shows how the ``parallelindx.dat`` file would be
set up to use 6 processors for a simulation.

::

   __Number_of_CPU
              6
   __Num_Domain_Partitiong
              6
   __Solver_Type
              2
   __Coloring_Input
    F
   __Wrting_Output_Time
     -1.00000000000000
   __Simulation_Restart
              1

You do not have to wait for ``phgs.exe`` to generate the
``parallelindx.dat`` file each time you run a simulation. You can copy
the file from a previous simulation to your current model folder.
Changing ``parallelindx.dat`` while the simulation is running will not
affect the number of processors being used. To change the level of
parallelization it is necessary to stop and restart the simulation.

HSPLOT (``hsplot.exe``)
~~~~~~~~~~~~~~~~~~~~~~~

The executable ``hsplot.exe`` is used the post-process the simulation
results for viewing in Tecplot. **HSPLOT** can be executed during an HGS
run or following its completion. The resulting output files
(*prefix*\ ``o.``\ *domain*\ ``.dat``) can be opened in Tecplot to view
the simulation results in three dimensions.

Running a Model
---------------

We conclude this chapter by describing the steps to run the Abdul model
problem, the model files for which can be found in

For additional details on this problem the user is referred to the
verification manual ``hydrosphere_verif.pdf``. The steps to run this
model problem are as follows.

#. Copy ``grok.exe``, ``phgs.exe``, and ``hsplot.exe`` to .

#. Run ``grok.exe``.

#. Run ``phgs.exe``.

#. Run ``hsplot.exe``.

#. Open ``abdulo.olf.dat`` and ``abdulo.pm.dat`` with Tecplot to view
   the simulation results.

Note that Windows users who receive a DLL error when running one of the
executable files should copy the DLL files from the installation folder
to the current simulation folder. Alternatively, Windows users can add
the directory to their system path. Updating the system path makes it
possible to run a model from any folder without copying any HGS
executable files or DLL files to that folder and is the preferred method
of operation.

.. _chap:io_instructions:

Input/Output Instructions
=========================

General
-------

[sec:io_approach] Before presenting in detail the input data needed for
the numerical simulations, some general information about the format and
nature of the input data is first given.

There are two steps involved in solving a given problem. First, a data
file is prepared for the pre-processor (called **grok**\  [1]_) which is
then run to generate the input data files for **HydroGeoSphere**.
Second, **HydroGeoSphere** is run to solve the problem and generate
output data files.

The **grok** input file name consists of a meaningful prefix of up to 40
characters to which the extension ``.grok`` is appended. This prefix
will determine the input and output filenames. The **grok** listing file
name will be the problem prefix to which the letter ``o`` and the file
extension ``.eco`` are appended. For example, if the problem prefix
specified by the user is ``test``, the general input file to be created
by the user will be ``test``\ ``.grok`` and the output listing, or echo,
file generated by the pre-processor will be ``testo.eco``. Some
simulations will require more than one input file (e.g. initial heads
read from file) and will result in the generation of more than one
output file. As a rule, all input files needed during a specific
simulation will have the problem prefix plus a given extension as
filename while all generated output files will have the problem prefix,
the letter ``o``, plus a given extension as filename.

Throughout the manual, we will adopt the convention of using *italics*
to indicate problem-dependent, user-defined portions of filenames
(e.g. prefix, species name etc.) and ``typewriter font`` to indicate
invariant portions generated by **HydroGeoSphere**. For example, in the
filename *prefix*\ ``o.conc.``\ *species*\ ``.0001`` the *prefix* and
*species* portions would be the user-defined prefix and name of a
solute, or species, while the ``o.conc.`` and ``.0001`` portions would
be generated by **HydroGeoSphere** automatically.

After the pre-processor starts executing, it prompts the user to enter
the prefix for the problem interactively from the keyboard. For cases in
which the same input file is being used repeatedly, you can create a
file called ``batch.pfx`` which consists of a single line which contains
the problem prefix. If the file is present, the prefix will
automatically be read from the file and you will not be prompted to
enter it from the keyboard. This file should be placed in the same
directory as the *prefix*\ ``.grok`` file.

Briefly, the pre-processor performs its tasks in the following order:

#. Read and allocate default array sizes [task:def_array]

#. Read problem identification information

#. Read instructions for generating grid [task:ggrid]

#. Perform grid modifications if necessary

#. Generate default properties for all parameters [task:defdata]

#. Read optional instructions for modifying the default parameters
   [task:moddef]

#. Write the **HydroGeoSphere**-compatible data files

Tasks \ `[task:ggrid] <#task:ggrid>`__ and
`[task:moddef] <#task:moddef>`__ are guided by instructions issued by
the user in the *prefix*\ ``.grok`` file. The generation of a complete
set of default data by Task \ `[task:defdata] <#task:defdata>`__ tends
to minimize the amount of data which must be supplied by the user.

Here is an example instruction and some input data which illustrates
some common conventions that will be used throughout the manual:

--------------

| Example instruction text

#. **xlen, nbx** Domain length [L] and number of blocks in the
   :math:`x`-direction.

#. **xi(i), i=1,nx** Nodal :math:`x`-coordinates [L].

#. **inode(i)...end** Node numbers.

--------------

| 

The pre-processor instruction is separated from the preceding text by a
horizontal line, and is written using the sans serif font. It must be
typed in the *prefix*\ ``.grok`` file exactly as shown, with the
exception that it is not case-sensitive, and blanks before and after the
instruction are optional. Note that only one blank is allowed between
any two words in an instruction.

If the instruction requires input data, there will follow a series of
numbered lines, each containing boldfaced **variable names** and a
description of what is to be read. Each numbered line will correspond to
one or more Fortran read statements.

Usually, the number of items required in the data file are indicated by
how many boldfaced variable names are present on the line. The default
Fortran variable naming conventions are in effect. This means variables
starting with the letters IN inclusive require integer values, while all
the rest require real values, unless stated otherwise in the case of
string or logical variables. Numerical values are read in free-format so
integers and reals do not need to be lined up in columns and they can be
separated by blanks or commas. A descriptive comment can be included
inline after the last data value has been read from the line, but should
be avoided when reading character strings (e.g., filenames).

In this example, three items of input are required. The first item **xl,
nbx** requires that the user enter a real value (i.e. domain length)
followed by an integer value (i.e. number of blocks) on the first
non-blank or uncommented line following the instruction.

The second item **xi(i), i=1,nx** reads **nx** real values into the
array **xi**. The size of **nx** is problem dependent (e.g., number of
nodes in :math:`x`, number of species, etc.) and it is up to the user to
supply enough values to satisfy the read statement. The values may be
entered on one line or spread out over multiple lines as desired. If
they are entered on one line, they should be separated by spaces or
commas.

Finally, the third item **inode(i)...end** indicates a list, in this
case of node numbers, that is to be read until an end instruction is
encountered. The list values must be entered one per line.

The end of the documentation that pertains to a specific instruction is
designated by three dots: :math:`\bullet \bullet \bullet`.

So for this example instruction, assuming that **nx** is equal to 5, the
following statements in the *prefix*\ ``.grok`` file would satisfy the
input requirements:

::

       Example instruction text
       10.0    100
       0.0   2.0   4.0   6.0   8.0   10.0
       1
       2
       3
       5
       6
       end

In some cases (there are not too many) an instruction will have a more
complex input structure of the form **val(i,j), i=1,m, j=1,n**. The
indices are always listed from fastest to slowest varying reading from
left to right. Hence, this input would be written in a file as:

::

       val11 val21 ... valm1
       val12 val22 ... valm2
         :     :         :
       val1n val2n ... valmn

Naturally, if index :math:`j` was listed first followed by index
:math:`i`, then the input in the file would be transposed. In all cases
the instruction will contain a helpful example to show how the input
should be formatted in the file.

Some instructions are controlled by input routines that have their own
subset of input instructions, some or all of which may be optional. For
example, the instruction Solute is used to define a new solute and in
its simplest form appears as:

::

       Solute
       end

In this case, the End instruction immediately follows the Solute
instruction, and no optional instructions have been issued. The End
statement is required so that **grok** knows when to exit the solute
definition routine. Such instructions will be indicated using the
following convention:

--------------

| Example instruction text...End

--------------

| 

where the text ...End indicates that the instruction (e.g. Solute) will
be followed by optional instructions or input and terminated by an End
instruction.

Before **grok** processes instructions contained in a
*prefix*\ ``.grok`` or a material properties file (see
Section \ `[sec:modify_named_material] <#sec:modify_named_material>`__)
it first makes a working copy of the file in which any line which is
completely blank or which begins with an exclamation point (!) is
removed and in which the contents of any included file are copied. This
allows you to include blank lines and comments when and where required
to improve the readability and clarity of the input.

Included files can be used to avoid having to cut and paste or comment
and uncomment large sections of input instructions. Long lists (e.g. of
node numbers or boundary condition data) and cases where various
different grid generation approaches are being tried are good candidates
for application of the include feature. For example, if we wanted to use
include to supply data to the example given above, we could use the
following instruction in *prefix*\ ``.grok``:

::

       Example instruction text
       10.0    100
       0.0   2.0   4.0   6.0   8.0   10.0
       include my.node_list

and where the file ``my.node_list`` could contain, for example:

::

       1
       2
       3
       5
       6
       end

If you now wanted to substitute another node list you could, for
example, supply different node numbers in the file
``my_other.node_list`` and then just change the file name given in the
include instruction.

Included files can contain groups of instructions and input, or just
bits of input for a single instruction. Only one level of include
instruction is allowed, and so included files can not themselves contain
include instructions.

As **grok** reads and processes the copy of the *prefix*\ ``.grok`` file
it also creates the *prefix*\ ``o.eco`` file. Results of the
**HydroGeoSphere** data generation procedures are written to this file
so if there are any problems reported by the pre-processor you should
check this file first to determine their nature and how you might fix
them. If an error occurs while reading the input data, then
**grok** will halt execution and issue an error message (to the screen
and the *prefix*\ ``o.eco`` file) of the form:

::

       INSTRUCTION: 500

       **************************************
       *** INPUT ERROR, HALTING EXECUTION ***
       **************************************

       GRID GENERATION: Unrecognized instruction

       Press any key to continue

In this case the last instruction (i.e. ``500``) has, for some reason,
caused an error. You should now check the input files to further
investigate the cause of the problem, starting with the
*prefix*\ ``.grok`` and material properties files.

File Process Control Options
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The following instructions control how the pre-processor treats
instructions in the *prefix*\ ``.grok`` file and can be inserted at any
point in the file and as often as required, except of course when input
for a specific instruction is expected.

--------------

| Echo off

By default, as instructions are read by **grok** they are echoed to the
screen. This command turns off this feature.

--------------

| 

--------------

| Echo on

This commands turns on the echoing of instructions to the screen.

--------------

| 

--------------

| Skip on

With skip mode turned on, **grok** will read but not act on any
subsequent instructions.

--------------

| 

--------------

| Skip off

Turns skip mode off, so **grok** will resume acting on instructions.

--------------

| 

--------------

| Skip rest

**grok** exits the loop for reading instructions from the
*prefix*\ ``.grok`` file and proceeds to generate the
**HydroGeoSphere** data files.

--------------

| 

--------------

| Pause

This instruction causes **grok** to pause at the current location in the
*prefix*\ ``.grok`` file until the user presses a key.

--------------

| 

User Defined Variables
^^^^^^^^^^^^^^^^^^^^^^

This section describes pre-processor commands that can be used to
define/undefine variables in your **grok** file and material properties
files (see Section \ `[sec:mat_props] <#sec:mat_props>`__), similar to
how variables are used in a batch script or shell script, albeit, on a
much simpler level. The syntax of these commands is different from other
**grok** commands you will encounter in this manual for two reasons:

#. To mimic the syntax for defining variables used by batch or shell
   scripts.

#. Because these commands are parsed by **grok** during scratch file
   generation and are not actually present in the final **grok** or
   material properties files.

We begin by describing how to define a new variable or overwrite the
value of an existing one:

::

       set variable $<varname>=<value>

The variable name (``$<varname>``) may consist of up to 256 characters,
is case insensitive, and must adhere to the following rules:

#. Contain at least two characters.

#. The first character must be the dollar sign ($), which is a special
   character reserved for identifying pre-processor variables.

#. The second character is a letter or underscore.

#. All remaining characters are letters, numbers, or an underscore.

The variable’s value (``<value>``), which is always treated as a string,
may consist of up to 4096 characters and must not contain a dollar sign
($) character. The ``set variable`` command ignores any leading/trailing
whitespace around the variable name and its value. Inline comments are
also ignored. For example, the following commands are equivalent:

::

       set variable $path=C:\my_file_path    ! inline comment
       set variable   $path =C:\my_file_path
       set variable $path=   C:\my_file_path
       set variable   $path  =  C:\my_file_path

Each command defines the variable ``$path`` to have the value
``C:‘ my_file_path``. If you wish to retain leading whitespace in the
variable value, then you can do so by enclosing it in double quotes ().
For example, the command

::

       set variable $path="   C:\my_file_path"

assigns to the variable ``$path`` the value ``   C:\my_file_path``. Note
that the enclosing double quotes are automatically stripped from the
variable value by the ``set variable`` command. If you would like to
assign an empty value to a variable, then you can do so as follows using
either of the equivalent commands:

::

       set variable $<varname>=
       set variable $<varname>=""

If you use the ``set variable`` command without any parameters, then a
list of all currently defined variables and their values will be written
to the console. To undefine a variable that is currently defined you may
use the following command:

::

       unset variable $<varname>

Similar to the ``set variable`` command, all leading/trailing whitespace
around the variable name is ignored. Note that calling
``unset variable`` on a variable that is undefined has no effect. In
addition, you may use the following command to undefine all currently
defined variables:

::

       unset all variables

Once a variable is defined, you can obtain its value via variable
substitution simply by writing the variable’s name followed directly by
a dollar sign ($). For example, the **grok** file commands

::

       set variable $path1=D:\projects\my_project\include_files
       set variable $path2=D:\projects\my_project\init_files

       include $path1$\file1.include

       initial head from file
       $path2$\head0.txt

are equivalent to

::

       include D:\projects\my_project\include_files\file1.include

       initial head from file
       D:\projects\my_project\init_files\head0.txt

Note that using the value of an undefined variable will result in a
warning message being written to the console.

As discussed above, pre-processor variables are supported by the
**grok** file, material properties files, and by all files included via
an Include command. It is important to keep in mind that pre-processor
variables have global scope among these files. For example, if your
**grok** file defines a variable and then includes a file, that variable
will be visible within the included file. If the included file then
defines a variable with the same name, its value will be overwritten and
will persist after the include statement has been processed. The same is
true for the material properties files. Therefore, as a best practice,
we recommend defining all pre-processor variables at the top of your
**grok** file.

We now describe in detail the various actions of the pre-processor,
giving instructions for setting up the *prefix*\ ``.grok`` file where
necessary.

Units and Physical Constants
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

[sec:io_units] The units used in the program are not preset, although a
default of kilogram-metre-second units is assumed and used to define the
values of certain physical constants as discussed below. The user should
decide which units will be used for mass (M), length (L), and time (T)
for the various input variables, issue the appropriate units instruction
(or assign appropriate values for the physical constants) and then
consistently use those chosen units for all other input data. The units
of temperature :math:`(\Theta)`, for example in the case of thermal
transport, are expected to be in degrees Celsius unless stated
otherwise. For example, if you want to specify the dimensions of your
domain in metres and the time at which you want a solution is in
seconds, then all measures of length and time will have to be in metres
and seconds, respectively. The hydraulic conductivity should therefore
be specified in m s\ :math:`^{-1}`, a pumping rate in m\ :math:`^3`
s\ :math:`^{-1}`, etc. The program does not perform any checks to ensure
unit consistency.

Default values are assigned for the gravitational acceleration and fluid
properties which correspond to standard values in the
kilogram-metre-second system. These parameters are used when defining
the properties of fractures, open wells and tile drains.

The following default values will be used for the physical constants and
correspond to typical values in the kilogram-metre-second system:

-  Gravitational acceleration :math:`g = 9.80665` m s\ :math:`^{-2}`,
   Equation \ `[eq:1a] <#eq:1a>`__.

-  Fluid density :math:`\rho = 1000.0` kg m\ :math:`^{-3}`,
   Equation \ `[eq:3] <#eq:3>`__.

-  Fluid viscosity :math:`\mu = 1.124 \times 10^{-3}` kg
   m\ :math:`^{-1}` s\ :math:`^{-1}`, Equation \ `[eq:3] <#eq:3>`__.

-  Fluid compressibility :math:`\alpha_w = 4.4 \times 10^{-10}`
   kg\ :math:`^{-1}` m s\ :math:`^2`, Equation \ `[eq:7b] <#eq:7b>`__.

-  Fluid surface tension :math:`\chi = 0.07183` kg s\ :math:`^{-2}`,
   Equation \ `[V-eq:a5] <#V-eq:a5>`__.

If you are using different units or you want to change the default
values you can do so using the following instructions.

--------------

| Units: kilogram-metre-minute

Converts the default values given above into the kilogram-metre-minute
system. This instruction also converts the porous media, dual continuum,
fractured media, and surface flow default properties that are defined in
the code. Note, however, that it does not convert properties specified
in any *prefix*\ ``.grok``, ``.mprops``, etc. files. Similar
instructions exist for converting to the following systems:

-  Kilogram-metre-hour.

-  Kilogram-metre-day.

-  Kilogram-metre-year.

-  Kilogram-centimetre-second.

-  Kilogram-centimetre-minute.

-  Kilogram-centimetre-hour.

-  Kilogram-centimetre-day.

-  Kilogram-centimetre-year.

You can change the default values of the physical constants using the
following instructions. If you change the default units from the
kilogram-metre-second system make sure the values given here are in the
new system.

--------------

| 

--------------

| Gravitational acceleration

#. **grav** Gravitational acceleration constant [L T:math:`^{-2}`],
   :math:`g` in Equation \ `[eq:1a] <#eq:1a>`__.

--------------

| 

--------------

| Reference fluid density

#. **rho** Fluid density [M L:math:`^{-3}`], :math:`\rho` in
   Equation \ `[eq:3] <#eq:3>`__.

--------------

| 

--------------

| Reference fluid viscosity

#. **visc** Fluid viscosity [M L:math:`^{-1}` T:math:`^{-1}`],
   :math:`\mu` in Equation \ `[eq:3] <#eq:3>`__.

--------------

| 

--------------

| Fluid compressibility

#. **wcomp** Fluid compressibility [M:math:`^{-1}` L T\ :math:`^2`],
   :math:`\alpha_w` in Equation \ `[eq:7b] <#eq:7b>`__.

--------------

| 

--------------

| Zero fluid compressibility

Assigns a value of zero for fluid compressibility (i.e.,
incompressible).

--------------

| 

--------------

| Fluid surface tension

#. **tensn** Fluid surface tension [M T:math:`^{-2}`], :math:`\chi` in
   Equation \ `[V-eq:a5] <#V-eq:a5>`__.

--------------

| 

Pre-Processor Considerations
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Array Dimensioning
^^^^^^^^^^^^^^^^^^

[sec:array_defaults] When performing
Task \ `[task:def_array] <#task:def_array>`__, **grok** first checks for
the existence of a file ``array_sizes.default`` in the directory where
the *prefix*\ ``.grok`` file is located. If it is not found, the file is
automatically created and default array sizes are written which are then
used by the pre-processor. Associated with each default are a descriptor
and a default value. A portion of the file is shown here:

::

       dual: material zones
               20
       dual flow bc: flux nodes
            10000

       ...etc...

       tiles: flux function panels
               20
       wells: injection concentration function panels
              100
       end

So, for example, the default maximum number of dual continuum material
zones is 20. If the problem is defined such that an array exceeds the
default maximum (e.g. the number of node sheets in the
:math:`z`-direction for layered grids exceeds 50) then **grok** will
halt execution and issue an error message (to the screen and the
*prefix*\ ``o.eco`` file) of the form:

::

       *********************************************
       *** DIMENSIONING ERROR, HALTING EXECUTION ***
       *********************************************

        Pre-processor request exceeds default array size

        mesh: node sheets in z for layered grids
        Default value: 50
        Requested value: 100

        Increase the default value in file ARRAY_SIZES.DEFAULT

Given the descriptor in the error message, you can now edit the
``array_sizes.default`` file and increase the appropriate value. Note
that the file is sorted alphabetically by descriptor. When you run
**grok** again, it will read the new default value from the file.
Re-compilation of the code is not necessary, since it uses Fortran
ALLOCATE statements to define array sizes at run-time.

**HydroGeoSphere** does not utilize the file ``array_sizes.default``,
but instead uses exact array sizes determined and passed by **grok**.

Remember, this process is problem dependent, and each time you run
**grok** in a different directory, a fresh ``array_sizes.default`` file
will be generated with default values.

Problem Identification
----------------------

The first section of the *prefix*\ ``.grok`` file should consist of a
description of the problem being defined. As for the rest of the file,
blank lines and lines beginning with an exclamation point (!) are
ignored.

The description can contain from zero up to as many lines as the user
requires to describe the problem. Each line can contain up to 60
characters. The description is printed at the beginning of the listing
files for **grok** (*prefix*\ ``o.eco``) and
**HydroGeoSphere** (*prefix*\ ``o.lst``).

The user must signal the end of the description using the End
instruction.

--------------

| End

This instruction signals the end of the description at which point
control is passed back to the pre-processor.

--------------

| 

Grid Generation
---------------

[chap:grid] The next section of the *prefix*\ ``.grok`` file should
consist of instructions for grid generation followed by an End
instruction.

Currently, **grok** is capable of generating grids which are composed of
either hexahedral blocks or triangular prisms.
Figure \ `[fig:element_types] <#fig:element_types>`__ shows the local
node numbering conventions for each of these elements and also the
positive directions of the :math:`x`-, :math:`y`-, and :math:`z`-axes.

.. figure:: conv
   :alt: Element types and local node numbering conventions.

   Element types and local node numbering conventions.

[fig:element_types]

We will first discuss options for generating simple grids, followed by
irregular grids.

Simple Grids
~~~~~~~~~~~~

[sec:simple_blocks] Simple grids can be generated for rectangular
domains which are adequate for many problems. They can have uniform or
variable element sizes and can be made of hexahedral block or triangular
prismatic elements. Each element in the grid is given a default zone
number of 1.

--------------

| Generate uniform blocks

#. **xlen, nbx, (x0)** Domain length [L] and number of blocks in the
   :math:`x`-direction, the optional origin in the :math:`x`-direction
   [L] (zero by default).

#. **ylen, nby, (y0)** Domain length [L] and number of blocks in the
   :math:`y`-direction, the optional origin in the :math:`y`-direction
   [L] (zero by default).

#. **zlen, nbz, (z0)** Domain length [L] and number of blocks in the
   :math:`z`-direction, the optional origin in the :math:`z`-direction
   [L] (zero by default).

Generates a grid for a rectangular domain made up of uniform blocks. In
this case, the grid is formed by subdividing the domain in the
:math:`x`-direction into **nbx** blocks, each of length **xlen/nbx**.
The domain is subdivided in a similar fashion in the :math:`y`- and
:math:`z`-directions, using the other input parameters.

--------------

| 

--------------

| Generate uniform prisms

Generates a grid for a rectangular domain made up of uniform prisms.
Requires identical input to the routine Generate uniform blocks
described above. In this case though, instead of generating block
elements, this instruction generates prism elements by subdividing each
block into two prism elements.

--------------

| 

--------------

| Generate variable blocks

#. **nx** Number of nodes in the :math:`x`-direction.

#. **x(i), i=1,nx** Nodal :math:`x`-coordinates [L].

#. **ny** Number of nodes in the :math:`y`-direction.

#. **y(i), i=1,ny** Nodal :math:`y`-coordinates [L].

#. **nz** Number of nodes in the :math:`z`-direction.

#. **z(i), i=1,nz** Nodal :math:`z`-coordinates [L].

Generates a grid for a rectangular domain made up of variably-sized
blocks. It is almost identical to the Generate uniform blocks
instruction except that instead of entering a domain length in each
direction we enter a list of coordinates, which are each used to define
the position of a plane of nodes along that axis. The structure **x(i),
i=1,nx** is called an implied do and means that you must supply **nx**
values for the array **xi**. One or more values can be entered per line
until the read statement is satisfied, then a new line should be started
for the next read statement. Note that the line length is limited by
3000 characters in any input instructions and thus, use additional lines
for **x(i), y(i), z(i)** should your input exceed this limit.

--------------

| 

--------------

| Generate variable prisms

Generates a grid for a rectangular domain made up of variably-sized
prisms. Requires identical input to the routine Generate variable blocks
described above. In this case though, instead of generating block
elements, this instruction generates prism elements by subdividing each
block into two prism elements.

--------------

| 

.. _sec:gen_blocks_interactive:

Interactive Block Grids
~~~~~~~~~~~~~~~~~~~~~~~

Interactive block instructions can be used to generate a grid made up of
variably-sized blocks. The user can grade the mesh as desired in each of
the three principal directions. This is particularly useful for regions
in which fine meshes are required, for example, near a discrete fracture
or well.

Note that these instructions cannot be used in conjunction with the
other grid generation instructions such as Generate uniform block,
Generate uniform prisms, Generate variable blocks, or Generate variable
prisms.

--------------

| Generate blocks interactive...End

Causes **grok** to begin reading a group of interactive block
instructions until it encounters an End instruction. The group should
contain of at least one instruction for each of the principal
directions.

--------------

| 

The available instructions are:

--------------

| Grade x

#. **x1, x2, dxstart, xfac, dxmax** Starting :math:`x`-coordinate [L],
   ending :math:`x`-coordinate [L], starting element size, element size
   multiplication factor, and maximum element size.

Grid lines (i.e. elements) are generated along the :math:`x`-axis from
**x1** to **x2** which grade up in size from **dxstart** to **dxmax**.
Element sizes are increased steadily by a factor of **xfac**.

--------------

| 

--------------

| Grade y

As above but for the :math:`y`-axis.

--------------

| 

--------------

| Grade z

As above but for the :math:`z`-axis.

--------------

| 

The instructions used to generate the mesh shown in
Figure \ `[fig:gen_blocks_mesh] <#fig:gen_blocks_mesh>`__ are:

::

       generate blocks interactive
       grade x
        75.0     0.0   0.01   1.5  5.0
       grade x
        75.0   100.0   0.01   1.5  5.0
       grade x
       125.0   100.0   0.01   1.5  5.0
       grade x
       125.0   200.0   0.01   1.5  5.0
       grade y
       100.0     0.0   0.01   1.5  5.0
       grade y
       100.0   200.0   0.01   1.5  5.0
       grade z
         1.0     0.0   0.25   1.0  0.25
       grade z
         3.0     1.0   0.01   1.3  0.25
       grade z
         3.0    11.0   0.01   1.3  0.25
       grade z
        11.0    12.0   0.25   1.0  0.25
       end generate blocks interactive

.. figure:: gen_blocks
   :alt: Example grid that was created using Generate blocks interactive
   instructions.
   :width: 80.0%

   Example grid that was created using Generate blocks interactive
   instructions.

[fig:gen_blocks_mesh]

.. _sec:rfgen:

3-D Random Fracture Generator for Block Grids
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The following command can be used to generate a 3-D random fracture
network in an orthogonal domain (i.e., composed of 8-node block
elements). Fractures with random locations, lengths, and apertures can
be generated.

--------------

| Rfgen driver

#. **rfgfile** Name of the file that contains the random fracture grid
   and fracture generation information.

The structure of the input file is described below.

--------------

| 

--------------

| Grid information

#. **x1, x2** :math:`x`-range [L] of the domain.

#. **y1, y2** :math:`y`-range [L] of the domain.

#. **z1, z2** :math:`z`-range [L] of the domain.

#. **botfracbnd** Elevation [L] of lowest extent of a fracture. No
   fractures will be generated below this elevation.

#. **nwell** Number of wells.

#. **xwell(i), ywell(i), i=1,nwell** :math:`xy`-coordinates [L] of the
   well. Generates :math:`x`- and :math:`y`-grid lines through each
   point.

#. **xsource1, xsource2** :math:`x`-coordinates [L] of the source.
   Generates :math:`x`-grid lines at these points.

#. **ysource1, ysource2** :math:`y`-coordinates [L] of the source.
   Generates :math:`y`-grid lines at these points.

#. **zsource1, zsource2** :math:`z`-coordinates [L] of the source.
   Generates :math:`z`-grid lines at these points.

#. **mingrspacx, mingrspacy, mingrspacz** Minimum grid spacing [L] in
   the :math:`x`-, :math:`y`-, and :math:`z`-directions, respectively.
   For example, a **mingrspacx** value of 1 would ensure that no
   gridlines are less than 1 length unit apart along the :math:`x`-axis.

#. **fixed_grid** Logical value (T/F) that controls whether grid lines
   are generated randomly (F) or according to fixed spacing input
   parameters (T). If true, then read the following:

   #. **fixed_space** Logical value (T/F) that controls whether uniform
      (T) or variable (F) grid line spacing is applied. If true, then
      read the following:

      #. **fixgrspacx, fixgrspacy, fixgrspacz** Fixed spacing [L] in the
         :math:`x`-, :math:`y`-, and :math:`z`-directions, respectively.

      Otherwise, read the following:

      #. **nx** Number of nodes in the :math:`x`-direction.

      #. **x(i), i=1,nx** Nodal :math:`x`-coordinates [L].

      #. **ny** Number of nodes in the :math:`y`-direction.

      #. **y(i), i=1,ny** Nodal :math:`y`-coordinates [L].

      #. **nz** Number of nodes in the :math:`z`-direction.

      #. **z(i), i=1,nz** Nodal :math:`z`-coordinates [L].

This instruction should be placed at the top of the file and should not
appear more than once.

--------------

| 

--------------

| Fracture information

#. **seed** Seed for the random number generator. If this number is
   changed, a new random number sequence is produced, which in turn
   causes new realizations of fracture location, length and aperture to
   be generated.

#. **xmeanfreq** Mean fracture frequency [L:math:`^{-1}`] in the
   :math:`x`-direction.

#. **ymeanfreq** Mean fracture frequency [L:math:`^{-1}`] in the
   :math:`y`-direction.

#. **zmeanfreq** Mean fracture frequency [L:math:`^{-1}`] in the
   :math:`z`-direction.

#. **decay** Aperture decay constant [L:math:`^{-1}`]. Aperture size can
   be made to decrease with increasing depth. Set to zero for no decay.

#. **lnsbetween** Minimum number of grid lines between fractures.

#. **cap** Maximum number of times to attempt generating a fracture.

This instruction should follow the Grid information instruction and
should not appear more than once.

--------------

| 

--------------

| Fracture location distribution x-axis

#. **type** An integer value indicating the probability distribution
   used to generate the variable fracture locations in the
   :math:`x`-direction. Acceptable values are:

   | A ĀA ̄ 1 Uniform.
   | 2 Normal.
   | 3 Exponential.

#. **var1, var2** Distribution parameters [L].

For the uniform distribution **var1** is the minimum and **var2** is the
maximum. For the normal distribution **var1** is the mean and **var2**
is the variance. For the exponential distribution **var1** is the mean
and **var2** is the standard deviation.

--------------

| 

The following instructions use the same input data structure as Fracture
location distribution x-axis except they are applied to the :math:`y`-
and :math:`z`-directions:

| 

The following instructions use the same input data structure as Fracture
location distribution x-axis to generate fracture lengths in the three
principal directions:

| 

The following instructions use the same input data structure as Fracture
location distribution x-axis to generate fracture apertures in the three
principal orientations:

| 

Note that when generating fracture apertures from the normal
distribution, random samples are truncated to the interval
:math:`(0,\infty)`. A negative fracture aperture generated from either
the truncated normal or uniform distribution will result in an error.

The remaining commands are optional but should not be used more than
once:

--------------

| Vertical fracture from top

#. **vertical_frac_top** Logical value (T/F), which if true, ensures
   that all vertical fractures start from the top of the domain.

--------------

| 

--------------

| Zone fractures how

#. **zone_rfgen_fracs** Controls how fracture zone numbers are assigned.
   Acceptable values are:

   | A ĀA ̄ 1 Assign zone numbers by fracture.
   | 2 Assign zone numbers by orientation.

If zoned by orientation, horizontal fractures are in zone 1, vertical
fractures parallel to the :math:`xy`-axis are in zone 2, and vertical
fractures parallel to the :math:`xz`-axis are in zone 3.

--------------

| 

--------------

| End

This instruction signals the end of the 3-D random fracture generator
input at which point control is passed back to the pre-processor.

--------------

| 

Once the 3-D grid is generated, it is possible to change the random
fracture apertures to zoned fracture apertures by following the
procedures outlined in
Section \ `[sec:modify_named_material] <#sec:modify_named_material>`__.

Axisymmetric Flow
~~~~~~~~~~~~~~~~~

--------------

| T
| his instruction is used for simulating radial flow to a well. It
  should only be applied to a vertical cross-section, of unit thickness
  in the :math:`y`-direction. The :math:`x`-coordinate is taken as the
  radial distance.

One should define a vertical cross-section of unit thickness in the
:math:`y`-direction (with two nodes in that direction), and locate a
pumping/injection well at the origin :math:`(x = 0)`.

--------------

| 

Manipulating the 3-D Grid
~~~~~~~~~~~~~~~~~~~~~~~~~

[sec:manipulate]

--------------

| 
| \*-.3in

#. **angle** Angle [deg] to rotate grid.

Rotates a grid by **angle** degrees about the :math:`x`-axis. Note that
a positive angle produces a counterclockwise rotation.

--------------

| 

--------------

| 
| \*-.3in

#. **angle** Angle [deg] to rotate grid.

Rotates a grid by **angle** degrees about the :math:`y`-axis. Note that
a positive angle produces a counterclockwise rotation.

--------------

| 

--------------

| Adapt grid to fractures

#. **adapt_g2f_mode** An integer value indicating how the grid is
   adapted to inclined fractures.

If block elements are used, two inclined fractures may intersect in the
middle of an element instead of on a grid node, so the fractures will
not be connected unless additional nodes are specified.

Acceptable values for the variable **adapt_g2f_mode** and the actions
taken in each case are:

| A ĀA ̄ 0 No action is taken.
| 1 New grid lines are added.
| 2 The block element is substituted by four prisms.
| 3 Inclined faces are not selected in the block element where the
  problem occurs.

The default value is 1.

--------------

| 

Ending Grid Generation
~~~~~~~~~~~~~~~~~~~~~~

--------------

| S
| ignals the end of the user-controlled portion of the grid definition
  section of the input data file. At this stage, the pre-processor will
  automatically perform grid modifications if appropriate. For example,
  if you read in 2-D slice data but did not specify layer information
  using for example, the Generate layers interactive instruction, the
  pre-processor would generate a default 3-D system by duplicating the
  2-D slice to form a single layer of unit-thickness elements.

--------------

| 

.. _app:output_files:

Output Files
============

Unless otherwise stated, the files described in this section are ASCII
formatted.

Grok
----

These files are created during regular execution:

| 123̄ ``array_sizes.default``
| See Section \ `[sec:array_defaults] <#sec:array_defaults>`__.
| ``grok.dbg``
| General purpose output file for debugging information.

These scratch files are created from scanning input files and removing
comments, blank lines, and white-space:

| 123̄ ``scratch_grok``
| Cleaned up copy of *prefix*\ ``.grok`` file.
| ``scratch_dprops``
| Cleaned up copy of ``.dprops`` file.
| ``scratch_fprops``
| Cleaned up copy of ``.fprops`` file.
| ``scratch_mprops``
| Cleaned up copy of ``.mprops`` file.
| ``scratch_oprops``
| Cleaned up copy of ``.oprops`` file.
| ``scratch_etprops``
| Cleaned up copy of ``.etprops`` file.
| ``scratch_cprops``
| Cleaned up copy of ``.cprops`` file.
| ``scratch_WellProps``
| Cleaned up copy of well properties file.
| ``scratch_TileProps``
| Cleaned up copy of tile properties file.
| ``fractran2f3d.lst``
| Listing file created if Read fractran 2d grid is used.

These files are created as data for a specific problem is processed.
Most are binary files that are read later by **HydroGeoSphere**:

| 123̄ *prefix*\ ``o.boundary_conditions``
| Boundary condition data.
| *prefix*\ ``o.ci``
| Initial nodal concentrations (binary).
| *prefix*\ ``o.coordinates_pm``
| 3-D subsurface mesh node coordinates (binary).
| *prefix*\ ``o.coordinates_dual``
| 3-D dual continua mesh node coordinates (binary).
| *prefix*\ ``o.coordinates_olf``
| 2-D surface flow mesh node coordinates (binary).
| *prefix*\ ``o.coordinates_frac``
| 2-D discrete fracture mesh node coordinates (binary).
| *prefix*\ ``o.coordinates_chan``
| 1-D channel flow mesh node coordinates (binary).
| *prefix*\ ``o.coordinates_well``
| 1-D well flow mesh node coordinates (binary).
| *prefix*\ ``o.coordinates_tile``
| 1-D tile flow mesh node coordinates (binary).
| *prefix*\ ``o.DecayRate_``\ *species*\ ``.0001``
| Elemental first-order decay constant for a solute (binary).
| *prefix*\ ``o.eco``
| **grok** listing file.
| *prefix*\ ``o.ElemIbedFraction_pm.0001``
| 3-D subsurface mesh elemental fraction of compressible interbeds
  (binary).
| *prefix*\ ``o.ElemK_dual.0001``
| 3-D dual continua mesh elemental hydraulic conductivity (binary).
| *prefix*\ ``o.ElemK_pm.0001``
| 3-D subsurface mesh elemental hydraulic conductivity (binary).
| *prefix*\ ``o.ElemPor_pm.0001``
| 3-D dual continua mesh elemental porosity (binary).
| *prefix*\ ``o.ElemStor_pm.0001``
| 3-D subsurface mesh elemental specific storage (binary).
| *prefix*\ ``o.ElemTort_pm.0001``
| 3-D subsurface mesh elemental tortuosity (binary).
| *prefix*\ ``o.elemental_rill_storage.dat``
| Elemental rill storage if Read rill storage from raster used.
| *prefix*\ ``o.elements_pm``
| 3-D subsurface mesh porous media element node lists (binary).
| *prefix*\ ``o.elements_dual``
| 3-D subsurface mesh dual continua element node lists (binary).
| *prefix*\ ``o.elements_olf``
| 2-D surface flow mesh element node lists (binary).
| *prefix*\ ``o.elements_frac``
| 2-D discrete fracture mesh element node lists (binary).
| *prefix*\ ``o.elements_chan``
| 1-D channel flow mesh element node lists (binary).
| *prefix*\ ``o.elements_well``
| 1-D well flow mesh element node lists (binary).
| *prefix*\ ``o.elements_tile``
| 1-D tile flow mesh element node lists (binary).
| *prefix*\ ``o.elements_et``
| 3-D subsurface mesh element ET zone, EDF, and RDF numbers (binary).
| *prefix*\ ``o.fac``
| 3-D subsurface mesh face node lists (binary).
| *prefix*\ ``o.gen``
| General input data (binary).
| *prefix*\ ``o.hi``
| Initial nodal heads (binary).

| 
| *prefix*\ ``o.nodal_fluid_mass_balance_selection.``\ *label*\ ``.dat``
| Node selection for nodal fluid mass balance commands.

| 
| *prefix*\ ``o.olf.ElemArea_MB``
| 2-D surface flow mesh element selection when
| Fluid mass balance for olf areas using shp file used (binary).
| *prefix*\ ``o.olf.WV_ElemArea``
| 2-D surface flow mesh element selection when
| Compute water volume by area using shp file used (binary).
| *prefix*\ ``o.p_s_table.``\ *prop*\ ``.dat``
| Pressure-saturation table for a material property.
| *prefix*\ ``o.pm.WV_ElemArea``
| 3-D subsurface mesh element selection when
| Compute water volume by area using shp file used (binary).
| *prefix*\ ``o.RegionalModelOutput``
| Regional model node and element mappings (binary).
| *prefix*\ ``o.s_k_table.``\ *prop*\ ``.dat``
| Saturation-relative permeability table for a material property.
| *prefix*\ ``o.seg``
| 3-D subsurface mesh segment node lists (binary).
| *prefix*\ ``o.siz``
| Array size data determined by **grok** and used by **HydroGeoSphere**
| for run-time array allocation.
| *prefix*\ ``o.species``
| Species (i.e. solute) data (binary).
| *prefix*\ ``o.wal``
| Cutoff wall elements (binary).

These files are created if the 2-D Random Fracture Generator
(Section `[sec:2drfgen] <#sec:2drfgen>`__) is used:

| 123̄ *prefix*\ ``o.rfrac.fractures``
| Listing file with fracture zone information.
| *prefix*\ ``o.rfrac.apertures``
| Listing file with fracture aperture information.
| *prefix*\ ``o.rfrac.lengths``
| Listing file with fracture length information.
| *prefix*\ ``o.rfrac.orientations``
| Listing file with fracture orientation information.

HydroGeoSphere
--------------

These files are created during regular execution:

| 123̄ ``hs.dbg``
| General purpose output file for debugging information.
| *prefix*\ ``.output_variable.control``
| Output variable control file.
| ``parallelindx.dat``
| Parallel simulation details.
| ``restart_file_info.dat``
| Restart information file.
| *prefix*\ ``o.restart``
| Restart head and concentration data file (binary).

These files are created for use with the Run-Time Debug Utility
described in Appendix \ `4 <#app:runtime_debug>`__:

| 123̄ ``debug.control``
| You can edit this file to change the run-time debug behaviour.
| ``scratch_debug``
| Cleaned up copy of ``debug.control`` file.
| ``progress.dat``
| Tecplot formatted file of CPU time vs system time.

These files are created for a specific problem as the simulation
progresses:

| 123̄ *prefix*\ ``o.age_statistics``
| Travel time PDF statistics.
| *prefix*\ ``o.avg_value_summary``
| Average value summary if Compute post simulation average used.
| *prefix*\ ``o.Bc.``\ *bcname*\ ``.dat``
| Tecplot formatted file for plotting a boundary condition.

| 
| *prefix*\ ``o.bclink_``\ *bcname*\ ``.dat``
| Total fluid flux at each timestep for a Flux nodal from outlet bc.

| 
| *prefix*\ ``o.cen``
| Nodal concentrations at the end of a simulation which may be used
| as initial concentrations for a simulation restart (binary).
| *prefix*\ ``o.conc.asv``
| Restart file for concentration if Auto save on used (binary).
| *prefix*\ ``o.cvo``
| Elemental velocity in channels.
| *prefix*\ ``o.dt``
| Timestep sizes if Time step sizes to file used.
| *prefix*\ ``o.ET_Detailed_MB_``\ *bcname*\ ``.dat``
| Detailed ET information for each PET boundary condition if Output ET
  details used.
| *prefix*\ ``o.flm``
| Mass flux if Flux output nodes used.
| *prefix*\ ``o.flm_b``
| Mass flux if Binary flux output nodes used (binary).
| *prefix*\ ``o.flm_sg``
| Surface water/groundwater advective-dispersive mass flux.
| *prefix*\ ``o.flu``
| Fluid flux if Flux output nodes used.
| *prefix*\ ``o.flu_b``
| Fluid flux if Binary flux output nodes used (binary).
| *prefix*\ ``o.flu_sg``
| Surface water/groundwater fluid flux.
| *prefix*\ ``o.fluid_entering_volume.``\ *volume*\ ``.dat``
| Mass flux information for a volume if slice fluxes are used.
| *prefix*\ ``o.fluid_exchange_chan.``\ *label*\ ``.dat``
| Exchange flux for channel flow domain chosen nodes.
| *prefix*\ ``o.fluid_exchange_olfz.dat``
| Exchange flux for each surface flow zone.
| *prefix*\ ``o.fluid_exchange_olf.``\ *label*\ ``.dat``
| Exchange flux for each surface flow domain for chosen nodes.
| *prefix*\ ``o.fluid_mass_balance.``\ *label*\ ``.dat``
| Surface flow mass balance for an area when
| Fluid mass balance for olf areas using shp file used.
| *prefix*\ ``o.fluid_volume.``\ *label*\ ``.dat``
| Volume of stored water in a region of the surface/subsurface.
| *prefix*\ ``o.head.asv``
| Restart file for head if Auto save on used (binary).
| *prefix*\ ``o.heat_source``
| Heat source at each time when Exponential zero order source is used.
| *prefix*\ ``o.hen``
| Final heads for restart (binary).
| *prefix*\ ``o.hp.dat``
| Heat pump injection/extraction temperature.
| *prefix*\ ``o.hydrograph.``\ *hydrograph*\ ``.dat``
| Total flux for a set of hydrograph nodes at each timestep.
| *prefix*\ ``o.inout_cdfs``
| Inlet/Outlet travel time CDFs.
| *prefix*\ ``o.inout_global_cdf``
| Inlet/Outlet global travel time CDF.
| *prefix*\ ``o.inout_global_cdf``
| Inlet/Outlet global travel time PDF.
| *prefix*\ ``o.inout_pdfs``
| Inlet/Outlet travel time PDFs.
| *prefix*\ ``o.isotherm.``\ *well*\ ``.dat``
| Observation well isotherm.
| *prefix*\ ``o.lst``
| **HydroGeoSphere** run-time output listing file.
| *prefix*\ ``o.mass_balance_cumulative.``\ *species*\ ``.dat``
| Cumulative solute mass balance at each timestep.
| *prefix*\ ``o.mass_balance_raw.``\ *species*\ ``.dat``
| Mass balance information for a solute at each timestep.
| *prefix*\ ``o.mass_balance_summary.``\ *species*\ ``.dat``
| Solute mass balance summary at each timestep.
| *prefix*\ ``o.mass_entering_volume.``\ *volume*\ ``.``\ *species*\ ``.dat``
| Mass flux information for a volume and solute if slice fluxes are
  used.
| *prefix*\ ``o.mass_exchange_olfz_``\ *species*\ ``.dat``
| Exchange flux for a solute for each surface flow zone.
| *prefix*\ ``o.maxvel_fr``
| Maximum discrete fracture velocity.
| *prefix*\ ``o.maxvel_pm``
| Maximum porous media velocity.
| *prefix*\ ``o.mf_interp``
| Interpolated mass flux.
| *prefix*\ ``o.newton_info.dat``
| Detailed Newton iteration information at each timestep for simulations
| with unsaturated flow conditions.

| 
| *prefix*\ ``o.nodal_fluid_mass_balance.``\ *label*\ ``.dat``
| Surface flow mass balance for an area when
| Nodal fluid mass balance for olf areas from shp file used.

| 
| *prefix*\ ``o.nodal_mass_storage.``\ *label*\ ``.``\ *species*\ ``.dat``
| Mass storage at a node at each timestep.
| *prefix*\ ``o.obc``
| Concentration for PEST calibration.
| *prefix*\ ``o.obs``
| Head for PEST calibration.
| *prefix*\ ``o.observation_well_conc.``\ *well*\ ``.``\ *species*\ ``.dat``
| Observation well concentration for a solute.
| *prefix*\ ``o.observation_well_conc_chem.``\ *well*\ ``.``\ *chemical*\ ``.dat``
| Observation well concentration for chemical species.
| *prefix*\ ``o.observation_well_flow.``\ *well*\ ``.dat``
| Observation well/point flow solution output.
| *prefix*\ ``o.observation_well_pdf_olf.``\ *well*\ ``.dat``
| Surface flow travel time PDF for observation well/point.
| *prefix*\ ``o.observation_well_pdf_pm.``\ *well*\ ``.dat``
| Porous media travel time PDF for observation well/point.
| *prefix*\ ``o.observation_well_silica_chemisty.``\ *well*\ ``.dat``
| Observation well concentration for silica solute.
| *prefix*\ ``o.observation_well_silica_chemisty_fracture.``\ *well*\ ``.dat``
| Observation well concentration for silica solute in fracture.

| 
| *prefix*\ ``o.particle_location.dat``
| Particle location when particle tracing is used.
| *prefix*\ ``o.particle_trace.``\ *trace_index*\ ``.dat``
| Trace path for each particle when particle tracing is used.
| *prefix*\ ``o.particle_travel_time.dat``
| Exit status and travel time for each particle when particle tracing is
  used.

| 
| *prefix*\ ``o.penetration_depth``
| Distance from domain top to elevation of the threshold concentration.
| *prefix*\ ``o.Rain_SnowMelt_balance.dat``
| Rain and snowmelt balance summary at each timestep when
| Rain and snowmelt bc is used.
| *prefix*\ ``o.reservoir_``\ *bcname*\ ``.dat``
| Pressure head and storage at each timestep for a Reservoir bc.
| *prefix*\ ``o.sim_time_report``
| Simulation time report file if Generate time report is used.
| *prefix*\ ``o.simoutflux``
| Outlet outflux PDF for production zone.
| *prefix*\ ``o.snow_balance.dat``
| Snow balance summary at each timestep when the Snowmelt bc is used.
| *prefix*\ ``o.soil_balance.dat``
| Amount of water in the partially- and fully-saturated subsurface, and
  in the surface.
| *prefix*\ ``o.Statistical_Plume_Props.dat``
| Statistical properties of plume.
| *prefix*\ ``o.tvo``
| Elemental velocity in tiles.
| *prefix*\ ``o.water_balance.dat``
| Fluid mass balance summary at each timestep.
| *prefix*\ ``o.water_table.``\ *label*\ ``.dat``
| Depth and elevation to water table at each timestep.
| *prefix*\ ``o.water_volume_olf_area.dat``
| Water volume stored in each surface flow area.
| *prefix*\ ``o.water_volume_olf_zone.dat``
| Water volume stored in each surface flow zone.
| *prefix*\ ``o.water_volume_pm_area.dat``
| Water volume stored in each porous media area.
| *prefix*\ ``o.water_volume_pm_zone.dat``
| Water volume stored in each porous media zone.
| *prefix*\ ``o.wvo``
| Elemental velocity in wells.
| *prefix*\ ``o.zone_fluid_balance.z.``\ *label*\ ``.dat``
| Surface flow mass balance for a zone when Fluid mass balance for olf
  zones is used.

The following binary output files may be created for a specific problem
at each output time, in this case number 0001, as the simulation
progresses. For many of these files, their creation is controlled by the
output variable control file,
*prefix*\ ``.output_variable``\ ``.control``, which is generated
automatically by **HydroGeoSphere** unless an existing file is found.
These files are processed for visualization by **HSPLOT** as described
in Appendix `7 <#app:hsplot>`__.

| 123̄ *prefix*\ ``o.absolute_ap_change.0001``
| Changes in fracture aperture.
| *prefix*\ ``o.absolute_kxx_change.0001``
| Changes in nodal hydraulic conductivity in :math:`x`-direction.
| *prefix*\ ``o.absolute_kyy_change.0001``
| Changes in nodal hydraulic conductivity in :math:`y`-direction.
| *prefix*\ ``o.absolute_kzz_change.0001``
| Changes in nodal hydraulic conductivity in :math:`z`-direction.
| *prefix*\ ``o.absolute_por_change.0001``
| Changes in nodal porosity.
| *prefix*\ ``o.compact_pm.0001``
| Porous media nodal compactions.
| *prefix*\ ``o.conc_chan.``\ *species*\ ``.0001``
| Channel flow concentration for a solute.
| *prefix*\ ``o.conc_dual.``\ *species*\ ``.0001``
| Dual continua concentration for a solute.
| *prefix*\ ``o.conc_frac.``\ *species*\ ``.0001``
| Discrete fracture concentration for a solute.
| *prefix*\ ``o.conc_olf.``\ *species*\ ``.0001``
| Surface flow concentration for a solute.
| *prefix*\ ``o.conc_pm.``\ *species*\ ``.0001``
| Porous media concentration for a solute.
| *prefix*\ ``o.conc_tile.``\ *species*\ ``.0001``
| Tile flow concentration for a solute.
| *prefix*\ ``o.conc_well.``\ *species*\ ``.0001``
| Well flow concentration for a solute.
| *prefix*\ ``o.conc_pm.``\ *species*\ ``-Redox.0001``
| Porous media redox capacity for a solute.
| *prefix*\ ``o.conc_tile.``\ *species*\ ``.0001``
| Tile flow concentration.
| *prefix*\ ``o.DeltaZ_pm.0001``
| Porous media nodal elevations.
| *prefix*\ ``o.DiffPeclet_pm.0001``
| Porous media element diffusion Peclet numbers if Output peclet number
  used.
| *prefix*\ ``o.DualExchFlux_olf.0001``
| Exchange flux between surface flow and dual continua domains.
| *prefix*\ ``o.DualExchSolAdv_olf.0001``
| Advective solute exchange flux between surface flow and dual continua
  domains.
| *prefix*\ ``o.DualExchSolDisp_olf.0001``
| Dispersive solute exchange flux between surface flow and dual continua
  domains.
| *prefix*\ ``o.envhead_pm.0001``
| Porous media environmental heads.
| *prefix*\ ``o.ETEvap_olf.0001``
| ET surface evaporation.
| *prefix*\ ``o.ETPmEvap_olf.0001``
| ET subsurface evaporation.
| *prefix*\ ``o.ETPmEvap3D_olf.0001``
| ET subsurface evaporation for 3-D mesh.
| *prefix*\ ``o.ETPmTranspire_pm.0001``
| ET subsurface transpiration.
| *prefix*\ ``o.ETPmTranspire3D_pm.0001``
| ET subsurface transpiration for 3-D mesh.
| *prefix*\ ``o.ETActual_olf.0001``
| Actual surface and subsurface ET.
| *prefix*\ ``o.ExchFlux_chan.0001``
| Exchange flux between porous media, surface flow, and channel flow
  domains.
| Positive fluxes are into the channel flow domain.
| *prefix*\ ``o.ExchFlux_frac.0001``
| Exchange flux between porous media and discrete fracture domains.
| Positive fluxes are into the discrete fracture domain.
| *prefix*\ ``o.Exchflux_olf.0001``
| Exchange flux between porous media and surface flow domains.
| Positive fluxes are into the surface flow domain.
| *prefix*\ ``o.ExchFlux_tile.0001``
| Exchange flux between porous media and tile flow domains.
| Positive fluxes are into the tile flow domain.
| *prefix*\ ``o.ExchFlux_well.0001``
| Exchange flux between porous media and well flow domains.
| Positive fluxes are into the well flow domain.
| *prefix*\ ``o.ExchFlux_pm2_chan.0001``
| Exchange flux from porous media to channel flow domain.
| *prefix*\ ``o.ExchFlux_olf2_chan.0001``
| Exchange flux from surface flow to channel flow domain.
| *prefix*\ ``o.ExchSolAdv_frac.0001``
| Advective solute exchange flux between porous media and fracture
  domains.
| *prefix*\ ``o.ExchSolAdv_olf.0001``
| Advective solute exchange flux between porous media and surface flow
  domains.
| *prefix*\ ``o.ExchSolDisp_frac.0001``
| Dispersive solute exchange flux between porous media and fracture
  domains.
| *prefix*\ ``o.ExchSolDisp_olf.0001``
| Dispersive solute exchange flux between porous media and surface flow
  domains.
| *prefix*\ ``o.exchsol_dual.``\ *species*\ ``.0001``
| Solute exchange flux between porous media and dual continua domains.
| *prefix*\ ``o.fluid_density.0001``
| Fluid densities.
| *prefix*\ ``o.flux_olf.0001``
| Specified flux over surface flow domain.
| *prefix*\ ``o.freeze_thaw_temp.0001``
| Porous media temperature.
| *prefix*\ ``o.friction_olf.0001``
| Surface flow friction.
| *prefix*\ ``o.head_chan.0001``
| Channel flow head.
| *prefix*\ ``o.head_dual.0001``
| Dual continua head.
| *prefix*\ ``o.head_frac.0001``
| Discrete fracture head.
| *prefix*\ ``o.head_olf.0001``
| Surface flow head.
| *prefix*\ ``o.head_pm.0001``
| Porous media head.
| *prefix*\ ``o.head_tile.0001``
| Tile flow head.
| *prefix*\ ``o.head_well.0001``
| Well flow head.
| *prefix*\ ``o.ice_sat_dual.0001``
| Dual continua ice saturation.
| *prefix*\ ``o.ice_sat_pm.0001``
| Porous media ice saturation.
| *prefix*\ ``o.iconc_pm.``\ *species*\ ``.0001``
| Porous media immobile concentration for a solute.
| *prefix*\ ``o.krw_pm.0001``
| Element-by-element relative permeability in porous media domain.
| *prefix*\ ``o.kxx.0001``
| Nodal hydraulic conductivity in :math:`x`-direction.
| *prefix*\ ``o.kyy.0001``
| Nodal hydraulic conductivity in :math:`y`-direction.
| *prefix*\ ``o.kzz.0001``
| Nodal hydraulic conductivity in :math:`z`-direction.
| *prefix*\ ``o.liquid_p.0001``
| Liquid precipitation over surface flow domain.
| *prefix*\ ``o.lp_chan.0001``
| Location probability for channel flow domain.
| *prefix*\ ``o.lp_frac.0001``
| Location probability for discrete fracture domain.
| *prefix*\ ``o.lp_olf.0001``
| Location probability for surface flow domain.
| *prefix*\ ``o.lp_pm.0001``
| Location probability for porous media domain.
| *prefix*\ ``o.Peclet_pm.0001``
| Porous media element Peclet numbers if Output peclet number used.
| *prefix*\ ``o.permafrost_frac.0001``
| Discrete fracture permafrost.
| *prefix*\ ``o.permafrost_pm.0001``
| Porous media permafrost.
| *prefix*\ ``o.pet_olf.0001``
| Nodal potential ET prescribed by Potential evapotranspiration bc.
| *prefix*\ ``o.por_pm.0001``
| Porous media porosity.
| *prefix*\ ``o.q_dual.0001``
| Dual-permeability Darcy flux.
| *prefix*\ ``o.q_pm.0001``
| Darcy flux.
| *prefix*\ ``o.rain_olf.0001``
| Liquid precipitation (including snowmelt) over surface flow domain.
| *prefix*\ ``o.rate_pm.0001``
| Porous media reaction rates.
| *prefix*\ ``o.sat_dual.0001``
| Dual continua saturation.
| *prefix*\ ``o.sat_frac.0001``
| Discrete fracture saturation.
| *prefix*\ ``o.sat_pm.0001``
| Porous media saturation.
| *prefix*\ ``o.snowcover.0001``
| Snow cover over surface flow domain.
| *prefix*\ ``o.snowdepth.0001``
| Snow depth over surface flow domain.
| *prefix*\ ``o.snowmelt.0001``
| Snowmelt over surface flow domain.
| *prefix*\ ``o.soilfrost_pm.0001``
| Porous media soil frost.
| *prefix*\ ``o.solid_p.0001``
| Solid precipitation over surface flow domain.
| *prefix*\ ``o.sw_pm.0001``
| Element-by-element water saturation in porous media domain.
| *prefix*\ ``o.tvk_pm.0001``
| Porous media time varying hydraulic conductivity.
| *prefix*\ ``o.v_dual.0001``
| Dual continua velocity.
| *prefix*\ ``o.v_frac.0001``
| Discrete fracture velocity.
| *prefix*\ ``o.v_olf.0001``
| Surface flow velocity.
| *prefix*\ ``o.v_pm.0001``
| Porous media velocity.

.. _app:runtime_debug:

Run-Time Debug Utility
======================

When using **HydroGeoSphere** to solve complex problems, especially
non-linear ones, it is often the case that the end user would like to,
for example, view intermediate results, modify input parameters or print
out the contents of various matrices. Normally, the program developer
would carry out such tasks with the aid of a debugger, which is normally
supplied as part of a program development package. Unfortunately, most
end users do not have access to such a package, and even if they did,
running a code which has been compiled in debug mode is generally much
slower than when it has been optimized for release to the public.

In order to address some of these problems, we have developed a run-time
debug utility which operates on the optimized
**HydroGeoSphere** executable and is able to be activated and
deactivated interactively without halting the execution of the program.

At various points during program execution, **HydroGeoSphere** checks
for the presence of a file called ``debug.control`` in the same
directory as the *prefix*\ ``.grok`` file and, if found, performs
certain actions as indicated in the file. If it is not found, a file
will be created which contains instructions for performing run-time
debug actions.

If you want to prevent **HydroGeoSphere** from performing run-time
debugging (including checking the debug control file), you can do so
using the instruction No runtime debug.

The head and tail of the ``debug.control`` file are shown in
Table \ `[tab:debug_control] <#tab:debug_control>`__.

::

       debug off
       ! ------------------------------------- Pause execution
       ! pause timestep
       ! pause at time
       !         10000.00000
       ! pause flow convergence loop
       ! ------------------------------------- Produce output
       ! write output files
       ! write saturated flow matrices

       ...etc...

       ! ------------------------------------- Adaptive timestep targets
       ! concentration change target
       !             0.05000
       ! mass change target
       !         0.10000E+21
       ! mass error target
       !           100.00000
       ! minimum timestep multiplier
       !             0.50000
       ! maximum timestep multiplier
       !             2.00000

[tab:debug_control]

Lines beginning with the comment character (!) are ignored. When first
generated, the only uncommented line is the first one, ``debug off``,
which causes **HydroGeoSphere** to run normally, and not to take any
run-time debug actions.

The contents of the file depend on the nature of the problem which is
being simulated. For example, if it has no transport component there
will not be any transport-related information written to the file.

To activate the debug utility, just comment out the ``debug off``
instruction, and uncomment one or more of the remaining instructions as
desired.

For example, if you modified and saved ``debug.control`` so it appeared
as shown in Table \ `[tab:debug_control_2] <#tab:debug_control_2>`__

::

       !debug off
       ! ------------------------------------- Pause execution
        pause timestep
       ...etc...

[tab:debug_control_2]

then **HydroGeoSphere** would pause at the end of the next timestep.

Note that if the ``debug.control`` file becomes corrupted, or if you
modify the problem in some way (e.g., activate transport) you can
automatically generate a fresh copy by deleting or renaming it before or
during **HydroGeoSphere** execution.

The effect caused by uncommenting the various instructions in the
``debug.control`` file will now be described, and input data
requirements will be discussed as required.

--------------

| Debug off

Ignore the rest of the contents of the ``debug.control`` file, whether
or not they are uncommented. To activate the run-time debug feature,
this line should be commented out.

--------------

| 

--------------

| Pause timestep

Pause at the beginning of the next timestep and issue the following
message on the screen:

``DEBUG CONTROL: pause timestep, press a key to continue``

This command is usually used to prevent other instructions, such as
Write output files, from producing too much output.

--------------

| 

--------------

| Pause at time

#. **time** Simulation time [T].

Proceed until the given simulation time is reached and then pause.

--------------

| 

--------------

| Pause newton loop

Pause at the beginning of the next Newton iteration.

--------------

| 

--------------

| Pause flow convergence loop

Pause at the beginning of the next flow solver iteration.

--------------

| 

--------------

| Write output files

Treat each timestep as if it was defined in the *prefix*\ ``.grok`` file
as an output time (i.e., using the instruction Output times). Head,
concentration, saturation, etc. output files will be written at each
timestep. This instruction is usually used in conjunction with Pause
timestep.

--------------

| 

--------------

| Watch node list

#. **node(i)...end** Node number list.

A list of node numbers for which you want detailed output.

--------------

| 

For each watch node, the following instructions write detailed output to
the ``hs.dbg`` file:

| 

Detailed output usually consists of the coefficient matrix and
right-hand side vector for the watch node.

In the case of the Write evapotranspiration instruction, the quantities
of fluid that are removed by the various process (e.g., canopy
evaporation, evaporation, transpiration, etc.) and the remaining
potential evapotranspiration are printed out at each timestep.

When using a feature such as Write flow matrices for example, be aware
that **HydroGeoSphere** may write a lot of information to disk, and so
they should be activated and deactivated for only a few timesteps at a
time.

--------------

| Write delval node info

Detailed information, including :math:`xyz`-coordinates, head,
saturation, and relative permeability, for the node corresponding to the
absolute maximum value of the linear solve solution (delval) at each
timestep is written to the *prefix*\ ``o.lst`` file.

--------------

| 

--------------

| Write resval node info

Detailed information, including :math:`xyz`-coordinates, head,
saturation and relative permeability, for the node corresponding to the
absolute maximum value of the Newton residual (resval) at each timestep
is written to the *prefix*\ ``o.lst`` file.

--------------

| 

--------------

| Write seepage face output to .lst file

Details of the seepage node calculations, including which nodes are
currently acting as seepage nodes and the fluid flux exiting the domain
at each active seepage node are written to the *prefix*\ ``o.lst`` file.

--------------

| 

--------------

| Time progress

Causes **HydroGeoSphere** to write a Tecplot file which contains values
of simulation time versus real time. The slope of this line is a measure
of the efficiency of **HydroGeoSphere** and it can be used to gauge how
effective changes in parameter values are in speeding up the simulation.
The first time this instruction is executed, the file ``progress.dat``
is created, and data is written to it as the run progresses. The results
for a given simulation are tagged with a date and timestamp. Results
from subsequent simulations are appended to the file so they can be
compared from run to run.

--------------

| 

--------------

| Write krw file

#. **filename** Name of the file to write the hydraulic conductivity
   values, at most 80 characters.

For each principal direction and for each element, outputs the product
of the relative permeability and the saturated hydraulic conductivity to
the file **filename**. This file can then be read in a subsequent
simulation using the instruction Read elemental k from file.

--------------

| 

--------------

| Time format

#. **type** Integer value indicating the type of format to use when
   writing time values to output files. Acceptable values are:

   | A ĀA ̄ 1 Fixed.
   | 2 Scientific.
   | 3 General.

--------------

| 

--------------

| Mass balance format

#. **type** Integer value indicating the type of format to use when
   writing mass balance output to file. Acceptable values are:

   | A ĀA ̄ 1 Fixed.
   | 2 Scientific.
   | 3 General.

--------------

| 

--------------

| Nodal flow check

#. **flow_check** Logical value (T/F), which if true, turns on the nodal
   flow check feature. Otherwise, it turns it off.

Turns on/off the nodal flow check feature. By default this feature is
turned off.

--------------

| 

--------------

| Force timestep

#. **delta_t** Timestep [T].

Sets all subsequent timesteps to **delta_t**.

--------------

| 

The following commands are identical to those used in the ``.grok``
input file and will just be listed here. They are used to modify
parameter values while **HydroGeoSphere** is running. Note that if you
change a parameter here, **HydroGeoSphere** will continue to use the new
value even if you disable run-time debugging (i.e., uncomment
instruction Debug off) or comment out the instruction that was used to
change the value.

| 

The following commands can be used to set the values of the various
targets used in the adaptive timestepping procedure.

| 

.. _app:runtime_output:

Run-Time Timestep Output
========================

| In this section we discuss the run-time output produced by HGS at each
  timestep when computing the flow solution. This information is useful
  for tracking the progress and convergence behaviour of the simulation
  as well as for diagnosing and remedying any poor performance issues.
  Before we dig in, a brief overview of the solution process in HGS will
  aide our discussion. At each timestep, HGS employs the NewtonRaphson
  iterative scheme to solve the nonlinear equations that arise from
  discretizing the flow equations. If we denote the solution vector by
  :math:`x` (typically pressure head), then the nonlinear equations
  being solved can be expressed as :math:`F(x) = 0`. In its most basic
  form, the NewtonRaphson iterative scheme for solving :math:`F(x) = 0`
  can be written as follows.

Set the initial guess for :math:`x` Build the Jacobian matrix
:math:`J \gets F'(x)` and Newton residual :math:`r \gets F(x)` Solve the
linear system :math:`J s = -r` for the Newton step vector :math:`s`
Update the relaxation factor :math:`\lambda` (if using), otherwise
:math:`\lambda \gets 1` Update the solution:
:math:`x \gets x + \lambda \cdot s` Check for convergence:
:math:`\|s\|_\infty \leq \tau_s` or :math:`\|r\|_\infty \leq \tau_r`

We will use the symbols and steps defined here when discussing the
run-time output.

Figure `[fig:runtime_out] <#fig:runtime_out>`__ shows a screen capture
of the run-time timestep output produced by HGS when running the Abdul
verification problem. The output consists of three sections: simulation
progress, summary of the nonlinear iteration, and the adaptive
timestepping update. We will discuss each of these sections in detail.

.. figure:: runtime_output.png
   :alt: Run-time timestep output for the Abdul verification problem.

   Run-time timestep output for the Abdul verification problem.

[fig:runtime_out]

Simulation Progress
-------------------

-  **Global Target Time:** This is the next target time that HGS is
   working toward. In the brackets () we can see that it is the third
   such target time out of nine total.

-  **%done:** The percentage of the simulation completed as measured
   from the initial start time to the previous successful simulation
   time. Note that the final target time is used as the end time of the
   simulation.

-  **Time:** The previous successful simulation time.

-  **delta_t:** The distance between the previous successful simulation
   time and the simulation time that is currently being computed.

-  **Tnext:** The simulation time at which the solution is currently
   being computed.

Summary of the Nonlinear Iteration
----------------------------------

-  **Iter:** The iteration number of the nonlinear solve step.

-  **Relfac:** The underrelaxation factor :math:`\lambda` for the Newton
   iteration.

-  **Delval:** The maximum absolute error of the current Newton
   iteration, given by :math:`s_i`, where :math:`i = \argmax_j |s_j|`
   and :math:`s` is the Newton step vector
   (:math:`\Delta \psi_{j}^{r+1}` in Equation \ `[eq:32] <#eq:32>`__).

-  **@Node:** The unknown number at which the absolute maximum occurs,
   i.e., :math:`i = \argmax_j |s_j|`.

-  **@NodePM:** The mesh node number at which the absolute maximum value
   occurs.

-  **NcNode:** The number of unknowns in :math:`s` that exceed the
   tolerance :math:`\tau_s` in absolute value (see the command Newton
   absolute convergence criteria).

-  **Resval:** The maximum absolute error of the current Newton
   iteration, given by :math:`r_i`, where :math:`i = \argmax_j |r_j|`
   and :math:`r` is the Newton residual vector (:math:`f_i^r` in
   Equation \ `[eq:32] <#eq:32>`__).

-  **@Node:** The unknown number at which the absolute maximum value
   occurs, i.e., :math:`i = \argmax_j |r_j|`.

-  **@NodePM:** The mesh node number at which the absolute maximum value
   occurs.

-  **NcNode:** The number of unknowns in :math:`r` that exceed the
   tolerance :math:`\tau_r` in absolute value (see the command Newton
   residual convergence criteria).

-  **Solv:** The linear system :math:`J s = -r` is solved approximately
   via an iterative solver, by default, BiCGStab. This value is the
   number of iterations required by the linear solve to satisfy its
   convergence criteria (see the commands Flow solver convergence
   criteria and Flow solver maximum iterations).

-  **Dom:** The domains at which **Delval** and **Resval** are achieved,
   respectively.

The final lines of this section indicate that convergence was achieved
after two iterations of the nonlinear solver. It shows the values of
**Delval** and **Resval**, and their corresponding convergence
tolerances. Note that only one such convergence criterion needs to be
satisfied.

Adaptive Timestep Update
------------------------

-  **Variable:** The quantity being tracked (i.e., head, water depth,
   saturation, and number of nonlinear solver iterations).

-  **Max. change:** The absolute maximum difference in the specified
   quantity between the last successful simulation time and the one
   being computed.

-  **Target change:** The targeted or expected change in the specified
   quantity over a single timestep as specified in the ``debug.control``
   file (see Appendix `4 <#app:runtime_debug>`__). For example, in the
   case of head, it would be the command Head change target.

-  **Dt multiplier:** The ratio of **Max. change** to **Target change**
   for the specified quantity.

-  **At node:** The index at which the maximum absolute difference
   occurs.

At the end of the timestep output it indicates that the solution at time
**Tnext** was accepted. It then shows how the next timestep is computed.
The timestep multiplier, :math:`\alpha_t`, is defined as the minimum of
all the **Dt multiplier** values, restricted to the interval
:math:`[\alpha_{t,\textrm{min}},\,\alpha_{t,\textrm{max}}]` (see
commands Minimum timestep multiplier and Maximum timestep multiplier).
The next timestep, :math:`{\Delta t}_\textrm{new}`, is then computed
from the previous timestep, :math:`\Delta t`, according to the formula:

.. math::

   \begin{aligned}
   {\Delta t}_\textrm{new} = \max\left(\min(\alpha_t\cdot\Delta t,\,{\Delta t}_\textrm{max}),\, {\Delta t}_\textrm{min}\right)\end{aligned}

The minimum and maximum timesteps can be set by the commands Minimum
timestep and Maximum timestep, respectively.

.. _app:win_batch_run:

**HSBATCH**: Batch Run Utility
==============================

A common requirement of a computer program is that it be able to run
several different problems in sequence, hopefully without user
intervention. For example, a Monte Carlo approach could require the user
to do several hundred runs with slightly varying data or parameter sets,
or a developer might want to execute a suite of verification runs with
each new release of the software.

With **HydroGeoSphere**  you have the capability to carry out multiple
batch runs by using the program **HSBATCH**. To meet our own
requirements, we have set up the verification examples for
**HydroGeoSphere** to be run in batch mode, and the end user can easily
adapt the approach to meet their own requirements.

The first step in setting up batch runs is to create directories that
contain all of the data files necessary to run the simulations. In the
case of the **HydroGeoSphere** verification problems, the
``verification`` directory nested under the
**HydroGeoSphere** installation directory ( on Windows) contains one or
more subdirectories for each verification problem. A partial list of its
contents are shown here:

::

       Directory of C:\Program_Files\HydroGeoSphere\verification

       2021-03-31  02:36 PM    <DIR>          .
       2021-03-31  02:36 PM    <DIR>          ..
       2021-03-31  02:36 PM    <DIR>          1D_backwards_transport
       2021-03-31  02:36 PM    <DIR>          1D_structures
       2021-03-31  03:42 PM    <DIR>          abdul
       ...etc...
       2021-03-31  03:42 PM    <DIR>          wang
       2021-03-31  03:42 PM    <DIR>          ward
       2021-03-31  03:42 PM    <DIR>          yang
       2021-03-31  01:28 PM                14 batch.pfx
       2021-03-31  01:31 PM             1,622 verification.hsbatch

First, a file with the extension ``.hsbatch`` is set up to run the
desired suite of batch problems. In this case we called it
``verification.hsbatch``, and here is a partial listing of its contents:

::

       !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
       ! Please specify file paths to the grok, hgs, and hsplot executables.
       ! File paths may be either absolute or relative. Relative file paths
       ! must be relative to the location of the .hsbatch file.
       !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
       grok executable path and name
       ..\grok.exe
       
       hydrogeosphere executable path and name
       ..\phgs.exe
       
       hsplot executable path and name
       ..\hsplot.exe
       
       ! verification problems
       batch directory list
           1D_backwards_transport\Backwards
           1D_backwards_transport\Forwards
   				
           ...etc...
   				
           wang
           ward
           yang
       end

The file can contain comments, which begin with an exclamation point
(!), blank lines, and Include instructions.

The instructions:

| Grok executable path and name
| Hydrogeosphere executable path and name
| Hsplot executable path and name

point to the locations of the program executable files. If the path to
an executable is not provided via one of these commands, then the
default location for that executable is used. In Windows, executables
are found by searching the default search path, which should contain the
installation directory following a typical program installation. Problem
data are to be found in subdirectories of the directory where the
``.hsbatch`` file is located, in this case .

The instruction Batch directory list is followed by a list of
subdirectories, one per line, that contain the problem data to be run in
batch mode, followed by an End instruction. Each subdirectory should
contain one set of problem data, and in order for the executables to run
without prompting the user for the problem prefix, it is necessary to
create a file called ``batch.pfx`` that consists of a single line
containing the problem prefix, in each problem directory.

To run **HSBATCH** for this example, open a command prompt, change
directories to , and type:

::

       hsbatch

As the program runs, output from **grok**, **HydroGeoSphere**, and
**HSPLOT** are echoed to the screen. A log of the **HSBATCH** process,
including program run times, is written to the file
*prefix*\ ``o.eco_hsbatch``.

A more complex situation could be one in which a user wants to use a set
of executables that are not in the default search path, or problem data
from more than one directory. The following example file illustrates how
to handle these types of situations:

::

       ! HydroGeoSphere batch processor input file
       !
       ! If you use this file without setting paths to the grok, hgs,
       ! and hsplot executables, then it will use the ones it finds
       ! in the default search path.
       !
       ! File paths may be either absolute or relative. Relative file 
       ! paths must be relative to the location of the .hsbatch file.

       grok executable path and name
       C:\Program_Files\HydroGeoSphere\grok.exe

       hydrogeosphere executable path and name
       C:\Program_Files\HydroGeoSphere\phgs.exe
   		
       hsplot executable path and name
       C:\Program_Files\HydroGeoSphere\hsplot.exe

       ! First run a verification problem
       change directory
       C:\Program_Files\HydroGeoSphere\verification

       batch directory list
       pm_cd
       end

       ! Now run an illustrative problem
       change directory
       C:\Program_Files\HydroGeoSphere\illustration

       batch directory list
       reactive_iron_barrier_in_fractures
       end

The command Change directory sets the parent directory to apply to all
subdirectories listed in the subsequent Batch directory list. As you can
see, the file can contain multiple sets of Change directory and Batch
directory list instructions. In this case, the location of the
``.hsbatch`` file is not critical, since absolute paths to the problem
data are supplied.

.. _app:hsplot:

**HSPLOT**: Visualization Post-Processor
========================================

The utility program **HSPLOT** can be used to convert raw
**HydroGeoSphere** output into files that are compatible with
third-party visualization packages. Similar to **grok** and
**HydroGeoSphere**, when **HSPLOT** starts executing, it requires a
problem prefix that can be entered interactively from the keyboard or
supplied via the ``batch.pfx`` file. The first time you run
**HSPLOT** for a specific problem, it creates the plot control file,
*prefix*\ ``.plot.control``, which contains a list of instructions that
affect the output file format and contents. Note that if the plot
control file becomes corrupted you can automatically generate a fresh
copy by deleting or renaming it before executing **HSPLOT**.

We will now discuss the various sections of the plot control file and
how they can be modified to produce the desired outputs.

Initialization
--------------

The first section of the plot control file controls which output format,
whether the time domain is truncated, and the data type (single or
double) of the :math:`x`- and :math:`y`-coordinate variables. This
section is terminated by an End command. Currently, **HSPLOT** supports
Tecplot and Paraview output formats. The first two lines of the plot
control file are:

::

        tecplot mode
       ! paraview mode

Lines beginning with the comment character (!) are ignored, and so by
default, the file is set up to generate Tecplot formatted output (i.e.
Tecplot mode instruction is uncommented). Uncommenting one of these two
instructions has the following effect:

--------------

| Tecplot mode

Causes **HSPLOT** to generate Tecplot formatted output.

--------------

| 

--------------

| Paraview mode

Causes **HSPLOT** to generate Paraview formatted output.

--------------

| 

The following command can be used to truncate the range of output times.

--------------

| 
| \*-.3in

#. **t1, t2** Time range [T] of the domain.

Causes **HSPLOT** to restrict the time range of the output so that only
output times that are between times **t1** and **t2** (inclusive) are
included. Note that the first output time is always included.

--------------

| 

--------------

| B
| y default all Tecplot variables within each zone have single
  precision. This command sets the precision of the :math:`x`- and
  :math:`y`-coordinate variables within each zone to be double
  precision.

--------------

| 

--------------

| 
| \*-.3in

#. **suffix_len** Number of digits in the output file suffix, an integer
   between 4 and 10, inclusive.

Sets the number of digits in the output file suffix to **suffix_len**.
By default the number of digits is set to four. Note that the number of
digits set in **HSPLOT** should match the number set in **grok**,
otherwise, **HSPLOT** will be unable to locate the output files.

--------------

| 

After reading the initialization section, the plot control file is read
until a terminating End command is encountered. Detailed output is
written to the file *prefix*\ ``o.hsplot.eco`` each time **HSPLOT** is
run.

Domain Specific Output
----------------------

The next sections of the plot control file control output for each of
the domains: *pm, dual, frac, olf, well, chan,* and *tile*. For example,
the plot control file could include the following section for the porous
media domain:

::

       write pm domain file
       ! no pm heads
       ! no pm linear velocities (vx, vy, vz)
       ! no pm darcy velocities (vx, vy, vz)
       ! no pm elemental k
       ! no pm elemental porosity
       ! isolate node
       !  1     ! node number
       !  0     ! extension factor
       ! truncate 3d domain
       ! -0.10000000E+21 0.10000000E+21
       ! -0.10000000E+21 0.10000000E+21
       ! -0.10000000E+21 0.10000000E+21
       ! truncate time domain
       ! -0.10000000E+21 0.10000000E+21
       ! ----------------------------

In general, each section begins with a general instruction that controls
whether or not that domain’s output is written, followed by a group of
instructions that can be used to tailor the output for that domain. For
example, in the case of the porous media domain we have the following
instruction.

--------------

| C
| auses **HSPLOT** to write the output files for the porous media
  domain. The default Tecplot formatted output file would contain all
  available data (i.e. heads, saturations, concentrations, velocities,
  etc.) at each output time and for the entire 3-D domain and would be
  named *prefix*\ ``o.pm.dat``.

--------------

| 

Similar commands exist for the other domains:

| 

In general, each command produces an output file named
*prefix*\ ``o.``\ *domain*\ ``.dat``, where *domain* is the
corresponding domain name. Instructions that follow one of these
commands prevent/allow **HSPLOT** from writing the named variables to
the output file. For example, in the case of the porous media domain the
following instructions are available:

| 

Some of these instructions are available for other domains by swapping
“pm” in the command name for the appropriate domain name. In general,
the presence of the instructions listed in the plot control file depends
on the domain and on the nature of the simulation. For example,
saturations would not be written unless the system is variably-saturated
and the No pm saturations instruction would then not appear in the plot
control file. Analogously, an instruction such as No frac depth to water
table would not be written since it is not meaningful for the discrete
fracture domain.

The commands Truncate pm domain and Truncate pm domain by layer require
additional inputs as described below.

--------------

| 
| \*-.3in

#. **x1, x2** :math:`x`-range [L] of the domain.

#. **y1, y2** :math:`y`-range [L] of the domain.

#. **z1, z2** :math:`z`-range [L] of the domain.

Causes **HSPLOT** to restrict the output domain size to within the given
:math:`xyz`-ranges. This command is used to zero in on an interesting
subregion of the 3-D domain or to reduce the size of the output file and
speed up file I/O. For example,

::

   	    truncate pm domain by layer
   	    100.0     200.0
         0.0    1000.0
       -0.10000000E+21 0.10000000E+21
   	

would reduce the domain size to the :math:`x`-range from 100 to 200, the
:math:`y`-range from 0 to 1000, but would leave the :math:`z`-range at
its full extent.

--------------

| 

--------------

| 
| \*-.3in

#. **x1, x2** :math:`x`-range [L] of the domain.

#. **y1, y2** :math:`y`-range [L] of the domain.

#. **lower, upper** Lower and upper layer numbers (inclusive).

Causes **HSPLOT** to restrict the output domain size to within the given
:math:`xy`-ranges and layer range. This command is used to zero in on an
interesting subregion of the 3-D domain or to reduce the size of the
output file and speed up file I/O. For example,

::

   	    truncate pm domain by layer
   	    100.0     200.0
         0.0    1000.0
         1         5
   	

would reduce the domain size to the :math:`x`-range from 100 to 200, the
:math:`y`-range from 0 to 1000, and would truncate the domain to layers
15.

--------------

| 

The instructions available for each of the other domains are listed in
the following subsections.

Dual Continua Output
~~~~~~~~~~~~~~~~~~~~

| 

Discrete Fracture Domain Output
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

| 

Surface Flow Domain Output
~~~~~~~~~~~~~~~~~~~~~~~~~~

| 

Tile Flow Domain Output
~~~~~~~~~~~~~~~~~~~~~~~

| 

Channel Flow Domain Output
~~~~~~~~~~~~~~~~~~~~~~~~~~

| 

Well Flow Domain Output
~~~~~~~~~~~~~~~~~~~~~~~

| 

General Output Control
----------------------

Additional commands are available that allow you to further tailor the
output files produced by **HSPLOT**. Note that each of these commands
should be issued at most once within the plot control file.

--------------

| 
| \*-.3in

#. **x1, x2** :math:`x`-range [L] of the domain.

#. **y1, y2** :math:`y`-range [L] of the domain.

#. **z1, z2** :math:`z`-range [L] of the domain.

Causes **HSPLOT** to write Tecplot formatted output files
*prefix*\ ``o.compare_heads``\ ``.dat`` and
*prefix*\ ``o.compare_heads_scatter``\ ``.dat`` that contain simulated
versus observed head data. Only head data that falls within the
restricted region defined by the :math:`xyz`-ranges is included. Note
that this instruction will only be present in the plot control file if
an observed head data file called *prefix*\ ``.observed_heads`` is
present in the directory that contains the *prefix*\ ``.grok`` file. The
file *prefix*\ ``.observed_heads`` should be of the following form:

::

   	    1255
   	    8.29842E+05	2.41079E+06	2.85000E+02 2.85000E+02
   	    8.34476E+05	2.39450E+06	2.86900E+02 2.95100E+02
   	    ...etc.
   	

where the first line is the number of observed head points in the file,
followed by one line for each point that contains the
:math:`xyz`-coordinates and observed head value at each point.

--------------

| 

--------------

| C
| auses **HSPLOT** to write Tecplot formatted output files
  *prefix*\ ``o.compare_GWTable_``\ ``Elevation.dat`` and
  *prefix*\ ``o.compare_GWTable_Elevation_scatter``\ ``.dat`` that
  contain simulated versus observed groundwater table elevation data.
  Note that this instruction will only be present in the plot control
  file if an observed groundwater table elevation file called
  *prefix*\ ``.observed_heads`` is present in the directory that
  contains the *prefix*\ ``.grok`` file.

--------------

| 

--------------

| C
| auses **HSPLOT** to write Tecplot formatted output files
  *prefix*\ ``o.compare_Depth2GWTable``\ ``.dat`` and
  *prefix*\ ``o.compare_D2GWT_scatter``\ ``.dat`` that contain simulated
  versus observed depth to groundwater data. Note that this instruction
  will only be present in the plot control file if an observed depth to
  groundwater file called *prefix*\ ``.observed_d2gwt`` is present in
  the directory that contains the *prefix*\ ``.grok`` file.

--------------

| 

--------------

| C
| auses **HSPLOT** to generate the Tecplot formatted output file
  *prefix*\ ``o.pm_Isopach``\ ``.dat`` that contains porous media domain
  isopach data. Note that this command requires a surface flow domain to
  be present.

--------------

| 

--------------

| C
| auses **HSPLOT** to generate the Tecplot formatted output file
  *prefix*\ ``o.pm_DepthToSurf``\ ``.dat`` that contains the depth of
  the porous media domain below ground surface. Note that this command
  requires a surface flow domain to be present.

--------------

| 

--------------

| 
| \*-.3in

#. **x1, x2** :math:`x`-range [L] of the domain.

#. **y1, y2** :math:`y`-range [L] of the domain.

#. **z1, z2** :math:`z`-range [L] of the domain.

Causes **HSPLOT** to write a Tecplot formatted output files
*prefix*\ ``o.compare_heads_olf``\ ``.dat`` and
*prefix*\ ``o.compare_heads_olf_scatter``\ ``.dat`` that contain
simulated versus observed head data. Only head data that falls within
the restricted region defined by the :math:`xyz`-ranges is included.
Note that this instruction will only be present in the plot control file
if an observed head data file called *prefix*\ ``.observed_heads_olf``
is present in the directory that contains the *prefix*\ ``.grok`` file.
Note that this command requires a surface flow domain to be present.

--------------

| 

--------------

| 
| \*-.3in

#. **node_num** Number of node to be isolated.

#. **extension_factor** An integer with a value of zero or greater.

Causes **HSPLOT** to restrict the output domain so that only elements
that contain **node_num** are plotted (**extension_factor** = 0).
Increasing **extension_factor** causes neighbouring elements to be
plotted as well. For example, **extension_factor** = 1 would be used to
include elements that share a node with an element that contains
**node_num**.

--------------

| 

--------------

| 
| \*-.3in

#. **time_offset, time_unit_factor** Time offset [T:math:`_o`] and
   scaling factor [T:math:`_o` T:math:`^{-1}`].

Causes **HSPLOT** to update the time stamp :math:`t` embedded within
each output file it processes via the equation

.. math:: t_\textrm{new} = \textbf{time\_offset} + \textbf{time\_unit\_factor}\cdot t

Note that the time unit T\ :math:`_o` may or may not be the same as T.

--------------

| 

--------------

| C
| auses **HSPLOT** to write each Tecplot formatted boundary condition
  location file, *prefix*\ ``o.Bc.``\ *bcname*\ ``.dat``, to the
  corresponding Paraview VTK formatted output file
  *prefix*\ ``o.Bc.``\ *bcname*\ ``.vtk``.

--------------

| 

.. _app:gms_files:

GMS File Formats
================

The following description is taken from the GMS Reference Manual,
Version 1.1. GMS divides files into logical units called cards. The
first component of each card is a short name which serves as an
identifier. The rest of the line contains information associated with
the card. Some cards can use multiple lines.

Two-Dimensional Meshes (Slices)
-------------------------------

[app:2dmesh]

The instructions Read gms 2d grid and 2D mesh to gms read and write 2-D
mesh data in GMS format, respectively. The portion of the 2-D mesh file
format recognized by **grok** is as follows:

::

     MESH2D                          ! File type identifier
     E3T id n1 n2 n3 mat             ! 3-node triangle
     E4Q id n1 n2 n3 n4 mat          ! 4-node quadrilateral
     ND id x y z                     ! Nodal coordinates

The cards E6T (6-node triangles) and E8Q (8-node quadrilaterals) are not
recognized by **grok**.

The card types used in the 2-D mesh file are as follows.

===============
===============================================================
**Card Type**   MESH2D
**Description** File type identifier. Must be on first line of file. No Fields.
**Required**    YES
===============
===============================================================

=============== ============ =========
==============================================================
**Card Type**                         
**Description**                       
**Required**                          
**Format**                            
**Sample**                            
**Field**       **Variable** **Value** **Description**
1               id           +         The id of the element.
2–4             n1–n3        +         The nodal incidences of the elements ordered counterclockwise.
5               mat          +         The material id of the element.
=============== ============ =========
==============================================================

=============== ============ =========
==============================================================
**Card Type**                         
**Description**                       
**Required**                          
**Format**                            
**Sample**                            
**Field**       **Variable** **Value** **Description**
1               id           +         The id of the element.
2–5             n1–n4        +         The nodal incidences of the elements ordered counterclockwise.
6               mat          +         The material id of the element.
=============== ============ =========
==============================================================

=============== ============ =========== ======================
**Card Type**                           
**Description**                         
**Required**                            
**Format**                              
**Sample**                              
**Field**       **Variable** **Value**   **Description**
1               id           +           The id of the node.
2–4             x, y, z      :math:`\pm` The nodal coordinates.
=============== ============ =========== ======================

ASCII Scalar Data Set Files
---------------------------

[sec:ab_fmt] The instruction Zone by layer can read a nodal data set in
order to define a variable surface (usually an elevation for the
:math:`z`-coordinate) for the base of the 3-D grid or the top of a
layer. This file should be written in GMS ASCII format.

The ASCII file format recognized by **grok** is as follows:

::

       DATASET                         ! File type identifier
       ...etc...
       ND n                            ! Number of data values
       ...etc...
       TS time                         ! Time step of the following data
       val_1                           ! Scalar data values
       val_2

       ...etc...

       val_n

In this case, the value ``n`` should correspond to the number of nodes.
Currently, the first line of the file must contain the string
``DATASET``, followed at some point by a ``ND`` card and then a ``TS``
card. Other other cards may be present in the file (e.g., ``STAT``) but
they will be ignored. You should not include status flag information in
files to be read by **grok**. **grok** only reads one set of scalar data
values per file.

The card type formats are as follows.

===============
===============================================================
**Card Type**   DATASET
**Description** File type identifier. Must be on first line of file. No Fields.
**Required**    YES
===============
===============================================================

=============== ============ =========
==================================
**Card Type**                         
**Description**                       
**Required**                          
**Format**                            
**Sample**                            
**Field**       **Variable** **Value** **Description**
1               n            +         The number of nodes per 2-D slice.
=============== ============ =========
==================================

=============== ============ ===========
==========================================
**Card Type**                           
**Description**                         
**Required**                            
**Format**                              
\                                       
\                                       
\                                       
\                                       
**Sample**                              
\                                       
\                                       
\                                       
\                                       
**Field**       **Variable** **Value**   **Description**
1               time         :math:`\pm` The time step value. Not used by **grok**.
2–(n+1)         val          :math:`\pm` The scalar values for each item.
=============== ============ ===========
==========================================

.. _app:raster_files:

Raster File Formats
===================

Raster data consists of a set of values that are laid out on a
rectangular grid with uniform spacing in the :math:`x`- and
:math:`y`-directions. The raster coordinates need not coincide with
**grok** mesh nodes and can even fall outside the model domain.

The raster must consist of header information containing a set of
keywords, followed by cell values in row-major order. The file format is
based on the ArcGIS ASCII file conventions and must consist of the
following:

::

       NCOLS xxx
       NROWS xxx
       XLLCORNER xxx
       YLLCORNER xxx
       CELLSIZE xxx
       NODATA_VALUE xxx
       row 1
       row 2
       .
       .
       .
       row n

where ``NCOLS`` and ``NROWS`` are the number of rows and columns in the
raster respectively, ``XLLCORNER`` and ``YLLCORNER`` are the :math:`x`-
and :math:`y`-coordinates of the lower left corner of the raster,
``CELLSIZE`` is the size of each raster cell, ``NODATA_VALUE`` is the
value in the ASCII file representing cells whose true value is unknown
(i.e., missing) and ``xxx`` is a number. Row 1 of the data is at the top
of the grid, row 2 is just under row 1, and so on. For example:

::

       ncols 480
       nrows 450
       xllcorner 378923
       yllcorner 4072345
       cellsize 30
       nodata_value -32768
       43 2 45 7 3 56 2 5 23 65 34 6 32 54 57 34 2 2 54 6
       35 45 65 34 2 6 78 4 2 6 89 3 2 7 45 23 5 8 4 1 62  ...

Cell values should be delimited by spaces. No carriage returns are
necessary at the end of each row in the grid. The number of columns in
the header is used to determine when a new row begins. The number of
cell values must be equal to the number of rows times the number of
columns.

.. [1]
   grok /grok/, var. /grohk/ vt. [from the novel “Stranger in a Strange
   Land”, by Robert A. Heinlein, where it is a Martian word meaning
   literally ‘to drink’ and metaphorically ‘to be one with’] The
   emphatic form is ‘grok in fullness’. 1. To understand, usually in a
   global sense. Connotes intimate and exhaustive knowledge. Contrast
   zen, which is similar supernal understanding experienced as a single
   brief flash. See also glark. 2. Used of programs, may connote merely
   sufficient understanding. “Almost all C compilers grok the void type
   these days.”

.. |image| image:: front_matter/aquanty_logo.png
   :width: 30.0%
