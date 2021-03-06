# Millfork benchmarks on Zilog Z80

### Measurement methodology

The target platform is ZX Spectrum 48k emulated via the Fuse emulator, version 1.3.3.

The program measures the number of frames as counted by a 24-bit variable `FRAMES` at address 23672.

The program is not allowed to disable or modify interrupts, except for disabling interrupts temporarily twice to read the `FRAMES` variable.

The `benchcommon` library provides the necessary `start` and `end` functions. The program should call `start`, run the benchmark, call `end` and enter an infinite loop.

The result is displayed on the screen as a hexadecimal value. Results below have been converted to decimal.

### Compared implementations

These benchmarks compare the Millfork compiler with two popular C compilers for Z80:

|&nbsp;&nbsp;&nbsp;&nbsp; | symbol               | compiler | version  | command line                                                                               | comment |
|-------------------------|----------------------|----------|----------|--------------------------------------------------------------------------------------------|-|
|![](../images/404040.png)| asm                  | Millfork | 0.3.22   | `millfork -t zxspectrum -O1`                                                               | assembly implementation |
|![](../images/008000.png)| mfk-0.3.22-z80       | Millfork | 0.3.22   | `millfork -t zxspectrum -O1`                                                               | minimal optimization level to not generate dumb code |
|![](../images/00aa44.png)| mfk-0.3.22-z80-o4    | Millfork | 0.3.22   | `millfork -t zxspectrum -O4 -finline -fipo`                                                |  |
|![](../images/44ee44.png)| mfk-0.3.22-8080      | Millfork | 0.3.22   | `millfork -t zxspectrum_8080 -O1`                                                          | without Z80 extensions |
|![](../images/dd0000.png)| sccz80-20170112      | SCCZ80   | 20170112 | `zcc +zx -lndos -clib=ansi -O3 -Cc -do-inline -create-app`                                 | optimizations to give it a fighting chance |
|                         | zsdcc-20170101-unopt | ZSDCC    | 20170101 | `zcc +zx -clib=sdcc_ix -Cs --reserve-regs-iy -create-app`                                  | no optimizations |
|![](../images/882ed8.png)| zsdcc-20170101-opt   | ZSDCC    | 20170101 | `zcc +zx -clib=sdcc_ix -Cs --reserve-regs-iy -SO3 --max-allocs-per-node200000 --allow-unsafe-read -create-app` | recommended options for good code |

Remarks:

* ZSDCC and SCCZ80 come from the Z88DK v1.99B.

* `millfork` is a script launching the Millfork compiler with the correct `-I` option.

* Input and output file options have been omitted for brevity.

* Charts show the most optimized results.

### Benchmark `sieve`

Source: [`sieve.c`](./sieve.c), [`sieve.mfk`](./sieve.mfk)

The benchmark calculates all the prime numbers from 1 to 2^(14) using the Sieve of Erasthotenes algorithm. It does it three times, for more precise results.

Results:

