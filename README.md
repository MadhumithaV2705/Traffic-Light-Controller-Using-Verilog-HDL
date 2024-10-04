# Traffic-Light-Controller-Using-Verilog-HDL
Aim
To design and simulate a traffic light controller using Verilog HDL, and verify its functionality through a testbench in the Vivado 2023.1 simulation environment. The objective is to control the traffic lights for a junction with a specific time-based sequence for Red, Yellow, and Green lights.

Apparatus Required
Vivado 2023.1 or equivalent Verilog simulation tool.
Computer system with a suitable operating system.
FPGA board (optional for hardware verification).
Procedure
Launch Vivado 2023.1:

Open Vivado and create a new project.
Design the Traffic Light Controller Verilog Code:

Write the Verilog code for the traffic light controller, using an FSM (Finite State Machine) to transition between Green, Yellow, and Red lights based on timing intervals.
Create the Testbench:

Write a testbench to simulate the traffic light controller. The testbench will check the sequence of light transitions based on time.
Add the Verilog Files:

Add the traffic light controller Verilog code and the testbench file to the project.
Run Simulation:

Run the behavioral simulation in Vivado to verify the correct sequence of the traffic lights.
Observe the Waveforms:

Examine the waveform output to verify that the traffic light transitions through the Green, Yellow, and Red lights in the correct sequence.
Save and Document Results:

Capture screenshots of the waveform and save the simulation logs to include in your report.

Verilog Code for Traffic Light Controller

// traffic_light_controller.v

module traffic_light_controller (
    input wire clk,
    input wire reset,
    output reg [2:0] lights  // 3-bit output: [2]=Red, [1]=Yellow, [0]=Green
);

    // Define states using parameter (Verilog style)
    parameter GREEN  = 2'b00;
    parameter YELLOW = 2'b01;
    parameter RED    = 2'b10;

    // State variables
    reg [1:0] current_state, next_state;
    reg [3:0] counter;  // 4-bit counter for timing

    // State transition based on the counter and clock
    always @(posedge clk or posedge reset) begin
        if (reset) begin
            current_state <= GREEN;  // Reset to GREEN state
            counter <= 0;            // Reset counter
        end else begin
            if (counter == 4'd9) begin  // Wait for 10 clock cycles
                current_state <= next_state;  // Move to the next state
                counter <= 0;  // Reset counter
            end else begin
                counter <= counter + 1;  // Increment counter
            end
        end
    end

    // Next state logic and control of traffic lights
    always @(*) begin
        case (current_state)
            GREEN: begin
                lights = 3'b001;  // Green light on
                next_state = YELLOW;  // Move to Yellow state next
            end
            YELLOW: begin
                lights = 3'b010;  // Yellow light on
                next_state = RED;  // Move to Red state next
            end
            RED: begin
                lights = 3'b100;  // Red light on
                next_state = GREEN;  // Move back to Green state next
            end
            default: begin
                lights = 3'b000;  // All lights off (should not happen)
                next_state = GREEN;  // Default back to Green
            end
        endcase
    end
endmodule

OUTPUT:
![image](https://github.com/user-attachments/assets/00b9dc79-5a74-4a57-b62b-0ae3694718c1)


Testbench for Traffic Light Controller

// traffic_light_controller_tb.v
`timescale 1ns / 1ps

module traffic_light_controller_tb;

// Inputs to the module (reg type in testbench)
reg clk;
reg reset;

// Outputs from the module (wire type in testbench)
wire [2:0] lights;

// Instantiate the traffic_light_controller module
traffic_light_controller uut (
    .clk(clk),
    .reset(reset),
    .lights(lights)
);

// Clock generation: 10ns period
always begin
    #5 clk = ~clk;  // Toggle clock every 5 time units (10 ns clock period)
end

// Initial block to apply stimulus (inputs)
initial begin
    // Initialize inputs
    clk = 0;
    reset = 1;  // Start with reset active
    
    // Wait for a few cycles with reset asserted
    #20;
    reset = 0;  // Release reset after 20ns

    // Let the simulation run for a while
    #1000;

    // End simulation
    $finish;
end

// Monitor the outputs during the simulation
initial begin
    // Print header
    $display("Time\tReset\tLights");
    $monitor("%d\t%b\t%b", $time, reset, lights);
end
endmodule

OUTPUT:
![image](https://github.com/user-attachments/assets/19ea9735-1b63-415c-a537-8cd0fcba6f2c)



Conclusion
In this experiment, a traffic light controller was successfully designed and simulated using Verilog HDL. The design controlled the traffic lights to switch between Green, Yellow, and Red in a cyclic manner based on timing intervals. The testbench verified that the traffic lights followed the correct sequence and timing. The simulation results confirm the correct functionality of the traffic light controller, demonstrating the effectiveness of Verilog HDL in designing FSM-based controllers for real-world applications.
