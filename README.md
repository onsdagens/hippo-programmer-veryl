# Veryl Hippomenes-Programmer

This repo aims to implement the Hippomenes Programmer (inspired by the [original SystemVerilog implementation](https://github.com/onsdagens/hippo-programmer)).

The current implementation consists of a top module (`Programmer`) instantiating a JTAG module.
The JTAG module provides an `IDCODE` register according to the JTAG specification, and a custom, 8-bit register under instruction `0x1` for receiving data from the host.

The `Programmer` module does the clock domain crossing and provides an easier interface for just getting the bytes out of the JTAG.
While `rdy_o` is held high, a received byte is available on the `data_o` line.

An example is provided in the `fpga_top` module, instantiating a small memory and allowing writing to it over JTAG.


## Dependencies

The toolchain used to transpile Veryl into Systemverilog is the standard [Veryl toolchain](https://veryl-lang.org/install).

Additionally, this project includes configuration for synthesis, place and route, and programming of the Numato Lab ECP-5 Mimas Mini development board. For these additional steps, we need the following tools:

- [Yosys](https://github.com/YosysHQ/yosys?tab=readme-ov-file#building-from-source) for synthesis
- [Slang] (https://github.com/MikePopoloski/slang) as a SystemVerilog frontend for Yosys
- [NextPNR](https://github.com/YosysHQ/nextpnr?tab=readme-ov-file#getting-started) for place and route
- [OpenOCD](https://openocd.org/pages/getting-openocd.html) for programming the device
- [Project Trellis](https://github.com/YosysHQ/prjtrellis) for bitstream generation, and other device specifics.

To interact with the JTAG interface, we have been using the [`ftdaye`](https://github.com/onsdagens/ftdaye) library. The `ECP5` example contains minimal code for connecting, reading out the `IDCODE` register and writing to the `0x1` custom register.

## Building and programming

With the toolchain set up, and the dev board connected, run 
```
make flash
```
to build the project and reprogram the FPGA. 

## Expected behavior

A 16-byte memory is instantiated with `[0x0..0xF]`. The monochrome LEDs display memory data one 4 bit chunk at a time. The currently displayed
index can be incremented/decremented by button 0 and 1 respectively. Writing a byte to the JTAG register `0x1` overwrites the data under the current index with the received value.

For our own (very simple testing) purposes, we have been using the `ftdaye` [`ECP5`](https://github.com/onsdagens/ftdaye/blob/master/examples/ECP5.rs) example to read out the `IDCODE` register, and for writing to the `0x1` custom register.