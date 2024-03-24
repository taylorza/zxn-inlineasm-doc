# Labels
The assembler recognizes both global and local (or scoped) labels. Global labels must be unique throughout the entire program, whereas local labels must be unique within their respective scope.

## Global Labels
Labels serve as identifiers for memory addresses or constants within an assembly application. To define a label, simply type a name at the beginning of the line, immediately followed by the ; that starts the assembly code line.

For instance, by defining a label named loop, we can easily refer to it in an assembly instruction to create an infinite loop, like so:
```
  10 .asm
  20 ;loop
  30 ;  jp loop
```

The `loop` label denotes the memory address where the corresponding code is assembled. During assembly, any reference to `loop` is substituted with its actual memory address.

In our example, `loop` is a global label. This designation means it must be unique; otherwise, the assembler will flag it with a “duplicate definition” error. While this ensures label uniqueness, it can be cumbersome when you want to reuse a label name for similar code patterns.

That’s where local or scoped labels come in handy. Each global label initiates a new scope, allowing you to define local labels within it. These local labels won’t conflict with others in different scopes. However, within the same scope, you must still avoid duplicate local label names

## Local Labels (v0.5e and later)
Local labels, similar to global labels, are used to identify memory addresses or constant values. The distinction lies in the prefix of a local label, which is a `.`. This differentiates it from a global label and confines its scope.

Here’s how you might structure your code using only global labels:
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

In this example, we encounter a common pattern at the end of string processing: exiting the print or send loop. Originally, we had to use distinct labels, `printDone` and `sendDone`, to differentiate the exit points for each routine. Similarly, unique names were required for each character-processing address. The use of `sendCh` was not possible due to the preexistence of a sendCh global label.

However, with local labels, the need for unique global names diminishes. Observe how the following example employs local labels to simplify the naming process

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
In this setup, the `.ch` and `.done` labels are confined to their respective routines, allowing for their reuse across different routines without naming conflicts. The `print` label establishes a new scope, and any labels beginning with a `.` are considered local to this scope. Similarly, the `send` label defines its own scope.

## Label names
Label names must meet the following requirements
|||
|--|--|
|Max length|15|
|Must start with|`.` or `_` or `a..z`|
|Rest of the name|`.` or `_` or `a..z` or `0..9`|

When prefixed with a `.`, a label becomes scoped to the global label most recently declared before it. The maximum length of a local label is contingent upon the length of its scope label’s name. For instance, within the `print` scope, the `.ch` label effectively has a length of 8 characters, whereas in the `send` scope, it has a length of 7 characters

## Referencing local labels across scopes
If you need to reference a local label from a different scope, you can use its fully qualified name. For instance, to jump to the `.done` label within the `print` routine from an external scope, you could use the following method:

```
  jp print.done
```



