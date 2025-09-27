# Day 3 – Optimization of Combinational & Sequential Circuits

This day focused on **design optimization** — making circuits leaner, faster, and more power-efficient.  
Both **combinational** and **sequential** techniques were explored, with hands-on Verilog labs.  

---

## Agenda
- Retiming  
- Cloning  
- Constant Propagation  
- State Optimization  
- Practical Labs (1–6)  

---

## Retiming

Shifts flip-flops around in the design **without altering functionality**.  
- Circuit modeled as a graph.  
- Registers moved across combinational paths.  
- Goal → balance path delays, shorten clock period, improve power.  

---

## Cloning

Used when one driver is overloaded.  
- Duplicate the logic cell.  
- Split its fanout to reduce wire length and load.  
- Improves timing, sometimes saves power too.  

---

## Constant Propagation

**Optimization trick**: replace variables with constants directly in equations.  
- Reduces logic size.  
- Improves speed.  
- Cuts unnecessary gates.  

---

## State Optimization

FSMs (finite state machines) can often be simplified.  
- Merge equivalent states → fewer total states.  
- Apply efficient state encodings.  
- Minimize equations with Boolean algebra.  
- Clock gating and other techniques reduce dynamic power.  

---

## Labs

### Lab 5 – DFF with Constant 1
```verilog
module dff_const1(input clk, input reset, output reg q);
always @(posedge clk, posedge reset)
begin
	if(reset)
		q <= 1'b0;
	else
		q <= 1'b1;
end
endmodule
```
- Asynchronous reset drives `q` to 0.  
- On clock edge → always loads constant `1`.  

---

### Lab 6 – DFF Always 1
```verilog
module dff_const2(input clk, input reset, output reg q);
always @(posedge clk, posedge reset)
begin
	if(reset)
		q <= 1'b1;
	else
		q <= 1'b1;
end
endmodule
```
- Regardless of reset or clock → `q` is always `1`.  

---

### Lab 1 – Conditional Check
```verilog
module opt_check (input a , input b , output y);
	assign y = a?b:0;
endmodule
```
- If `a = 1` → `y = b`  
- If `a = 0` → `y = 0`  
- With synthesis cleanup (`opt_clean -purge`), redundant logic is dropped.  

---

### Lab 2 – Simple MUX
```verilog
module opt_check2 (input a , input b , output y);
	assign y = a?1:b;
endmodule
```
- Works like a multiplexer:  
  - `a = 1` → output is 1  
  - `a = 0` → output is `b`  

---

### Lab 3 – Another MUX Form
```verilog
module opt_check2 (input a , input b , output y);
	assign y = a?1:b;
endmodule
```
- Functionally same as Lab 2 → highlights optimization recognition in synthesis.  

---

### Lab 4 – Nested Logic
```verilog
module opt_check4 (input a , input b , input c , output y);
 assign y = a?(b?(a & c ):c):(!c);
endmodule
```
- After simplification →  
  ```
  y = a ? c : !c
  ```  

---

## Wrap-Up

- **Retiming** → moved registers for balanced timing.  
- **Cloning** → duplicated cells to reduce load.  
- **Constant Propagation** → replaced variables with constants for simpler logic.  
- **State Optimization** → reduced FSM states, smarter encoding.  
- **Labs** → covered DFF behavior (Labs 5 & 6) first, then combinational simplifications (Labs 1–4).  

