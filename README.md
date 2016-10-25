# 8_bit_cpu
An 8 bit CPU made in the logic simulator, MultiMedia Logic
```
Made by NoxNode (https://github.com/NoxNode)
Made in under 72 hours (10/22/2016 - 10/24/2016)

In order to open and run the 8 bit cpu, you need to have MML (MultiMedia Logic)
	Download: http://www.softronix.com/logic.html
Link that help understand how this thing works:
	https://www.youtube.com/watch?v=cNN_tTXABUA

Instruction Set
xx = register code

Instruction	Opcode	Operand 1	Mode		Operand 2	Notes
JMP		000	xx (src)			nzp		Jumps to the address in register xx if specified flag is 1
ADD		001	xx (src1)	0		xx (src2)	Always outputs to R0
AND		010	xx (src1)	0		xx (src2)	Always outputs to R0
MOV/NOT		011	xx (dest)	0/1		xx (src)	The mode bit for MOV means to NOT the input or not
LD		100	xx (dest)	0		xx (src)	Loads from address in src into dest
ST		101	xx (src)	0		xx (dest)	Stores value from src into address in dest
IN		110	xx (dest)	0		00		Stores user input into dest
OUT		111	xx (src)	0		00		Outputs src to output

Setps done before every instruction (after each enable/set or disable/set, the IC is incremented)
	enable	IP
	set	MAR
	
	enable	MAR
	enable	Read
	set	MDR
	
	enable	MDR
	set	IR
	
	increment IP
		enable	IP
		enable	Cin0
		set	AO
		
		enable	AO
		set	IP

Steps the control unit takes for each instruction
JMP
	check cc flags
	if one matches
		enable	src
		set	IP
	else
		do nothing
ADD
	enable	src1
	set	AIReg

	enable	AIReg
	enable	src2
	set	AO

	enable	AO
	set	dest
AND
	enable	src1
	set	ANDI
	
	enable	src2
	enable	ANDI
	set	ANDO
	
	enable	ANDO
	set	dest
MOV
	if immediate mode == 0
		enable	src
		set	dest
	else if immediate mode == 1
		enable	src
		set	NReg
		
		enable	NReg
		set	dest
LD
	enable	src
	set	MAR
	
	enable	MAR
	enable	Read
	set	MDR
	
	enable	MDR
	set	dest
ST
	enable	src
	set	MDR
	
	enable	dest
	set	MAR
	
	enable	MAR
	enable	MDR
	enable	Write
IN
	enable	input
	set	dest
OUT
	enable	src
	set	output

Example Program	(assembly)
NOP
IN	R0		; (user inputs 0)
ST	R0	R0	; (store 0 into address 0)
IN	R1		; (user inputs A)
MOV	R0	R1	; (move A into R0)
OUT	R0		; (output A)
IN	R2		; (user inputs B)
IN	R3		; (user inputs whether to do A+B or A-B)
IN	R0		; (user inputs 0)
LD	R0	R0	; (load back into R0)
AND	R0	R3	; (check if R3 is a 0 or 1)
IN	R0		; (user inputs 0xF)
JMPz	R0		; (if R3 is 0, jump over taking 2SC of B)
NOT	R2	R2	; (NOT B)
IN	R0		; (user inputs 1)
ADD	R2	R0	; (R2 is now the 2SC of the inputted value)
ADD	R1	R0	; (do the addition)
OUT	R0		; (output the result)
JMPnzp	R3		; (loop back to instruction 0 or 1)
Example Program (hex)
00
C0
A0
C8
61
E0
D0
D8
C0
80
43
C0
02
76
C0
30
28
E0
1F

Instructions again

Inst	Opcode	Operand 1	Mode	Operand 2	Notes
JMP	000	xx (src)		nzp		Jumps to the address in register xx if specified flag is 1
ADD	001	xx (src1)	0	xx (src2)	Always outputs to R0
AND	010	xx (src1)	0	xx (src2)	Always outputs to R0
MOV/NOT	011	xx (dest)	0/1	xx (src)	The mode bit for MOV means to NOT the input or not
LD	100	xx (dest)	0	xx (src)	Loads from address in src into dest
ST	101	xx (src)	0	xx (dest)	Stores value from src into address in dest
IN	110	xx (dest)	0	00		Stores user input into dest
OUT	111	xx (src)	0	00		Outputs src to output
```
