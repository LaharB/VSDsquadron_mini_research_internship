# VSDSquadron_mini

## Task 1

The first task to be performed is the installation of RISC-V toolchain.

After the installation is complete, we write a C code to perfrom the sum of numbers from 1 to n. For this we open any editor (*here we have used leafpad* ),we open leafpad by the following command-

``leafpad sum1ton.c``

![Alt text](<opening leafpad.png>)

We write the required C code and put the value of n = 5

![Alt text](<opening leafpad and writing c code.png>)

Next we compile this C code and execute it using the gcc compiler using the respective commands -

``gcc sum1ton.c``

``./a.out``

![Alt text](<c code output.png>)

The next step is to compile the same code using RISC-V compiler.We use two modes for compilation process - 

### 1) Compiltion using O1 mode 
We do the compilation using the command : 

``riscv64-unknown-elf-gcc -O1 -mabi=lp64 -march=rv64i -o sum1ton.o sum1ton.c``

The object file **sum1ton.o** is created 

![Alt text](<O1 mode riscv compiling .png>)

After the compilation and creation of object file, we go to a new terminal and use **cat sum1ton.c** to display the code on the terminal and use the following command to see the assembly level instructions : 

``riscv64-unknown-elf-objdump -d sum1ton.o | less``

**Note** : We use **| less** to see less number of instructions and our main concern is with the **main()** part of the code so we use **/main** to go that part and press **n** to enter it as shown: 

![Alt text](<number of instructions for O1 mode.png>)

Then we count the number of instructions for the execution of the **main()** part.For that we simply subtract the starting instruction address in **main() block** i.e. **10184** from the next starting instruction of the **next block** i.e **101B0** and divide the result by 4 as **there is a difference of 4 between any two instruction addresses.** We find the number of instructions to be 11.

![Alt text](<O1 mode address difference.png>)
![Alt text](<O1 mode calculation of number of instructions.png>)

### 2) Compiltion using Ofast mode
We now perform the RISC-V compilation of the C code using ofast mode using the following command : 

``riscv64-unknown-elf-gcc -Ofast -mabi=lp64 -march=rv64i -o sum1ton.o sum1ton.c``

![Alt text](<Ofast mode riscv compiling.png>)

And again give the command :
``riscv64-unknown-elf-objdump -d sum1ton.o | less`` to see the assembly level instructions.We count the number of instructions in the same manner and find it to be 11 as well.

![Alt text](<Ofast mode calculation of number of instructions.png>)

There is no difference in the number of instructions while using O1 mode and Ofast mode is because the value of n is small ( *in our case n =5* )

Thus we perform TASK 1 successfully.

## Task 2

The second task is to identify various RISC-V instruction types  and their exact 32-bit instruction type format for all the given instructions.

### What is RISC-V ?

- RISC-V (Reduced Instruction Set Computer - V) is an open-source ISA (Instruction Set Architecture) based on the principles of RISC.
- It was designed to be simple ,efficient and scalable across a wide variety of applications

### Overview of RISC-V Architecture
- **Simplicity and Extensibility**: RISC-V is designed to have a small core instruction set, which can be expanded with optional extensions.
- **Modular Design**: The base ISA can be extended with various standard extensions, making it highly customizable for different applications.
- **Open Source**: Unlike many other ISAs, RISC-V is open and free, allowing anyone to implement it without licensing fees.

### RISC-V Instruction Sets
RISC-V has several standard base instruction sets:
- RV32I: 32-bit integer base ISA.
- RV64I: 64-bit integer base ISA.
- RV128I: 128-bit integer base ISA (still in development).

### Instruction Formats
RISC-V instructions are encoded in a base 32-bit format using several types of encodings:

``R, I, S, B, U, and J.``

### 1. R-Type (Register)

- These instructions are used for operations that involve three registers - two source registers and one destination register
- Used for arithmetic/logical operations
  
![Alt text](<R-type .png>)


- opcode (7): partially specifies operation
– e.g. R-types have opcode = 0b0110011,
SB (branch) types have opcode = 0b1100011
- funct7+funct3 (10): combined with opcode,
these two fields describe what operation to
perform
- rs1 (5): 1st operand (“source register 1”)
- rs2 (5): 2nd operand (second source register)
- rd (5): “destination register” — receives the result of computation

### 2. I-type (Immediate)

- Instructions with immediates, loads
- These instructions are used for arithmetic operations with a small constant and for load instructions.

![Alt text](<Task 2/I-type.png>)

**NOTE - Only imm field is different from R-format: rs2 and funct7 replaced by 12-bit signed immediate, imm[11:0]**

- opcode (7): uniquely specifies the
instruction
- rs1 (5): specifies a register operand
- rd (5): specifies destination register that receives result of computation
- immediate (12): 12 bit number
  1. All computations done in words, so 12-bit
