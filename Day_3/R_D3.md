
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

Synthesis & optimization commands: exmaple - opt_Check4.v 
```
yosys> read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
yosys> read_verilog opt_check4.v
yosys> synth -top opt_check4
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

<img width="650" height="450" alt="Comb_yosys_opt" src="https://github.com/user-attachments/assets/7154dec8-ecdc-4343-a37d-36f3c6ddd4fa" />

#### --- Sequential Logic Optimizations ---
To understand optimization with yosys, lets take an exmaple of dff_const5.v :
In the below circuit we can see that the circuit obatained after synthesis and optimization is similar to what we expected as per RTL code. Thus in this case no optimization is possible. 

<img width="650" height="400" alt="Sequential_yosys_opt" src="https://github.com/user-attachments/assets/b8e7f12a-78de-4786-8db1-b421cb6b1e60" />

### 3.5 Sequential optimizations for unused outputs:
##### Example: Counter Optimization in Synthesis

At first glance, the code appears to describe a **3-bit counter**, so one might expect three flip-flops after synthesis.  

However, Examing closely:
- After reset, the value of `count` is `000`.
- On each positive clock edge, `count` increments.
- The output `q` simply follows `count[0]` (the least significant bit).

### Key Insight
Since only `count[0]` is used in the design:
- The synthesizer recognizes that higher bits of `count` are **unused**.
- The optimized circuit infers **only one flip-flop**.
- The output of that flip-flop (`Q`) corresponds directly to `count[0]`.
- The input to the flip-flop is the **complement of its output**, effectively creating a **toggle flip-flop**.

### Result
- **Expected (naïve view):** 3 flip-flops for a 3-bit counter.  
- **Actual (optimized):** 1 flip-flop, functioning as a toggle, with `Q = count[0]`.  

Thus we can say that, the logic which is no way related to primary output will be optimized by the synthesis tool on executing the **opt_clean -purge** command.

<img width="600" height="400" alt="Sequential_unused_output_opt" src="https://github.com/user-attachments/assets/335e1a66-5b3c-447e-80c7-ab7c370373cd" />



