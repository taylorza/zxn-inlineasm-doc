# Getting and Setting NextBASIC Integer Variables

There are two challenges that this feature is aimed at addressing
* Referencing data in your NextBASIC application at the time of assembly
* Providing information about the assembled code back to NextBASIC

## Getting the value of a NextBASIC Integer Variable
This example demonstrates how a memory bank allocated in NextBASIC can be referenced in the assembly code to direct the assembler to generate the machine code into the target bank.

```
  10 BANK NEW %a
  20 .asm
  30 ;  BANK %a
  40 ;  ld bc, 42
  50 ;  ret
  60 PRINT BANK %a USR 0
```

Looking at this line by line

`10 BANK NEW %a`
Here we allocate the bank in BASIC and have the bank number stored in the `%a` integer variable.

`20 .asm` 
Invokes the Inline Assembler, which will assemble the assembly code.

`30 ;  BANK %a`
This is the first line of assembly, this is a pseudo-op that directs the assembler to generate the code to a specific bank. In this case we are using the bank number that was stored in the variable `%a` in line 10 to provide that information to the assembler.

```
  40 ;  ld bc, 42
  50 ;  ret
```
Lines 40 and 50 are our actual machine code, in this case we are just loading the value 42 into the BC register and then returning to the caller. In our case the caller will be BASIC. Once the assembler has completed the assembly process, it will hand the reigns back to the BASIC interpreter to continue where the assembler left off.

`60 PRINT BANK %a USR 0`
Back in the BASIC interpreter, we want to execute the code that the assembler generated for us. Here we use the BASIC `BANK USR` statement to execute code in the target bank. In this case the code resides at offset 0 within the target memory bank.

If everything went according to plan, running this code will assemble the routine into the target bank, then execute the routine and print the value `42` on the screen. It is a feature of the `USR` and `BANK USR` statements that what ever value is left in the `BC` register will be returned to the caller.

## Setting the value of a NextBASIC Integer Variable
This example demonstrates how passing information about the assembled code back to the BASIC interpreter. It is not always easy to know what at what address a particular piece of code in your assembly will land. And even if you do add up the bytes manually any subsequent change to the code will potentially result in you needing to recalculate the addresses.

In this first example, we will create two routines in assembly language and then pass the addresses of those routines back to BASIC so that we can execute them as needed. I will keep the routines simple for illustration purposes.

```
   5 CLEAR $bfff
  10 %z=13
  20 .asm
  30 ;fn1
  40 ;  ld bc, %z*2
  50 ;  ret
  60 ;fn2
  70 ;  ld bc, 42
  80 ;  ret
  90 ;  LET %a=fn1
 100 ;  LET %b=fn2
 110 PRINT USR (%a)
 120 PRINT USR (%b)
```

Let's review the key pieces of code in this example

`5 CLEAR $bfe0`
We did not do this in the first example! In this example we are going to assemble our code into the same memory area that is usually used by BASIC. The `CLEAR` statement tells BASIC interpreter what memory we will be using, in this case the memory above `$bfff` starting at `$c000`. Coincidentally, this is also the default address the assembler uses unless told otherwise. After this, statement BASIC will ensure that it does not overwrite any memory in this area.

`10 %z=13`
This is standard NextBASIC syntax to assign the value 13 to integer variable `%a`

`Line 20` triggers the assembler as mentioned in the first example

`30 ;fn1`
This creates a label `fn1`, the value of the label is the memory address that the next instruction will be assembled to. In assembly this is useful for naming locations that you will jump to or call within the assembly code.

`40 ;  ld bc, %z*2`
Similar to the first example we are loading a value in the BC register. However, in this case we are getting that value from the `%z` integer variable, which we had set to `13` prior to the assembler being triggered. As you can see, these accesses can also be used in expressions, here was are doubling the value before it is loaded into `BC`.

`50 ;  ret`
As before, we return to the caller with the `ret` instruction.

```
  60 ;fn2
  70 ;  ld bc, 42
  80 ;  ret
```
`Line 60-80` creates a second routine at address `fn2`. This is the same routine we saw in our earlier example, it just load the value `42` into the `BC` register and returns to the caller

```
  90 ;  LET %a=fn1
 100 ;  LET %b=fn2
```
And finally, we get to the code that will pass information back to the BASIC interpreter. The `LET` pseudo-op uses a syntax similar to that of BASIC, but do not be confused, this is not BASIC. The assembler will assign the addresses stored in `fn1` and `fn2` to the integer variable `%a` and `%b` respectively.

Now that we have the code assembled and the addresses of our two routines in `%a` and `%b` we can invoke them at will. Again I show the simple case of just printing the value that was left in the `BC` register.

`110 PRINT USR (%a)`
We use `USR` to call our machine code routine `fn1`, the address of which is stored in the `%a` integer variable.

`120 PRINT USR (%b)`
Similarly we call to the machine code routine `fn2`, which has it's address stored in the `%b` integer variable.