immediate must be extended to 32 bits
  2. always sign-extended to 32-bits before use in an
arithmetic operation

### 3. S-type (Store)

These instructions are used for store instructions

![Alt text](<Task 2/S-type.png>)

- **Store** needs to read two registers, rs1 for base memory
address, and rs2 for data to be stored, as well as need
immediate offset!
- Cannot have both rs2 and immediate in same place as other
instructions
**Note: stores don’t write a value to the register file, no rd**
- RISC-V design decision is move low 5 bits of immediate to
where rd field was in other instructions – keep rs1/rs2
fields in same place

### 4. B-type (Branch)

- These instructions are used for conditional branch instructions
- They compare two registers and branch to an address if the condition is met.
  
![Alt text](<Task 2/B-type.png>) 

*Here is a simplified flowchart of how a B-type instruction like **BEQ (Branch if Equal)** might work:*

- Start: The process begins when the B-type instruction is fetched from memory.
- Decode Instruction: The instruction is decoded to identify it as a B-type instruction, and the relevant fields (source registers rs1 and rs2, funct3 for the specific operation, and the 12-bit immediate for the branch address) are extracted.
- Read Registers: The values in the source registers rs1 and rs2 are read from the register file.
- Compare Registers: The values from rs1 and rs2 are compared. If the condition specified by funct3 is met (for BEQ, this is equality), proceed to the next step. If not, go to step 6.
- Calculate Branch Address: If the condition is met, the program counter (PC) is updated with the branch address, which is calculated by adding the PC and the sign-extended 12-bit immediate, shifted left by 1 bit. Then, go to step 7.
- Increment PC: If the condition is not met, the PC is simply incremented to the next instruction.
- End: The process ends, and the next instruction cycle begins at the updated PC.

### 5. U-type (Upper immediate)

- Used for large immediate values, setting the upper 20 bits of a register.
- They are typically used for loading large constants into registers. The U-type format is used by the LUI (Load Upper Immediate) and AUIPC (Add Upper Immediate to PC) instructions.

![Alt text](<Task 2/U-type.png>)

- opcode (7 bits): This field determines the operation to be performed. For LUI, the opcode is 0110111, and for AUIPC, it’s 0010111.
- rd (5 bits): This is the destination register where the result of the operation will be stored.
- imm[31:12] (20 bits): This is the immediate value that is used in the operation. It’s a 20-bit value that gets loaded into the upper 20 bits of the destination register.

### How the two U-type instructions work?

1. LUI (Load Upper Immediate): The LUI instruction loads a 20-bit immediate into the upper 20 bits of the destination register. The lower 12 bits are filled with zeros. For example, LUI x5, 1000 would load the value 1000 shifted left by 12 bits into register x5.
2. AUIPC (Add Upper Immediate to PC): The AUIPC instruction adds a 20-bit immediate, shifted left by 12 bits, to the program counter (PC), and stores the result in the destination register. This is useful for constructing large offsets for jumps and branches. For example, AUIPC x5, 1000 would add the value 1000 shifted left by 12 bits to the PC, and store the result in register x5.

### 6. J-type (Jump)

- These instructions are used for unconditional jump instructions
- The J-type format is used by the JAL (Jump and Link) instruction.

![Alt text](<Task 2/J-type.png>)

- opcode (7 bits): This field determines the operation to be performed. For JAL, the opcode is 1101111.
- rd (5 bits): This is the destination register where the result of the operation will be stored.
- imm[20|10:1|11|19:12] (20 bits): This is the immediate value that is used in the operation. It’s a 20-bit value that represents the offset to add to the program counter (PC).

### Now for the given set of instructions given in the task : 
ADD r6, r2, r1   
SUB r7, r1, r2    
AND r8, r1, r3   
OR r9, r2, r5   
XOR r10, r1, r4   
SLT r11, r2, r4   
ADDI r12, r4, 5   
SW r3, r1, 2   
SRL r16, r14, r2   
BNE r0, r1, 20   
BEQ r0, r0, 15   
LW r13, r1, 2   
SLL r15, r1, r2

``ADD r6, r2, r1``
- This is an **R-type** instruction that adds the contents of registers r2 and r1 and stores the result in register r6. 
- The 32-bit instruction code format is: 
  opcode(ADD)=0110011   
  funct3=000   
  rs1=00010  
  rs2=00001   
  rd=00110   
  funct7=0000000  
  32-bit instruction : **0000000 00001 00010 000 00110 0110011**

``SUB r7, r1, r2``
- This is an **R-type** instruction that subtracts the contents of register r2 from r1 and stores the result in register r7. 
- The 32-bit instruction code format is: 
  opcode(SUB)=0110011  
  funct3=000  
  rs1=00001  
  rs2=00010  
  rd=00111    
  funct7=0100000  
  32-bit instruction : **0100000 00010 00001 000 00111 0110011**

