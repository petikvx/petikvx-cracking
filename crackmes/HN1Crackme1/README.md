## Modifier le programme pour avoir immédiatement le bon message

```
CPU Disasm
Address   Hex dump             Command                                         Profile  Comments
004010D8   > \83FA 0A          CMP EDX,0A                                      ;
004010DB   .  74 19            JE SHORT 004010F6                               ;
004010DD   .  6A 00            PUSH 0                                          ;
004010DF   .  6A 00            PUSH 0                                          ;
004010E1   .  6A 10            PUSH 10                                         ;
004010E3   .  68 85204000      PUSH OFFSET 00402085                            ;           ASCII "Wrong password."
004010E8   .  FF35 04204000    PUSH DWORD PTR DS:[402004]                      ;
004010EE   .  FF15 58304000    CALL DWORD PTR DS:[<&KERNEL32.WriteConsoleA>]   ;
004010F4   .  EB 17            JMP SHORT 0040110D                              ;
004010F6   >  6A 00            PUSH 0                                          ;
004010F8   .  6A 00            PUSH 0                                          ;
004010FA   .  6A 18            PUSH 18                                         ;
004010FC   .  68 95204000      PUSH OFFSET 00402095                            ;           ASCII "Password is correct! ;)"
00401101   .  FF35 04204000    PUSH DWORD PTR DS:[402004]                      ;
00401107   .  FF15 58304000    CALL DWORD PTR DS:[<&KERNEL32.WriteConsoleA>]   ;
```
**JE SHORT 004010F6**
doit être modifié en
**JNE SHORT 004010F6**

ou en NOP

## Trouver le bon serial

La vérification se fait sur 10 caractères

```
CPU Disasm
Address   Hex dump             Command                                         Profile  Comments
00401056   .  FC               CLD                                             ;
00401057   >  AC               LODS BYTE PTR DS:[ESI]                          ;
00401058   .  3C 0D            CMP AL,0D                                       ;
0040105A   .  74 7C            JE SHORT 004010D8                               ;
0040105C   .  41               INC ECX                                         ;
0040105D   .  80F9 01          CMP CL,1                                        ;
00401060   .  75 08            JNE SHORT 0040106A                              ;
00401062   .  42               INC EDX                                         ;
00401063   .  3C 47            CMP AL,47                                       ;
00401065   .^ 77 F0            JA SHORT 00401057                               ; 1er : > 'G'
00401067   .  4A               DEC EDX                                         ;
00401068   .^ EB ED            JMP SHORT 00401057                              ;
0040106A   >  80F9 02          CMP CL,2                                        ;
0040106D   .  75 08            JNE SHORT 00401077                              ;
0040106F   .  42               INC EDX                                         ;
00401070   .  3C 6D            CMP AL,6D                                       ;
00401072   .^ 7C E3            JL SHORT 00401057                               ; 2nd : < 'm'
00401074   .  4A               DEC EDX                                         ;
00401075   .^ EB E0            JMP SHORT 00401057                              ;
00401077   >  80F9 03          CMP CL,3                                        ;
0040107A   .  75 08            JNE SHORT 00401084                              ;
0040107C   .  42               INC EDX                                         ;
0040107D   .  3C 56            CMP AL,56                                       ; 3e : 'V'
0040107F   .^ 74 D6            JE SHORT 00401057                               ;
00401081   .  4A               DEC EDX                                         ;
00401082   .^ EB D3            JMP SHORT 00401057                              ;
00401084   >  80F9 04          CMP CL,4                                        ;
00401087   .  75 08            JNE SHORT 00401091                              ;
00401089   .  42               INC EDX                                         ;
0040108A   .  3C 66            CMP AL,66                                       ;
0040108C   .^ 73 C9            JNB SHORT 00401057                              ; 4e pas en dessous de 'f'
0040108E   .  4A               DEC EDX                                         ;
0040108F   .^ EB C6            JMP SHORT 00401057                              ;
00401091   >  80F9 05          CMP CL,5                                        ;
00401094   .  75 08            JNE SHORT 0040109E                              ;
00401096   .  42               INC EDX                                         ;
00401097   .  3C 33            CMP AL,33                                       ;
00401099   .^ 76 BC            JBE SHORT 00401057                              ; 5e <= '3'
0040109B   .  4A               DEC EDX                                         ;
0040109C   .^ EB B9            JMP SHORT 00401057                              ;
0040109E   >  80F9 06          CMP CL,6                                        ;
004010A1   .  75 08            JNE SHORT 004010AB                              ;
004010A3   .  42               INC EDX                                         ;
004010A4   .  3C 79            CMP AL,79                                       ;
004010A6   .^ 77 AF            JA SHORT 00401057                               ; 6e > 'y'
004010A8   .  4A               DEC EDX                                         ;
004010A9   .^ EB AC            JMP SHORT 00401057                              ;
004010AB   >  80F9 07          CMP CL,7                                        ;
004010AE   .  75 08            JNE SHORT 004010B8                              ;
004010B0   .  42               INC EDX                                         ;
004010B1   .  3C 38            CMP AL,38                                       ;
004010B3   .^ 7D A2            JGE SHORT 00401057                              ; 7e >= '8'
004010B5   .  4A               DEC EDX                                         ;
004010B6   .^ EB 9F            JMP SHORT 00401057                              ;
004010B8   >  80F9 08          CMP CL,8                                        ;
004010BB   .  75 08            JNE SHORT 004010C5                              ;
004010BD   .  42               INC EDX                                         ;
004010BE   .  3C 4E            CMP AL,4E                                       ;
004010C0   .^ 7C 95            JL SHORT 00401057                               ; 8e < 'N'
004010C2   .  4A               DEC EDX                                         ;
004010C3   .^ EB 92            JMP SHORT 00401057                              ;
004010C5   >  80F9 09          CMP CL,9                                        ;
004010C8   .  75 08            JNE SHORT 004010D2                              ;
004010CA   .  42               INC EDX                                         ;
004010CB   .  3C 52            CMP AL,52                                       ;
004010CD   .^ 75 88            JNE SHORT 00401057                              ; 9e != 'R'
004010CF   .  4A               DEC EDX                                         ;
004010D0   .^ EB 85            JMP SHORT 00401057                              ;
004010D2   >  42               INC EDX                                         ;
004010D3   .  3C 32            CMP AL,32                                       ;
004010D5   .  74 01            JE SHORT 004010D8                               ; 10e = "2"
004010D7   .  4A               DEC EDX                                         ;
004010D8   >  83FA 0A          CMP EDX,0A                                      ; Avoir 10 caractères
004010DB   .  74 19            JE SHORT 004010F6                               ;
```

Exemple de solution : **HaVg3z8AB2**

## Screenshot
![crackme1-cracked](/crackmes/HN1Crackme1/crackme1-cracked.png)
![good-password](/crackmes/HN1Crackme1/good-password.png)