|                         | symbol               | frames (less is better) |
|-------------------------|----------------------|-------:|
|                         | asm                  | –   |
|![](../images/008000.png)| mfk-0.3.22-z80       | 71 |
|                         | mfk-0.3.22-z80-o4    | 71 |
|![](../images/44ee44.png)| mfk-0.3.22-8080      | 91 |
|![](../images/dd0000.png)| sccz80-20170112      | 482 |
|                         | zsdcc-20170101-unopt | 112 |
|![](../images/882ed8.png)| zsdcc-20170101-opt   | 107 |
![](https://image-charts.com/chart?cht=bhg&chs=700x400&chd=t:71|91|482|107&chds=0,1000&chdl=mfk-0.3.22-z80|mfk-0.3.22-8080|sccz80-20170112|zsdcc-20170101-opt&chtt=Sieve%20benchmark%20(time%20in%20frames,%20less%20is%20better)&chma=10,10&chxt=y,x&chco=008000,44ee44,dd0000,882ed8&chxl=0:||&chxr=1,0,700)

### Benchmark `plasma`

Source: [`plasma.c`](./plasma.c), [`plasma.mfk`](./plasma.mfk)

The benchmark displays a plasma demo effect. 20 frames are rendered.

The code is based on an example from the CC65 repository: https://github.com/cc65/cc65/blob/master/samples/plasma.c

The effect is squished, since each cell is 8×1 pixels instead of 8×8 as in the original.

Results:

|                         | symbol               | frames (less is better) |
|-------------------------|----------------------|-------:|
|                         | asm                  | –   |
|![](../images/008000.png)| mfk-0.3.22-z80       | 481 |
|                         | mfk-0.3.22-z80-o4    | 481 |
|![](../images/44ee44.png)| mfk-0.3.22-8080      | 500 |
|![](../images/dd0000.png)| sccz80-20170112      | 1492 |
|                         | zsdcc-20170101-unopt | 494 |
|![](../images/882ed8.png)| zsdcc-20170101-opt   | 492 |

![](https://image-charts.com/chart?cht=bhg&chs=700x400&chd=t:481|500|1492|492&chds=0,1500&chdl=mfk-0.3.22-z80|mfk-0.3.22-8080|sccz80-20170112|zsdcc-20170101-opt&chtt=Plasma%20benchmark%20(time%20in%20frames,%20less%20is%20better)&chma=10,10&chxt=y,x&chco=008000,44ee44,dd0000,882ed8&chxl=0:||&chxr=1,0,1500)

### Benchmark `fib`

Source: [`fib.c`](./fib.c), [`fib.mfk`](./fib.mfk), [`fib-asm.mfk`](./fib-asm.mfk)

The benchmark calculates the first 25 Fibonacci numbers using the naïve recursive algorithm.

Results:

|                         | symbol               | frames (less is better) |
|-------------------------|----------------------|-------:|
|![](../images/404040.png)| asm                  | 523 |
|![](../images/008000.png)| mfk-0.3.22-z80       | 551 |
|                         | mfk-0.3.22-z80-o4    | 551  |
|![](../images/44ee44.png)| mfk-0.3.22-8080      | 1079 |
|![](../images/dd0000.png)| sccz80-20170112      | 982 |
|                         | zsdcc-20170101-unopt | 599 |
|![](../images/882ed8.png)| zsdcc-20170101-opt   | 599 |

![](https://image-charts.com/chart?cht=bhg&chs=700x400&chd=t:523|551|1079|982|599&chds=0,1100&chdl=asm|mfk-0.3.22-z80|mfk-0.3.22-8080|sccz80-20170112|zsdcc-20170101-opt&chtt=Fibonacci%20benchmark%20(time%20in%20frames,%20less%20is%20better)&chma=10,10&chxt=y,x&chco=404040,008000,44ee44,dd0000,882ed8&chxl=0:||&chxr=1,0,1100)


### Benchmark `linkedlist`

Source: [`linkedlist.c`](./linkedlist.c), [`linkedlist.mfk`](./linkedlist.mfk)

The benchmark creates a single-linked list with 3000 entries, with a 16-bit integer at each node, and then traverses the list and sums all the values together. It does it 25 times, for more precise results.

SCCZ80 failed to compile this benchmark correctly, so its results are not present

Results:

|                         | symbol               | frames (less is better) |
|-------------------------|----------------------|-------:|
|                         | asm                  | n/a  |
|![](../images/008000.png)| mfk-0.3.22-z80       | 548  |
|![](../images/00aa44.png)| mfk-0.3.22-z80-o4    | 482  |
|![](../images/44ee44.png)| mfk-0.3.22-8080      | 580  |
|                         | sccz80-20170112      | n/a  |
|                         | zsdcc-20170101-unopt | 750  |
|![](../images/882ed8.png)| zsdcc-20170101-opt   | 664  |

![](https://image-charts.com/chart?cht=bhg&chs=700x400&chd=t:548|482|580|664&chds=0,1500&chdl=mfk-0.3.22-z80|mfk-0.3.22-z80-o4|mfk-0.3.22-8080|zsdcc-20170101-opt&chtt=Linked%20list%20benchmark%20(time%20in%20frames,%20less%20is%20better)&chma=10,10&chxt=y,x&chco=008000,00aa44,44ee44,882ed8&chxl=0:||&chxr=1,0,800)


### Benchmark `romsum`

Source: [`romsum.c`](./romsum.c), [`romsum.mfk`](./romsum.mfk), [`romsum-asm.mfk`](./romsum-asm.mfk)

The benchmark sums all the bytes in the ROM and prints their sum (as a 16-bit integer). The benchmark is repeated 6 times, for more precise results.

SCCZ80 failed to compile this benchmark, so its results are not present.

Results:

|                         | symbol               | frames (less is better) |
|-------------------------|----------------------|-------:|
|![](../images/404040.png)| asm                  | 207  |
|![](../images/008000.png)| mfk-0.3.22-z80       | 493  |
|                         | mfk-0.3.22-z80-o4    | 493  |
|![](../images/44ee44.png)| mfk-0.3.22-8080      | 749  |
|                         | sccz80-20170112      | n/a  |
|                         | zsdcc-20170101-unopt | 451  |
|![](../images/882ed8.png)| zsdcc-20170101-opt   | 451  |

![](https://image-charts.com/chart?cht=bhg&chs=700x400&chd=t:207|493|749|451&chds=0,1100&chdl=asm|mfk-0.3.22-z80|mfk-0.3.22-8080|zsdcc-20170101-opt&chtt=ROM%20sum%20benchmark%20(time%20in%20frames,%20less%20is%20better)&chma=10,10&chxt=y,x&chco=404040,008000,44ee44,882ed8&chxl=0:||&chxr=1,0,1100)
