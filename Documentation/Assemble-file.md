# Assembling files directly
Starting from version 0.8b, you now have the ability to pass an assembler source file to the assembler for assembly.

This feature can be utilized either within your NextBASIC application or directly from the command line. Similar to include files, the source code in the assembly file should not contain line numbers and should not be prefixed with a `;`, unless you want the line to be commented out and thus ignored by the assembler.

## Example
For this example we will recreate the Hello dotCommand.

### Create the source file hello.asm

```
    output "c:/dot/hello"
    org $2000
    ld hl, msg
nxtch
    ld a,(hl)
    or a
    ret z
    rst $10
    inc hl
    jr nxtch
msg db "Hello world",0
```

### Assemble the file
You can either assemble the command at the command line as shown here

`.asm hello.asm`

When the assembly completes the `hello` binary will exist in the `dot` directory.

Alternatively you can assemble the file as part of your NextBASIC application.

```
  10 .asm hello.asm
```

## Practical Example: Externalizing Assembly Library Code
As a practical example of assembling from within your BASIC code, you can externalize the assembly library code that you use in your BASIC program. Here, we will create something simple that will return the number `42` to BASIC.

### Assembly File: basiclib.asm

```
    org $c000  ; Assemble directly to memory address $c000
    ld bc, 42
    ret
```

### BASIC Program

```
  10 CLEAR $bfff
  20 .asm basiclib.asm 
  30 PRINT USR ($c000)
```

Running the BASIC program will assemble `basiclib.asm` directly into memory at address $c000. Executing the code using `USR ($c000)` returns the value in the `BC` register which in this case will be `42`. 