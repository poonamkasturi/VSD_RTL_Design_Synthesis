## 🔰 DAY 4: GLS, blocking vs non-blocking and Synthesis-Simulation mismatch

-	Topics Explored
	- 4.1. [GLS concepts and flow using iverilog](#GLS-concepts-and-flow-using-iverilog)
	- 4.2. [GLS setup using iverilog ](#GLS-setup-using-iverilog)
	- 4.3. [Synthesis-Simulation Mismatch](#Synthesis---Simulation-Mismatch)
	   * [Missing Sensitivity list](#Missing-Sensitivity-list)
	   * [Blocking and Non-blocking assignments in verilog](#Blocking-and-Non---blocking-assignments-in-verilog)

### 4.1 GLS concepts and flow using iverilog :

The term "gate level" refers to the netlist view of a circuit, usually produced by logic synthesis.

#### Gate Level Simulation (GLS)?
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

#### Purposes of GLS
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

### *D4Lab13 - GLS  ternary_operator_mux.v : check similarity of RTL Simulation and GLS* -----------

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
RTL DESIGN
```verilog
module ternary_operator_mux (input i0 , input i1 , input sel , output y);
	assign y = sel?i1:i0;
	endmodule
```
![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_4/Assets_D4/Screenshot%202026-10-01%20150121%20D4Lab1%2021mux%20gtk.png)
![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_4/Assets_D4/Screenshot%202026-10-01%20150342%20D4Lab1%2021mux%20synth.png)
![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_4/Assets_D4/Screenshot%202026-10-01%20151530%20%20D4Lab1%2021mux%20GLS%20gtk.png)

**The close match between RTL and GLS waveforms confirms that the synthesized netlist conforms to the intended RTL functionality.**

### 4.3 Synthesis-Simulation Mismatch :

Although the generated netlist is the true representation of the RTL design, it is still necessary to validate its functionality to ensure correctness and avoid potential hidden mismatches. The **synthesis–simulation mismatches** can arise from:

- **Missing sensitivity list**  - Incomplete sensitivity lists in RTL can cause simulation behavior to differ from synthesis results.  

- **Blocking vs. Non-Blocking assignments** - Incorrect usage of `=` (blocking) vs. `<=` (non-blocking) 

- **Non-standard Verilog coding** - Constructs outside synthesizable Verilog or poor coding practices can cause discrepancies between RTL simulation and gate-level behavior.  







  


Now, take a look of below example of "bad_mux.v" :
<img width="800" height="600" alt="bad_mux" src="https://github.com/user-attachments/assets/bd4895d2-d978-4d5a-be20-389edde10b18" />



In this we can see that the  RTL simulation output waveform and synthesized netlist gate level simulation output waveform do not have similar waveform. This is the because of problem of "missing sensitivity list". 
In the RTL simulation , we can see that always block is evaluated only when 'sel' is changing ans is independent of change in inputs(i0,i1). Thus as output is not evaluated for change in inputs, when we do RTL simulation ,simulator will infer the mux as latch. So this comes under problem of "missing sensitivity list".

To solve this issue we can write always block as : always(*)
So, always block will be evaluated when any if the inputs i0, i1, sel change.
	
But we can also see that when synthesis tool evaluate the same RTL code and the gate level simulation is done. 
The output waveform shows the MUX behaviour. Thus systhesis tool has inferred the same code as mux.
This is a problem of synthesis- simulation mismatch and that is the main reason to perform GLS.

#### *****  Missing Sensitivity list  ******
let us understand this with example 'ternary_operator_mux.v' and do RTL simulation , synthesis and gate-level simulation.

	
### 4.3.2 Blocking and Non-blocking assignments in verilog :
Blocking and Non-blocking statements come into picture when we are using "always" block. 

**Blocking statements** : 
* Inside always block , if we are using "equal to "(=) to make assignments, the assignment is called as blocking statement.
* Blocking statements execute the statements in the sam eorder they are written.
* So the first statement is evaluated first ,then second and so on. Thus behaviour of such statements is sequential.


**Non-Blocking statements** : 
* Inside always block, the non- blocking assignment is done using "less than equal to"(<=).
* It executes all the RHS when entered in always block and assign it to LHS.
* All the statements are evaluated in parallel, so their order does not matter.
* Thus, the evaluation of staements is done parallely.									   
	
**Caveats with Blocking Statements** :
lets understand the cavest in blocking statements using an example :	

<img width="800" height="600" alt="cavet_blocking" src="https://github.com/user-attachments/assets/140367e0-5ebc-4bde-bbd9-5720f5db5e86" />


											 
We can see the in the above case the RTL simulation, the latch behaviour is inferred by simulator. This is because the assignmenst are blocking assignment. Thus first value of 'd' is evaluated which will use old value of 'x' as the value of 'x' is evaluated after the first statement.

****************************************************************************************************************
****************************************************************************************************************









<img width="800" height="600" alt="good_mux" src="https://github.com/user-attachments/assets/7484fc86-2215-444a-b8f9-8318ccd02061" />
























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


TO CHECK BAD COUNTER FOR DAY 3********************

```verilog
module bad_counter (input clk , input reset , output reg [1:0] cnt);
wire res_int;

assign res_int = (cnt == 2'b11) | reset;

always @(posedge clk , posedge res_int)
begin
	if(res_int)
		cnt <= 2'b00;
	else
		cnt <= cnt+1;
end



endmodule

```




```verilog

```




```verilog

```
