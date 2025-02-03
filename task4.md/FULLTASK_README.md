
<summary><b>Task 4:</b> Simulating and Viewing the RISC-V core instructions using Verilog netlist and testbench </summary>   
<br>
In this task, we will be observing output waveforms of RISC-V instructions by performing functional simulations using a verilog netlist .
 
**Software** : iverilog, GTKWave

Command to install iverliog and GTKWave:-
```
$ sudo apt install iverilog gtkwave

```
Follow the following steps to perform the simulation:
-
1. Create a directory with the command:-
```
mkdir <name>
```
2. create 2 files with the ```touch``` command as ```name_rv32i.v``` and ```name_rv32i_tb.v``` for verilog netlist and testbench code respectively.

We will not be writing the verilog codes, we shall take it from the following reference github repository.
Github repository: [iiitb_rv32i](https://github.com/vinayrayapati/rv32i/)* 


3. After getting the verilog codes and saving them, we can now simulate and verify.
Use the following code:-
```
$ iverilog -o name_rv32i name_rv32i.v name_rv32i_tb.v
```
After the above command is run, it will create ```iiitb_rv32i.vcd``` file.

4. Now we shall open GTKWave with the above generated file, and view the output waveforms 

Command for opening GTKWave:
```
$ gtkwave iiitb_rv32i.vcd
```
The instructions in the verilog code are hard-coded.

**Hard-coded ISA** - That means the instructions do not follow the RISC-V 32-bit pattern, they have been encoded by designer with custom pattern.

The following table explains the instructions in detail and the shows difference between RISC-V and custom encoding of instructions:

| **Instruction**      | **Explanation**                                                                          | **RISC-V Encoding**  | **Custom Encoding**  |
|-----------------------|-----------------------------------------------------------------------------------------|----------------------|----------------------|
| **ADD R4, R3, R2**    | Computes the sum of the values in R3 and R2, placing the result in R4                   | `32'h00210333`       | `32'h02308400`       |
| **SUB R5, R2, R3**    | Subtracts the value stored in R3 from R2 and stores the result in R5                    | `32'h403103b3`       | `32'h02309480`       |
| **AND R6, R2, R4**    | Executes a bitwise AND operation between R2 and R4, saving the result in R6             | `32'h0040e433`       | `32'h0240a400`       |
| **OR R7, R3, R5**     | Combines the bits of R3 and R5 using the OR operation, writing the output to R7         | `32'h005174b3`       | `32'h02513480`       |
| **XOR R8, R2, R4**    | Performs an XOR operation on the values in R2 and R4, storing the result in R8          | `32'h0040c633`       | `32'h0240c500`       |
| **SLT R2, R3, R5**    | Sets R2 to 1 if the value in R3 is smaller than R5, otherwise stores 0                  | `32'h00516133`       | `32'h02516580`       |
| **ADDI R10, R5, 8**   | Adds the immediate value 8 to the contents of R5 and stores the result in R10           | `32'h00512033`       | `32'h00820500`       |
| **BEQ R0, R0, 12**    | Compares R0 with R0, and if they are equal, branches to a target offset of 12           | `32'h00000c63`       | `32'h00c00002`       |
| **SW R2, R6, 4**      | Saves the value in R2 to the memory location calculated as R6 plus an offset of 4       | `32'h0020a223`       | `32'h00409281`       |
| **LW R9, R6, 4**      | Loads a word from memory at address (R6 + 4) into the R9 register                       | `32'h0040a683`       | `32'h00408681`       |
| **SRL R10, R8, R2**   | Shifts the bits in R8 right by the amount specified in R2, and stores the result in R10 | `32'h0020c293`       | `32'h0028a203`       |
| **SLL R11, R2, R3**   | Shifts the bits in R2 left by the value in R3, placing the result in R11                | `32'h003091b3`       | `32'h00308783`       |



Viewing the Output waveforms of the instructions in GTKWave :
--

1. ADD R6,R2,R1

![inst-1](https://github.com/user-attachments/assets/b620b73f-b2fd-450a-8be4-47c30f9bbb09)

2. SUB R7,R1,R2

![inst2-sub](https://github.com/user-attachments/assets/e4053e9c-6f78-4180-b15e-c699f6e81b2a)

3. AND R8,R1,R3

![inst3-AND](https://github.com/user-attachments/assets/0d277649-7941-4f31-bf0a-6693fff7472c)

4. OR R9,R2,R5

![inst4-OR](https://github.com/user-attachments/assets/50a69faf-d96e-4241-8290-39f239262ffe)

5. XOR R10,R1,R4

![inst5-XOR](https://github.com/user-attachments/assets/05c49a20-b0de-4097-ba4d-1c7aa1552c17)

6. SLT R1,R2,R4 : 
The SLT (Set on Less Than) instruction is an R-Type instruction in RISC-V assembly language, used to compare two registers and set a destination register to 1 if the first source register is less than the second source register. Otherwise, the destination register is set to 0.

![inst6-SLT](https://github.com/user-attachments/assets/428bd51d-478f-4cf4-a712-80878ad2f8dc)

7. ADDI R12,R4,5

![inst7-ADDI](https://github.com/user-attachments/assets/2c1f3a63-e42a-4f80-8f49-e66f7801a0a8)

8. SW R3,R1,2 : 
The SW (Store Word) instruction stores a 32-bit word from a source register into a memory address calculated as the sum of a base register and an immediate offset.

![inst8-SW](https://github.com/user-attachments/assets/3b2f8b35-5255-4ccd-ba43-b7b86f59e87f)


9. SRL R16,R11,R2: 
The SRL (Shift Right Logical) instruction shifts the value in a source register to the right by a specified number of bits, filling the vacated bits with zeros.

![inst9-SRL](https://github.com/user-attachments/assets/f9b69424-72b1-4abf-9764-1b049aeae6f9)

10. BEQ R0, R0, 15
The BEQ (Branch if Equal) instruction in RISC-V compares two registers; if their values are equal, it updates the Program Counter (PC) to branch to a specified offset. Otherwise, the PC increments to the next sequential instruction.

![inst10-BEQ](https://github.com/user-attachments/assets/46c4ff6a-f9c2-4fe8-a65f-b87a8e8039da)

The new PC value is: 10 + 15 = 25 (0x19)

11. BNE R0, R1, 20 :
The BNE (Branch if Not Equal) instruction in RISC-V compares two registers; if their values are not equal, it updates the Program Counter (PC) to branch to a specified offset. Otherwise, the PC increments to the next sequential instruction.

![inst11-BNE](https://github.com/user-attachments/assets/c7561294-4fa1-4edc-9906-cf549092640b)

The new PC value is = 10 + 15 = 25 (0x19)

12. SLL R15, R1, R2: 
The SLL (Shift Left Logical) instruction shifts the value in a source register to the left by a specified number of bits, filling the vacated bits with zeros.

![Inst12-SLL](https://github.com/user-attachments/assets/a80def9a-4553-44bb-9c5e-31c85c8f5eb0)

