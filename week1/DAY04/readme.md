# Day 4 – GLS, Blocking vs. Non-Blocking, and Synthesis-Simulation Mismatch  

The session revolved around **simulation at the gate-level**, pitfalls in RTL coding, and the subtle but critical differences between **blocking** and **non-blocking** assignments.  

---

## Agenda
- Gate-Level Simulation (GLS)  
- Blocking vs. Non-Blocking Assignments  
- Synthesis-Simulation Mismatch  
- Labs (1–7)  

---

## Gate-Level Simulation (GLS)

**What is GLS?**  
Simulation performed on the **synthesized gate-level netlist** instead of RTL.  

**Why important?**  
- Checks if synthesis preserved RTL functionality.  
- Exposes timing issues (setup/hold violations when delays are annotated).  
- Validates scan chains and DFT features.  

**When is it done?**  
- After synthesis.  
- Before physical design.  

**Types of GLS:**  
- *Functional* → logic-only, unit delay.  
- *Timing* → with SDF annotation for realistic delay checks.  

---

## Synthesis-Simulation Mismatch

Mismatch occurs when **RTL simulation results ≠ GLS results**.  

**Common reasons:**  
- Using delays, `initial` blocks, or other non-synthesizable constructs.  
- Missing `else` in combinational always blocks.  
- Wrong sensitivity list.  
- Ambiguous RTL constructs → tools may interpret differently.  

**Key takeaway:** write **synthesizable + unambiguous** RTL.  

---

## Blocking vs. Non-Blocking in Verilog

### Blocking (`=`)  
- Executes **immediately** in sequence.  
- Suitable for **combinational logic**.  
```verilog
always @(*) y = a & b;
```

### Non-Blocking (`<=`)  
- Updates **scheduled**, occur together at the end of timestep.  
- Used for **sequential logic**.  
```verilog
always @(posedge clk) q <= d;
```

### Quick Comparison  

| Aspect              | Blocking (`=`)                 | Non-Blocking (`<=`)              |
|---------------------|--------------------------------|----------------------------------|
| Execution           | Immediate, sequential          | Concurrent, scheduled            |
| Good for            | Combinational always blocks    | Flip-flops, sequential logic     |
| Order of code       | Matters                        | Updates applied together         |
| Hardware inferred   | Gates                          | Registers                        |

---

## Labs  

### Lab 1 – Ternary Operator MUX
```verilog
module ternary_operator_mux (input i0, input i1, input sel, output y);
  assign y = sel ? i1 : i0;
endmodule
```
- Simple 2:1 MUX → output is `i1` if `sel=1`, else `i0`.  

---

### Lab 2 – Yosys Synthesis of MUX  
Follow the regular Yosys flow (`read_liberty`, `read_verilog`, `synth`, `abc`, `show`).  

---

### Lab 3 – GLS of MUX  
```bash
iverilog /path/to/primitives.v /path/to/sky130_fd_sc_hd.v ternary_operator_mux.v testbench.v
```
- Ran simulation and verified against RTL.  

---

### Lab 4 – Bad MUX Example  
```verilog
module bad_mux (input i0, input i1, input sel, output reg y);
  always @ (sel) begin
    if (sel)
      y <= i1;
    else 
      y <= i0;
  end
endmodule
```
**Problems:**  
- Sensitivity list incomplete.  
- Non-blocking assignment used inside combinational always.  

**Corrected:**  
```verilog
always @(*) begin
  if (sel)
    y = i1;
  else
    y = i0;
end
```

---

### Lab 5 – GLS of Bad MUX  
- Simulated and observe mismatches caused by incorrect coding.  

---

### Lab 6 – Blocking Caveat  
```verilog
module blocking_caveat (input a, input b, input c, output reg d);
  reg x;
  always @(*) begin
    d = x & c;
    x = a | b;
  end
endmodule
```
Problem → `d` uses **old** value of `x`.  

**Correct order:**  
```verilog
always @(*) begin
  x = a | b;
  d = x & c;
end
```

---

### Lab 7 – Synthesis of Blocking Caveat  
Ran synthesis on corrected version and inspect netlist.  

---

## Wrap-Up  

- **GLS** confirms design correctness after synthesis.  
- **Synthesis-simulation mismatch** → avoid non-synthesizable/ambiguous RTL.  
- **Blocking vs. Non-Blocking** →  
  - Blocking for combinational logic.  
  - Non-blocking for sequential logic.  
- Labs highlighted **real pitfalls** like bad sensitivity lists and blocking assignment issues.  

