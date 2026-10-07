## RTL Design and Synthesis Workshop (VSD)

This repository is for the RTL Design And Synthesis Workshop Using Sky130 conducted by VLSI System Design for 10 days. 

Batch : Sep 28, 2026 - Oct 7, 2026 

### Support Repository from VLSI System Design
- Disk image with pre-installed open source tools
- ***https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop.git*** - github repository containing Verilog RTL design files, testbenches, synthesis scripts, sky130 PDK to clone

**Technology used:** *Sky130 PDKs - sky130_fd_sc_hd.v, sky130_fd_sc_hd__tt_025C_1v80.v.*

### TOOLS worked on
- Icarus Verilog : HDL Language - verilog for RTL simulation
- GTKWAVE - to observe simulated waveforms
- YOSYS - To synthesize the design
- ABC - integrated in yosys for technology mapping
- GRAPHVIZ - to observe synthesized and technology mapped hardware schematic 
 
### Overview of My Work
Simulation and Synthesis outputs developed during the **Sky130 RTL Design and Synthesis Workshop**.  
The focus is on understanding:
- RTL coding styles and their impact on synthesis.
- Latch inferences and synthesis–simulation mismatches
- Case vs. if constructs
- Impact of for and Loop constructs on coding styles
- Neat, readable, functional and scalable coding practices.
- Scalable hardware replication using `for-generate`
- Gate-Level Simulation (GLS) for functional verification
- OPTIMIZATION - to improve synthesized hardware

---
***Day wise work is documented in respective day_wise folders***

---

### Directory Structure as generated on git cloning the repository in my VLSI_PK folder

```text
VLSI_PK/
└── sky130RTLDesignAndSynthesisWorkshop/
    ├── verilog_files/          # RTL modules and testbenches
    │   ├── bad_case_net.v
    │   ├── mux_generate.v
    │   ├── tb_diff_const3.v
    │   ├── tb_up_dn_cntr.v
    │   ├── ternary_operator_mux.v
    │   └── ... (other .v files)
    ├── lib/                    # Technology library
    │   └── sky130_fd_sc_hd__tt_025C_1v80.lib
    ├── myLib/                  # Standard cell models
    │   └── verilog_model/
    │       ├── sky130_fd_sc_hd
    │       └── primitives.v
    └── simulation_outputs/     # Waveform dumps (.vcd)
```

_____________________________________________________
#### *Acknowledgment*

I would like to express my sincere gratitude to:

- **VSD (VLSI System Design)** for organizing the *Sky130 RTL Design and Synthesis Workshop* and providing hands-on learning resources.  
- **SkyWater Technology Foundry** for making the **SKY130 PDK** openly available, enabling practical exploration of RTL-to-GDS design flow.  
- **Open-source tool developers** of **Yosys, Icarus Verilog, and GTKWave**, whose contributions make digital design education accessible to all.  
- **Mentors and peers** who offered guidance, discussions, and constructive feedback during the workshop.  
- The broader **open-source hardware community**, whose collaborative spirit continues to inspire innovation in VLSI design.

***This work is a small step in the journey of learning and contributing to the open-source semiconductor ecosystem.***
_______________________________________________________________
