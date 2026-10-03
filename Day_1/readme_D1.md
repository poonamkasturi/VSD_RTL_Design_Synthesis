# RTL Design and Synthesis using sky130 PDK

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
![gedit](https://img.shields.io/badge/yosys-gedit%20good__mux__netlist.v-red)

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

![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_1/Assets/Screenshot%202026-09-30%20131029%20Th%20D1_1.png)


* **Outputs:** Outputs from the design are to be observed using another tool - gtkwave
_____________________________________   
	   
### 1.2 Simulation Flow of the Designs - iverilog
* **Simulation :** Simulation is the process by which the HDL design model gets executed
  to verify the functional correctness of the digital design.

* **Simulator :** It is the tool used for simulating the design. **"iverilog"** is being used in this work.
	
* **How does a simulator work ?**
  -	Simulator works by continuously monitoring the changes in the inputs.
  - Upon a change in any of the inputs, the output is re-evaluated.
  - Simulator dumps the changes in the ouputs according to the change in input to a file as ***.vcd**.
  
* **Inputs to the simulator**:

  The **iverilog** simulator accepts two main inputs.  
		1. **RTL Design**  
		2. **Test Bench**

  		- Test bench instantiates the verilog module of design.
  		- Gives stimulus to the input ports of the RTL design.
  		- Instantiated RTL module is executed as per the stimuli.
         	
* **Outputs of the simulator** :
  
The iverilog simulator outputs a value chage dump (.vcd) file as output.
This vcd file can be viewed using the GTKWave viewer tool.

![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_1/Assets/Screenshot%202026-09-30%20130931%20Th%20D1_2.png)
___________________________________________

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

🔹	**ABC** (A System for Sequential Logic Synthesis and Formal Verification) is an external tool developed at UC Berkeley. 
	
	-	It specializes in logic optimization, technology mapping, and verification.

🔹 In **Yosys**, ABC tool is called using the abc command, 
	
	-	This internally hands the netlist over to the ABC tool. 
	-	Yosys then uses ABC’s algorithms to perform logic optimization and technology mapping 
	-	Technology mapping is done against a given standard cell library (like Sky130).

* **Why Do Libraries Have Different Gate "Flavors"?**

  .lib file contains many versions of each gate (like AND, OR, NOT) with different properties:
```
	Performance: Faster gates for critical paths, slower for power savings
	Power: Some gates use less energy
	Area: Smaller gates for compact chips
	Drive Strength: Stronger gates to drive more load
	Signal Integrity: Specialized gates for noise/performance
```
---

## 2. LAB_WORK_FLOW 

### 2.1 Setting Up the environment : Open-Source EDA Toolchain Setup on Ubuntu
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

- latest version of ubuntu (18 or above) that is available to be used
- The harddisk image provided by VSD is to be attached with the VM
____________________________________

#### **** CREATE THE WORKING SPACE - git-clone VSD repository
- Create a directory on Desktop - .......... user specified working directory
- in this user specified working directory gitclone the repository 
		https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop.git
-  gitclonning will create a copy of the repository in the user specified working directory with complete DIRECTORY STRUCTURE
-  change to the main repository directory ---- skyRTLDesignAndSynthesisWorkshop

```
$ cd Desktop      .............. current directory will become Desktop
$ mkdir VLSI_PK   .............. NOTE: This is a user specified directory - newly created
$ cd VLSI_PK      .............. present working directory path will be ~/Desktop/VLSI_PK
$ gitclone https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop.git ........... Directory structure will be created in ~/Desktop/VLSI_PK
$ ls              .............. will list the contents in pwd
$ cd sky130RTLDesignAndSynthesisWorkshop ........... pwd path will now be ~/Desktop/VLSI_PK/sky130RTLDesignAndSynthesisWorkshop
```
###### NOTE: 
	before doing a gitclone check if git is installed	
	$ git --version

![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_1/Assets/Screenshot_2026-09-30_17-59-58.png)
_____________________________________


#### **** System Check for TOOL INSTALLATION and Verification

|Tool |Description |Folder |Status | 
|------|------------------|--------|-----|
| **Yosys**| RTL synthesis for Verilog designs | yosys/ | Pre-Installed|
| **iverilog**| Verilog Simulation and Compilation | icarus-verilog/ |To-be-Installed|
| **GTKwave**| Waveform Viewer & Analysis | gtkwave/ |To-be-Installed|
_________________________________________________

#### **** Open-Source TOOL INSTALLATION - iverilog and gtkwave

```
$ sudo apt install iverilog      .............. open-source tool iverilog will be installed
$ sudo apt install gtkwave       .............. open-source tool gtkwave will be installed
```
The command **"sudo apt install `<package-name>`"** is used on Ubuntu (and other Debian‑based Linux systems) to install software packages from the system’s package repositories.
```
sudo :→ Runs the command with superuser (administrator) privileges. Installing software modifies system directories, so elevated rights are required.
apt :→ The modern package manager interface for Debian/Ubuntu. It handles installing, updating, and removing software.
install :→ The specific action telling apt to fetch and install the package.
<package-name> :→ The name of the software to be install (e.g., iverilog, gtkwave, vim, gcc).
```

![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_1/Assets/Screenshot_2026-09-30_18-03-22.png)
![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_1/Assets/Screenshot_2026-09-30_18-08-53.png)

---

### 2.2 Directory Structure 

![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_1/Assets/Screenshot_2026-10-03_15-29-42%20DirStr.png)

---

### 2.3 LAB 1 --- GOOD MUX 2x1
---
### *D1Lab1-sim - iverilog Simulation of Multiplexer(MUX)*
**Recap:** ....

**iverilog**
*  Takes **RTL design** and **test bench** as **inputs**
*  Generates an executable output file " **a.out**".
*  Executing "a.out", dumps the simulation data in **value change dump** format(**.vcd file**).
  
**GTKWave**
*  Takes **.vcd** file and **displays** the simulation waveforms.

#### 1. Run iverilog with the design verilog file and the testbench as inputs. 
   
```
$ cd sky130RTLDesignAndSynthesisWorkshop  ... pwd path becomes ~/Desktop/VLSI_PK/sky130RTLDesignAndSynthesisWorkshop
$ ls -ltr
$ cd verilog_files  ........ pwd path becomes ~/Desktop/VLSI_PK/sky130RTLDesignAndSynthesisWorkshop/verilog_files
$ iverilog good_mux.v tb_good_mux.v ........ execute the iverilog command with two inputs RTL design and Test Bench 
```
_____________________________
	-   good_mux.v :→ Design file, contains RTL description of mux .
	-   tb_good_mux.v :→ Testbench file 
________________________
	output file a.out generated in pwd....
	pwd path is ~/Desktop/VLSI_PK/sky130RTLDesignAndSynthesisWorkshop/verilog_files

![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_1/Assets/Screenshot_2026-10-03_15-53-24%20good_mux.png)
	

#### 2. Executing file a.out will generate the value change dump (.vcd) file.

```
$ ./a.out
```
./ represents pwd  

![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_1/Assets/Screenshot_2026-09-30_18-13-50.png)

#### 3. Run GTKwave with the vcd file as input to view the simulation waveform.
```
$ gtkwave tb_good_mux.vcd
```
##### To display the signals on the wave window 
	1). Select the design module in the gtkwave window
	2). This will display the list of signals in the module.
	3). Select and drag each of them to the signal column.
	
	We can see from the waveforms that output y follows the input as per the selection line, sel
	y = i0 if Sel = 0 and y = i1 if sel = 1

