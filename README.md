# MIPS Notes

Credit to [MIPT-ILab](https://github.com/MIPT-ILab/mipt-mips/wiki/MIPS-pseudo-instructions/_edit) and [this page](http://pages.cs.wisc.edu/~markhill/cs354/Fall2008/notes/MAL.instructions.html) for the MIPS docs used as a starter for this page.

These are basically the docs from these 2 sites along with some clarification notes I'm making to study for my CE 12 (Assembly Language) final but I hope they're helpful to someone also struggling with MIPS.

## Load and Store

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
      

## Branches

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

### Multiplication/Division ###

| **Name** | **Assembly syntax** | **Expansion** | **Operation in C** |
|:---------|:--------------------|:--------------|:-------------------|
| multiplicate<br/>and return 32 bits | `mul $d, $s, $t`    | `mult $s, $t`<br />`mflo $d` | `d = (s * t) & 0xFFFFFFFF` |
| quotient | `div $d, $s, $t`    | `div $s, $t`<br />`mflo $d` | `d = s / t`        |
| remainder | `rem $d, $s, $t`    | `div $s, $t`<br />`mfhi $d` | `d = s % t`        |

### Jumps ###

| **Name** | **Assembly syntax** | **Expansion** | **Operation in C** |
|:---------|:--------------------|:--------------|:-------------------|
| jump register and link to ra | `jalr $s`        | `jalr $s, $ra` | `ra = PC + 4; goto s;` |

### Logical operations ###

| **Name** | **Assembly syntax** | **Expansion** | **Operation in C** |
|:---------|:--------------------|:--------------|:-------------------|
| not      | `not $t, $s`        | `nor $t, $s, $zero` | `t = ~s`           |

### No-operations ###

| **Name** | **Assembly syntax** | **Expansion** | **Operation in C** |
|:---------|:--------------------|:--------------|:-------------------|
| nop      | `nop`               | `sll $zero, $zero, 0` | `{}`               |



In fact, every MIPS instruction that has `$zero` as its destination and doesn't touch memory, access I/O system, and/or call a trap, can be treated as a `nop`; but using `sll $zero, $zero, 0` is the most convenient because it's byte code is all-zeroes `0x00000000`.

This just does nothing basically and tells the compiler there's no instructions that cycle

---
