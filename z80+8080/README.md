# Millfork benchmarks on Zilog Z80

### Measurement methodology

The target platform is ZX Spectrum 48k emulated via the Fuse emulator, version 1.3.3.

The program measures the number of frames as counted by a 24-bit variable `FRAMES` at address 23672.

The program is not allowed to disable or modify interrupts, except for disabling interrupts temporarily twice to read the `FRAMES` variable.

The `benchcommon` library provides the necessary `start` and `end` functions. The program should call `start`, run the benchmark, call `end` and enter an infinite loop.

The result is displayed on the screen as a hexadecimal value. Results below have been converted to decimal.

### Compared implementations

# Warning: These benchmark are not actually against Millfork 0.3.2, but against the current development version.

These benchmarks compare the Millfork compiler with two popular C compilers for Z80:

| symbol               | compiler | version  | command line                                                                               | comment |
|----------------------|----------|----------|--------------------------------------------------------------------------------------------|-|
| mfk-0.3.2-z80        | Millfork | 0.3.2    | `millfork -t zxspectrum -O1`                                                               | the Z80 backend doesn't differentiate between optimization levels yet |
| mfk-0.3.2-8080       | Millfork | 0.3.2    | `millfork -t zxspectrum_8080 -O1`                                                          | the same, but without Z80 extensions |
| sccz80-20170112      | SCCZ80   | 20170112 | `zcc +zx -lndos -clib=ansi -O3 -Cc -do-inline -create-app`                                 | optimizations to give it a fighting chance |
| zsdcc-20170101-unopt | ZSDCC    | 20170101 | `zcc +zx -clib=sdcc_ix -Cs --reserve-regs-iy -create-app`                                  | no optimizations |
| zsdcc-20170101-opt   | ZSDCC    | 20170101 | `zcc +zx -clib=sdcc_ix -Cs --reserve-regs-iy -SO3 --max-allocs-per-node200000 -create-app` | recommended options for good code |

Remarks:

* ZSDCC and SCCZ80 come from the Z88DK v1.99B.

* `millfork` is a script launching the Millfork compiler with the correct `-I` option.

* Input and output file options have been omitted for brevity.

* Charts show the most optimized results.

### Benchmark `sieve`

Source: [`sieve.c`](./sieve.c), [`sieve.mfk`](./sieve.mfk)

The benchmark calculates all the prime numbers from 1 to 2^(14) using the Sieve of Erasthotenes algorithm. It does it three times, for more precise results.

Results:

| symbol               | frames (less is better) |
|----------------------|-------:|
| mfk-0.3.2-z80        | 102 |
| mfk-0.3.2-8080       | 124 |
| sccz80-20170112      | 695 |
| zsdcc-20170101-unopt | 115 |
| zsdcc-20170101-opt   | 115 |

![](https://image-charts.com/chart?cht=bhg&chs=700x400&chd=t:102|124|695|115&chds=0,1000&chdl=mfk-0.3.2-z80|mfk-0.3.2-8080|sccz80-20170112|zsdcc-20170101-opt&chtt=Sieve%20benchmark%20(time%20in%20frames,%20less%20is%20better)&chma=10,10&chxt=x,y&chco=008000,44ee44,dd0000,226666&chxl=0:||&chxr=1,0,700)

### Benchmark `plasma`

Source: [`plasma.c`](./plasma.c), [`plasma.mfk`](./plasma.mfk)

The benchmark displays a plasma demo effect. 20 frames are rendered.

The code is based on an example from the CC65 repository: https://github.com/cc65/cc65/blob/master/samples/plasma.c

The effect is squished, since each cell is 8×1 pixels instead of 8×8 as in the original.

Results:

| symbol               | frames (less is better) |
|----------------------|-------:|
| mfk-0.3.2-z80        | 485 |
| mfk-0.3.2-8080       | 503 |
| sccz80-20170112      | 1492 |
| zsdcc-20170101-unopt | 494 |
| zsdcc-20170101-opt   | 492 |

![](https://image-charts.com/chart?cht=bhg&chs=700x400&chd=t:485|503|1492|492&chds=0,1500&chdl=mfk-0.3.2-z80|mfk-0.3.2-8080|sccz80-20170112|zsdcc-20170101-opt&chtt=Sieve%20benchmark%20(time%20in%20frames,%20less%20is%20better)&chma=10,10&chxt=x,y&chco=008000,44ee44,dd0000,226666&chxl=0:||&chxr=1,0,1500)

### Benchmark `fib`

Source: [`fib.c`](./fib.c), [`fib.mfk`](./fib.mfk)

The benchmark calculates the first 25 Fibonacci numbers using the naïve recursive algorithm.

Results:

| symbol               | frames (less is better) |
|----------------------|-------:|
| mfk-0.3.2-z80        | 551 |
| mfk-0.3.2-8080       | 1079 |
| sccz80-20170112      | 982 |
| zsdcc-20170101-unopt | 599 |
| zsdcc-20170101-opt   | 599 |

![](https://image-charts.com/chart?cht=bhg&chs=700x400&chd=t:551|1079|982|599&chds=0,1100&chdl=mfk-0.3.2-z80|mfk-0.3.2-8080|sccz80-20170112|zsdcc-20170101-opt&chtt=Sieve%20benchmark%20(time%20in%20frames,%20less%20is%20better)&chma=10,10&chxt=x,y&chco=008000,44ee44,dd0000,226666&chxl=0:||&chxr=1,0,1100)
