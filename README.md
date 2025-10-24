# Exp-5-Design-and-Simulate the-Memory-Design-using-Verilog-HDL
#Aim
To design and simulate a RAM,ROM,FIFO using Verilog HDL, and verify its functionality through a testbench in the Vivado 2023.1 environment.
Apparatus Required
Vivado 2023.1
Procedure
1. Launch Vivado 2023.1
Open Vivado and create a new project.
2. Design the Verilog Code
Write the Verilog code for the RAM,ROM,FIFO
3. Create the Testbench
Write a testbench to simulate the memory behavior. The testbench should apply various and monitor the corresponding output.
4. Create the Verilog Files
Create both the design module and the testbench in the Vivado project.
5. Run Simulation
Run the behavioral simulation to verify the output.
6. Observe the Waveforms
Analyze the output waveforms in the simulation window, and verify that the correct read and write operation.
7. Save and Document Results
Capture screenshots of the waveform and save the simulation logs. These will be included in the lab report.

# Code
# RAM
// Verilog code

      module ram(
      input clk,rst,en,
      input [7:0] d,
      input [9:0] a,
      output reg [7:0] do);
      reg [7:0] ram [1023:0];
      always@(posedge clk)
      begin
      if(rst)
      do <= 0;
      else if(en)
      ram[a] <= d;       
      else
      do <= ram[a];      
      end
      endmodule
// Test bench


    module ram_tb;
    reg clk_t, rst_t, en_t;
    reg [7:0] datain_t;
    reg [9:0] address_t;
    wire [7:0] dataout_t;
    ram dut (
    .clk(clk_t),
    .rst(rst_t),
    .en(en_t),
    .datain(datain_t),
    .address(address_t),
    .dataout(dataout_t)
    );

    initial begin
    clk_t = 1'b0;
    rst_t = 1'b1;
    #100
    rst_t = 1'b0;
    en_t=1'b1;
    address_t = 10'd800;
    datain_t = 8'd50;
    #100;
    address_t = 10'd950;
    datain_t = 8'd60;
    #100;
    en_t = 1'b0;
    address_t = 10'd800;
    #100;
    address_t = 10'd950;
    #100;
    end
    endmodule
// output Waveform

<img width="1919" height="1199" alt="Screenshot 2025-10-14 150406" src="https://github.com/user-attachments/assets/fab9ff42-b45c-41a0-b2a4-eb842da92ca1" />

# ROM
 // write verilog code for ROM using $random
 
    module rom(
    input clk,rst,
    input[9:0]a,
    output reg [9:0]do );
    reg[7:0] rom[1023:0];
    initial 
    begin
    rom[10'd100]=8'd100;
    rom[10'd1023]=8'd125;
    end
    always@(posedge clk)
    begin
    if(rst)
    do<=8'b0;
    else
    do<=rom[a];
    end
    endmodule
 // Test bench

    module rom_tb;
    reg clk_t,rst_t;
    reg [9:0] a_t;
    wire [7:0] do_t;
    rom dut(.clk(clk_t),.rst(rst_t),.a(a_t),.do(do_t));
    initial
    begin
    clk_t=1'b0;
    rst_t=1'b1;
    a_t=10'd0;
    #50 rst_t=1'b0;
    a_t=10'd100;
    #100;
    a_t=10'd1023;
    #100;
    a_t=10'd200;
    #100;
    $finish;
    end
    always #10 clk_t=~clk_t;
    endmodule
// output Waveform

<img width="1917" height="1199" alt="Screenshot 2025-10-14 135424" src="https://github.com/user-attachments/assets/f1ae42b2-5274-44fc-b8d2-058dd5e6efdf" />

 # FIFO
 // write verilog code for FIFO

    module fifo #(parameter DEPTH=8, DATA_WIDTH=8) (input clk, rst_n,
    input w_en, r_en,
    input [DATA_WIDTH-1:0] data_in,
    output reg [DATA_WIDTH-1:0] data_out,
    output full, empty
    );
 
    reg [$clog2(DEPTH)-1:0] w_ptr, r_ptr;
    reg [DATA_WIDTH-1:0] fifo[DEPTH-1:0];
 
    // Set Default values on reset.
    always@(posedge clk) begin
    if(!rst_n) begin
     w_ptr <= 0; r_ptr <= 0;
     data_out <= 0;
    end
    end
 
    // To write data to FIFO
    always@(posedge clk) begin
    if(w_en & !full)begin
     fifo[w_ptr] <= data_in;
     w_ptr <= w_ptr + 1;
     end
     end
 
    // To read data from FIFO
    always@(posedge clk) begin
    if(r_en & !empty) begin
     data_out <= fifo[r_ptr];
     r_ptr <= r_ptr + 1;
    end
    end
 
    assign full = ((w_ptr+1'b1) == r_ptr);
    assign empty = (w_ptr == r_ptr);
    endmodule
 // Test bench

    module fifo_tb;
    reg clk_t, rst_t;
    reg w_en_t, r_en_t;
    reg [7:0] data_in_t;
    wire [7:0] data_out_t;
    wire full_t, empty_t;

    fifo dut (.clk(clk_t),.rst_n(rst_t),.w_en(w_en_t),.r_en(r_en_t),.data_in(data_in_t),
    .data_out(data_out_t),
    .full(full_t),
    .empty(empty_t)
    );

    always #10 clk_t = ~clk_t;

    initial begin
    clk_t = 1'b0;
    rst_t = 1'b0;
    w_en_t = 1'b0;
    r_en_t = 1'b0;
    data_in_t = 8'd0;

    #50 rst_t = 1'b1;
    #20 w_en_t = 1'b1; data_in_t = 8'd10;
    #20 data_in_t = 8'd20;
    #20 data_in_t = 8'd30;
    #20 data_in_t = 8'd40;
    #20 w_en_t = 1'b0;
    #40 r_en_t = 1'b1;
    #100 r_en_t = 1'b0;
    end
    endmodule



// output Waveform

<img width="1919" height="1199" alt="Screenshot 2025-10-14 151313" src="https://github.com/user-attachments/assets/c203221e-d016-4c00-9b5d-d896e39d2571" />


# Conclusion
The RAM, ROM, FIFO memory with read and write operations was designed and successfully simulated using Verilog HDL. The testbench verified both the write and read functionalities by simulating the memory operations and observing the output waveforms. The experiment demonstrates how to implement memory operations in Verilog, effectively modeling both the reading and writing processes.
 
 

