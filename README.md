# VSDSquadron_mini

## Task 1

The first task to be performed is the installation of RISC-V toolchain.

After the installation is complete, we write a C code to perfrom the sum of numbers from 1 to n. For this we open any editor (*here we have used leafpad* ),we open leafpad by the following command-

<mark>leafpad sum1ton.c</mark>

![Alt text](<opening leafpad.png>)

We write the required C code and put the value of n = 5

![Alt text](<opening leafpad and writing c code.png>)

Next we compile this C code and execute it using the gcc compiler using the respective commands -

<mark>gcc sum1ton.c</mark>

<mark>./a.out</mark>

![Alt text](<c code output.png>)

The next step is to compile the same code using RISC-V compiler.We use two modes for compilation process - 

### 1) Compiltion using O1 mode 
We do the compilation using the command : 

<mark>riscv64-unknown-elf-gcc -O1 -mabi=lp64 -march=rv64i -o sum1ton.o sum1ton.c

The object file **sum1ton.o** is created 

![Alt text](<O1 mode riscv compiling .png>)

After the compialtion and creation of object file, we go to a new terminal and use **cat sum1ton.c** to display the code on th terminal and use the following command to see the assembly level instructions : 

<mark>riscv64-unknown-elf-objdump -d sum1ton.o | less</mark>

**Note** : We use **| less** to see less number of instrcutions and our main concern is with the **main()** part of the code so we use **/main** to go that part and press **n** to enter it as shown: 

![Alt text](<number of instructions for O1 mode.png>)

Then we count the number of instructions for the execution of the **main()** part.For that we simply subtract the starting instruction address in **main() block** i.e. **10184** from the next starting instruction of the **next block** i.e **101B0** and divide the result by 4 as **there is a difference of 4 between any two instruction addresses.** We find the number of instructions to be 11.

![Alt text](<O1 mode address difference.png>)
![Alt text](<O1 mode calculation of number of instructions.png>)


### 2) Compiltion using Ofast mode
We now perform the RISC-V compilation of the C code using ofast mode using the following command : 

<mark>riscv64-unknown-elf-gcc -Ofast -mabi=lp64 -march=rv64i -o sum1ton.o sum1ton.c

![Alt text](<Ofast mode riscv compiling.png>)

And again give the command :
<mark>riscv64-unknown-elf-objdump -d sum1ton.o | less</mark> to perform see the assembly level instructions.We count the number of instructions in the same manner and find it to be 11 as well.

![Alt text](<Ofast mode calculation of number of instructions.png>)

There is no difference in the number of instructions while using O1 mode and Ofast mode is because the value of n is small ( *in our case n =5* )

Thus we perform TASK 1 successfully.
