# Pseudo-ops

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





