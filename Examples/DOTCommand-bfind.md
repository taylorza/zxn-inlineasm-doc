# BFIND DOT Command
BFIND is a NextZXOS DOT command that enables you to search to text strings in your BASIC source code. The command will search any text in the source file including comments and if found will position the editor at the line the text was found.

For a detailed explanation of creating DOT commands with the NextBASIC Inline Assembler, please see the [HELLO DOT Command Example](DOTCommand-Hello.md)

This example does show how to interact with the NextBASIC Editor. As I am still learning about this myself, do not take this as an example of the right way to do it but rather just an example of what is possible with the NextBASIC Inline Assembler.

After entering and executing the code below, you should have a file `bfind` in the DOT folder on the SD Card. You can test the DOT Command out in this file, for example to find the "usage" message you can enter the following directly in the BASIC editor

`.bfind usage db`

This will launch the DOT command and scan the source code for the string "usage db". The search is not case sensitive, you might want to experiment with adding additional options to support case sensitive searches.

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
 280 ;;  ld (oldsp),sp
 290 ;
 300 ;  ld a,(hl)         
 310 ;  cp "@"             ;Check for line switch
 320 ;  jr z,findlineno    ;  find starting line
 330 ;  ld de,(PROG)       ;else start at first line
 340 ;  ld (line),de       
 350 ;  jr cpystr          ;Copy search string
 360 ;
 370 ;parselineno
 380 ;  ld b,LIMIT
 390 ;nxtdigit
 400 ;  ld a,(hl)          ;Get argument character
 410 ;  cp ENTER           ;Check for end of argument
 420 ;  jp z,r_badsrch     ;  exit if no search string
 430 ;  cp "0"             ;Else check for valid digit
 440 ;  ret c
 450 ;  cp "9"+1
 460 ;  ret nc
 470 ;  sub "0"            ;ASCII to 0-9
 480 ;  push hl
 490 ;    ld hl,(lineno)   
 500 ;    ld e,l
 510 ;    ld d,h
 520 ;    add hl,hl        ;x2
 530 ;    adc hl,hl        ;x4
 540 ;    adc hl,hl        ;x8
 550 ;    adc hl,de
 560 ;    adc hl,de        ;x10
 570 ;    add hl,a         ;+unit value
 580 ;    ld (lineno),hl   ;update line no
 590 ;  pop hl
 600 ;  inc hl             ;move to next digit
 610 ;  djnz nxtdigit
 620 ;  jp r_badlinex
 630 ;;  dec b
 640 ;;  jp z,r_badline
 650 ;;  jr nxtdigit
 660 ;  
 670 ;findlineno
 680 ;  inc hl             ;Skip '@'
 690 ;  ld a,(hl)
 700 ;  cp SPACE           ;Check for separator
 710 ;  jr z,useeppc       ; no line, lineo=eppc
 720 ;  call parselineno
 730 ;  ld c,a             ;Save terminating token
 740 ;  ld a,LIMIT         ;Count for empty buffer
 750 ;  cp b
 760 ;  jr z,move2line     ;Presume @LABEL
 770 ;  ld a,c             ;Restore terminating token
 780 ;  cp SPACE           ;Expect separator
 790 ;  jp nz,r_badline    ;  else exit with error
 800 ;  inc hl             ;Skip space
 810 ;  jr move2line
 820 ;useeppc
 830 ;  ld de,(EPPC)
 840 ;  inc de             ;Move to just after eppc
 850 ;  ld (lineno),de     ;  set starting line number
 860 ;move2line
 870 ;  push hl            ;Save commandline
 880 ;    ld hl,(PROG)
 890 ;cmplineno
 900 ;    push hl          ;Save source line
 910 ;      ld d,(hl)         
 920 ;      inc hl
 930 ;      ld e,(hl)      ;DE-Current line no
 940 ;      ld hl,(lineno) ;HL-Target line no
 950 ;      or a
 960 ;      ex de,hl
 970 ;      sbc hl,de      ;Compare current with target
 980 ;    pop hl           ;Restore source line
 990 ;    jr nc,atline     ;If current>=target set start
