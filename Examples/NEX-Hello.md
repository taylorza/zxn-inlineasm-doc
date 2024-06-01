# Simple NEX file
This example demonstrates how you can create a standalone NEX file (https://wiki.specnext.dev/NEX_file_format) using the NextBASIC Inline Assembler.

We will be creating a program that prints a greeting on the screen. 

The example below will create the file `hello.nex` on your SD card. You can then use the browser to navigate to the file and execute it directly. NEX files take over the machine completely, so you will need to reset the machine after executing the program.

To include a memory bank in the NEX file, you must explicitly use the `BANK` pseudo-op to specify the bank. In this case we will use BANK 0 at address $c000 which is the default for NEX files so does not require further configuration.

```
  10 CLEAR $bfff
  20 .asm
  30 ;  bank 0
  40 ;  org $c000
  50 ;start
  60 ;  ld sp,stacktop
  70 ;  ld a,2
  80 ;  call $1601 ;Open chan 2
  90 ;  ld hl,msg
 100 ;nxtch
 110 ;  ld a,(hl)
 120 ;  or a
 130 ;  jr z,done
 140 ;  rst $10
 150 ;  inc hl
 160 ;  jr nxtch
 170 ;done
 180 ;  jr $
 190 ;msg db "Hello world",0
 200 ;  ds 32      ;Stack space
 210 ;stacktop equ $
 220 ;;-------------------------
 230 ;  savenex "hello.nex",start, stacktop

```

Running the above program will generate our dot command. Since it is already in the right location, we can just invoke it using the following command

```
.hello
```
This can be run from NextBASIC or from the Command Line.






