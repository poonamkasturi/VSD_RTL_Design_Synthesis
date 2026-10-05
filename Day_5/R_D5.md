### 🔰 DAY 5: CONDITIONAL and LOOP STATEMENTS -----

#### 🔰 TOPICS  EXPLORED    -----
5.1 [CONDITIONAL statements](#CONDITIONAL-Statements) 
  * [if-eLse Statement](#If-Else-Statement)
  * [case Statement](#CASE-Statement)
    * [Caveats with CASE statements](#Caveats-with-CASE-statements)

5.2 [LOOP Statements](#LOOP-Statements)
  * [for loop Statement](#For-loop-Statement)
  * [for generate Statement](#For-generate-Statement)

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

### *D5Lab16 - Incomplete if statement ---- incomp_if.v : UNINTENDED LATCH Inference* ...................... 

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

### *D5Lab17 - Nested if-else - incomplete if statement ---- incomp_if2.v : UNINTENDED LATCH Inference* ...................... 

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

🔹 RTL Simulation Waveform Observation
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

### *D5Lab18 - case - Completely Defined ---- comp_case.v : MUX Inferred* ......................   GTK SYNTH OUTPUTS REQUIRED

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

![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_5/Assets_D5/Screenshot_2026-10-05_13-24-37%20comp_case%20gtk.png)
![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_5/Assets_D5/Screenshot_2026-10-05_13-27-45%20comp_case%20synth.png)

********************************
---

### Caveats with CASE statements:
	- Incomplete case Assignment
	- Partial case Assignment
	- Overlapping case Assignment
___________________________________

### * *Incomplete case Assignment :*   ***************************************

### *D5Lab19 - case - Incompletely Defined ---- incomp_case.v :  LATCH inferred* ......................  GTK SYNTH OUTPUTS REQUIRED

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

#####  ***  EXAMINING THE SIMULATED BEHAVIOUR  ***

RTL Simulation Waveform Observation
- In RTL simulation, when `sel[1] = 1` (i.e., `sel = 10` or `11`), the output `y` does not change according to the inputs.  
- Instead, `y` **holds its last value**, which is clear evidence of latch behavior.  
- The waveform confirms that the output is not updating as expected for these cases.

#### *The simulated behaviour of the design - clear evidence of latch inference due to incomplete case.*

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

### *D5Lab20 - case - Partially Defined ---- partial_case.v :  LATCH inferred* ......................  SYNTH OUTPUTS REQUIRED

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
- In simulation, the waveform shows that **x** infers a **latch** for `sel = 01`.  
- Meanwhile, the output **y** behaves as a proper **multiplexer**, which matches the intended design.  
- Thus, latching behavior is inferred **only** for **x**, **not** for **y**.

#####  ***  EXAMINING THE SIMULATED BEHAVIOUR  ***
RTL Simulation Waveform Observation
- 

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

### *D5Lab21 - case - Overlapping ---- bad_case.v :  LATCH inferred* ......................  

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

Impact of Partial case Assignment
- In the RTL code, the last case statement uses the condition **sel = 2'b1?**.  
- This overlaps with the case **sel = 2'b10**.  
- Such overlapping cases confuse the simulator, leading to **synthesis–simulation mismatch** 
- Different simulators may show different behavior for overlapping cases, resulting in **unpredictable outputs**.

<p></p>
RTL Simulation - Pre Synthesis Simulation
<p></p>

![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_5/Assets_D5/Screenshot%202026-10-01%20164909%20D5Lab4%20bad_case%20gtk.png)

<p></p>
SYNTHESIZED SCHEMATIC - NETLIST
<p></p>

##### - From the synthesized netlist, we can see that **no latch is inferred** for the overlapping case.  
- Gate-Level Simulation (GLS) shows the **correct output behavior** as expected.
  
![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_5/Assets_D5/Screenshot_2026-10-05_13-44-16%20bad_case%20synth.png)

<p></p>
GLS - POST SYNTHESIS
<p></p>

![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_5/Assets_D5/Screenshot_2026-10-05_13-50-03%20bad_case%20gtk_GLS.png)


**********************************************
**********************************************

> In a true combinational circuit, latches should never be inferred.
---
#### *Key Insight - Combinational circuits must define outputs for all input conditions.*
---
### 🔹 Best Practice
- For a **large number of conditions**, prefer `case` over `if-else`.  
- `case` produces cleaner parallel logic, while `if-else` can lead to deeper chains and timing issues.  

### 🔹 Summary
- **`if-else` → Priority logic (priority encoder)**  
- **`case` → Parallel logic (multiplexer)**

Avoid deep `if` nesting; use case statements for cleaner parallel logic.
To avoid inferring latches with case, use case statements with 'default' case in the code, which will cover all other cases not mentioned in case statement. However, using defalut case would not always avoid inferring latch in case of 'partial assignment cases'.

Thus overlapping case statements create synthesis-simulation mismatch
To avoid this, all the conditional statements of case construct should be mutually exclusive which is a correct way of coding.

### Best Practice partial case
To avoid inferring latches in such cases, always assign **all outputs in all segments** of the case statement:
  *********************************************************************
  *********************************************************************

## 5.3 For loop and For generate constructs :
### 5.3.1 For loop :
* It is used inside the 'always' block.
* It is used for evaluating expressions.
* For loop is not used for instantiating hardware, gates.

lets understand this with example "mux_generate.v": 

<img width="700" height="500" alt="For_loop_1" src="https://github.com/user-attachments/assets/2d80a14c-309f-4c13-bf1f-f9801f9f6558" />

<img width="700" height="500" alt="For_loop_2" src="https://github.com/user-attachments/assets/fee7a077-dfbd-41f7-a190-ddc29763f3a9" />




we can see that the ouput waveform for RTL code functional simulation and synthesized netlist GLS are same. This is implemetation of small size mux, but we can make large size mux simply using same code except that we only need to change the input bus size and number fo times loop runs.	If we use 'case' statements for same size mux, it will not be difficult but when we increase the size of MUX, the implementation using 'case' statements will become cumbersome which can be built using "for-loop " easily.


```verilog
module mux_generate (input i0 , input i1, input i2 , input i3 , input [1:0] sel  , output reg y);
wire [3:0] i_int;
assign i_int = {i3,i2,i1,i0};
integer k;
always @ (*)
begin
for(k = 0; k < 4; k=k+1) begin
	if(k == sel)
		y = i_int[k];
end
end
endmodule
```

```verilog
module demux_generate (output o0 , output o1, output o2 , output o3, output o4, output o5, output o6 , output o7 , input [2:0] sel  , input i);
reg [7:0]y_int;
assign {o7,o6,o5,o4,o3,o2,o1,o0} = y_int;
integer k;
always @ (*)
begin
y_int = 8'b0;
for(k = 0; k < 8; k++) begin
	if(k == sel)
		y_int[k] = i;
end
end
endmodule
```
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

```verilog
module rca (input [7:0] num1 , input [7:0] num2 , output [8:0] sum);
wire [7:0] int_sum;
wire [7:0]int_co;

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

```verilog
module fa (input a , input b , input c, output co , output sum);
	assign {co,sum}  = a + b + c ;
endmodule
```

		  
### 5.3.2 For generate :
* It is used outside the 'always' block.
* It can not be used inside 'always' block.		  
* It is used for instantiating hardware multiple times.

lets understand this with example "rca.v": 		  

<img width="700" height="500" alt="Generate_1" src="https://github.com/user-attachments/assets/1507027c-e2d3-4a82-9ca7-da05ab3d02bd" />

<img width="700" height="500" alt="Generate_2" src="https://github.com/user-attachments/assets/69b8061a-893f-44d0-bbd1-458cbfb0f3ce" />

<img width="700" height="500" alt="Generate_3" src="https://github.com/user-attachments/assets/c40e4d7c-6db3-4b6c-8280-2881cb53f4bf" />


The above images shows that the ouput waveform for RTL code functional simulation and synthesized netlist GLS show similar behaviour. Here the code has implemented 8-bit ripple carry adder using "for-generate" statement. Using "for-generate" helps us to replicate the hardware easily when we need same hardware to repeat large number of times, else we have to instantiate each hardware individually which will be cumbersome unlike number of times the hardware replication is required is small.




      *******************************
      ********************************
      *********************************
      Use non-blocking assignments (<=) inside clocked blocks for sequential logic.

For combinational blocks, always use always @(*) to ensure sensitivity to all inputs.

	🔹 Common Pitfalls
Missing sensitivity list → causes mismatches between RTL simulation and synthesized hardware.

Blocking assignments (=) in sequential logic → may cause simulation mismatches.

Uncovered conditions → synthesis infers latches if outputs are not assigned in all paths.


