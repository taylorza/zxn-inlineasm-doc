# NextBASIC ToUpper$ Function

This example we create a simple function to convert strings to upper case. This routine takes advantage of the fact that you can pass string arguments the `USR` and `USR$` commands.

``
   5 CLEAR $bfff
  10 .asm
  20 ;  ret nz ; expect string
  30 ;  push bc    ;Save string
  40 ;  push de    ; for return
  50 ;casecvt
  60 ;  ld a,(de)  ;Get char
  70 ;  cp "a"     ;
  80 ;  jr c,next  ;Skip if < a 
  90 ;  cp "z"+1
 100 ;  jr nc,next ;Skip if > z 
 110 ;  and $5f    ;a..z->A..Z
 120 ;  ld (de),a  ;Replace char
 130 ;next
 140 ;  inc de     ;Next char
 150 ;  dec bc     ;Dec count
 160 ;  ld a,c    
 170 ;  or b       ;Test for 0
 180 ;  jr nz,casecvt 
 190 ;  pop de     ;Restore
 200 ;  pop bc     ; string ptr
 210 ;  ret
```

With that assembled, we can now create a convenient NextBASIC functions to wrap the machine code routine. NextBASIC makes this very easy using the `DEF FN` statement

```
 500 DEF FN ToUpper$( s$)= USR $($c000,s$)
```

And now we can convert strings to uppercase
```
 1000 a$="hello"
 1010 PRINT FN ToUpper$(a$)
```

To have the upper case version of the string stored, you can do assign the result back to the original string or to a new string
```
1000 a$ = FN ToUpper$(a$)
```

You can even change the case of literal strings
```
1000 PRINT FN ToUpper$("test")
```



