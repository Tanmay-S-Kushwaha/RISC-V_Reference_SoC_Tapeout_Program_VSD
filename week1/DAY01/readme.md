# Day 1 – RTL Design & Synthesis with Verilog

Day 1 of the RTL Design & Synthesis Workshop introduces the basics of Verilog RTL coding, open-source simulation using **iverilog**, and digital synthesis with **Yosys**. Concepts are reinforced through hands-on practice, making it easier to connect theory with real implementation.  

---

## Outline
- Understanding Simulator, Design, and Testbench  
- Setting up iverilog and GTKWave  
- Practical Lab: 2x1 Multiplexer Simulation  
- RTL Code Walkthrough  
- Introduction to Yosys and Cell Libraries  
- First Synthesis Flow with Yosys  
- Recap  

---

## Simulator, Design, and Testbench

- **Simulator**: Software that mimics hardware behavior by applying test vectors and checking output responses.  
- **Design**: The Verilog RTL code describing the intended digital logic.  
- **Testbench**: A wrapper around the design that drives input sequences and verifies the output.  
<img width="1799" height="834" alt="Screenshot from 2025-09-27 11-55-37" src="https://github.com/user-attachments/assets/036a7caf-092a-479c-87b2-9ac9ddea662e" />

---

## Preparing the Simulation Environment

Icarus Verilog (**iverilog**) serves as the simulator, while **GTKWave** is used for viewing waveforms.  
The typical flow involves:  
1. Compile design + testbench.  
2. Run simulation.  
3. Analyze generated `.vcd` file in GTKWave.  

---

## Lab Exercise – 2:1 Multiplexer

### Setup
```bash
git clone https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop.git
cd sky130RTLDesignAndSynthesisWorkshop/verilog_files
```
### Compilation And Simulation
```bash
iverilog good_mux.v tb_good_mux.v
./a.out
gtkwave tb_good_mux.vcd
```
<img width="993" height="58" alt="Screenshot from 2025-09-27 12-35-52" src="https://github.com/user-attachments/assets/def8130b-6f67-4a61-8c34-97e5d3201a2a" />

GTKWave opens to inspect the simulated waveforms.
<img width="990" height="534" alt="Screenshot from 2025-09-27 12-34-59" src="https://github.com/user-attachments/assets/6863e260-fe06-4245-b18b-0ee19a4726a4" />

### RTL Design 2-to-1 MUX
```verilog
module good_mux (input i0, input i1, input sel, output reg y);
always @ (*)
begin
    if(sel)
        y <= i1;
    else 
        y <= i0;
end
endmodule
```
- **Inputs**: i0, i1 (data lines), sel (select line) 
- **Output**: y 
- **Logic**: Output follows i1 if sel = 1, else follows i0.

## Yosys & Technology Libraries

Yosys is an open-source toolchain that translates RTL into gate-level logic.  
It provides the following capabilities:  

- **Synthesis** – Convert RTL into logic gates  
- **Optimization** – Improve performance or reduce area  
- **Technology mapping** – Match logic to available standard cells  
- **Visualization** – Inspect synthesized circuits  

### Why multiple cell versions exist in libraries?

- Different speed–power tradeoffs  
- Area optimization for compact layouts  
- Drive strength variations for handling different loads  
- Specialized cells for noise or performance improvements  

---

## Synthesis Flow with Yosys

1. **Launch yosys**
   ```bash
   yosys
   ```
2. **Load Standard Cell Library**
```bash
read_liberty -lib /path/to/sky130_fd_sc_hd__tt_025C_1v80.lib
```

3. **Import Verilog Source**
```bash
read_verilog good_mux.v
```

4. **Run Synthesis**
```bash
synth -top good_mux
```

5. **Technology Mapping**
```bash
abc -liberty /path/to/sky130_fd_sc_hd__tt_025C_1v80.lib
```

6. **View Gate-Level Schematic**
```bash
show
```
<img width="300" height="319" alt="image" src="https://github.com/user-attachments/assets/e47288dd-9a2a-4555-a342-fa38ab36e660" />

---

## Recap

- Clarified simulator, RTL design, and testbench roles.  
- Ran first Verilog simulation using iverilog and GTKWave.  
- Studied RTL implementation of a 2x1 multiplexer.  
- Gained exposure to Yosys and standard cell libraries.  
- Performed synthesis and explored gate-level representation.
