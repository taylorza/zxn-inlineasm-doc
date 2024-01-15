# Assembling directly to a file
Assembling to memory is not the only option. You can also assemble directly to a file, not requiring you to allocate any memory banks or reserve space for the generated machine code.

Some use-cases for this feature might include
1. Shipping just the binaries with your NextBASIC program, which loads the binaries into memory (BANKED or not) and uses the routines.
2. Creating DOT Commands
3. Creating NextOS Drivers

The OUTPUT pseudo-op instructs the assembler to write the generated machine code to a file.

Syntax of the OUTPUT pseudo-op
```
  OUTPUT "filename"
```
* *filename* is the file that the code will be written to. If the file already exists it will be overwritten so be careful.

## Example 1
The first example we will look at demonstrates creating a binary file that can later be used from BASIC without needing to re-assemble the code every time.

1. Create our machine code routine and generate the binary file "asm-lib"
```
   5 SAVE "asm-create-lib.bas"
  10 .asm
  20 ;  output "asm-lib"
  30 ;  ld bc,42 ;Value returned
  40 ;  ret
```
Now run this using the `RUN` command. If all goes well you should now have a file "asm-lib" on the disk. If you want to be sure the assembled code is not in memory go ahead and reset your machine, just to prove there is no trickery going on here :)

2. Next we will create a simple program that loads our library and uses the routine. 
```
   5 SAVE "asm-use-lib.bas"
   6 REM Load the machine code library into memory. For this example we will allocate a bank and load it into that bank
   7 PRINT "Loading Machine Code Routine"
  10 BANK NEW %b
  20 LOAD "asm-lib" BANK %b
  30 REM Now we can call the routine as needed
  40 PRINT "Calling Machine Code Routine"
  50 PRINT "And the answer is "; BANK %b USR 0
```

Now all you need to ship to a user is your BASIC program "asm-use-lib.bas" and the binary file containing the machine code "asm-lib"

**NOTE** We did not need to load the machine code into a BANK, we could have just as easily loaded the code into "normal" memory.

