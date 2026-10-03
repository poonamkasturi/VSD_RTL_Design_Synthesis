# D Flip-Flop with Asynchronous Reset

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

