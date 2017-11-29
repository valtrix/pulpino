# Introduction

This README file contains the instructions to setup the PULPino testbench for
STING execution.

## Steps

Run the following commands in the same order to setup the test bench

The repository can be cloned using the following command

    git clone https://github.com/valtrix/pulpino.git

Once the cloning is complete, checkout the branch on which all the development
has been done

    cd pulpino
    git checkout pulpino_05f0dbe

Run the dotbashrc script to include the paths to ModelSim and PULPino toolchain
installation in the PATH environment variable. Make sure to override the correct
installation paths from your system in the script

    source dotbashrc

PULPino uses multiple git subrepositories which can be cloned and updated, using

    ./update-ips.py

After fetching all the IPs, run the following script to apply the patches which
are required to run STING on PULPino test bench

    ./patch-ips.sh


In order to run simulations, create a temporary build directory inside sw folder

    cd sw; mkdir build

Copy the cmake-configure.riscv.gcc.sh bash script to the build folder. It has been
already updated to enabled compressed instructions

    cp cmake_configure.riscv.gcc.sh build

Execute the cmake script inside the build folder to setup everything to perform
simulations using ModelSim.

    ./cmake_configure.riscv.gcc.sh

Inside the build folder, execute the following command to compile the RTL libraries
using ModelSim.

    make vcompile

A dummy STING executable needs to be created the first time we setup the test bench.

    make sting.elf

This will touch the source files and setup a working area inside the build directory.
In subsequent executions, STING executables will be copied into the build directory
from outside.

    cp <STING.ELF> sw/build/apps/sting

To run simulations in the modelsim GUI use

    make sting.vsim

In case batch processing of a large number of tests is desired, run the following command
to launch it in console.

    make sting.vsimc

The waves, core trace and simulation output will be copied into sw/build/apps/sting at the
end of simulation

### Overview of Testbench Changes

The default memory map has been changed in this test bench in order to facilitate STING
execution.

- Instead of a split instruction and data RAM of 16K each, a single (unified) RAM of 2M
  size has been created. A larger memory block allows more number of test instructions
  and larger data buffers.

- The boot address has been changed to 0x2000. This has been done to match the memory
  buffer used for the reference run on SPIKE.

- The ROM start has been moved to 0x80000000 as the unified RAM was overlapping with the
  ROM start in the default memory map
