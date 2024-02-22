# BFIND DOT Command
BFIND is a dot command to assist with navigation within NextBASIC programs, written by Chris Taylor. It can find specified text within the current program and automatically positions the BASIC editor cursor at the matching line.

After entering and executing the code below, you should have a file `bfind` in the DOT folder on the SD Card.

Enter 

`.bfind`

for usage information, e.g:

    BASIC Find String v0.4
    bfind [@]|[@nnnn] <str>
     @     search from edit line
     @nnnn search from line nnnn
     <str> string to find

`.bfind usage db`

Will launch the DOT command and scan the entire source code for the string "usage db". The search is not case sensitive, you might want to experiment with adding additional options to support case sensitive searches.

In addition to doing a global search, you can also specify an `@` argument to specify the line at which to start the search. There are two forms of the argument, with with an explicit line number and the other with no line number specified.

`.bfind @500 usage db`

Will search for the string "usage db" starting from line 500 to the end of the source code.

`.bfind @ usage db`

Will search for the string "usage db" starting after the current line in the editor. This form is most useful when used immediately after a global search to search for the next occurrence of a search string.

For a detailed explanation of creating DOT commands with the NextBASIC Inline Assembler, please see the [HELLO DOT Command Example](DOTCommand-Hello.md)

```
 100 RUN AT 3
 110 SAVE "asmfind.bas"
 120 .asm
 130 ;  output "/dot/bfind"
 140 ;  org $2000
 150 ;PROG    equ     $5c53
 160 ;VARS    equ     $5c4b
 170 ;EPPC    equ     $5c49
 180 ;PPC     equ     $5C45
 190 ;BUFLEN  equ     $80
 200 ;LIMIT   equ     5    ;Count for digit testing
 210 ;ENTER   equ     13
 220 ;SPACE   equ     32   ;First drawn character code
 230 ;find
 240 ;  ld a,l
 250 ;  or h
 260 ;  jp z,showusage     ;Show usage if no arguments
 270 ;
 280 ;  ld a,(hl)         
 290 ;  cp "@"             ;Check for line switch
 300 ;  jr z,findlineno    ;  find starting line
 310 ;  ld de,(PROG)       ;else start at first line
 320 ;  ld (line),de       
 330 ;  jr cpystr          ;Copy search string
 340 ;
 350 ;parselineno
 360 ;  ld b,LIMIT
 370 ;nxtdigit
 380 ;  ld a,(hl)          ;Get argument character
 390 ;  cp ENTER           ;Check for end of argument
 400 ;  jp z,r_badsrch     ;  exit if no search string
 410 ;  cp "0"             ;Else check for valid digit
 420 ;  ret c
 430 ;  cp "9"+1
 440 ;  ret nc
 450 ;  sub "0"            ;ASCII to 0-9
 460 ;  push hl
 470 ;    ld hl,(lineno)   
 480 ;    ld e,l
 490 ;    ld d,h
 500 ;    add hl,hl        ;x2
 510 ;    adc hl,hl        ;x4
 520 ;    adc hl,hl        ;x8
 530 ;    adc hl,de
 540 ;    adc hl,de        ;x10
 550 ;    add hl,a         ;+unit value
 560 ;    ld (lineno),hl   ;update line no
 570 ;  pop hl
 580 ;  inc hl             ;move to next digit
 590 ;  djnz nxtdigit
 600 ;  jp r_badlinex
 610 ;  
 620 ;findlineno
 630 ;  inc hl             ;Skip '@'
 640 ;  ld a,(hl)
 650 ;  cp SPACE           ;Check for separator
 660 ;  jr z,useeppc       ; no line, lineo=eppc
 670 ;  call parselineno
 680 ;  ld c,a             ;Save terminating token
 690 ;  ld a,LIMIT         ;Count for empty buffer
 700 ;  cp b
 710 ;  jr z,move2line     ;Presume @LABEL
 720 ;  ld a,c             ;Restore terminating token
 730 ;  cp SPACE           ;Expect separator
 740 ;  jp nz,r_badline    ;  else exit with error
 750 ;  inc hl             ;Skip space
 760 ;  jr move2line
 770 ;useeppc
 780 ;  ld de,(EPPC)
 790 ;  inc de             ;Move to just after eppc
 800 ;  ld (lineno),de     ;  set starting line number
 810 ;move2line
 820 ;  push hl            ;Save commandline
 830 ;    ld hl,(PROG)
 840 ;cmplineno
 850 ;    push hl          ;Save source line
 860 ;      ld d,(hl)         
 870 ;      inc hl
 880 ;      ld e,(hl)      ;DE-Current line no
 890 ;      ld hl,(lineno) ;HL-Target line no
 900 ;      or a
 910 ;      ex de,hl
 920 ;      sbc hl,de      ;Compare current with target
 930 ;    pop hl           ;Restore source line
 940 ;    jr nc,atline     ;If current>=target set start
 950 ;    inc hl
 960 ;    inc hl           ;Skip line number
 970 ;    ld e,(hl)        ;Load length in DE
 980 ;    inc hl
 990 ;    ld d,(hl)
1000 ;    inc hl           ;Point to start of code
1010 ;    add hl,de        ;Point to end of line
1020 ;    ld de,(VARS)     ;Check for end of source
1030 ;    sbc hl,de
1040 ;    add hl,de        ;If less than target
1050 ;    jr c,cmplineno   ;  continue search
1060 ;  pop hl
1070 ;  ret
1080 ;atline  
1090 ;  ld (line),hl       ;Set start line for search
1100 ;  pop hl             ;Restore commandline
1110 ;
1120 ;cpystr
1130 ;  ld a,(hl)        
1140 ;  cp SPACE+1         ;Check for whitespace
1150 ;  inc hl
1160 ;  jr c,cpystr 
1170 ;  dec hl             ;Move back to char     
1180 ;  ld de,str
1190 ;  ld b,BUFLEN-1
1200 ;cpyloop
1210 ;  or a
1220 ;  jr z,search        ;If 0-terminator start search
1230 ;  cp ENTER  
1240 ;  jr z,search        ;If eol start search
1250 ;  call toupper       ; else convert to upper case
1260 ;  ld (de),a          ; and copy to search buffer
1270 ;  inc de
1280 ;  inc hl
1290 ;  ld a,(hl)          ;Load next character
1300 ;  djnz cpyloop       ;Loop to copy
1310 ;
1320 ;r_badsrch
1330 ;  pop hl             ;Balance stack after CALL
1340 ;  ld hl,e_badsrch
1350 ;  jp report          ;Error if string too long
1360 ;
1370 ;search
1380 ;  ld a,ENTER
1390 ;  ld (de),a          ;Add terminator to str
1400 ;
1410 ;  ld hl,(line)       ;Get pointer to source line
1420 ;searchline
1430 ;  ld a,(hl)          ;Read line number in big-endian
1440 ;  ld (lineno+1),a 
1450 ;  inc hl
1460 ;  ld a,(hl)
1470 ;  ld (lineno),a
1480 ;  inc hl             ;Move to length
1490 ;  inc hl
1500 ;  inc hl             ;Skip length
1510 ;  
1520 ;  call cmpistr
1530 ;  jr z,setlineno
1540 ;
1550 ;  inc hl             ;Move to next char on line
1560 ;  or a
1570 ;  ld de,(VARS)
1580 ;  sbc hl,de
1590 ;  add hl,de
1600 ;  jp nc,r_nfound
1610 ;  jr searchline 
1620 ;
1630 ;cmpistr
1640 ;  ld (line),hl       ;Save search start location
1650 ;  ld de,str          ;Load search string
1660 ;cmpch
1670 ;  ld a,(de)
1680 ;  cp ENTER           ;End of search string
1690 ;  jr z,match         ;  we matched all prior chars
1700 ;  ld b,a
1710 ;  ld a,(hl)
1720 ;  cp 14              ;Check num prefix
1730 ;  jr nz, notnum      ; if not continue
1740 ;  ld bc,6
1750 ;  add hl, bc         ;skip 6 byte binary number 
1760 ;  jr cmpistr         ;Restart search
1770 ;notnum
1780 ;  call toupper       ;Convert to upper case
1790 ;  cp ENTER           ;Check if EOL
1800 ;  jr z,notfound      ; then string not found
1810 ;  inc hl             ;Move to next char in source
1820 ;  inc de             ;Move to next char in search string
1830 ;  cp b               ;Compare current characters
1840 ;  jr z, cmpch        ;If match, continue compare
1850 ;  ld hl,(line)       ;Get current search start
1860 ;  inc hl             ;Move to next start position
1870 ;  jr cmpistr         ; restart search
1880 ;
1890 ;notfound
1900 ;  xor a
1910 ;  inc a              ;Clear Z-flag
1920 ;  ret
1930 ;
1940 ;match               
1950 ;  ret
1960 ;
1970 ;setlineno
1980 ;  ld hl,(lineno)
1990 ;  ld (EPPC),hl
2000 ;  ld (PPC),hl
2010 ;  ld hl,m_found
2020 ;  jr report
2030 ;  
2040 ;toupper
2050 ;  cp "a"  
2060 ;  ret c
2070 ;  cp "z"+1
2080 ;  ret nc
2090 ;  and %11011111      ;Change to upper case
2100 ;  ret
2110 ;
2120 ;showusage
2130 ;  ld hl,usage
2140 ;printstr
2150 ;  ld a,(hl)
2160 ;  or a
2170 ;  ret z
2180 ;  rst $10
2190 ;  inc hl
2200 ;  jr printstr
2210 ;
2220 ;r_nfound
2230 ;  ld hl,m_nfound
2240 ;  jr report
2250 ;r_badlinex
2260 ;  pop hl             ;Tidy stack from CALL
2270 ;r_badline
2280 ;  ld hl,e_badline
2290 ;report
2300 ;  xor a              ;Flag report back to BASIC
2310 ;  scf
2320 ;  ret
2330 ;  
2340 ;exit
2350 ;  xor a           
2360 ;  ret
2370 ;
2380 ;e_badline db "Invalid line numbe","r"|$80
2390 ;e_badsrch db "Invalid search strin","g"|$80
2400 ;m_nfound  db "Not "
2410 ;m_found   db "Foun","d"|$80
2420 ;usage db "BASIC Find String v0.4",ENTER
2430 ;      db "bfind [@]|[@nnnn] <str>",ENTER
2440 ;      db " @     search from edit line",ENTER
2450 ;      db " @nnnn search from line nnnn",ENTER
2460 ;      db " <str> string to find",ENTER,ENTER,0
2470 ;
2480 ;lineno  dw 0        ;current line number
2490 ;line    dw 0        ;pointer to current line
2500 ;str     ds BUFLEN   ;string to search for
```

