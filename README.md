# MIPS Notes

Credit to [MIPT-ILab](https://github.com/MIPT-ILab/mipt-mips/wiki/MIPS-pseudo-instructions/_edit) and [this page](http://pages.cs.wisc.edu/~markhill/cs354/Fall2008/notes/MAL.instructions.html) for the MIPS docs used as a starter for this page.

These are basically the docs from these 2 sites along with some clarification notes I'm making to study for my CE 12 (Assembly Language) final but I hope they're helpful to someone also struggling with MIPS.

### Load and Store 

`addr` is composed of offset($register), which is equal to the address $register + offset (in bytes). 

Note that for the load instructions (beginning with l) the destination is the first argument. Whereas for the store instructions (beginning with s), the destination is the second argument.

| Name                | Mnemonic | Operands       | Operation                                                                                                                                                                                 |
|---------------------|----------|----------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Load word           | lw       | d, addr        | A word (4 bytes) is loaded from addr and placed into d (the addr must be word aligned). This dereferences a pointer                                                                       |
| Load byte           | lb       | d, addr        | A byte is loaded from addr and placed into the rightmost byte of d  (sign extension defines the other bits of d)                                                                          |
|  Load byte unsigned | lbu      | d, addr        |  A byte is loaded from addr and placed into the rightmost byte of d (see [this SO answer on unsigned lbu vs signed lb](https://stackoverflow.com/questions/7226147/clarifications-on-signed-unsigned-load-and-store-instructions-mips))  | 
| Load immediate      | li       | d, immed (int) | The immediate value of immed is placed into d                                                                                                                                             |  
| Load address        | la       | d, addr        | The address assigned to label (pointer to the label) is placed into d. This is how you create a pointer                                                                                   |   
| Store word          | sw       | d, addr        | The contents of d is stored into addr (the addr must be word aligned)                                                                                                                     |   
| Store byte          | sb       | d, addr        | The rightmost/least significant byte of d is stored in addr                                                                                                                               |   
      
### Sizes

| Type         | Size (bytes) | Size (bits) |
|--------------|--------------|-------------|
| 1 hex digit      | 1/2 byte     | 4 bits      |
| 1 ASCII char | 1 byte       | 8 bits      |
| 1 word       | 4 bytes      | 32 bits     |

### Branches

If the conditional is true for two registers, then jump to the label C.

| **Name** | **Assembly syntax** | **Expansion** |
|:---------|:--------------------|:--------------|
| branch unconditionally | `b C`               | `beq $zero, $zero, C` |
| branch unconditionally<br/>and link | `bal C`             | `bgezal $zero, C` |
| branch if greater than | `bgt $s, $t, C`     | `slt $at, $t, $s`<br />`bne $at, $zero, C`|
| branch if less than | `blt $s, $t, C`     | `slt $at, $s, $t`<br />`bne $at, $zero, C`|
| branch if greater than<br/>or equal | `bge $s, $t, C`     | `slt $at, $s, $t`<br />`beq $at, $zero, C`|
| branch if less than<br/>or equal | `ble $s, $t, C`     | `slt $at, $t, $s`<br />`beq $at, $zero, C`|
| branch if greater than<br/>unsigned | `bgtu $s, $t, C`    | `sltu $at, $t, $s`<br />`bne $at, $zero, C`|
| branch if zero | `beqz $s, C`        | `beq $s, $zero, C`|
| branch if equal to immediate | `beq $t, V, C` | `ori $at, $zero, V`<br />`beq $t, $at, C`|
| branch if not equal to immediate | `bne $t, V, C` | `ori $at, $zero, V`<br />`bne $t, $at, C`|


### Logical and Arithmetic Shifts

The arithmetic shifts are signed, logical is unsigned. `sll` and `srl` pad the displaced bits with 0's, and `sra` pad the displaced bits with the most significant bits to retain the sign in two's complement notation. Note that logical shift left isn't necessary because it would be the same as arithmetic shift left.

| Name                   | Syntax            | Operand                                                             | Sign                                                                                   |
|------------------------|-------------------|---------------------------------------------------------------------|-------------------------------|
| Shift Left Logical     | `sll $d, $s, shft`  |  Shifts `$s` left by `<shft>` bits;  <br/> 'Multiplies' value of `$s` by 2^shft | Unsigned                                                                                                     |
| Shift Right Logical    | `srl $d, $s, shft`  |  Shifts `$s` left by `<shft>` bits;  <br/> 'Divides' value of `$s` by 2^shft    |  Unsigned                                                                              |
| Shift Right Arithmetic | `sra $d, $s, $shft `|  Shifts `$s` left by `<shft>` bits; <br/>  'Divides' value of `$s` by 2^shft    |  Signed |

### Multiplication/Division 

| **Name** | **Assembly syntax** | **Expansion** | **Operation in C** |
|:---------|:--------------------|:--------------|:-------------------|
| Multiplication without overflow | `mul $d, $s, $t`    | `mult $s, $t`<br />`mflo $d` | `d = (s * t) `<br />`d = the product` |
| Quotient | `div $d, $s, $t`    | `div $s, $t`<br />`mflo $d` | `d = s / t`<br />`d = the quotient`        |
| Remainder | `rem $d, $s, $t`    | `div $s, $t`<br />`mfhi $d` | `d = s % t` <br />`d = the remainder`       |


### Directives 

Directives go under .data. For the rest of the directives, see [this reference](http://students.cs.tamu.edu/tanzir/csce350/reference/assembler_dir.html).

| Name    | Function              | Example              | Notes                                                              |
|---------|-----------------------|----------------------|--------------------------------------------------------------------|
| .space  | Reserves len bytes    | array: .space 20     | << would be enough to hold 20 bytes / 4 bytes for a word = 5 words |
| .byte   | Saves a byte          | myByte: .byte 0x33   | Can only save one byte                                             |
| .ascii  | Saves an ASCII string | str: .ascii "hello"  | Does not null terminate the string                                 |
| .asciiz | Saves an ASCII string | str: .asciiz "hello" | Does null terminate the string


### Jumps

| **Name** | **Assembly syntax** | **Expansion** | **Operation in C** |
|:---------|:--------------------|:--------------|:-------------------|
| jump register and link to ra | `jalr $s`        | `jalr $s, $ra` | `ra = PC + 4; goto s;` |


### No-operations

| **Name** | **Assembly syntax** | **Expansion** | **Operation in C** |
|:---------|:--------------------|:--------------|:-------------------|
| nop      | `nop`               | `sll $zero, $zero, 0` | `{}`               |



In fact, every MIPS instruction that has `$zero` as its destination and doesn't touch memory, access I/O system, and/or call a trap, can be treated as a `nop`; but using `sll $zero, $zero, 0` is the most convenient because it's byte code is all-zeroes `0x00000000`.

This just does nothing basically and tells the compiler there's no instructions that cycle

---

## Machine Code
#### Instruction addresses
* Instructions for a program each take 4 bytes
* However, some instructions are pseudo operators, which maps to more than one real instruction
* Additionally, some instructions (immediate structions which can have 16 bit or 32 bit arguments) can be different lengths
* Generally all instructions will have 3 addresses associated iwth them

#### $pc register
* `$pc` controls program floor and points to the instruction to execute

## Translating assembly into machine code 


### Instruction formats
There are 3 types of formats: R-type, I-Type, and J-type instructions that are each encoded in different ways.

| Type               | Used when                                                              | Example                  |
|--------------------|------------------------------------------------------------------------|--------------------------|
| R-type (register)  | Input values to the ALU come from two registers                        | `add rd, rs, rt`         |
| I-type (immediate) | Input values to the ALU come from one register and one immediate value | `addi rd, rs, immediate` |
| J-type (jump)      | A jump needs to be performed to a label                                | `j label`                |

Each instruction is 32 bits and each type has a different structure:



#### R-Type

The R-Type format is used in arithmetic instructions using three registers.

|                                                                                  | opcode                                                                                       | rs                    | rt                     | rd                    | shift (shamt)                                           | funct                                                                                                       |
|----------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------|-----------------------|------------------------|-----------------------|---------------------------------------------------------|-------------------------------------------------------------------------------------------------------------|
| **Size**                                                                            | `6 bits`                                                                                     | `5 bits`              | `5 bits`               | `5 bits`              | `5 bits`                                                | `6 bits`                                                                                                    |
| **Description**                                                                      |  Machine code representation of the pneumonic   <br> (this is 0 for all R-types) | First source register | Second source register | Destination register  |  Shift amount   <br> (only used for shift instructions) |  Contains the code to determine the instructions to differentiate between mnemonics with identical opcodes  |
|   **Example:** Decode `0x012F4020`  = `000000 01001 01111 01000 00000 100000` | `000000` = `0x00`                                                                            | `01001 = reg 9 = $t1` | `01111 = reg 15 = $t7` | `01000 = reg 8 = $t0` | `00000` (no shift)       | `100000 = 0010 0000 = 0x20 = add`                                                                           |
In the example, the answer is `add $t0 $t1 $t7 ` or alternatively, `add $8 $9 $15`. (See "How to decode a MIPS instruction" below for a more detailed explanation).

#### I-Type
The I-Type format is used in load, store, branch, and immediate instructions.

|             | opcode                                                     | rs              | rt              | immediate                                    |
|-------------|------------------------------------------------------------|-----------------|-----------------|----------------------------------------------|
| **Size**        | `6 bits`                                                   | `5 bits`        | `5 bits`        | `16 bits`                                    |
| **Description** |  Machine code representation of the instruction pneumonic  | Source register | Target register |  Immediate value   <br>The offset or address |

####J-Type
The J-Type is used in `j` (jump), and `jr`. 

|             | opcode                                                     | immediate                                                                                                                                                                         |
|-------------|------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Size**        | `6 bits`                                                   | `26 bits`                                                                                                                                                                         |
| **Description** |  Machine code representation of the instruction pneumonic  | Word address (not offset) of the destination (the two least significant and four most significant bits are removed, assumed to be the same as the current instruction's address). |


### How to Decode a MIPS Instruction
Given a 32 bit hex number (for example, `0x012F4020`) the procedure is:

1. Translate it into binary (example: `000000010010111101000 00000100000`)
2. Find the opcode (first 6 bits), and determine if it is an I, J, or R type using [a reference](https://s3.studylib.net/store/data/005881474_1-b7b7efa41df3896b621102d255d01db0.png) (example: opcode is 0, so R-type)
3. Group the binary digits accordingly into the bit structure of the type (in example: R-type so 6 bits, 5 bits, 5 bits, 5 bits, 5 bits, 6 bits (`000000 01001 01111 01000 00000 100000`)
4. Decode each attribute from binary to get the instruction (see R-type chart)