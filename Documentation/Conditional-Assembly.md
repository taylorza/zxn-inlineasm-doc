# Conditional Assembly
Conditional assembly allows you to control which blocks of code are included/excluded from assembly. This can be useful in cases where you might want to include debug code during development but exclude it from the final assembly. Using conditional assembly the debug code can be "removed" without physically removing the code and therefore easily be reenabled as required.

Three pseudo-ops work together to provide support for conditional assembly.
[IF](Pseudo-ops.md#ifelseend)
[ELSE](Pseudo-ops.md#ifelseend) (optional)
[END](Pseudo-ops.md#ifelseend)

Syntax of the OUTPUT pseudo-op
```
  IF <expr>
    ; code to assemble if <expr> is true (non-zero)
  ELSE
    ; code to assemble if <expr> is false (zero)
  ENDIF
```
**NOTE** The `ELSE` block is optional

## Example 1

In this example we see how conditional assembly can be used to select between two different code blocks to be assembled based on a `DEBUG` variable.
```
  10 CLEAR $bfff
  20 .asm
  30 ;DEBUG equ 1 ; Enable debug build
  40 ;  IF DEBUG
  50 ;    ld a,2 ; red border if debug
  60 ;  ELSE
  65 ;    ld a,4 ; green border if not debug
  70 ;  ENDIF
  75 ;  out ($fe),a
  80 ;  ret
 100 RANDOMIZE % USR $c000
 ```

 ## Example 2

 This example builds on the previous example, demonstrating nested conditionals as well as referencing NextBASIC integer variables to control what is assembled.

 ```
  10 CLEAR $bfff
  20 FOR %d=0 TO 1
  30 FOR %l=0 TO 1
  40 .asm
  50 ;DEBUG equ %d
  60 ;  IF DEBUG
  70 ;    IF %l=1
  80 ;      ld a,2
  90 ;    ELSE
 100 ;      ld a, 3
 110 ;    ENDIF
 120 ;  ELSE
 130 ;    IF %l=0
 140 ;      ld a, 4
 150 ;    ELSE
 160 ;      ld a, 6
 170 ;    ENDIF
 180 ;  ENDIF
 190 ;  out ($fe),a
 200 ;  ret
 210 RANDOMIZE % USR $c000
 220 PRINT AT 0,0;"DEBUG=";%d
 230 PRINT "LEVEL=";%l
 240 PRINT "Press any key to re-assemble": PAUSE 0
 250 NEXT %l: NEXT %d
 ```


