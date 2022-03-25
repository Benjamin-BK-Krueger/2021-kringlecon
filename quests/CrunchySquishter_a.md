Sometimes it's easier not to invent the wheel again and to use existing code.
See https://numato.com/kb/generating-square-wave-using-fpga/ as reference. I used this code and modified it to suit the required use case.

```verilog
// Note: For this lab, we will be working with QRP Corporation's CQC-11 FPGA.
// The CQC-11 operates with a 125MHz clock.
// Your design for a tone generator must support the following 
// inputs/outputs:
// (NOTE: DO NOT CHANGE THE NAMES. OUR AUTOMATED GRADING TOOL
// REQUIRES THE USE OF THESE NAMES!)
// input clk - this will be connected to the 125MHz system clock
// input rst - this will be connected to the system board's reset bus
// input freq - a 32 bit integer indicating the required frequency
//       (0 - 9999.99Hz) formatted as follows:
//       32'hf1206 or 32'd987654 = 9876.54Hz
// output wave_out - a square wave output of the desired frequency
// you can create whatever other variables you need, but remember
// to initialize them to something!

`timescale 1ns/1ns
module tone_generator (
  input clk, // 125MHz system clock
  input rst, // reset
  input [31:0] freq,
  output wave_out
);

  // ---- DO NOT CHANGE THE CODE ABOVE THIS LINE ---- 
  // ---- IT IS NECESSARY FOR AUTOMATED ANALYSIS ----
  // TODO: Add your code below. 
  // Remove the following line and add your own implementation. 
  // Note: It's silly, but it compiles...
  // assign wave_out = (clk | rst | (freq > 0));

  reg [31:0] one_second_counter;
  reg waver;
  assign wave_out = waver;
  localparam CLOCK_FREQUENCY = 125000000;

  always @(posedge clk or posedge rst)
  begin
    if(rst==1)
      begin
        one_second_counter <= 32'h00;
        waver <= 1'b0;
      end
    else
      begin
        if(one_second_counter == 32'h00)
          begin
            // one_second_counter <= 0;
            // waver <= waver ^1'b1;
            waver <= ~waver;
            one_second_counter <= CLOCK_FREQUENCY/(freq / 50)- 1; 
          end
        else
          one_second_counter <= one_second_counter - 1;
      end
  end
endmodule 
```