![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_1/Assets/Screenshot_2026-09-30_18-26-53.png)

    
### *D1Lab1-synth - Synthesis of Multiplexer(MUX) 2x1 with yosys*

**Recap:** ....

**yosys**
*  Takes **RTL design** and **liberty file - .lib** as **inputs**
*  Synthesizes the RTL design into netlist, the gate level representation of the RTL design.

**ABC**
*  Maps the netlist to technology specific standard cells **liberty file - .lib**.

**Grapeviz**
*  The synthesized design can be viewed on grapeviz
  
#### 1. Invoke Yosys:   
```
$ cd verilog_files   ...pwd path becomes ~/Desktop/VLSI_PK/sky130RTLDesignAndSynthesisWorkshop/verilog_files
$ yosys       .... invokes yosys tool
```

#### 2. Reading sky130 standard library :
```
yosys> read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib  
```
	read_liberty : It reads cells from liberty file as modules into current design.
	-lib: Creates empty blackbox modules.
	~/Desktop/VLSI_PK/sky130RTLDesignAndSynthesisWorkshop/lib/   ..path to the liberty file 
__________________________________

	1) ~/Desktop/VLSI_PK/sky130RTLDesignAndSynthesisWorkshop/verilog_files is the pwd
	2) ../ moves the directory one step up 
	3) Thus takes the path to ~/Desktop/VLSI_PK/sky130RTLDesignAndSynthesisWorkshop 
	       
