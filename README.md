## Overview

This is an introduction for the method we propose to add new ISA on [riscv-dv](https://github.com/chipsalliance/riscv-dv). While the original riscv-dv supports the addition of custom instructions, it is limited to existing instruction format types, and cannot support new ISA. Therefore, we are sharing this method to address this limitation.

## Getting Started

Through the analysis of the source codes, we have determined the process of generating the instruction flow.
First, We have located the definition file of ISA. Then, create two new files to describe the instruction types and encoding mode in the directory **src/isa/**.A specific description approach can be referenced from existing definitions in other files.

Second, define the automatic generation of new instructions. Add instrcution mnemonic and file path into the **src/riscv_instr_pkg.sv**
```
    AMOAND_W,   // in the enum type -- riscv_instr_name_t
    ...
    `include "isa/riscv_amo_instr.sv"
```

Third, find the main executive file **run.py** and add the ISA information in the function **load_config** like 
```
def load_config(...):
    ...
    elif args.target == "rv64gc":
            args.mabi = "lp64"
            args.isa = "rv64gc"
```

Finally, modify the variable **cmd** to your own compiler in the function **gcc_compile**.
```
def gc_compile(...):
    ...
    cmd = ("{} -static -mcmodel=medany \
             -fvisibility=hidden -nostdlib \
             -nostartfiles {} \
             -I{}/user_extension \
             -T{}/scripts/link.ld {} -o {} ".format(
                get_env_var("RISCV_GCC", debug_cmd=debug_cmd), asm, cwd,
                cwd, opts, elf))
```
