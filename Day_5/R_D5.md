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
> In a true combinational circuit, latches should never be inferred.

BEST PRACTICE : Always provide a complete assignment for all conditions - include an else or default assignment to avoid latch inference.
Avoid deep `if` nesting; use case statements for cleaner parallel logic.

---

<p></p>
SYNTHEZED SCHEMATIC :
<p></p>

#### ***Confirms Latch inferred***

![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_5/Assets_D5/Screenshot%202026-10-01%20163317%20D5Lab1%20incomp_if%20synth.png)

### *D5Lab17 - Incomplete if statement ---- incomp_if2.v : UNINTENDED LATCH Inference* ...................... 

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


![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_5/Assets_D5/Screenshot%202026-10-01%20163543%20D5Lab2%20incomp_if2%20gtk.png)
![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_5/Assets_D5/Screenshot%202026-10-01%20163602%20D5Lab2%20incomp_if2%20gtk_expand.png)
![](https://github.com/poonamkasturi/VSD_RTL_Design_Synthesis/blob/main/Day_5/Assets_D5/Screenshot%202026-10-01%20163730%20D5Lab2%20incomp_if2%20synth.png)
		  
## 5.2 CASE statements
The case statement checks if the given expression matches one of the expressions in the list and branches accordingly. It typically infers MUX. In comparison to if statement, if there are many conditions to check ."if-else " construct may not be suitable and would synthesize the priority encoder instead of multiplexer. Thus, when large number conditions are required to check "case" is better option to choose.

### 5.2.1 Caveats with CASE statements:
* Incomplete case statement :
lets understand this caveat with exmaple "incomp_case.v".

<img width="700" height="500" alt="incomp_case" src="https://github.com/user-attachments/assets/2fe6e035-1577-4d1b-8748-47a3709a8b91" />



From the above image, we can see that all the possible cases for 2-bit 'sel' is not defined in RTL code, that shows incomplete case statement. Beacuse of this ,the output will infer latch in case when sel = 10, 11. So, we can also say when sel[1] is 1, there will be latching action which can be seen from RTL code simulation waveform. The output 'y' is not changing in accordance to input for sel= 10, 11. 
From the synthesized netlist also, we can find out that the synthesizer has inferred latch for the RTL code.

To avoid inferring latches with case, use case statements with 'default' case in the code, which will cover all other cases not mentioned in case statement. However, using defalut case would not always avoid inferring latch in case of 'partial assignment cases'.
		  
		  
* Partial case assignment :		  
lets understand this caveat with exmaple "partial_case_assign.v".

<img width="700" height="500" alt="Partial_case_1" src="https://github.com/user-attachments/assets/1ef32b0d-72db-42c1-9c02-639d00d19d5c" />

<img width="700" height="500" alt="Partial_case_2" src="https://github.com/user-attachments/assets/7e44dab6-65bb-44fa-9778-a023260f4f75" />





From the RTL code , we can find out that the output 'x' is not assigned any value for sel = 01 and from the simulation we can figure out that the output waveform of 'x' is inferring latch for sel = 01. While output 'y' is inferring mux which is expected as per code. Thus a latching behaviour is inferred for output 'x' only.

From the synthesized netlist also we can see that synthezier has inferred latch for output 'x' only. 
To avoid inferrin latches in such cases, assign all the outputs in all the segments of case statements.
		  
* Overlapping case assignment :
lets understand this caveat with exmaple "bad_case.v".

<img width="700" height="500" alt="baad_case_1" src="https://github.com/user-attachments/assets/d7de6837-29b1-4136-8b46-494bc4f8817e" />

<img width="700" height="500" alt="baad_case_2" src="https://github.com/user-attachments/assets/4b475e12-1972-4e8d-b729-c60d61bbf1f4" />




From the RTL code, we can see that last case statement has condition ```sel= 2'b1? ```. This case is overlapping case ``` sel = 2'b10 ```. Thus this cse confuses the simulator causing the synthesis-simulation mismatch. This create overlapping case and different simulator shows different behaviour for such case. The tool will give unpredictable ouput for overlapping case statements.

From the synthesized netlist, we can find out that no latch is inferred for overlapping case and the GLS shows correct output behaviour as expected.
Thus overlapping case statements create synthesis-simulation mismatch and to avoid this , all the statements of case statement should be mutually exclusive which is a correct way of coding.


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

```verilog

```

| Feature | ``if-else`` (Priority Logic) | ``case`` (Parallel Logic) |
| --- | --- | --- |
| Hardware mapping | Priority-encoded mux chain | Parallel mux structure |
| Best for | Conditions with hierarchy | FSMs, opcode decoding |
| Risk | Deep chains → timing issues | Cleaner timing, less nesting |
| Example | Priority encoder | ALU operation selector |

## 5.3 For loop and For generate constructs :
### 5.3.1 For loop :
* It is used inside the 'always' block.
* It is used for evaluating expressions.
* For loop is not used for instantiating hardware, gates.

lets understand this with example "mux_generate.v": 

<img width="700" height="500" alt="For_loop_1" src="https://github.com/user-attachments/assets/2d80a14c-309f-4c13-bf1f-f9801f9f6558" />

<img width="700" height="500" alt="For_loop_2" src="https://github.com/user-attachments/assets/fee7a077-dfbd-41f7-a190-ddc29763f3a9" />




we can see that the ouput waveform for RTL code functional simulation and synthesized netlist GLS are same. This is implemetation of small size mux, but we can make large size mux simply using same code except that we only need to change the input bus size and number fo times loop runs.	If we use 'case' statements for same size mux, it will not be difficult but when we increase the size of MUX, the implementation using 'case' statements will become cumbersome which can be built using "for-loop " easily.
		  		  
		  
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