#### 3. Read the RTL design(verilog file):
```
yosys> read_verilog good_mux.v  
```
**read_verilog :** This command is used to read the verilog desgin file. 
It load modules from a Verilog file to the current design.


#### 4. Synthesize the top level module:
```
yosys> synth -top good_mux  
```
**synth :** This command runs the default synthesis script. This command does not operate on partly selected designs.

**-top <module> :** This option use the specified module as top module (default='top'). - "good_mux"

![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_1/Assets/Screenshot_2026-10-03_15-38-18%20yosys%20synth.png)
	
#### 5. Technology Mapping to standard library 
```
yosys> abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```
	The abc pass invokes ABC tool to perform technology mapping, converting Yosys’s internal gate representation (netlist)
	into cells from the target architecture (standard cell sky130_fd_sc_hd__tt_025C_1v80.lib). 

```
abc → Calls the ABC tool from inside Yosys. ABC specializes in logic optimization and mapping.
-liberty → Specifies the standard cell library (in Liberty .lib format) to be used for mapping.
../lib/sky130_fd_sc_hd__tt_025C_1v80.lib → Path to the Sky130 standard cell library file.
		sky130_fd_sc_hd → High‑density standard cell library.
		tt_025C_1v80 → “Typical‑typical” corner, at 25 °C and 1.8 V supply. 
		....This defines timing and power characteristics under typical operating conditions....
```
**NOTE:** The path of the sky130_fd_sc_hd__tt_025C_1v80.lib   should match with the path where the file is saved in the system
![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_1/Assets/Screenshot_2026-10-03_15-40-15%20yosys%20abc.png)


#### 6. To view the result as a graphviz use the below command
```
yosys> show
``` 
**Show :** It creates graphviz DOT file for the selected part of the design and compile it to a graphics file (usually SVG or PostScript).
![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_1/Assets/Screenshot_2026-10-03_15-42-47%20yosys%20show.png)
![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_1/Assets/Screenshot_2026-09-30_18-52-21.png)

#### 7. To write the netlist in a .v file
yosys> write_verilog -noattr <filename.v>

##  TO CHECK IF IT IS TO BE DONE
![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_1/Assets/Screenshot_2026-10-03_15-48-01%20yosys%20write%20exit.png)
![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_1/Assets/Screenshot_2026-10-03_15-48-44%20good_mux_netlist1.png)


### SUMMARY of Commands

#### Installation
```
$ cd Desktop
$ mkdir VLSI_PK
$ gitclone https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop.git
$ sudo apt install iverilog
$ sudo apt install gtkwave
$ cd sky130RTLDesignAndSynthesisWorkshop
$ cd verilog_files

```
#### Simulation 
```
$ iverilog good_mux.v tb_good_mux.v
$ ./a.out
$ gtkwave tb_good_mux.vcd
```

#### Synthesis
```
$ yosys
yosys> read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
yosys> read_verilog good_mux.v
yosys> synth -top good_mux
yosys> abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lb
yosys> show
yosys> write_verilog -noattr <filename.v>
```
