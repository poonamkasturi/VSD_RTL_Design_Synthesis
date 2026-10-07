## 🔰 DAY 4: GLS, blocking vs non-blocking and Synthesis-Simulation mismatch

-	Topics Explored
	- 4.1. [GLS concepts and flow using iverilog](#GLS-concepts-and-flow-using-iverilog)
	- 4.2. [GLS setup using iverilog ](#GLS-setup-using-iverilog)
		 - **D4Lab16** - GLS  (*ternary_operator_mux.v*)
	- 4.3. [Synthesis-Simulation Mismatch](#Synthesis---Simulation-Mismatch)
 	   * [Missing Sensitivity list](#Missing-Sensitivity-list)
	        * **D4Lab17** - Missing Sensitivity List (*bad_mux.v*) 	
	   * [Blocking and Non-blocking assignments in verilog](#Blocking-and-Non---blocking-assignments-in-verilog)
		    * **D4Lab18** - Caveat in Blocking Statements (*blocking_caveat.v*) 

### 4.1 GLS concepts and flow using iverilog :

The term "gate level" refers to the netlist view of a circuit, usually produced by logic synthesis.

#### Gate Level Simulation (GLS)
- Simulation performed on the **synthesized netlist** of a design is referred to as **Gate-level Simulation (GLS)**.  
- RTL simulation is **pre-synthesis**, while GLS is **post-synthesis**.  
- The netlist view consists of gates and IP models with complete functional and timing behavior.  
- Since the netlist is logically equivalent to the RTL, test bench used for RTL simulation can be reused.

#### Importance of GLS
- Boosts confidence in the correctness of the design implementation.  
- Verifies **dynamic circuit behavior** that static methods cannot capture.  
- Overcomes limitations of static timing analysis.  
- Increasingly used due to:
  - Low-power design challenges  
  - Complex timing checks  
  - Design-for-Test (DFT) insertion at gate level  
  - Power-aware verification requirements  

#### Purpose of GLS
- **Logical correctness verification** after synthesis.  
- **Timing validation** of the design.  
  - For timing checks, GLS must run with **delay annotation** (timing-aware GLS).  


### 4.2 GLS setup using iverilog :
Below image show the inputs to the iverilog tool and output for Gate Level Simulation:

<img width="500" height="200" alt="GLS_Setup" src="https://github.com/user-attachments/assets/a522b120-05ec-457f-ba80-4b93f6cdfe81" />
 
**Gate-Level Verilog Model**
The gate-level Verilog model is one of the inputs to **Icarus Verilog (iverilog)**.  
It is used to inform the simulator about the **standard cell models** referenced in the synthesized netlist.

#### Types of Gate-Level Models
- **Functional Model**  
  - Validates the functionality of the design alone.  
  - Ignores timing information, focusing only on logical correctness.  

- **Timing-Aware Model**  
  - Validates both functionality and timing.  
  - Ensures that the design meets timing requirements in addition to logical correctness.  

### *D4Lab16 - GLS  ternary_operator_mux.v : check similarity of RTL Simulation and GLS* -----------

pwd path :  ~Desktop/VLSI_PK/Sky130RTLDesignAndSynthesisWorkshop/verilog_models

**RTL Simulation:**
```
$ iverilog ternary_operator_mux.v tb_ternary_operator_mux.v
$ ./a.out
$ gtkwave tb_ternary_operator_mux.vcd	
```
	
**Synthesis**
```
$ yosys
yosys> read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
yosys> read_verilog ternary_operator_mux.v
yosys> synth -top ternary_operator_mux
yosys> abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
yosys> write_verilog -noattr ternary_operator_mux_net.v			
```
	
**Gate_level Simulation**
```
$ iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/sky130_fd_sc_hd.v ternary_operator_mux_net.v tb_ternary_operator_mux.v
$ ./a.out
$ gtkwave tb_ternary_operator_mux_net.vcd	
```

`../my_lib/verilog_model/primitives.v` .......................  
Contains basic primitive definitions (AND, OR, NOT, etc.) used by the standard cell library.

`../my_lib/verilog_model/sky130_fd_sc_hd.v` ..................  
The Sky130 standard cell library model. Defines how synthesized cells (flip-flops, muxes, etc.) behave in simulation.

`ternary_operator_mux_net.v` ................................  
The synthesized netlist of the design (ternary operator mux). This is the netlist obtained from ABC tool and is now the Design Under Test (DUT) for GLS.

`tb_ternary_operator_mux.v` ................................  
The testbench that applies inputs and checks outputs. Same as the one used for RTL simulation


RTL DESIGN
```verilog
module ternary_operator_mux (input i0 , input i1 , input sel , output y);
	assign y = sel?i1:i0;
endmodule
```
<p></p>
RTL Simulation - Pre Synthesis Simulation
<p></p>

![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_4/Assets_D4/Screenshot%202026-10-01%20150121%20D4Lab1%2021mux%20gtk.png)

<p></p>
GLS - Post Synthesis Simulation
<p></p>

![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_4/Assets_D4/Screenshot%202026-10-01%20151530%20%20D4Lab1%2021mux%20GLS%20gtk.png)

### The close match between RTL and GLS waveforms confirms that the synthesized netlist conforms to the intended RTL functionality..............

<p></p>
Synthesized Schematic
<p></p>

![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_4/Assets_D4/Screenshot%202026-10-01%20150342%20D4Lab1%2021mux%20synth.png)

---

### 4.3 Synthesis-Simulation Mismatch :
____________________________

If GLS waveforms do not match with the RTL simulation waveforms, it points to potentially hidden **synthesis–simulation mismatches**. 
These mismatches can arise from 
- **Missing sensitivity list**  - Incomplete sensitivity lists in RTL can cause simulation behavior to differ from synthesis results.  

- **Blocking vs. Non-Blocking assignments** - Incorrect usage of `=` (blocking) vs. `<=` (non-blocking) 

- **Non-standard Verilog coding** - Constructs outside synthesizable Verilog or poor coding practices can cause discrepancies between RTL simulation and gate-level behavior.  


### Missing Sensitivity List : **********************

### *D4Lab17 - Missing Sensitivity List *** bad_mux.v : Mismatch between RTL Simulation and GLS* -----------

RTL Design

```verilog
module bad_mux (input i0 , input i1 , input sel , output reg y);
always @ (sel)
begin
	if(sel)
		y <= i1;
	else 
		y <= i0;
end
endmodule
```
<p></p>
RTL Simulation - Pre Synthesis Simulation
<p></p>

![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_4/Assets_D4/Screenshot_2026-10-04_22-22-09%20bad_mux%20gtk.png)

<p></p>
GLS - Post Synthesis Simulation
<p></p>

![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_4/Assets_D4/Screenshot_bad_mux%20gtk%20GLS.png)

#### OBSERVATIONS: RTL simulation output waveform and the GLS waveform do `NOT match`.  

#### *****************     EXPLANATION for MISMATCH    ***************************

##### RTL Simulation ------------------
- The `always` block is evaluated only when a change happens on **`sel`** input.  
- It is **independent of changes in inputs (`i0`, `i1`)**.  
- As a result, the output is not updated when inputs i0 or i1 change.  
- The simulator interprets this behavior as a **latch**, rather than a multiplexer as intended in the RTL Design.

##### GLS Simulation -----------------
- The synthesized netlist correctly infers a **mux**

#### *****************     CONFLICT and CAUSE of Conflict   ***********************
- GLS infers  **mux** ------------------------------- RTL simulation infers a **latch**.  
- **REASON: missing sensitivity list**. - incomplete sensitivity list in the always block

#### ****************   Fixing the Missing Sensitivity List Problem   **************
It is important to write **complete sensitivity lists** in RTL code to avoid unintended latch inference.

`always` block should be written as:

```verilog
always @(*)
begin
   // mux logic here
end
```

<p></p>
Synthesized Schematic
<p></p>

![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_4/Assets_D4/Screenshot%202026-10-01%20152500%20D4Lab2%20Bad_mux%20synth.png)

___________________________________________________________________________

### Blocking and Non-blocking assignments in verilog : **********************
Blocking and Non-blocking statements come into picture when we are using "always" block. 

**Blocking statements (=)** : 
* Suitable for: Combinational Logic
* Execution of blocking statements is sequential.
* Syntax: =

**Non-Blocking statements (<=)**: 
* Suitable for: Sequential logic
* When entered in always block, it executes all the RHS in parallel 
* Assignment to LHS is scheduled at the end of the time step.
* Execution: Scheduled, executes concurrently at the end of the time step.
* So, order of statements does not matter.
* Syntax: <=				   

#### Blocking vs. Non-Blocking Assignments

| Aspect                          | Blocking (`=`)                           | Non-Blocking (`<=`)                       |
|---------------------------------|------------------------------------------|-------------------------------------------|
| Operator                        |  `=`                                     |  `<=`                                     |
| Execution Style                 | Sequential, immediate execution          | Concurrent, scheduled at end of timestep  |
| Update Behavior (Assign)        | Instantly in code order                  | At the end of time step                   |
| Usage                           | Combinational logic, temp variables      | Sequential logic, registers/flip-flops    |
| Hardware Inference              | Infers gates                             | Infers flip-flops                         |


**Synthesis–Simulation Mismatch: Blocking Assignments** :

### *D4Lab18 - Caveat in Blocking Statements   *** blocking_caveat.v : Latch behaviour inferred* -----------

```verilog
module blocking_caveat (input a , input b , input  c, output reg d); 
reg x;
always @ (*)
begin
	d = x & c;
	x = a | b;
end
endmodule
```
<p></p>
RTL Simulation - Pre Synthesis Simulation
<p></p>

![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_4/Assets_D4/Screenshot%202026-10-01%20154947%20D4Lab3%20blocking_caveat%20gtk.png)
![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_4/Assets_D4/Screenshot%202026-10-01%20155618%20D4Lab3%20blocking_caveat%20gtk_expand.png)

#### OBSERVATIONs:
In the above case, the RTL simulation shows **latch-like behavior** being inferred.  
This occurs because the assignments inside the `always` block are **blocking assignments (`=`)**.

##### What’s Happening?
- The order of assignments causes **`d` to use the old value of `x`**, not the newly computed one.  
- Since `x` is updated only in the subsequent statement, the first assignment evaluates with stale data.  
- This results in incorrect simulation behavior **NOT** reflecting the **intended logic**.
- Consequently, the simulator infers **UNINTENDED latch behavior**.

```
- As blocking assignments execute sequentially, they can lead to mismatches between RTL simulation and synthesized hardware. 
```

**MODIFIED RTL Design -**

```verilog
module blocking_caveat_M (input a , input b , input  c, output reg d); 
reg x;
always @ (*)
begin
	x = a | b;
	d = x & c;
	
end
endmodule

```
<p></p>
RTL Simulation - Pre SYnthesis
<p></p>

![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_4/Assets_D4/Screenshot%202026-10-01%20160308%20D4Lab3%20blocking_caveat_M%20gtk_expand.png)

<p></p>
GLS - Post Synthesis
<p></p>

![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_4/Assets_D4/Screenshot%202026-10-01%20160923%20D4Lab3%20blocking_caveat_M%20gtk%20GLS.png)

<p></p>
Synthesized Schematic
<p></p>

![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_4/Assets_D4/Screenshot%202026-10-01%20160605%20D4Lab3%20blocking_caveat_M%20synth.png)

****************************************************************************************************************
****************************************************************************************************************
