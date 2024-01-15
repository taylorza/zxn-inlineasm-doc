# Assembling code to a memory bank
One of the nice features of the assembler is the ability to assemble your code into a target bank outside of the standard memory map used by BASIC and the NextOS. You can also have the actual assembly code stored in dedicated banks, that addressed elsewhere in the documentation.

The BANK pseudo-op is used to instruct the assembler to generate the machine code directly into the specified bank

Syntax of the BANK pseudo-op
```
  BANK bankNumber[,offset]
```
* *bankNumber* is the bank that you want to target
* *offset* is an optional offset into the bank to start the assembly. If not specified the offset defaults to 0.


For our first example, we will arbitrarily choose 40 as our target bank and hope it does not conflict with anything else loaded. We will solve this issue shortly but first lets keep the example simple.

```
  10 .asm
  20 ;  BANK 40
  30 ;  ld bc, 42
  40 ;  ret
```

When this code is run, the assembler will assemble the code into memory bank 40. Next, to execute the code we can use NextBASIC's `BANK USR` statement.

```
  50 PRINT BANK 40 USR 0
```

This last line will execute our assembly code from bank 40 starting at offset 0, and the result printed to the screen. In this case we should see `42` printed to the screen.

## Playing nice with NextBASIC
As mentioned earlier, it can be a little risky arbitrarily hardcoding bank numbers. You never know who else might be using that bank and if it is a bank used by the OS we could cause all kinds of havoc.

NextBASIC offers the facility to allocate banks and if everyone plays by the rules this will help ensure that there are not conflicts. The next example allocates the bank in NextBASIC which is then referenced in the assembly code. We will us the same example from before, but this time in a more environment friendly manner.

```
  10 BANK NEW %a
  20 .asm
  30 ;  BANK %a
  40 ;  ld bc, 42
  50 ;  ret
  60 PRINT BANK %a USR 0
```

Line 10 allocates the new memory bank using the NextBASIC `BANK NEW` statement, and assigns the new bank to the `%a` integer variable.

Line 30 references the `%a` integer variable at assembly time, directing the assembler to assemble the code into the bank we allocated earlier.

Line 60 executes our banked routine from NextBASIC.

## Using the offset
If you are using an existing bank and want to place a machine code routine near the top of that bank so that you do not need waste an additional bank you could use the offset parameter.

```
  10 BANK NEW %a
  20 .asm
  30 ;  BANK %a, 1024
  40 ;  ld bc, 42
  50 ;  ret
  60 PRINT BANK %a USR 1024
```

Line 30 specifies the target bank as well as the offset, in this case 1024 bytes into the bank.
Line 60 will execute the routine at offset 1024 in the bank.

## So what about ORG
Under normal circumstances, the ORG pseudo-op will specify where in memory the code is assembled as well as the base address used in all the address calculations. However, when assembling to a target memory bank, that already takes care of the where so ORG only impacts the memory address calculations.

This could be useful in scenarios where you are assembling into a memory bank that you will later save to disk and load back into a specific memory area. In that case you need to ensure that the addresses calculated at assembly time align with the address you intend to load the code into.

**NOTE** To take effect of the code in the new bank, ORG must be set after the BANK is specified.




