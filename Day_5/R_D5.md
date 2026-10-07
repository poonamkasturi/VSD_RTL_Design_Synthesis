### 🔰 DAY 5: CONDITIONAL and LOOP STATEMENTS -----

#### 🔰 TOPICS  EXPLORED    -----
5.1 [CONDITIONAL statements](#CONDITIONAL-Statements) 
  * [if-eLse Statement](#If-Else-Statement)
	* **D5Lab17** - if-else ---incomplete if (*incomp_if.v*)
	* **D5Lab18** - nested if-else ---incomplete if (*incomp_if2.v*)
  * [case Statement](#CASE-Statement)
  	* **D5Lab19** - complete case (*comp_case.v*)
    * [Caveats with CASE statements](#Caveats-with-CASE-statements)
	    * **D5Lab20** - Incomplete case Assignment (*incomp_case.v*)
	    * **D5Lab21** - Partial case Assignment  (*partial_case_assign.v*)
	    * **D5Lab22** - Overlapping case Assignment  (*bad_case.v*)
5.2 [LOOP Statements](#LOOP-Statements)
  * [for loop Statement](#For-loop-Statement)
	  * **D5Lab23** - for Loop 4x1 MUX  (*mux_generate.v)
	  * **D5Lab24** - for Loop 1x8 DeMUX (*demux_generate.v)
	  * **D5Lab25** - case 1x8 DeMUX (*demux_case.v*)
  * [for generate Statement](#For-generate-Statement)
	  * **D5Lab26** - RCA 8bit  (*rca.v*)

---
## 5.1 CONDITIONAL Statements ----------------------------
'If' and 'case' statements are conditional statements used inside the 'always' block, 
Variables to which output value will be assigned in both statements should be a register variable.

### if-else Statement  *****************************************

-  In Verilog, **if-else** statements are used to implement conditional logic
-  All 'if-else' statements are **mutually exclusive** - Typically synthesizing into **priority logic** (mux chains). 
-  They are essential for both combinational and sequential circuits
  
- `if` constructs must be written carefully to avoid **unintended latch** inference.
- The main reason for inferred unintended latches is **"incomplete if statements"**.
_________________________________________

### *D5Lab17 - Incomplete if statement ---- incomp_if.v : UNINTENDED LATCH Inference* ...................... 

RTL Design

```verilog
module incomp_if (input i0 , input i1 , input i2 , output reg y);
always @ (*)
   begin
       if(i0)
           y <= i1;
   end
endmodule
```

<p></p>
RTL Simulation - Pre Synthesis Simulation
<p></p>

![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_5/Assets_D5/Screenshot%202026-10-01%20162845%20D5Lab1%20incomp_if%20gtk.png)

#### OBSERVATIONs
- Latch inferred instead of a MUX

##### What Happened
-  When i0 is 1, y is assigned the value of i1.
-  When i0 is 0, the code does not specify what y should be.
-  The synthesis tool assumes that y must retain its previous value, which implies a latch is inferred.

***This is unintended, because the designer meant to create a combinational circuit, not a sequential element.***

Due to bad coding style (incomplete if statement), synthesis tool infers a latch.

BEST PRACTICE : Always provide a complete assignment for all conditions - include an else or default assignment to avoid latch inference.

---

<p></p>
SYNTHEZED SCHEMATIC :
<p></p>

#### ***Confirms Latch inferred***

![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_5/Assets_D5/Screenshot%202026-10-01%20163317%20D5Lab1%20incomp_if%20synth.png)

### *D5Lab18 - Nested if-else - incomplete if statement ---- incomp_if2.v : UNINTENDED LATCH Inference* ...................... 

RTL DESIGN

```verilog
module incomp_if2 (input i0 , input i1 , input i2 , input i3, output reg y);
always @ (*)
begin
	if(i0)
		y <= i1;
	else if (i2)
		y <= i3;

end
endmodule
```
#####  ***  UNDERSTANDING THE RTL DESIGN  ***

Impact of the Incomplete if Statement
-	When i0 = 1, the output y follows i1.
-	When i0 = 0 and i2 = 1, the output y follows i3.
-	When i0 = 0 and i2 = 0, there is no assignment to y.
-	No Assignment to y for all other possible combinations of values of i0, i1, i2 and i3

The tool interprets this as: **retain the previous value of y**............
This behavior corresponds to a **latch** being inferred.

#####  ***  EXAMINING THE SIMULATED BEHAVIOUR  ***

RTL Simulation Waveform Observation
-	In the simulation, y updates correctly when i0 or i2 are active.
-	But when both are inactive (0), y does not go to a defined value (like 0 or X).
-	Instead, y holds its last value, which is exactly the behavior of a latch.

#### *The simulated behaviour of the design - clear evidence of latch inference due to incomplete if.*

<p></p>
RTL Simulation - Pre Synthesis Simulation
<p></p>

![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_5/Assets_D5/Screenshot%202026-10-01%20163602%20D5Lab2%20incomp_if2%20gtk_expand.png)

<p></p>
SYNTHESIZED SCHEMATIC
<p></p>

#### ***Confirms Latch inferred***

![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_5/Assets_D5/Screenshot%202026-10-01%20163730%20D5Lab2%20incomp_if2%20synth.png)


________________________________________________________
		  
### case Statement   *****************************************

- The `case` statement checks if the given expression matches one of the listed values.  
- It branches accordingly and typically infers a **multiplexer (MUX)**.  
- Best suited when multiple conditions are **mutually exclusive** and parallel.

#### COMPARISON BETWEEN if-else and case CONSTRUCTS
______________________________________________________________

| Feature | ``if-else`` (Priority Logic) | ``case`` (Parallel Logic) |
| --- | --- | --- |
| Hardware mapping | Priority-encoded mux chain | Parallel mux structure |
| Best for | Conditions with hierarchy | FSMs, opcode decoding |
| Risk | Deep chains → timing issues | Cleaner timing, less nesting |
| Example | Priority encoder | ALU operation selector |

### *D5Lab19 - case - Completely Defined ---- comp_case.v : MUX Inferred* ......................   

```verilog
module comp_case (input i0 , input i1 , input i2 , input [1:0] sel, output reg y);
always @ (*)
begin
	case(sel)
		2'b00 : y = i0;
		2'b01 : y = i1;
		default : y = i2;
	endcase
end
endmodule
```

#####  ***  UNDERSTANDING THE RTL DESIGN  ***

Impact of Completely Defined case Assignment
- sel = 00, y = i0.  
- sel = 01, y = i1.  
- sel = 10 or 11, y = i2  


#####  ***  EXAMINING THE SIMULATED BEHAVIOUR  ***
RTL Simulation Waveform Observation
-	waveforms conform with the RTL analysis

<p></p>
RTL Simulation - Pre Synthesis Simulation
<p></p>

![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_5/Assets_D5/Screenshot_2026-10-05_13-24-37%20comp_case%20gtk.png)

<p></p>
SYNTHESIZED SCHEMATIC
<p></p>

#### ***Confirms output is assigned a defined value for all the sel input options***
![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_5/Assets_D5/Screenshot_2026-10-05_13-27-45%20comp_case%20synth.png)

********************************
---

### Caveats with CASE statements:
- Incomplete case Assignment
- Partial case Assignment
- Overlapping case Assignment
___________________________________

### * *Incomplete case Assignment :*   ***************************************

### *D5Lab20 - case - Incompletely Defined ---- incomp_case.v :  LATCH inferred* ......................  

RTL Design

```verilog
module incomp_case (input i0 , input i1 , input i2 , input [1:0] sel, output reg y);
always @ (*)
begin
	case(sel)
		2'b00 : y = i0;
		2'b01 : y = i1;
	endcase
end
endmodule
```

#####  ***  UNDERSTANDING THE RTL DESIGN  ***

Impact of the incomplete case Assignment
- In the RTL code, not all possible cases for the 2‑bit `sel` are defined.  
- Specifically, when `sel = 10` or `sel = 11`, the output `y` is left **undefined**.  
- The synthesis tool interprets this as: *retain the previous value of `y`*.  
- This behavior corresponds to a **latch** being inferred.

#####  ***  EXAMINING THE SIMULATED BEHAVIOUR - RTL ***

RTL Simulation Waveform Observation
- In RTL simulation, when `sel[1] = 1` (i.e., `sel = 10` or `11`), the output `y` does not change according to the inputs.  
- Instead, `y` **holds its last value** - latch behavior.  
- The waveform confirms that the output is not updating as expected for these cases.

#### *The simulated behaviour of the design - clear evidence of latch inference due to incomplete case assignment.*

<p></p>
RTL Simulation - Pre Synthesis Simulation
<p></p>

![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_5/Assets_D5/Screenshot_2026-10-05_13-31-47%20incomp_case%20gtk.png)

<p></p>
SYNTHESIZED SCHEMATIC
<p></p>

#### ***Confirms Latch inferred for incomplete case assignment***
- This matches the simulation behavior, reinforcing that the incomplete specification leads to unintended sequential elements.

![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_5/Assets_D5/Screenshot_2026-10-05_13-33-24%20incomp_case%20synth.png)


### * *Partial case Assignment :* **************************************

### *D5Lab21 - case - Partially Defined ---- partial_case.v :  LATCH inferred* ......................  

RTL Design

```verilog
module partial_case_assign (input i0 , input i1 , input i2 , input [1:0] sel, output reg y , output reg x);
always @ (*)
begin
	case(sel)
		2'b00 : begin
			y = i0;
			x = i2;
			end
		2'b01 : y = i1;
		default : begin
		           x = i1;
			       y = i2;
			  end
	endcase
end
endmodule
```

#####  ***  UNDERSTANDING THE RTL DESIGN  ***

Impact of Partial case Assignment
- From the RTL code, the output **x** is not assigned any value when `sel = 01`.  
- Meanwhile, the output **y** behaves as a proper **multiplexer**, which matches the intended design.  


#####  ***  EXAMINING THE SIMULATED BEHAVIOUR - RTL ***
RTL Simulation Waveform Observation
- Waveform shows that **x** infers a **latch** for `sel = 01`.  
- Meanwhile, the output **y** behaves as a proper **multiplexer**, which matches the intended design.  
- Thus, latching behavior is inferred **only** for **x**, **not** for **y**.

<p></p>
RTL Simulation - Pre Synthesis Simulation
<p></p>

![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_5/Assets_D5/Screenshot_2026-10-05_13-37-10%20partial_case_assign%20gtk.png)
![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_5/Assets_D5/Screenshot_2026-10-05_13-38-04%20partial_case_assign%20gtk_expand.png)

<p></p>
SYNTHESIZED SCHEMATIC
<p></p>

#### ***Confirms that tool has inferred a latch for `x only` for partial case assignment***
- This matches the simulation behavior, reinforcing that the partial specification leads to unintended sequential elements.

![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_5/Assets_D5/Screenshot_2026-10-05_13-39-53%20partial_case_assign%20synth.png)

		  
### * *Overlapping case Assignment :* **************************************

### *D5Lab22 - case - Overlapping ---- bad_case.v :  LATCH inferred* ......................  

RTL Design

```verilog
module bad_case (input i0 , input i1, input i2, input i3 , input [1:0] sel, output reg y);
always @(*)
begin
	case(sel)
		2'b00: y = i0;
		2'b01: y = i1;
		2'b10: y = i2;
		2'b1?: y = i3;
		//2'b11: y = i3;
	endcase
end

endmodule
```

#####  ***  UNDERSTANDING THE RTL DESIGN  ***

Impact of Overlapping case Assignment
- In the RTL code, the last case statement uses the condition **sel = 2'b1?**.  
- This overlaps with the case **sel = 2'b10**.  
- Such overlapping cases confuse the simulator, leading to **synthesis–simulation mismatch** 
- Different simulators may show different behavior for overlapping cases, resulting in **unpredictable outputs**.

#####  ***  EXAMINING THE SIMULATED BEHAVIOUR - RTL ***
RTL Simulation Waveform Observation
- Waveform shows a latching behaviour - **unpredictable output**.  

<p></p>
RTL Simulation - Pre Synthesis Simulation - "simulation - synthesis mismatch"
<p></p>

***- RTL Simulation shows latching behaviour***
![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_5/Assets_D5/Screenshot%202026-10-01%20164909%20D5Lab4%20bad_case%20gtk.png)

<p></p>
GLS - Post Synthesis Simulation
<p></p>

***- Gate-Level Simulation (GLS) shows the **correct output behavior** as expected.***

![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_5/Assets_D5/Screenshot_2026-10-05_13-50-03%20bad_case%20gtk_GLS.png)

<p></p>
SYNTHESIZED SCHEMATIC 
<p></p>

##### *- **no latch is inferred** for the overlapping case.*  
NOTE : It is a bad coding style as **'simulation - synthesis mismatch'** happens.
  
![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_5/Assets_D5/Screenshot_2026-10-05_13-44-16%20bad_case%20synth.png)

---
#### ***********  Key Insight  ************

-	Combinational circuits must define outputs for all input conditions.
-	In a true combinational circuit, latches should never be inferred.
-	Avoid deep `if` nesting; use case statements for cleaner parallel logic.

***To avoid inferring latches*** 
-	if-else staements should be completely specified
-	use case statements with 'default' case in the code
-	always assign **all outputs in all segments** of the case statement:
-	However, using defalut case would not always avoid inferring latch in case of 'partial assignment cases'.
-	all the conditional statements of case construct should be mutually exclusive to avoid synthesis-simulation mismatch

 _______________________________________________
 _______________________________________________

## 5.2 Loop Statements:--------------------
_____________________________________________________

### for loop :************************************************
* It is used inside the 'always' block.
* It is used for evaluating expressions.
* For loop is not used for instantiating hardware, gates.

### *D5Lab23 - for Loop  - ---- mux_generate.v :  4x1 MUX* ...................... 

RTL Design
```verilog
module mux_generate (input i0 , input i1, input i2 , input i3 , input [1:0] sel  , output reg y);
wire [3:0] i_int;
assign i_int = {i3,i2,i1,i0};
integer k;
always @ (*)
begin
	for(k = 0; k < 4; k=k+1)
		begin
			if(k == sel)
			y = i_int[k];
		end
end
endmodule
```

#####  ***  UNDERSTANDING THE RTL DESIGN  ***

Impact of for loop construct
- This example demonstrates a **small-size multiplexer** implementation.  
- To build a **larger multiplexer**, the same code structure can be reused.
- Only the **input bus size** and the **number of loop iterations** need to be adjusted.
- incomplete if will infer a latch - **INTENDED latch** to hold the mux output.
- @ (*) ensures that the always block is triggered and re‑executed whenever an event occurs on any of the input signals.
- This captures the combinational logic nature of a mux.
- Using `case` statements for small MUXes is manageable, but as the size increases, the code becomes **cumbersome**.
- A **`for` loop** provides a concise and scalable way

#####  ***  EXAMINING THE SIMULATED BEHAVIOUR - RTL and GLS ***
- The **RTL functional simulation** and the synthesized netlist **GLS** show **matching output waveforms**, confirming correctness.

<p></p>
RTL Simulation - Pre Synthesis Simulation
<p></p>

![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_5/Assets_D5/Screenshot%202026-10-01%20173453%20D5Lab6%20mux_generate%20gtkwave.png)

<p></p>
GLS - Post Synthesis Simulation
<p></p>

![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_5/Assets_D5/Screenshot_2026-10-05_17-39-08%20mux_generate%20gtk_GLS.png)

<p></p>
SYNTHESIZED SCHEMATIC 
<p></p>

#### ***Confirms the inference of an INTENDED latch***
![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_5/Assets_D5/Screenshot%202026-10-01%20175000%20D5Lab6%20mux_generate%20synth%20w_purge.png)

### *D5Lab24 - for Loop  - ---- demux_generate.v :  1x8 DEMUX* ...................... 

RTL Design

```verilog
module demux_generate (output o0 , output o1, output o2 , output o3, output o4, output o5, output o6 , output o7 , input [2:0] sel  , input i);
reg [7:0]y_int;
assign {o7,o6,o5,o4,o3,o2,o1,o0} = y_int;
integer k;
always @ (*)
begin
	y_int = 8'b0;
	for(k = 0; k < 8; k++)
	begin
		if(k == sel)
		   y_int[k] = i;
	end
end
endmodule
```

<p></p>
RTL Simulation - Pre Synthesis Simulation
<p></p>

![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_5/Assets_D5/Screenshot%202026-10-01%20180910%20D5Lab8%20demux_generate%20for-loop%20gtk.png)

<p></p>
GLS - Post Synthesis Simulation
<p></p>

![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_5/Assets_D5/Screenshot_2026-10-05_17-53-04%20demux_generate%20gtk_GLS.png)

<p></p>
SYNTHESIZED SCHEMATIC
<p></p>

#### ***Comparison of the Synthesized schematics with and without optimization Confirms the removal of unnecessary and internal logic***
**Synthesized schematic without optimization**
![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_5/Assets_D5/Screenshot%202026-10-02%20144859%20D5Lab8%20demux_generate%201x8%20synth%20wo_purge.png)

**Synthesized schematic with optimization  `opt_clean -purge`**
![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_5/Assets_D5/Screenshot%202026-10-02%20144716%20D5Lab8%20demux_generate%201x8%20synth%20w_purge.png)


### *D5Lab25 - case  - ---- demux_case.v :  1x8 DEMUX* ...................... 

RTL Design

```verilog
module demux_case (output o0 , output o1, output o2 , output o3, output o4, output o5, output o6 , output o7 , input [2:0] sel  , input i);
reg [7:0]y_int;
assign {o7,o6,o5,o4,o3,o2,o1,o0} = y_int;
integer k;
always @ (*)
begin
y_int = 8'b0;
	case(sel)
		3'b000 : y_int[0] = i;
		3'b001 : y_int[1] = i;
		3'b010 : y_int[2] = i;
		3'b011 : y_int[3] = i;
		3'b100 : y_int[4] = i;
		3'b101 : y_int[5] = i;
		3'b110 : y_int[6] = i;
		3'b111 : y_int[7] = i;
	endcase

end
endmodule
```

<p></p>
RTL Simulation - Pre Synthesis Simulation
<p></p>

![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_5/Assets_D5/Screenshot%202026-10-01%20175506%20D5Lab7%20demux_case%20gtk.png)

<p></p>
GLS - Post Synthesis Simulation
<p></p>

![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_5/Assets_D5/Screenshot_2026-10-05_17-49-12%20demux_case%20gtk_GLS.png)

<p></p>
SYNTHESIZED SCHEMATIC
<p></p>

#### ***Comparison of the Synthesized schematics with and without optimization Confirms the removal of unnecessary and internal logic***
**Synthesized schematic without optimization**
![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_5/Assets_D5/Screenshot%202026-10-01%20175828%20D5Lab7%20demux_case%20synth%20wo_purge.png)

**Synthesized schematic with optimization  `opt_clean -purge`**
![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_5/Assets_D5/Screenshot%202026-10-01%20175623%20D5Lab7%20demux_case%20synth%20w_purge.png)

#### *NOTE: It is observed from D5Lab23 and D5Lab24 - the functionality as well as Synthesized Design (netlist) achieved with both `for` loop and `case` construct are identical

### for-generate :***************************************
* It is used outside the 'always' block.
* It can not be used inside 'always' block.		  
* It is used for instantiating hardware multiple times.

### *D5Lab26 - for-generate  - ---- rca.v :  8 bit Ripple Carry Adder* ...................... GLS NEEDED

RTL Design

```verilog
module rca (input [7:0] num1 , input [7:0] num2 , output [8:0] sum);
wire [7:0] int_sum;
wire [7:0] int_co;

genvar i;
generate
	for (i = 1 ; i < 8; i=i+1) begin
		fa u_fa_1 (.a(num1[i]),.b(num2[i]),.c(int_co[i-1]),.co(int_co[i]),.sum(int_sum[i]));
	end

endgenerate
fa u_fa_0 (.a(num1[0]),.b(num2[0]),.c(1'b0),.co(int_co[0]),.sum(int_sum[0]));

assign sum[7:0] = int_sum;
assign sum[8] = int_co[7];
endmodule
```

***1 bit Full Adder module instantiated in 8 bit RCA***

```verilog
module fa (input a , input b , input c, output co , output sum);
	assign {co,sum}  = a + b + c ;
endmodule
```

#####  ***  UNDERSTANDING THE RTL DESIGN  ***
Impact of for-generate construct
- The `for-generate` statement allows easy **replication of hardware structures**.  
- When the same hardware needs to be repeated many times, `for-generate` avoids the **cumbersome manual instantiation** for large designs. 

##### *****   Advantage of for-generate construct   *****
- **Scalability:** Simply change the **bus size** and the **loop count** to build larger adders.  
- **Maintainability:** Code remains concise and easy to modify.  
- **Efficiency:** Reduces repetitive coding and potential errors.

#####  ***  EXAMINING THE SIMULATED BEHAVIOUR - RTL and GLS ***
- The RTL functional simulation and the synthesized netlist GLS show **similar output behavior**, confirming correctness.  
- The code implements an **8-bit ripple carry adder** using the `for-generate` construct.

<p></p>
RTL Simulation - Pre Synthesis Simulation
<p></p>

![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_5/Assets_D5/Screenshot%202026-10-02%20145317%20D5Lab9%20rca%20gtk.png)

***Addition of 8 bit data***
***Possibility 1 - carry out from the most significant bit is 0 ...........DATA represented in HEX notation*** 
![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_5/Assets_D5/Screenshot%202026-10-02%20151429%20D5Lab9%20rca%20gtk%20carry_0_Hex.png)

***Possibility 2 - carry out from the most significant bit is 1 ...........DATA represented in HEX notation*** 
![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_5/Assets_D5/Screenshot%202026-10-02%20151614%20D5Lab9%20rca%20gtk%20carry_1_Hex.png)

***Possibility 2 - carry out from the most significant bit is 1 ...........DATA represented in DECIMAL notation*** 
![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_5/Assets_D5/Screenshot%202026-10-02%20151657%20D5Lab9%20rca%20gtk%20carry_1_Decimal.png)


<p></p>
GLS - Post Synthesis Simulation
<p></p>

![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_5/Assets_D5/Screenshot_2026-10-05_19-09-52%20rca%20gtk_GLS.png)

***Addition of 8 bit data***
***Possibility 1 - carry out from the most significant bit is 0 ...........DATA represented in HEX notation*** 
![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_5/Assets_D5/Screenshot_2026-10-05_19-14-20%20rca%20gtk_GLS%20carry_0_Hex%20.png)



<p></p>
SYNTHESIZED SCHEMATIC
<p></p>

***RCA - schematic with instantiated blocks of fa*** ..............................................

![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_5/Assets_D5/Screenshot%202026-10-02%20152427%20D5Lab9%20rca%20synth_rca.png)

***fa - schematic of the block instantiated in the top module***................................................

![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_5/Assets_D5/Screenshot%202026-10-02%20152529%20D5Lab9%20rca%20synth_fa.png)



Commands used 
```
$ yosys
yosys> read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
yosys> read_verilog rca.v fa.v ................... INSTANTIATED MODULE ALSO NEEDS TO BE READ
yosys> synth -top rca ............................ To synthesize only the TOP module needs to be specified
yosys> abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
yosys> show rca   ................................  will show the specified module (Top Module rca) with instantiated modules as blocks in Graphviz window
yosys> show fa    ................................  will show the specified module (only the instantiated module fa) in Graphviz window
```

##### POINTS WORTH NOTING
-	INSTANTIATED MODULE ALSO NEEDS TO BE READ
-	only the TOP module needs to be specified for synthesis 
-	Specific module name needs to be mentioned to show it in the graphviz window


![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_5/Assets_D5/Screenshot%202026-10-02%20152735%20D5Lab9%20terminal1.png)

![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_5/Assets_D5/Screenshot%202026-10-02%20152851%20D5Lab9%20terminal2.png)

![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_5/Assets_D5/Screenshot%202026-10-02%20153013%20D5Lab9%20terminal3.png)

![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_5/Assets_D5/Screenshot%202026-10-02%20153116%20D5Lab9%20terminal4%20show-rca.png)

![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_5/Assets_D5/Screenshot%202026-10-02%20153219%20D5Lab9%20terminal4%20show-fa.png)


