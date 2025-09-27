# Day 2 – Timing Libraries, Synthesis Approaches & Flip-Flop Coding

Day 2 of the RTL workshop focuses on three main aspects:  
- Understanding the **.lib timing library** used in SKY130 PDK  
- Comparing **hierarchical vs. flat synthesis flows**  
- Exploring **efficient coding practices for flip-flops** in RTL  

---

## Contents
- Timing Libraries  
- SKY130 PDK Overview  
- Decoding `tt_025C_1v80`  
- Opening and Reading the `.lib` File  
- Hierarchical vs. Flattened Synthesis  
- Flip-Flop Coding Styles  
- Simulation & Synthesis Workflow  
- Summary  

---

## Timing Libraries

### SKY130 PDK Overview
The **SKY130 PDK** is an open-source Process Design Kit (130nm CMOS technology from SkyWater).  
It provides models and libraries for IC design, covering:  
- Timing  
- Power  
- Process variation  

### Decoding `tt_025C_1v80`
- **tt** → Typical process corner  
- **025C** → Temperature = 25°C  
- **1v80** → Core voltage = 1.8V  

This naming style defines the specific **PVT (Process, Voltage, Temperature)** conditions modeled in the library.  

### Opening and Reading `.lib` File
Install a text editor:  
```bash
sudo apt install gedit
```

Open the library file:  
```bash
gedit sky130_fd_sc_hd__tt_025C_1v80.lib
```

---

## Hierarchical vs. Flattened Synthesis

### Hierarchical Synthesis
- **Definition** → Keeps module hierarchy from RTL, synthesizes each block separately.  
- **How it works** → Tools like Yosys use the `hierarchy` command to manage modules.  
- **Pros**:  
  - Faster for large designs  
  - Easier debugging due to preserved structure  
  - Modular integration possible  
- **Cons**:  
  - Limited cross-module optimization  
  - Extra effort in reporting  

---

### Flattened Synthesis
- **Definition** → Merges all modules into a single-level netlist.  
- **How it works** → Using `flatten` in Yosys collapses hierarchy.  
- **Pros**:  
  - Allows cross-module optimizations  
  - Produces one unified netlist  
- **Cons**:  
  - Slower for large designs  
  - Loss of hierarchy → harder to debug  
  - Can increase memory/netlist complexity  

---

### Key Differences

| Aspect            | Hierarchical Synthesis | Flattened Synthesis |
|-------------------|-------------------------|---------------------|
| Hierarchy         | Preserved              | Collapsed           |
| Optimization Scope| Module-level           | Entire design       |
| Runtime           | Faster for large designs | Slower for large designs |
| Debugging         | Easier (traces back to RTL) | More difficult |
| Output Structure  | Modular netlist         | Single flat netlist |
| Typical Use Case  | Modularity, analysis    | Aggressive optimization |

---

## Flip-Flop Coding Styles

Flip-flops are the backbone of sequential circuits.  
Below are efficient coding examples in Verilog:  

### Asynchronous Reset DFF
```verilog
module dff_asyncres (input clk, input async_reset, input d, output reg q);
  always @ (posedge clk, posedge async_reset)
    if (async_reset)
      q <= 1'b0;
    else
      q <= d;
endmodule
```
- Reset immediately clears `q` to 0.  
- Captures `d` on clock edge when reset = 0.  

---

### Asynchronous Set DFF
```verilog
module dff_async_set (input clk, input async_set, input d, output reg q);
  always @ (posedge clk, posedge async_set)
    if (async_set)
      q <= 1'b1;
    else
      q <= d;
endmodule
```
- Overrides clock, immediately setting `q` to 1.  

---

### Synchronous Reset DFF
```verilog
module dff_syncres (input clk, input sync_reset, input d, output reg q);
  always @ (posedge clk)
    if (sync_reset)
      q <= 1'b0;
    else
      q <= d;
endmodule
```
- Reset is checked **only on clock edge**.  

---

## Simulation & Synthesis Workflow

### Simulation with Icarus Verilog
Compile design + testbench:  
```bash
iverilog dff_asyncres.v tb_dff_asyncres.v
```
Run simulation:  
```bash
./a.out
```
View waveforms:  
```bash
gtkwave tb_dff_asyncres.vcd
```

---

### Synthesis with Yosys
Start yosys:  
```bash
yosys
```

Read standard cell library:  
```bash
read_liberty -lib /path/to/sky130_fd_sc_hd__tt_025C_1v80.lib
```

Read Verilog design:  
```bash
read_verilog /path/to/dff_asyncres.v
```

Run synthesis:  
```bash
synth -top dff_asyncres
```

Map flip-flops:  
```bash
dfflibmap -liberty /path/to/sky130_fd_sc_hd__tt_025C_1v80.lib
```

Technology mapping:  
```bash
abc -liberty /path/to/sky130_fd_sc_hd__tt_025C_1v80.lib
```

View gate-level netlist:  
```bash
show
```

---

## Summary

- Explored SKY130 timing libraries and `.lib` file structure.  
- Compared hierarchical vs. flat synthesis flows.  
- Studied different flip-flop coding techniques (async/sync reset, async set).  
- Simulated designs using **iverilog** and performed synthesis with **Yosys**.  

