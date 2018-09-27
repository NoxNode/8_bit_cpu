# 8_bit_cpu
An 8 bit CPU made in the logic simulator, MultiMedia Logic
```
Made by NoxNode (https://github.com/NoxNode)
Made in under 72 hours (10/22/2016 - 10/24/2016)
This was made before I learned about pipelining so every instruction stalls until the one before it is done.

In order to open and run the 8 bit cpu, you need to have MML (MultiMedia Logic)
	Download: http://www.softronix.com/logic.html
Link that helps understand how this thing works:
	https://www.youtube.com/watch?v=cNN_tTXABUA

Instruction Set
xx = register code

Inst	Opcode	Operand 1	Mode	Operand 2	Notes
JMP	000	xx (src)		nzp		Jumps to the address in register xx if specified flag is 1
ADD	001	xx (src1)	0/1	xx (src2)	Always outputs to R0
AND	010	xx (src1)	0/1	xx (src2)	Always outputs to R0
MOV/NOT	011	xx (dest)	0/1	xx (src)	The mode bit for MOV means to NOT the input or not
LD	100	xx (dest)	0	xx (src)	Loads from address in src into dest
ST	101	xx (src)	0	xx (dest)	Stores value from src into address in dest
IN	110	xx (dest)	0	00		Stores user input into dest
OUT	111	xx (src)	0	00		Outputs src to output

Setps done before every instruction (after each step (separated by empty lines), the IC (Instruction Counter) is incremented)
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
	if immediate mode == 1
		set IVR

	enable	AIReg
	if immediate mode == 0
		enable	src2
	if immediate mode == 1
		enable IVR
	set	AO

	enable	AO
	set	dest
AND
	enable	src1
	set	ANDI
	if immediate mode == 1
		set IVR
	
	enable	ANDI
	if immediate mode == 0
		enable	src2
	if immediate mode == 1
		enable IVR
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

Example Program	(assembly) - adds 2 user inputted number then loops
NOP
IN	R0
MOV	R1, R0
IN	R0
ADD	R0, R1
OUT	R0
AND	R0, 0
JMPnzp	R0

Example Program (hex) - adds 2 user inputted number then loops
00
C0
68
C0
21
E0
44
07

Instructions again

Inst	Opcode	Operand 1	Mode	Operand 2	Notes
JMP	000	xx (src)		nzp		Jumps to the address in register xx if specified flag is 1
ADD	001	xx (src1)	0/1	xx (src2)	Always outputs to R0
AND	010	xx (src1)	0/1	xx (src2)	Always outputs to R0
MOV/NOT	011	xx (dest)	0/1	xx (src)	The mode bit for MOV means to NOT the input or not
LD	100	xx (dest)	0	xx (src)	Loads from address in src into dest
ST	101	xx (src)	0	xx (dest)	Stores value from src into address in dest
IN	110	xx (dest)	0	00		Stores user input into dest
OUT	111	xx (src)	0	00		Outputs src to output

Acornyms;Meaning;Notes

NOP	No operation (do nothing)
CC	condition code
nzp	negative, zero, positive
CLR	clear
CLK	clock
MAR	Memory Address Register
MDR	Memory Data Register
IP	Instruction Pointer		Same as PC (Program Counter)
IR	Instruction Register
IC	Instruction Counter		Each instruction takes multiple signal sending steps to finish the instruction, so the instruction counter keeps track of which signal sending step we're on
DIC	Decoded IC
OR	Output Register
NReg	Negation Register
ANDI	AND Input Register
ANDO	AND Output Register		I made AND and ADD have different input and output registers just because I already had the extra registers setup and would rather just rename things than make new logic to send signals to tell the ALU which operation to perform
AIReg	ADD Input Register
AOReg	ADD Output Register
IVR	Immediate value register
U	Usually means user
S	Usually means set
E	Usually means enable
0-7	Bits 0-7
In	input
Out	output
IN	IN instruction
OUT	OUT instruction

TODO
	make assembler
	make logic simulator able to read from assembled binary file

```
