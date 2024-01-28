# Pseudo-ops

[$](#dollar)
[#](#hash)
[ALIGN](#align)
[ASSERT](#assert)
[BANK](#bank)
[BRK](#brk)
[DB](#db)
[DS](#ds)
[DW](#dw)
[EQU](#equ)
[IF/ELSE/END](#ifelseend)
[LET](#let)
[NBRK](#brk)
[ORG](#org)
[OUTPUT](#output)
[RELOC_COUNT](#reloc_count)
[RELOC_END](#reloc_end)
[RELOC_START](#reloc_start)
[RELOC_TABLE](#reloc_table)

# $ (dollar)
Returns the current address/program counter at the point of assembly

# # (hash)
Temporarily turns off relocation for an expression. It should immediately precede the expression to be affected.
Only useful inside a relocation block.

## ALIGN
Aligns the next instruction or data element to the specified boundary. The alignment must be a power of 2

**Example**
Aligns the next instruction to an address on an 8 byte boundary. If padding is required to move the boundary, the padded memory fill be zeroed.
```
ALIGN 8
```

## ASSERT
Test that an expression is true at the time of assembly and raises an error if the expression is false.

**Example**
Check that the current address assembly address is below $c100
```
ASSERT $ < $c100
```

## BANK
Set the target bank and optional offset into the bank for the assembled machine code

**Examples**
Assemble to bank 40
```
BANK 40
```

Assemble to the bank specified in the `%b` NextBASIC integer variable
```
BANK %b
```

Assemble to bank 40 at offset 256
```
BANK 40, 256
```

## BRK
Inserts a CSpect BREAK instruction into the code. At runtime this will trigger the CSpect emulator to break into the debugger

**Example**
```
BRK
```

## NBRK
Inserts a hardware break point into the code. At runtime this will trigger the ZX Spectrum Next debugger. 

**Example**
```
NBRK
```
This is equivalent to `nextreg $02, $08`

## DB
Defines a byte or a string of bytes

**Example**
```
DB 1,2,3,5,8
DB 1,42,"@"
DB "Hello World",$0d,0
```

## DS
Defines the specified number of bytes. The bytes are all set to 0

**Example**
Define a block of 128 bytes
```
DS 128
```

## DW
Defines a word

**Example**
```
DW 1263, $c100
DW "Hello World", $000d
```

## EQU
Define a constant

**Example**
```
BLACK   EQU 0
BLUE    EQU 1
RED     EQU 2
MAGENTA EQU 3
GREEN   EQU 4
CYAN    EQU 5
YELLOW  EQU 6
WHITE   EQU 7
```

## IF/ELSE/END
Can be used to exclude certain code section from assembly based on the specified conditions. The condition can be any valid constant expression, including referencing NextBASIC Integer variables. Conditional code blocks can be nested up to 7 levels deep.

**Example**
In the following example the register A will be zeroed using `xor`. Changing the `0` (false) in the `IF` to a non-Zero value like `1` will cause the `ld a,0` path to be assembled.
```
  IF 0
    ld a, 0
  ELSE
    xor a
  ENDIF
```

## LET
Assigns a value to a NExtBASIC integer variable. This is an assembly time assignment.

**Example**
Assign the program counter for the next instruction to be assembled to the `%p` NextBASIC integer variable
```
LET %p = $
```

## ORG
The behavior of this pseudo-op depends on the assembly target.

|Target|Behavior|
|------|--------|
|Standard memory|The code is assembled starting at the address specified by `ORG`, and label addresses are calculated relative to the address specified|
|Memory Bank|The code is assembled to the specified bank and offset and label addresses are calculated relative to the address specified|
|Output to file|The code is assembled directly to the target file and label addresses are calculated relative to the address specified|

**NOTE:** Only when compiling to standard memory can you use `ORG` to explicitly place code at specific memory addresses.

**Examples**
Place code at address $8000
```
ORG $8000
```

## OUTPUT
Directs the assembler to write the generated machine code directly to a file. This is useful for creating machine code libraries that can be used at runtime by your NextBASIC program. This can also be used to create NextOS drivers or DOT Commands.

**Examples**
Assemble the code to a file called "asmlib". If you do not provide a fully qualified file path the current drive and path is used
```
OUTPUT "asmlib"
```

## RELOC_COUNT
Return the number of relocation tracked for the code between [RELOC_START](#reloc_start) and [RELOC_END](#reloc_end) blocks.

## RELOC_END
End a relocation block. The assembler will stop tracking relocations.

## RELOC_START
Start a relocation block. The assembler will start tracking relocations at this point until the block is terminated using [RELOC_END](#reloc_end) blocks.

## RELOC_TABLE
Emits the relocation table. This is a raw dump of the high byte of every address that needs to be patched to relocate the code. The format of this table is specifically designed to support the creation of NextZXOS Drivers.







