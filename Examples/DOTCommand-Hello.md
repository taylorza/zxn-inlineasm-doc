# Simple DOT Command
This example demonstrates how you could create a simple DOT Command using the NextBASIC Inline Assembler.

We will be creating a DOT Command that d prints a greeting on the screen. Some important things to remember about DOT commands
1. They must be assembled to run at address $2000.
2. NextOS will look in the DOT directory in the root of your SD Card to locate a DOT command, if you have it somewhere else you will need to explicitly provide the path to locate and execute the DOT command.
3. DOT Commands are limited to being 8K in size, however you can allocate additional memory banks and break out of that limitation.

**Cool Fact**: The assembler is implemented as a DOT Command. The assembler itself currently resides within the 8K limit and allocates additional memory banks for the symbol table.

Running the following code will generate the DOT command "hello" directly in the "C:/DOT/" so that it can be executed without requiring an explicit path to be specified. 

Notice how we are specifying the ORG as $2000. When not specified the default is $c000, but as stated earlier DOT commands need to be assembled to be executed at address $2000.

```
  10 .asm
  20 ;  output "c:/dot/hello"
  30 ;  org $2000
  40 ;  ld hl, msg
  50 ;nxtch
  60 ;  ld a,(hl)
  70 ;  or a
  80 ;  ret z
  90 ;  rst $10
 100 ;  inc hl
 110 ;  jr nxtch
 120 ;msg db "Hello world",0
```

Running the above program will generate our dot command. Since it is already in the right location, we can just invoke it using the following command

```
.hello
```
This can be run from NextBASIC or from the Command Line.