``AND r8, r1, r3``

- This is an **R-type** instruction that performs a bitwise AND operation on the contents of registers r1 and r3 and stores the result in register r8. 
- The 32-bit instruction code format is: 
  opcode(AND)=0110011   
  funct3=111       
  rs1=00001        
  rs2=00011    
  rd=01000   
  funct7=0000000  
  32-bit instruction : **0000000 00011 00001 111 01000 0110011**

``OR r9, r2, r5``

- This is an **R-type** instruction that performs a bitwise OR operation on the contents of registers r2 and r5 and stores the result in register r9. 
- The 32-bit instruction code format is: 
  opcode(OR)=0110011  
  funct3=110     
  rs1=00010   
  rs2=00101   
  rd=01001  
  funct7=0000000  
  32-bit instruction : **0000000 00101 00010 110 01001 0110011**

``XOR r10, r1, r4``

- This is an **R-type** instruction that performs a bitwise XOR operation on the contents of registers r1 and r4 and stores the result in register r10. 
- The 32-bit instruction code format is: 
  opcode(XOR)=0110011 
  funct3=100
  rs1=00001
  rs2=00100, 
  rd=01010, 
  funct7=0000000
  32-bit instruction : **0000000 00100 00001 100 01010 0110011**

``SLT r11, r2, r4`` 

- This is an **R-type** instruction that sets register r11 to 1 if the contents of register r2 is less than the contents of register r4, otherwise it sets r11 to 0. 
- The 32-bit instruction code format is:   
  opcode(SLT)=0110011   
  funct3=010   
  rs1=00010   
  rs2=00100   
  rd=01011   
  funct7=0000000
  32-bit instruction : **0000000 00100 00010 010 01011 0110011**

``ADDI r12, r4, 5``

- This is an **I-type** instruction that adds the immediate value 5 to the contents of register r4 and stores the result in register r12. 
- The 32-bit instruction code format is:   
  opcode(ADDI)=0010011   
  funct3=000   
  rs1=00100  
  rd=01100   
  imm=000000000101  
  32-bit instruction : **000000000101 00100 000 01100 0010011**

``SW r3, r1, 2``  

- This is an **S-type** instruction that stores the word in register r3 to the memory address computed by adding the immediate value 2 to the contents of register r1. 
- The 32-bit instruction code format is: 
  opcode(SW)=0100011  
  funct3=010   
  rs1=00001  
  rs2=00011   
  imm=0000000|00010  
  32-bit instruction : **0000000 00011 00001 010 00010 0100011**

``SRL r16, r14, r2`` 

- This is an **R-type** instruction that performs a logical right shift on the contents of register r14 by the amount specified in register r2 and stores the result in register r16. 
- The 32-bit instruction code format is: 
  opcode(SRL)=0110011   
  funct3=101   
  rs1=01110  
  rs2=00010   
  rd=10000  
  funct7=0000000  
  32-bit instruction : **0000000 00010 01110 101 10000 0110011**

``BNE r0, r1, 20`` 

- This is a **B-type** instruction that branches to the instruction 20 steps ahead if the contents of registers r0 and r1 are not equal. 
- The 32-bit instruction code format is: 
  opcode(BNE)=1100011   
  funct3=001   
  rs1=00000   
  rs2=00001   
  imm=0|00001|0100|0  
  32-bit instruction : **0000000 00001 00000 001 0001 00110011**

``BEQ r0, r0, 15``

- This is a **B-type** instruction that branches to the instruction 15 steps ahead if the contents of registers r0 and r0 are equal. 
- The 32-bit instruction code format is: 
  opcode(BEQ)=1100011   
  funct3=000   
  rs1=00000  
  rs2=00000    
  imm=0|00000|1111|0  
  32-bit instruction : **0000000 00000 00000 000 1111 00110011**

``LW r13, r1, 2``

- This is an **I-type** instruction that loads a word from the memory address computed by adding the immediate value 2 to the contents of register r1 and stores it in register r13. 
- The 32-bit instruction code format is: 
  opcode(LW)=0000011   
  funct3=010   
  rs1=00001   
  rd=01101   
  imm=000000000010  
  32-bit instruction : **000000000010 00001 010 01101 0000011**

``SLL r15, r1, r2``

- This is an **R-type** instruction that performs a logical left shift on the contents of register r1 by the amount specified in register r2 and stores the result in register r15. 
- The 32-bit instruction code format is: 
  opcode(SLL)=0110011 
  funct3=001
  rs1=00001
  rs2=00010 
  rd=01111 
  funct7=0000000
  32-bit instruction : **0000000 00010 00001 001 01111 0110011**






































 