1000 ;    inc hl
1010 ;    inc hl           ;Skip line number
1020 ;    ld e,(hl)        ;Load length in DE
1030 ;    inc hl
1040 ;    ld d,(hl)
1050 ;    inc hl           ;Point to start of code
1060 ;    add hl,de        ;Point to end of line
1070 ;    ld de,(VARS)     ;Check for end of source
1080 ;    sbc hl,de
1090 ;    add hl,de        ;If less than target
1100 ;    jr c,cmplineno   ;  continue search
1110 ;  pop hl
1120 ;  ret
1130 ;atline  
1140 ;  ld (line),hl       ;Set start line for search
1150 ;  pop hl             ;Restore commandline
1160 ;
1170 ;cpystr
1180 ;  ld a,(hl)        
1190 ;  cp SPACE+1         ;Check for whitespace
1200 ;  inc hl
1210 ;  jr c,cpystr 
1220 ;  dec hl             ;Move back to char     
1230 ;  ld de,str
1240 ;  ld b,BUFLEN-1
1250 ;cpyloop
1260 ;  or a
1270 ;  jr z,search        ;If 0-terminator start search
1280 ;  cp ENTER  
1290 ;  jr z,search        ;If eol start search
1300 ;  call toupper       ; else convert to upper case
1310 ;  ld (de),a          ; and copy to search buffer
1320 ;  inc de
1330 ;  inc hl
1340 ;  ld a,(hl)          ;Load next character
1350 ;  djnz cpyloop       ;Loop to copy
1360 ;
1370 ;r_badsrch
1380 ;  pop hl             ;Balance stack after CALL
1390 ;  ld hl,e_badsrch
1400 ;  jp report          ;Error if string too long
1410 ;
1420 ;search
1430 ;  ld a,ENTER
1440 ;  ld (de),a          ;Add terminator to str
1450 ;
1460 ;  ld hl,(line)       ;Get pointer to source line
1470 ;searchline
1480 ;  ld a,(hl)          ;Read line number in big-endian
1490 ;  ld (lineno+1),a 
1500 ;  inc hl
1510 ;  ld a,(hl)
1520 ;  ld (lineno),a
1530 ;  inc hl             ;Move to length
1540 ;  inc hl
1550 ;  inc hl             ;Skip length
1560 ;  
1570 ;  call cmpistr
1580 ;  jr z,setlineno
1590 ;
1600 ;  inc hl             ;Move to next char on line
1610 ;  or a
1620 ;  ld de,(VARS)
1630 ;  sbc hl,de
1640 ;  add hl,de
1650 ;  jp nc,r_nfound
1660 ;  jr searchline 
1670 ;
1680 ;cmpistr
1690 ;  ld (line),hl       ;Save search start location
1700 ;  ld de,str          ;Load search string
1710 ;cmpch
1720 ;  ld a,(de)
1730 ;  cp ENTER           ;End of search string
1740 ;  jr z,match         ;  we matched all prior chars
1750 ;  ld b,a
1760 ;  ld a,(hl)
1770 ;  cp 14              ;Check num prefix
1780 ;  jr nz, notnum      ; if not continue
1790 ;  ld bc,6
1800 ;  add hl, bc         ;skip 6 byte binary number 
1810 ;  jr cmpistr         ;Restart search
1820 ;notnum
1830 ;  call toupper       ;Convert to upper case
1840 ;  cp ENTER           ;Check if EOL
1850 ;  jr z,notfound      ; then string not found
1860 ;  inc hl             ;Move to next char in source
1870 ;  inc de             ;Move to next char in search string
1880 ;  cp b               ;Compare current characters
1890 ;  jr z, cmpch        ;If match, continue compare
1900 ;  ld hl,(line)       ;Get current search start
1910 ;  inc hl             ;Move to next start position
1920 ;  jr cmpistr         ; restart search
1930 ;
1940 ;notfound
1950 ;  xor a
1960 ;  inc a              ;Clear Z-flag
1970 ;  ret
1980 ;
1990 ;match               
2000 ;  ret
2010 ;
2020 ;setlineno
2030 ;  ld hl,(lineno)
2040 ;  ld (EPPC),hl
2050 ;  ld (PPC),hl
2060 ;  ld hl,m_found
2070 ;  jr report
2080 ;  
2090 ;toupper
2100 ;  cp "a"  
2110 ;  ret c
2120 ;  cp "z"+1
2130 ;  ret nc
2140 ;  and %11011111      ;Change to upper case
2150 ;  ret
2160 ;
2170 ;showusage
2180 ;  ld hl,usage
2190 ;printstr
2200 ;  ld a,(hl)
2210 ;  or a
2220 ;  ret z
2230 ;  rst $10
2240 ;  inc hl
2250 ;  jr printstr
2260 ;
2270 ;r_nfound
2280 ;  ld hl,m_nfound
2290 ;  jr report
2300 ;r_badlinex
2310 ;  pop hl             ;Tidy stack from CALL
2320 ;r_badline
2330 ;  ld hl,e_badline
2340 ;report
2350 ;  xor a              ;Flag report back to BASIC
2360 ;  scf
2370 ;;  ld sp,(oldsp)
2380 ;  ret
2390 ;  
2400 ;exit
2410 ;  xor a           
2420 ;;  ld sp,(oldsp)      ;Restore stack and exit
2430 ;  ret
2440 ;
2450 ;e_badline db "Invalid line numbe","r"|$80
2460 ;e_badsrch db "Invalid search strin","g"|$80
2470 ;m_nfound  db "Not "
2480 ;m_found   db "Foun","d"|$80
2490 ;usage db "BASIC Find String v0.4",ENTER
2500 ;      db "bfind [@]|[@nnnn] <str>",ENTER
2520 ;      db " @     search from edit line",ENTER
2535 ;      db " @nnnn search from line nnnn",ENTER
2540 ;      db " <str> string to find",ENTER,ENTER,0
2550 ;
2560 ;oldsp   dw 0        ;entry stack pointer
2570 ;lineno  dw 0        ;current line number
2580 ;line    dw 0        ;pointer to current line
2590 ;str     ds BUFLEN   ;string to search for

```

