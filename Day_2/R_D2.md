## Day 2: Timing Libraries, Synthesis Approaches, and Efficient Flip-Flop Coding

### Topics Explored:
1. Sky130 PDK - Understanding the .lib timing library (sky130_fd_sc_hd__tt_025C_1v80.lib)
2. Comparing hierarchical vs. flat synthesis methods.
3. Exploring efficient coding styles for flip-flops in RTL design.

### 2.1 SKY130 PDK - Understanding the .lib timing library (sky130_fd_sc_hd__tt_025C_1v80.lib)

The SKY130 Process Design Kit (PDK) is an open‑source resource built on SkyWater Technology’s 130 nm CMOS platform. It offers the fundamental models and libraries required for integrated circuit (IC) development, encompassing timing, power, and process variation data essential for accurate design and verification.

**Library name:**  
`sky130_fd_sc_hd__tt_025C_1v80.lib`

### Naming Convention (Glossary)
Specifies the **process**, **voltage**, and **temperature** conditions modeled by each library, along with the library type.

| **Component** | **Meaning** |
|---------------|-------------|
| **sky130**    | Refers to the SkyWater 130 nm CMOS process |
| **fd_sc**     | Foundry Standard Cell library |
| **hd**        | High Density variant (optimized for area efficiency) |
| **tt**        | Process Fabrication corner (e.g., `tt` = typical, `ff` = fast, `ss` = slow) |
| **025C** | Temperature - Operating condition (e.g., `025C` = 25 °C, `085C` = 85 °C) |
| **1v80**   | Voltage Supply level modeled (e.g., `1v80` = 1.80 V, `1v62` = 1.62 V) |

---
---

### D Flip-Flop with Asynchronous Reset

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

