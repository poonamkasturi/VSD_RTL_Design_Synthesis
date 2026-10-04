
### 🔰 DAY 3: COMBINATIONAL and SEQUENTIAL OPTIMIZATION -----

#### Topics Explored
 - [3.1 Introduction to Optimizations](#31-introduction-to-optimizations)
 - [3.2 Combinational Logic Optimizations](#32-combinational-logic-optimizations)
    - [Constant Propagation](#constant-propagation)
    - [Boolean Logic Optimization](#boolean-logic-optimization)
 - [3.3 Sequential Logic Optimizations](#33-sequential-logic-optimizations)
    - [Sequential Constant Propagation](#sequential-constant-propagation)
    - [Advanced Techniques](#advanced-techniques)
 - [3.4 Logic Optimizations with Yosys](#34-logic-optimizations-with-yosys)
    - [Combinational Logic Optimizations](#combinational-logic-optimizations)
    - [Sequential Logic Optimizations](#sequential-logic-optimizations)
 - [3.5 Sequential Optimizations for Unused Outputs](#35-sequential-optimizations-for-unused-outputs)


### 3.1 Introduction to Logic Optimization - Overview
______________________________________

Logic optimization is the process of deriving an equivalent representation of a given logic circuit under specified constraints.  
- **Purpose:** To ensure the design meets timing, area, and power requirements while improving simulation efficiency.  
- **Method:** The design is iteratively transformed until it satisfies the desired specifications.  
- **Outcome:** Each gate corresponds to one or more statements in the compiled code; by reducing redundant logic, optimization decreases program size and execution time.  
---

### 3.2 Combinational Logic Optimizations:
____________________________________________________
#### Common techniques used for optimizing combinational logic:
* Constant Propagation 
	* Direct Optimization technique
* Boolean Logic Optimization.
	* Karnaugh map
	* Quine Mckluskey

#### --- Constant Propagation ---

Constant propagation is the process of substituting the values of known constants directly into expressions.  
- It eliminates redundant assignments where values are copied from one variable to another.  
- In logic circuits, a constant input is propagated to the output, resulting in a minimized expression.  
- This optimization reduces program size and execution time, while improving simulation efficiency.  

### Example
Below image shows how a constant input propagates through the circuit to simplify the output expression:
<img width="400" height="200" alt="image" src="https://github.com/user-attachments/assets/e0e51e26-1ed0-43f2-9987-2167247e632a" />


#### --- Boolean Logic Optimization ---
In Boolean algebra, optimization is the process of simplifying a complex expression into an equivalent but more efficient form.  
- **Goal:** To produce the same logical results as the original expression with fewer operations.  
- **Method:** Apply Boolean algebra rules and theorems (e.g., distributive, associative, absorption laws) to minimize the logic.  
- **Benefit:** Reduced circuit complexity, lower area and power usage, and improved simulation efficiency.  

### Example
Below image illustrates the optimization of a given Boolean logic expression into a simpler equivalent form:

<img width="500" height="250" alt="Boolean_logic_optimization" src="https://github.com/user-attachments/assets/fe36ec7f-6765-47c1-80e6-1b7a83a52b78" />

---

### 3.3 Sequential Logic Optimizations
____________________________________________

Techniques used for optimizimg the sequential logic :
* Basic Tecnique
	* Sequential Constant Propagation
* Advanced Technique
	* State Optimization
	* Retiming
	* Sequential Logic cloning(Floorplan aware synthesis)

#### --- Sequential Constant Propagation --- NEEDS TO BE CHECKED >> CONTENT NOT CORRECT

In code below if ```set = 1 ``` then ``` Q = 1 ``` and when ``` set = 0 , clk = 1 ``` then ``` Q = 0 ```. Thus output is following input 'd' at clock edge. So the output Q can not be optimized, thus sequential constant can not propagate. 
	If a constant connected to the input of a D Flop makes the Q output always constant.. 
                then the flop can be optimized (replaced by the constant propogated)

<img width="400" height="200" alt="Sequential_Constant_propagation_opt" src="https://github.com/user-attachments/assets/38699711-bb3a-4c99-9d18-750fcccab4d5" />


  But if a constant connected to the input of a D Flop does not makes its Q output a constant value
                then that flop or logic can not be optimized. The Flop needs to be retained

<img width="350" height="170" alt="Sequential_Constant_NO_propagation_" src="https://github.com/user-attachments/assets/d0a23e40-b98f-4880-8f9e-332e4ccd5424" />

**NOTE** :
* A constant connected to the input of a flop does not mean that we can always optimize its output.
* Every flop with D input tied to '0' is not a sequential constant.
* For flop to become sequential constant , the Q output pin should always take a constant value.

	
#### --- Advanced Techniques ---
1. State Optimization : it is used  for Optimization of unused states.
2. Sequential Logic cloning : It is done when we are using physical aware synthesis.
3. Retiming : It is done to reduce combinational delay and to improve the performance of the circuit.
	
	
### 3.4 Logic optimizations with Yosys
#### --- Combinational Logic Optimizations ---

```
yosys> read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
yosys> read_verilog <verilog_file_name>       : e.g. opt_check.v
yosys> synth -top <top_module_name)     : e.g  opt_check
yosys> opt_clean -purge 				: command to do all optimizations
yosys> abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
yosys> show		
```
##### Yosys Optimization Pass: `opt_clean -purge`

##### `opt_clean`
- Identifies **unused wires and cells** in the design and removes them.
- Other passes may remove cells but leave wires, or reconnect wires but leave old cells.  
- `opt_clean` cleans up after such passes to ensure a minimal netlist.
- Operates only on **completely selected modules without processes**.

##### `purge` option
- Extends the cleanup to remove **internal nets** even if they have a public name
- Collapses/Removes aggressively redundant intermediates (buffers, unused nets or cells)
- Ensures only essential logic remains
- Thus produces a minimal netlist that's easier to simulate and synthesize

##### Significance
- Reduces Circuit Size and Simulation Time while maintaining the logic of the design


## Simulation and Synthesis with `opt_clean` command
### *D3Lab6 - Optimization - opt_check.v -- 2 input AND gate* ********************

```verilog
module opt_check (input a , input b , output y);
	assign y = a?b:0;
endmodule
```

![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_3/Assets_D3/Screenshot%202026-10-01%20131944%20opt%20check.png)
![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_3/Assets_D3/Screenshot%202026-10-01%20132712%20D3Lab1%202input_AND%20synth.png)

### *D3Lab7 - Optimization - opt_check2.v -- 2 input OR gate* ********************
```verilog
module opt_check2 (input a , input b , output y);
	assign y = a?1:b;
endmodule
```
![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_3/Assets_D3/Screenshot%202026-10-01%20133951%20D3Lab1%202input_AND%20gtkwave.png)
![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_3/Assets_D3/Screenshot%202026-10-01%20134229%20D3Lab2%202input_OR%20synth.png)

### *D3Lab8 - Optimization - opt_check3.v -- 3 input AND gate* ********************
```verilog
module opt_check3 (input a , input b, input c , output y);
	assign y = a?(c?b:0):0;
endmodule
```
![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_3/Assets_D3/Screenshot%202026-10-01%20134832%20D3Lab3%203input_AND%20gtkwave.png)
![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_3/Assets_D3/Screenshot%202026-10-01%20135204%20D3Lab3%203input_AND%20synth.png)

### *D3Lab9 - Optimization - opt_check4.v -- Logic optimized to 2 input XNOR gate* ********************
```verilog
module opt_check4 (input a , input b , input c , output y);
 assign y = a?(b?(a & c ):c):(!c);
 endmodule
```
<img width="650" height="450" alt="Comb_yosys_opt" src="https://github.com/user-attachments/assets/7154dec8-ecdc-4343-a37d-36f3c6ddd4fa" />

![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_3/Assets_D3/Screenshot%202026-10-01%20140852%20D3Lab4%20XNOR%20gtkwave.png)
![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_3/Assets_D3/Screenshot%202026-10-01%20141157%20D3Lab4%20XNOR%20synth.png)

#### --- Sequential Logic Optimizations ---
To understand optimization with yosys, lets take an exmaple of dff_const5.v :
In the below circuit we can see that the circuit obatained after synthesis and optimization is similar to what we expected as per RTL code. Thus in this case no optimization is possible. 

<img width="650" height="400" alt="Sequential_yosys_opt" src="https://github.com/user-attachments/assets/b8e7f12a-78de-4786-8db1-b421cb6b1e60" />

### *D3Lab10 -*
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
![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_3/Assets_D3/Screenshot%202026-10-01%20143021%20D3Lab5%20dff_const1%20gtkwave.png)
![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_3/Assets_D3/Screenshot%202026-10-01%20143525%20D3Lab5%20dff_const1%20synth.png)

### *D3Lab11 -*
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
![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_3/Assets_D3/Screenshot%202026-10-01%20144526%20D3Lab6%20dff_const2%20gtkwave.png)
![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_3/Assets_D3/Screenshot%202026-10-01%20144938%20%20D3Lab6%20dff_const2%20synth.png)


## 3.5 Sequential - Optimizations for Unused Outputs:

### *D3Lab12 - Optimization  counter_opt.v : Optimization of a 3-Bit Counter* **************  

```verilog
module counter_opt (input clk , input reset , output q);
reg [2:0] count;
assign q = count[0];

always @(posedge clk ,posedge reset)
begin
	if(reset)
		count <= 3'b000;
	else
		count <= count + 1;
end

endmodule
```

At first glance, the RTL code appears to describe a **3-bit counter**, so one would expect **three flip-flops** after synthesis. 

### Detailed Observation from Simulation waveforms
- After reset, the value of `count` is `000`.  
- On each positive clock edge, `count` increments.  
- The output `q` simply follows `count[0]` (the least significant bit).

![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_3/Assets_D3/Screenshot_2026-10-04_17-46-57%20counter_opt%20gtk.png)

### Synthesized Schematic Without Optimization
- The synthesized design reflects the expected **3-bit counter** as captured in the RTL description.  
- All three flip-flops are present, even though only `count[0]` is functionally used.

![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_3/Assets_D3/Screenshot_2026-10-04_17-50-19%20counter_opt%20synth%20wo_purge.png)

### Synthesized Schematic After Optimization
- The synthesis tool removes **unused outputs** while preserving functionality.  
- Only **one flip-flop** is inferred, corresponding to `count[0]`.  
- The input to this flip-flop is the **complement of its output**, effectively creating a **toggle flip-flop**.

![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_3/Assets_D3/Screenshot_2026-10-04_17-50-19%20counter_opt%20synth%20w_purge.png)

### Key Takeaway - on executing the `opt_clean -purge` command.
Optimization ensures that:
- **Redundant logic** is eliminated.  
- **Functionality** of the design is maintained

---
---

<img width="600" height="400" alt="Sequential_unused_output_opt" src="https://github.com/user-attachments/assets/335e1a66-5b3c-447e-80c7-ab7c370373cd" />

### *D3Lab13 - counter_opt2.v*  
```verilog
module counter_opt (input clk , input reset , output q);
reg [2:0] count;
assign q = (count[2:0] == 3'b100);

always @(posedge clk ,posedge reset)
begin
	if(reset)
		count <= 3'b000;
	else
		count <= count + 1;
end

endmodule
```
![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_3/Assets_D3/Screenshot_2026-10-04_17-46-57%20counter_opt2%20gtk.png)
![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_3/Assets_D3/Screenshot_2026-10-04_17-50-19%20counter_opt2%20synth%20wo_purge.png)
![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_3/Assets_D3/Screenshot_2026-10-04_17-50-19%20counter_opt2%20synth%20w_purge.png)
