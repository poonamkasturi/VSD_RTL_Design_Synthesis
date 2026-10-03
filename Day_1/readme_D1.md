# RTL Design and Synthesis 

## DAY 1 

This repository documents the labs and learning materials from the [sky130RTLDesignAndSynthesisWorkshop](https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop) workshop. It covers simulation and synthesis flow to introduce the fundamentals of Register Transfer Level (RTL) design using open-source tools like **Icarus Verilog**, **gtkwave** and **Yosys**, tailored for the Sky130 process node.

#### ➡️ Simulation ➡️ Synthesis ➡️ Netlist
<!-- 🟦 Simulation Stage -->
![Simulation](https://img.shields.io/badge/STAGE-Simulation-blue)     
![iverilog](https://img.shields.io/badge/iverilog-good__mux.v%20tb__good__mux.v-blue)
![run](https://img.shields.io/badge/run-./a.out-orange)
![gtkwave](https://img.shields.io/badge/gtkwave-tb__good__mux.vcd-green)

<!-- 🟨 Synthesis Stage -->
![Synthesis](https://img.shields.io/badge/STAGE-Synthesis-yellow)     
![yosys](https://img.shields.io/badge/tool%20invoke-Yosys-blue)     
![read_liberty](https://img.shields.io/badge/yosys-read__liberty%20--lib%20../lib/sky130__fd__sc__hd___tt__025C__1v80.lib-lightgrey)
![read_verilog](https://img.shields.io/badge/yosys-read__verilog%20good__mux.v-lightgrey)
![synth](https://img.shields.io/badge/yosys-synth%20--top%20good__mux-yellow)
![abc](https://img.shields.io/badge/yosys-abc%20--liberty%20../lib/sky130__fd__sc__hd___tt__025C__1v80.lib-lightgrey)

<!-- 🟪 Visualization & Netlist -->
![Netlist](https://img.shields.io/badge/STAGE-Netlist_&_Visualization-purple)     
![show](https://img.shields.io/badge/yosys-show-purple)
![write_verilog](https://img.shields.io/badge/yosys-write__verilog%20--noattr%20good__mux__netlist.v-green)
![gvim](https://img.shields.io/badge/yosys-!gvim%20good__mux__netlist.v-red)

---

## 1: Introduction to Verilog RTL design and Synthesis

---
**📌 Key Highlights**  
- Practical RTL workflows aligned with the **Sky130 Process Design Kit (PDK)**  
- Simulation using **Icarus Verilog** and waveform analysis with **GTKWave**  
- Synthesis using **Yosys**, targeting Sky130 standard cell libraries  
- Real-world design flow from **RTL** to **gate-level netlist**


**🏁 Outcome** 

To Gain a fabrication-aware understanding of digital design, bridging theory with open-source implementation.

---

### Table of Contents

- [Tools Used](#-tools-used)
    - [1️⃣ iverilog](#1-iverilog)
    - [2️⃣ gtkwave](#2-gtkwave)
    - [3️⃣ Yosys](#3-yosys)
    - [4️⃣ Technology Used sky130 PDKs](#4-Technology-file-sky130-pdks)  
- [Work_Flow_D1 – Introduction to Verilog RTL Design and Synthesis](#-Work-Flow-D-1--introduction-to-verilog-rtl-design-and-synthesis)
  - [1️⃣ Introduction to open-source simulator Icarus Verilog - iverilog](#1-introduction-to-open-source-simulator-icarus-verilog-iverilog)
  - [2️⃣ Labs Using iverilog and gtkwave](#2-labs-using-iverilog-nd-gtkwave)
  - [3️⃣ Introduction to Yosys and Logic Synthesis](#3-introduction-to-yosys-and-logic-synthesis)
  - [4️⃣ Labs using Yosys and Sky130 PDKs](#4-labs-using-yosys-and-sky130-pdks)
  - Yosys Synthesis Flow Setup - how to synthesize Verilog designs using **Yosys**, targeting the Sky130 standard cell library.
  - Verifying the Synthesized Netlist - Inspect the synthesized netlist and validate its structure and logic using **NetlistSVG** or other visualization tools.

  
### TOOLS USED:

* **Icarus Verilog:** It is a verilog simulation and synthesis tool. It operates as a compiler, compiling source code written in Verilog (IEEE-1364) into some target format.Icarus Verilog is an open source Verilog compiler that supports the IEEE-1364 Verilog HDL including IEEE1364-2005 plus.     
* **GTKwave	:** GTKWave is a VCD waveform viewer based on the GTK library. This viewer support VCD and LXT formats for signal dumps.It also reads LXT, LXT2, VZT, FST and GHW files as well as standard Verilog VCD/EVCD files and allows their viewing.     
* **Yosys 	:** Yosys is a framework for Verilog RTL synthesis. It currently has extensive Verilog-2005 support and provides a basic set of synthesis algorithms for various application domains.
* **Technology used:** Sky130 PDKs - sky130_fd_sc_hd.v, sky130_fd_sc_hd__tt_025C_1v80.v.   
	
### Work_Flow_D1 - Introduction to Verilog RTL Design and Synthesis
The first day of the workshop covers the brief description of iverilog simulator, Test Bench setup, iverilog simulation flow  and lab using iverilog, gtkwave, yosys tools.    

### 1.1 Introduction to Design and Test Bench	
* **RTL Design :** Register Transfer Level (RTL) is the HDL (verilog) code or set of HDL (verilog) codes which have the intended functionality to meet with the required specifications.
  The digital design is coded in Hardware Descriptive Language (HDL - VHDL, Verilog)
  and is an abstraction for defining the digital portions of a design.
  The RTL design has primary inputs and primary outputs.

  
* **Test Bench :** Stimuli is to be given to all the primary inputs of the HDL model and all primary outputs are to be observed.
  This **necessitates** stimulus generator at the input and observer at the output.

  Test Bench is the setup (stimulus) that generates input test_vectors applied to the HDL design model
  to verify its functionality against specified requirements.
  The design (module) is instantiated in the test bench and then stimulus is applied to it.

  Test bench doesn't have any primary inputs and primary outputs of its own.

 <dl>
  <dd>Below image shows the test bench set up :</dd>
 </dl>

 ```

```
  

  * **Outputs:** Outputs from the design are to be observed using another tool - gtkwave
   
	   

 
### 1.2 Simulation Flow of the Designs - iverilog
* **Simulation :** Simulation is the process by which the HDL design model gets executed
  to verify the functional correctness of the digital design.

* **Simulator :** It is the tool used for simulating the design. **"iverilog"** is being used in this work.
	
* **How does a simulator work ?**
  - Simulator works by continuously monitoring the changes in the inputs.
  - Upon a change in any of the inputs, the output is re-evaluated.
  - Simulator dumps the changes in the ouputs according to the change in input to a file as ***.vcd**.
  
* **Inputs to the simulator**:  
    The **iverilog** simulator accepts two main inputs.  
	  1. **RTL Design**  
    2. **Test Bench** 
          - Test bench instantiates the verilog module of design, gives stimulus to the input ports of the RTL design.
          - the instantiated RTL module is executed as per the stimuli.
         	
* **Outputs of the simulator** :  
 The iverilog simulator outputs a value chage dump (.vcd) file as output.

 **This vcd file can be viewed using the GTKWave viewer tool.**  
<dl>
  <dd>Below image shows the complete iverilog simulation flow : </dd>
</dl>	 
 
```

```

### 1.3 Synthesis FLow - yosys

* **Synthesis:** Process during which RTL design actually gets converted into a hardware design circuit.... 
The tool is Yosys.
In simple terms:
	-	It translates RTL design into generic logic gates (AND, OR, Flip Flops...) expressed in terms of boolean equations.
	-	And connections are made between them.
	-	This complete information is given out as a file called netlist.
	-	This netlist is how the circuit will look.
	-	The netlist is still abstract and doesn’t yet know about the physical cells available in a real chip.
  
* **Technology Mapping:** Ensures the design is implementable on silicon (synthesizable) and not just simulated....
The tool is abc
	-	Technology Mapping maps the generic gates in the netlist onto standard cells defined in the PDKs used - (SkyWater-Sky130 PDK).

<br>
<p></p>
🔹	ABC (A System for Sequential Logic Synthesis and Formal Verification) is an external tool developed at UC Berkeley. 
	
	-	It specializes in logic optimization, technology mapping, and verification.

🔹 In Yosys, ABC tool is called using the abc command, 
	
	-	This internally hands the netlist over to the ABC tool. 
	-	Yosys then uses ABC’s algorithms to perform logic optimization and technology mapping 
	-	Technology mapping is done against a given standard cell library (like Sky130).
<p></p>
<br>


  
```

```

---

## 2. LAB_WORK_FLOW 

### 2.1 Setting Up the environment - Open-Source EDA Toolchain Setup on Ubuntu
The first step is setting up the development environment and installing the essential open-source tools
all running on Ubuntu inside a VirtualBox VM.
The goal is to create a reliable and efficient workspace for synthesis, simulation, and design tasks.

##### **** VIRTUAL MACHINE CONFIGURATION SET-UP 
        Oracle virtual machine link      https://www.virtualbox.org/wiki/Downloads

| Specification | Details|
|---------------|----------------|
| **Operating System**| Ubuntu **20.04+** |  
| **RAM**| 6GB |
| **Storage**| 50 GB HDD |
| **vCPU**| 4|

- latest version of ubuntu (18 or above) that is available
 
**The harddisk image provided by VSD is to be attached with the VM** - 

<br>

#### **** CREATE THE WORKING SPACE - git-clone VSD repository
- Created a directory on Desktop
- mkdir VLSI_PK
- gitcloned the repository in VLSI_PK directory
-
- https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop.git
- 
-  This comes with a pre-configured VM environment
-  and installed DIRECTORY STRUCTURE
<br>

<br>

#### **** System Check for TOOL INSTALLATION and Verification

|Tool |Description |Folder |Status | 
|------|------------------|--------|-----|
| **Yosys**| RTL synthesis for Verilog designs | yosys/ | Pre-Installed|
| **iverilog**| Verilog Simulation and Compilation | icarus-verilog/ |To-be-Installed|
| **GTKwave**| Waveform Viewer & Analysis | gtkwave/ |To-be-Installed|

<br>

#### **** Open-Source TOOL INSTALLATION - iverilog and gtkwave


---
### 2.2 Directory Structure 


---
---


## Lab - iverilog Simulation of Multiplexer(MUX)
Iverilog simulation is done as per below steps:
*  iverilog takes RTL design and test bench as input and generates a executable file " a.out".
*  On executing "a.out" ,it dumps the simulation in value change dump format(.vcd file).
*  Then GTKWave takes the .vcd file and display the simulation waveform.
The above steps are shown below:

### 1. Run iverilog with the design verilog file and the testbench as inputs. 
####   This will create an executable named a.out.
   
```
$ cd sky130RTLDesignAndSynthesisWorkshop
$ ls -ltr
$ cd verilog_files 
$ iverilog good_mux.v tb_good_mux.v
```

### 2. Execute the file a.out. This will generate the value change dump (.vcd) file.

```
$ ./a.out
```
### 3. Now run GTKwave with the vcd file as input to view the simulation waveform.
```
$ gtkwave tb_good_mux.vcd
```
### 4. To view the signal on the wave window click and drag them to the signal column..


     
## 1.6 Synthesis with Yosys

	      
### 1.6.1 Yosys Synthesis Flow setup
The synthesis tool takes the RTL design and the liberty file(.lib) as inputs and synthesize the RTL design into netlist which is the gate level representation of the RTL design.

Below image shows the Yosys synthesis flow setup: 


   
Below are the steps to synthesize the multiplexer design(good_mux.v):
### 1. To invoke Yosys:   
```
$ cd verilog_files
$ yosys
```
Below image show the yosys synthesis suite:

### 2. Reading sky130 standard library :
```
$ read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib  
```
read_liberty : It reads cells from liberty file as modules into current design.
	       The option "-lib"  only create empty blackbox modules.
	       
### 3. Reading the RTL design(verilog file) :
```
$ read_verilog good_mux.v  
```
**read_verilog :** This command is used to read the verilog desgin file. It load modules from a Verilog file to the current design.
Below image show the yosys synthesis suite:

### 4. Synthesize the top level module  : Below command is used to synthesize the module
```
$ synth -top good_mux  
```
**synth :** This command runs the default synthesis script. This command does not operate on partly selected designs.
**-top <module> :** This option use the specified module as top module (default='top'). Here we have module name "good_mux".
	
	
### 5. Mapping to the standard library 
```
$ abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```
**abc :** This pass uses the ABC tool for technology mapping of yosys's internal gate library to a target architecture. This command converts RTL code into gates,cells which is taken from the sky130_fd_sc_hd__tt_025C_1v80.lib file.
**-liberty <file> :** It generate netlists for the specified cell library (using the liberty file format).

**NOTE:** The path of the sky130_fd_sc_hd__tt_025C_1v80.lib   should match with the path where the file is saved in the system
	
### 6. To view the result as a grapviz use the below command
```
$ show
``` 
**Show :** It creates  graphviz DOT file for the selected part of the design and compile it to a graphics file (usually SVG or PostScript).It is used to show the logic realized from the verilog code after synthesis.


### 7. To write the netlist in a .v file
$ write_verilog -noattr <filename.v>
