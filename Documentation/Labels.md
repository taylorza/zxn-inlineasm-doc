# Labels
The assembler supports global and local or scoped labels. Global labels need to be unique across the entire program unit while local labels need to be unique with the current scope.

## Global Labels
Labels are identify memory addresses or constant values that can be references in the assembly application. Defining a label is as simple as typing a name starting in the left most column right against the `;` that introduces the line of assembly code.

In this example we create a simple label called `loop` and then jump to the label in an infinite loop :)
```
  10 .asm
  20 ;loop
  30 ;  jp loop
```

The label `loop` represents the memory address that the code at that point is being assembled to. When you reference the label, the assembler will replace the label with the memory address represented by the label at the time of assembly.

In the above example, `loop` is a global label, which means that you cannot declare another label called `loop` without the assembler raising a "duplicate definition" error. This can be a little inconvenient, since you now need to create unique names for labels and sometimes you would like to use a consistent label name when you have similar pattern.

This brings us to local or scoped labels. Every global label creates a new scope, within this scope you can create local labels that without worrying about name conflicts with local labels in other scopes. Of course you cannot use the same local label name within the same scope.

## Local Labels (v0.5e and later)
Like global labels, local labels identify memory addresses or constant values. What differentiates a local label from a global label is that local labels are prefixed with a `.`. 

An example will make this clearer. The example will use has two routines, one that prints characters to the screen and another that sends characters out the serial port. 

First lets look at how using only global labels the code might look
```
  10 .asm
  20 ;print
  30 ;  ld a, 2
  40 ;  call openChan
  50 ;printCh
  60 ;  ld a, (hl)
  70 ;  or a
  80 ;  jr z, printDone
  90 ;  call printCh
 100 ;  inc hl
 110 ;  jp print
 120 ;printDone
 130 ;  jp printNL
 140 ;
 150 ;send
 160 ;  ld bc,9600
 170 ;  call initUart
 180 ;nextCh
 190 ;  ld a, (hl)
 200 ;  or a
 210 ;  jr z, sendDone
 220 ;  call sendCh
 230 ;  inc hl
 240 ;  jp nextCh
 250 ;sendDone
 260 ;  call sendNL
 260 ;  jp closeUart
 ...
 ...
 ...
```

In this contrived example you see that we use a similar pattern when reaching the end of the string to process ie. we jump out of the print or send loop. Here we had to create two different labels `printDone` and `sendDone` to ensure that we jump to the correct termination location for the respective routines, we also need to use unique names for the target address to loop through each character. In fact, we could not use the same naming pattern `printCh` and `nextCh` because another routine with the global label `sendCh` already exists.

Using local labels the burden for creating unique names is reduced, contrast the previous example with the example below that uses local labels.

```
  10 .asm
  20 ;print
  30 ;  ld a, 2
  40 ;  call openChan
  50 ;.ch
  60 ;  ld a, (hl)
  70 ;  or a
  80 ;  jr z, .done
  90 ;  call printCh
 100 ;  inc hl
 110 ;  jp print
 120 ;.done
 130 ;  jp printNL
 140 ;
 150 ;send
 160 ;  ld bc,9600
 170 ;  call initUart
 180 ;.ch
 190 ;  ld a, (hl)
 200 ;  or a
 210 ;  jr z, .done
 220 ;  call sendCh
 230 ;  inc hl
 240 ;  jp .ch
 250 ;.done
 260 ;  call sendNL
 260 ;  jp closeUart
 ...
 ...
 ...
```

Here we see that the `.ch` and `.done` labels are local to their respective routines and we could reuse those name across the two routines without conflicts. The label `print` creates a new scope and all labels prefixed with a `.` are local to that scope, in the same way the label `send` creates a new scope.

## Label names
Label names have the following restriction

|--|--|
|Max length|15|
|Must start with|`.` or `_` or `a..z`|
|Rest of the name|`.` or `_` or `a..z` or `0..9`

When a label is prefixed with a `.` it is scoped to the most recently declared global label prior to its declaration. Due to the way local labels are implemented, the maximum length of a local label is determined by the length of the name of the scope label. For example, the `.ch` label in the `print` scope has an effective label length of 8, while the `.ch` label in the `send` scope has an effective length of 7.

## Referencing local labels across scopes
If you do find yourself needing to reference a local label in another scope, you can use a fully qualified label name. For example, if you wanted to jump the `.done` label in the `print` routine you could do the following from outside of the `print` routines scope.

```
  jp print.done
```



