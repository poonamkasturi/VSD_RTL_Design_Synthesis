

```verilog
module ternary_operator_mux (input i0 , input i1 , input sel , output y);
	assign y = sel?i1:i0;
	endmodule
```


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
