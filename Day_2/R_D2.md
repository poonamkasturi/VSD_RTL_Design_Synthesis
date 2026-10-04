## Day 2: Timing Libraries, Synthesis Approaches, and Efficient Flip-Flop Coding

#### 🔰 DAY 2: TOPICS -----
[![Introduction to Timing Libs](https://img.shields.io/badge/TOPIC-Timing%20Libs-blue)](#31-introduction-to-timing-libs)
[![Hierarchical vs Flat Synthesis](https://img.shields.io/badge/TOPIC-Hierarchical%20vs%20Flat%20Synthesis-green)](#hierarchical-vs-flat-synthesis)
[![Efficient Flop Coding Styles](https://img.shields.io/badge/TOPIC-Efficient%20Flop%20Coding%20Styles-purple)](#efficient-flop-coding-styles)
[![Interesting Optimizations](https://img.shields.io/badge/TOPIC-Interesting%20Optimizations-olive)](#interesting-optimizations)   
[RTL Design] → [Elaboration] → [dfflibmap 🧬] → [Synthesis 🛠️] → [Netlist Generation]
_____________________________________________________

### Topics Explored   

2.1	 Sky130 PDK - Introduction to Timing (.lib) library 
```
- Library Naming Convention  
- Liberty File (.lib)
```     
2.2  Hierarchical and Flat Synthesis
```
- Hierarchical Synthesis  
- Flat Synthesis
- Submodule Level Synthesis
 ```    
2.3  Flip Flop Coding   
```
- Asynchronous Reset
- Synchronous Reset
```

 
### 2.1 SKY130 PDK - Introduction to Timing (.lib) 

The SKY130 Process Design Kit (PDK) is an open‑source resource built on SkyWater Technology’s 130 nm CMOS platform. It offers the fundamental models and libraries required for integrated circuit (IC) development, encompassing timing, power, and process variation data essential for accurate design and verification.

[![Library Naming Convention](https://img.shields.io/badge/Section-2.1.1--Library%20Naming%20Convention-blue)](#311-library-naming-convention)     
[![Liberty File](https://img.shields.io/badge/Section-2.1.3--Liberty%20File-green)](#312-liberty-filelib)

**Library name:** sky130_fd_sc_hd__tt_025C_1v80.lib

#### Library Naming Convention
SkyWater Technology Foundry provides **seven standard cell libraries** for use in SKY130 designs. These libraries differ in intended applications and are available in **three separate cell heights**.
It Specifies the **process**, **voltage**, and **temperature** conditions modeled by each library, along with the library type.

| **Component** | **Description** |
|---------------|-------------|
| **sky130**    | Refers to the SkyWater 130 nm CMOS process |
| **fd_sc**     | Foundry Standard Cell library |
| **hd**        | High Density variant (optimized for area efficiency) |
| **tt**        | **Process** Fabrication corner (e.g., `tt` = typical, `ff` = fast, `ss` = slow) |
| **025C** | **Temperature** - Operating condition (e.g., `025C` = 25 °C, `085C` = 85 °C) |
| **1v80**   | **Voltage** Supply level modeled (e.g., `1v80` = 1.80 V, `1v62` = 1.62 V) |

<span style="color:#d73a49"><code>sky130_fd_sc_hd__tt_025C_1v80.lib</code></span>

This library belongs to the `sky130_fd_sc_hd` family, which is designed for **high density**. It offers:            
     - Higher routed gate density          
     - Lower dynamic power consumption         
     - Comparable timing and leakage power         

> ⚠️ **Trade-off**: Lower drive strength compared to other variants.
____________________________________________________________________________________

#### Liberty File (.lib)

Liberty files are defined by IEEE standard
They are used to describe the **characteristics of standard cells** in a given technology node.     

#### File Attributes

![ASCII Format](https://img.shields.io/badge/File%20Type-ASCII%20Format-blue)
![Extension .lib](https://img.shields.io/badge/File%20Extension-.lib-green)
![Logic Modules](https://img.shields.io/badge/Content-Logic%20Modules%20%2F%20Standard%20Cells-purple)

#### Included Parameters

![PVT Characterization](https://img.shields.io/badge/Includes-PVT%20Characterization-purple)
![I/O Relationships](https://img.shields.io/badge/Includes-Input%20%2F%20Output%20Relationships-blue)
![Timing & Power](https://img.shields.io/badge/Includes-Timing%20%26%20Power%20Data-green)
![Noise Parameters](https://img.shields.io/badge/Includes-Noise%20Parameters-red)

> These files are essential for synthesis, timing analysis, and power estimation in digital design flows.  
> ...They are widely supported across EDA tools and form the backbone of cell-level timing models

some details of sky130_fd_sc_hd__tt_025C_1v80.lib :

<img width="300" height="350" alt="Lib_details" src="https://github.com/user-attachments/assets/60b1e088-ea0f-4d2c-9d5f-943e050ed7f2" />

	
"sky130_fd_sc_hd__a21110_1" :  cell definitions inside the .lib file as shown in the image below.

<img width="400" height="250" alt="cell_a2111o" src="https://github.com/user-attachments/assets/f4333e8c-2b55-4a91-b1fb-091ec4262f37" />


This cell implements logic function with 5 inputs.       
The logic implemens AND of first two inputs and then output of AND along-with three more inputs is ORed.      
The cell definition also shows amount of leakage power for different combinations of 5 inputs(32 combinations here).

Different flavours of same cell in the .lib file.

<img width="450" height="300" alt="and_versions" src="https://github.com/user-attachments/assets/b4df3b3d-9d58-4011-9f2a-789fc2b9e8fb" />

 
As per above image, the different flavours of  "AND" gate have different size, area and power consumption.       
Below details show the comparison between these different versions of " AND " gate:
```
Area  : and2_0  < and2_2 < and2_4
Power : and2_0  < and2_2 < and2_4 
Delay : and2_0  > and2_2 > and2_4
```
* Larger cells have wider transistors, thus more area, more power consumption but are faster cells(less delay).
* Smaller cells have thin transistors, thus less area, power consumptions but are slower (more delay). 

## 2.2 Hierarchical and Flat Synthesis

[![Hierarchical Synthesis](https://img.shields.io/badge/Section-2.2.1--Hierarchical%20Synthesis-blue)](#221-hierarchial-synthesis)  
[![Flat Synthesis](https://img.shields.io/badge/Section-2.2.2--Flat%20Synthesis-green)](#222-flat-synthesis)  
[![Submodule Level Synthesis](https://img.shields.io/badge/Section-2.2.3--Submodule%20Level%20Synthesis-purple)](#223-submodule-level-synthesis)  
____________________

### 2.2.1 Hierarchial Synthesis :

**Hierarchial Synthesis - RTL Design Overview**
- RTL design with **two sub-modules**, both instantiated inside the **top module** 

###### Example: Multiple Modules in Verilog

```verilog

module sub_module2 (input a, input b, output y);
	assign y = a | b;
endmodule

module sub_module1 (input a, input b, output y);
	assign y = a&b;
endmodule

module multiple_modules (input a, input b, input c , output y);
    wire net1;

    sub_module1 u1(.a(a), .b(b), .y(net1));  // net1 = a & b
    sub_module2 u2(.a(net1), .b(c), .y(y));  // y = net1 | c → y = (a & b) + c
endmodule

```
**Hierarchial Synthesis - Expected Netlist Behavior**   
Based on the RTL code, we expect the synthesized netlist to consist of:   
    - AND gates      
    - OR gates      
> These gates are expected to be inferred from the standard cell library.
<img width="500" height="300" alt="mutiple_modules_synth" src="https://github.com/user-attachments/assets/25083310-61ef-4956-b8a7-4468e747b55f" />

![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_2/Assests_D2/Screenshot_2026-10-04_03-56-19%20MM_H_terminal1.png)
![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_2/Assests_D2/Screenshot_2026-10-04_03-56-58%20MM_H_terminal2.png)



**Hierarchial Synthesis - Synthesis Result**   
However, the synthesis result reveals that the **hierarchy has been preserved**.     
Instead of flattening the design into gates, the synthesis tool retains the **sub-module structure**.   

> NOTE: Sub-modules `u1` and `u2` are used in place of individual gates in the synthesized design hierarchy.

![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_2/Assests_D2/Screenshot_2026-10-04_03-49-23%20mult_mod%20synth%20hier.png)


**Hierarchial Synthesis - Netlist from ABC Synthesis Tool**       
     - The netlist confirms that hierarchy is preserved.       
     - Sub-modules `u1` and `u2` appear explicitly in the netlist.       
     - This matches the structure expected from the RTL code.       

>  This behavior is typical when synthesis is performed in **hierarchical mode**, allowing better modularity and reuse.

![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_2/Assests_D2/Screenshot_2026-10-04_04-04-56%20MM_H_netlist1.png)
![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_2/Assests_D2/Screenshot_2026-10-04_04-04-56%20MM_H_netlist2.png)

---

**Hierarchial Synthesis - Summary**
- RTL design includes two sub-modules: `u1` and `u2`
- Synthesis preserves hierarchy instead of flattening to gates
- ABC tool netlist reflects sub-module structure
- Matches RTL expectations
  
![Hierarchy Preserved](https://img.shields.io/badge/Synthesis-Hierarchy%20Preserved-blue)
![Submodules Inferred](https://img.shields.io/badge/Netlist-Submodules%20u1%20%26%20u2%20Inferred-green)
_______________________________________


### 2.2.2. Flattening the Design in Yosys
In Yosys, the **flatten** command is used to eliminate hierarchy by replacing module instances with their actual implementation.
This is especially useful for inspecting gate-level netlists and verifying that submodules have been fully expanded.

 **What Does flatten Do?**      
* The flatten pass replaces cells with their implementation using the current design as the mapping library.   
* It behaves similarly to the techmap pass, but uses the current design instead of an external mapping file.   
* Modules or cells with the keep_hierarchy attribute will not be flattened.

 **Synthesis and Flattening Workflow**   
After synthesizing the design and generating the netlist, the design is flattened to verify if all hierarchies are removed and gates are instantiated directly instead of submodules.

_______________________________________
**TO FLATTEN THE DESIGN**
_____________________
```
yosys> read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib    : READ LIBERTY FILE
yosys> read_verilog multiple_modules.v                               : READ FILE with multiple sub-modules
yosys> synth -top multiple_modules                                   : RUN SYNTHESIS WITH THE TOP MODULE 
yosys> abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib         : MAP TO STANDARD CELLS
yosys> flatten							                             : FLATTEN THE DESIGN TO ...
yosys> write_verilog -noattr multiple_modules_Flat.v			     : WRITE FLATTENED NETLIST
```
	
netlist when desing is flattened:

![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_2/Assests_D2/Screenshot_2026-10-04_04-13-10%20MM_flatten.png)
![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_2/Assests_D2/Screenshot_2026-10-04_04-15-03%20MM_flat_netlist.png)

**Difference between the Hierarchical and flattened synthesized netlists:**

<img width="500" height="400" alt="Hier_vs_Flat" src="https://github.com/user-attachments/assets/ca19d930-6aa2-4402-ba5c-d8c7a24b2a6d" />

_______________________________________

### 2.2.3 Submodule Level Synthesis   
With multiple sub modules in  top level module, sub module level synthesis can be done.  
* **Reuse of Synthesized Logic:** In case of top module having multiple instances of same submodule, synthesis of a submodule helps to synthesize single instance and using it for the other instances. It saves time as only single instance has to be instantiated.
* **Scalability for Large Designs:** For large designs, divide and conquer approach is followed to synthesize submodules which helps in reducing load on synthesis tool.

Example: Synthesizing sub_module1 from multiple_modules.v

To understand submodule-level synthesis better, synthesize only sub_module1 and observe the resulting netlist.

```
yosys> read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
yosys> read_verilog multiple_modules.v
yosys> synth -top sub_module1                                      : RUN SYNTHESIS WITH SUB-MODULE 1 only 
yosys> abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
yosys> write_verilog -noattr multiple_modules_sub.v			
```
After synthesis It is obvious that synth command looks at the specified sub_module1 alone and sub_module2 is ommitted from synthesis.

Below images show the sub module1 synthesis image and synthesized netlist of sub_module1:

<img width="300" height="250" alt="sub_all" src="https://github.com/user-attachments/assets/723915ae-7bd7-49d9-8018-179ad820e439" />
<img width="300" height="150" alt="sub_net" src="https://github.com/user-attachments/assets/7ac8c534-aef3-4245-b20d-653bbe05bfc2" />

![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_2/Assests_D2/Screenshot_2026-10-04_04-03-14%20synth%20sub_module1.png)
![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_2/Assests_D2/Screenshot_2026-10-04_04-04-56%20MM_H_sub1_netlist3.png)

_______________________________

### 2.3 Flop Coding Styles and Optimization
Before diving into flip-flop coding styles, it's important to understand why flip-flops are essential in digital design.

#### Why Flip-Flops Are Needed
In a purely combinational circuit, every gate introduces a propagation delay - a finite time for any of the input changes to reflect at the output. When gates have varying delays, **input transitions** can cause **unwanted output fluctuations**, known as: **Glitches**

#### Boolean vs Reality
For the given circuit from Boolean algebra, the output should always be 1.    
However, due to gate delays, the actual output may momentarily dip, as shown in the timing diagram:

<img width="400" height="200" alt="Glitch wave" src="https://github.com/user-attachments/assets/fca5f3b9-3e30-4008-873d-a11eb57a7b8c" />

```
Glitch and Timing Waveform
The more complex the combinational logic, higher the chances of glitches.
```

#### Avoiding Glitches with Flip-Flops
To mitigate glitches, storage elements—flip-flops are introduced.
- Flip-flops store the output of a combinational block.
- Their outputs change only on clock edges, isolating them from input transitions between edges.
- By placing flip-flops between combinational paths, glitch propagation is prevented and stable outputs are ensured.

#### Flip-Flops as Timing Barriers
Flip-flops act as barriers at the input of a combinational circuit, allowing outputs to settle before being used downstream. This results in:
- Stable inputs to combinational blocks
- Predictable and glitch-free outputs
- Improved timing closure and design reliability

#### Importance of Initial States in Flip-Flop Coding 
When coding flip-flops, it's critical to specify their initial states:
- The output of a flip-flop often feeds into combinational logic.
- If the initial state is unspecified or unknown, the combinational logic may evaluate to garbage values, leading to unpredictable behavior.

**Controlling Initial States: RESET and SET**   
To avoid such issues, control pins are introduced to manage the initial values of flip-flops:
- RESET: Sets the flip-flop output to 0
- SET: Sets the flip-flop output to 1

##### Flip-Flop Initialization Control

| Control Signal | Output Value | Timing Type     | Description                                                                 |
|----------------|--------------|------------------|-----------------------------------------------------------------------------|
| `RESET`        | 0            | Asynchronous     | Output is immediately set to 0, independent of clock                        |
| `RESET`        | 0            | Synchronous      | Output is set to 0 only on the active clock edge                           |
| `SET`          | 1            | Asynchronous     | Output is immediately set to 1, independent of clock                        |
| `SET`          | 1            | Synchronous      | Output is set to 1 only on the active clock edge                           |

> Proper use of RESET and SET ensures stable initialization and prevents logic corruption during simulation or synthesis.

________________________________________


#### Flip Flop Mapping During RTL Synthesis
When synthesizing RTL code, it's crucial to correctly map flip-flops using the **dfflibmap** command. This ensures that the synthesis tool knows where to source the flop cells from to map the sequential elements of the code.

#### Command Used
```
yosys> dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

command to be executed after synth -top and before abc 
```
#### Why Use dfflipmap
- In many library flows, flip-flops and standard cells are stored in separate Liberty files.
- The synthesis tool needs explicit direction to locate the flop cells.
- This ensures that flip-flops in the RTL are properly recognized and mapped during synthesis.

✅ Our Case
- The same Liberty file holds information for both standard cells and flops.
- Hence, the same path is passed to dfflibmap.
 `      yosys> dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib      

Tip: Always verify Liberty file contains both standard cells and sequential elements.

**Synthesizing various D-Flip flop with set and reset being synchronous or asynchronous**

#### Asynchronous reset D-Flip flop
    - reset does not waits for the clock edge

```verilog
module dff_asyncres (
    input clk,
    input async_reset,
    input d,
    output reg q
);

  always @ (posedge clk, posedge async_reset)
    if (async_reset)
      q <= 1'b0;
    else
      q <= d;
endmodule
```
![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_2/Assests_D2/output_dff_asyncres.png)
![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_2/Assests_D2/dff_AH_async_rst.png)

OBSERVATIONS:
```
1. Asynchronous reset - when 1 output goes to 0 irrespective of clock
2. Asynchronous reset - when 0 output follows d input at the positive clock edge
```

![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_2/Assests_D2/Screenshot%202026-10-01%20130034%20dff_asyncrst%20synth.png)


#### Synchronous reset D-Flip flop 
    - reset will happen only at the next clock edge despite the time when set signal is asserted
<img width="400" height="300" alt="dff_synch_res" src="https://github.com/user-attachments/assets/0eb6d010-2584-4bda-a1e8-d7a8cf7fb0e2" />



