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

