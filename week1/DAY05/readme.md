
# Day 5 – Optimization in Verilog Synthesis

This session covers **optimization techniques in Verilog synthesis**, focusing on proper use of `if-else` statements, `for` loops, generate blocks, and avoiding inferred latches. Practical labs are included to solidify concepts.

---

## Contents

1. If-Else Statements in Verilog  
2. Latch Inference in Verilog  
3. Labs: If-Else and Case  
4. For Loops  
5. Generate Blocks  
6. Ripple Carry Adder (RCA)  
7. Labs with Loops and Generate  
8. Summary  

---

## 1. If-Else Statements

Used for conditional execution inside procedural blocks (`always`, `initial`, tasks, functions).

**Syntax:**

```verilog
if (condition) begin
    // executed if condition is true
end else begin
    // executed if condition is false
end
````

* `condition`: evaluates to true (non-zero) or false (0)
* `begin ... end`: groups multiple statements
* `else` is optional

**Nested Example:**

```verilog
if (cond1) begin
    // cond1 true
end else if (cond2) begin
    // cond2 true
end else begin
    // neither true
end
```

---

## 2. Inferred Latches

A latch is inferred if **some execution paths do not assign a value** to a register in combinational logic.

**Problem Example:**

```verilog
module ex (
    input wire a, b, sel,
    output reg y
);
always @(a, b, sel) begin
    if (sel)
        y = a; // y undefined when sel==0
end
endmodule
```

**Solution – cover all cases:**

```verilog
module ex (
    input wire a, b, sel,
    output reg y
);
always @(a, b, sel) begin
    case(sel)
        1'b1: y = a;
        default: y = 1'b0; // default assignment
    endcase
end
endmodule
```

---

## 3. Labs – If-Else and Case

### Lab 1 – Incomplete If

```verilog
module incomp_if (input i0, i1, i2, output reg y);
always @(*) begin
    if (i0)
        y <= i1;
end
endmodule
```

### Lab 2 – Synthesis Result

Latches inferred due to missing `else`.

### Lab 3 – Nested If-Else

```verilog
module incomp_if2 (input i0, i1, i2, i3, output reg y);
always @(*) begin
    if (i0)
        y <= i1;
    else if (i2)
        y <= i3;
end
endmodule
```

### Lab 4 – Synthesis Result

Latch may still appear; incomplete branches.

### Lab 5 – Complete Case

```verilog
module comp_case (input i0,i1,i2, input [1:0] sel, output reg y);
always @(*) begin
    case(sel)
        2'b00: y = i0;
        2'b01: y = i1;
        default: y = i2;
    endcase
end
endmodule
```

### Lab 6 – Synthesis Result

No latch inferred.

### Lab 7 – Incomplete Case

```verilog
module bad_case (
    input i0,i1,i2,i3,
    input [1:0] sel,
    output reg y
);
always @(*) begin
    case(sel)
        2'b00: y = i0;
        2'b01: y = i1;
        2'b10: y = i2;
        2'b1?: y = i3; // wildcard can cause issues
    endcase
end
endmodule
```

### Lab 8 – Partial Assignments

Some branches do not assign all outputs → latch inferred.

---

## 4. For Loops

Used to replicate logic inside procedural blocks. Synthesizable only if loop bounds are constant at compile time.

```verilog
for (init; condition; increment) begin
    // repeated statements
end
```

**Example – 4-to-1 MUX:**

```verilog
module mux_4to1_for_loop(input [3:0] data, input [1:0] sel, output reg y);
integer i;
always @(data, sel) begin
    y = 1'b0;
    for (i=0; i<4; i=i+1) begin
        if (i==sel)
            y = data[i];
    end
end
endmodule
```

---

## 5. Generate Blocks

Used to create multiple instances or logic at compile time.

```verilog
genvar i;
generate
    for (i=0; i<4; i=i+1) begin : gen_loop
        and_gate and_inst(.a(in[i]), .b(in[i+1]), .y(out[i]));
    end
endgenerate
```

---

## 6. Ripple Carry Adder (RCA)

Adds binary numbers using chained full adders. Carry-out from one stage connects to the next stage.

**RCA Example:**

```verilog
module rca(input [7:0] num1, num2, output [8:0] sum);
wire [7:0] int_sum, int_co;
genvar i;
generate
    for (i=1; i<8; i=i+1) begin
        fa u_fa(.a(num1[i]), .b(num2[i]), .c(int_co[i-1]), .co(int_co[i]), .sum(int_sum[i]));
    end
endgenerate
fa u_fa0(.a(num1[0]), .b(num2[0]), .c(1'b0), .co(int_co[0]), .sum(int_sum[0]));
assign sum[7:0] = int_sum;
assign sum[8] = int_co[7];
endmodule

module fa(input a,b,c, output co,sum);
assign {co,sum} = a+b+c;
endmodule
```

---

## 7. Labs on Loops and Generate

* Lab 9 – 4-to-1 MUX with `for`
* Lab 10 – 8-to-1 Demux with `case`
* Lab 11 – 8-to-1 Demux with `for`
* Lab 12 – 8-bit RCA with generate

---

## 8. Summary

* Complete all conditional branches to prevent unintended latches.
* For loops and generate blocks allow scalable, efficient RTL coding.
* Always assign outputs in **every possible path** for combinational blocks.
* Hands-on labs reinforce correct vs incorrect synthesis behaviors.

```

