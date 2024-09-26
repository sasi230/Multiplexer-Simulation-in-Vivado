SIMULATION AND IMPLEMENTATION OF MULTIPLEXER
```AIM:
To design and simulate a 4:1 Multiplexer (MUX) using Verilog HDL in four different modeling styles—Gate-Level, Data Flow, Behavioral, and Structural—and to verify its functionality through a testbench using the Vivado 2023.1 simulation environment. The experiment aims to understand how different abstraction levels in Verilog can be used to describe the same digital logic circuit and analyze their performance.

APPARATUS REQUIRED:
Vivado 2023.1

Procedure
1. Launch Vivado
Open Vivado 2023.1 by double-clicking the Vivado icon or searching for it in the Start menu.
2. Create a New Project
Click on "Create Project" from the Vivado Quick Start window.
In the New Project Wizard:
Project Name: Enter a name for the project (e.g., Mux4_to_1).
Project Location: Select the folder where the project will be saved.
Click Next.
Project Type: Select RTL Project, then click Next.
Add Sources:
Click on "Add Files" to add the Verilog files (e.g., mux4_to_1_gate.v, mux4_to_1_dataflow.v, etc.).
Make sure to check the box "Copy sources into project" to avoid any external file dependencies.
Click Next.
Add Constraints: Skip this step by clicking Next (since no constraints are needed for simulation).
Default Part Selection:
You can choose a part based on the FPGA board you are using (if any).
If no board is used, you can choose any part, for example, xc7a35ticsg324-1L (Artix-7).
Click Next, then Finish.
3. Add Verilog Source Files
In the "Sources" window, right-click on "Design Sources" and select Add Sources if you didn't add all files earlier.
Add the Verilog files (mux4_to_1_gate.v, mux4_to_1_dataflow.v, etc.) and the testbench (mux4_to_1_tb.v).
4. Check Syntax
Expand the "Flow Navigator" on the left side of the Vivado interface.
Under "Synthesis", click "Run Synthesis".
Vivado will check your design for syntax errors. If any errors or warnings appear, correct them in the respective Verilog files and re-run the synthesis.
5. Simulate the Design
In the Flow Navigator, under "Simulation", click on "Run Simulation" → "Run Behavioral Simulation".
Vivado will open the Simulations Window, and the waveform window will show the signals defined in the testbench.
6. View and Analyze Simulation Results
The simulation waveform window will display the signals (S1, S0, A, B, C, D, Y_gate, Y_dataflow, Y_behavioral, Y_structural).
Use the time markers to verify the correctness of the 4:1 MUX output for each set of inputs.
You can zoom in/out or scroll through the simulation time using the waveform viewer controls.
7. Adjust Simulation Time
To run a longer simulation or adjust timing, go to the Simulation Settings by clicking "Simulation" → "Simulation Settings".
Under "Simulation", modify the Run Time (e.g., set to 1000ns).
8. Generate Simulation Report
Once the simulation is complete, you can generate a simulation report by right-clicking on the simulation results window and selecting "Export Simulation Results".
Save the report for reference in your lab records.
9. Save and Document Results
Save your project by clicking File → Save Project.
Take screenshots of the waveform window and include them in your lab report to document your results.
You can include the timing diagram from the simulation window showing the correct functionality of the 4:1 MUX across different select inputs and data inputs.
10. Close the Simulation
Once done, close the simulation by going to Simulation → "Close Simulation".

Logic Diagram

![image](https://github.com/user-attachments/assets/d4ab4bc3-12b0-44dc-8edb-9d586d8ba856)

Truth Table

![image](https://github.com/user-attachments/assets/c850506c-3f6e-4d6b-8574-939a914b2a5f)

Verilog Code

4:1 MUX Gate-Level Implementation
```
module mux4x1_gatelevel(output Y, input I0, I1, I2, I3, input S0,S1);
 wire nS0, nS1, a, b, c, d;
 not (nS0, S0);
 not (nS1, S1);
 and (a, I0, nS0, nS1);
 and (b, I1, S0, nS1);
 and (c, I2, nS0, S1);
 and (d, I3, S0, S1);
 or (Y, a, b, c, d);
endmodule
```


4:1 MUX Data Flow Implementation
```
// mux4_to_1_dataflow.v
module mux4x1_dataflow(output Y, input I0, I1, I2, I3, input S0,S1);
 assign Y = (~S1 & ~S0 & I0) | (~S1 & S0 & I1) | (S1 & ~S0 & I2) | (S1 & S0 & I3);
endmodule
```

4:1 MUX Behavioral Implementation
```
module mux4x1_behavioral(output reg Y, input I0, I1, I2, I3, input
S0, S1);
 always @(*)
 begin
 case ({S1, S0})
 2'b00: Y = I0;
 2'b01: Y = I1;
 2'b10: Y = I2;
 2'b11: Y = I3;
 default: Y = 1'bx;
 endcase
 end
endmodule
```

4:1 MUX Structural Implementation
```
module mux4x1_structural(output Y, input I0, I1, I2, I3, input S0,S1);
 wire a, b, c, d;

 and g1 (a, I0, ~S0, ~S1);
 and g2 (b, I1, S0, ~S1);
 and g3 (c, I2, ~S0, S1);
 and g4 (d, I3, S0, S1);
 or g5 (Y, a, b, c, d);
endmodule
```
Testbench Implementation
```
module tb_mux4x1;
 reg I0, I1, I2, I3;
 reg S0, S1;
 wire Y_gate, Y_dataflow, Y_structural, Y_behavioral;
 mux4x1_gatelevel u1 (Y_gate, I0, I1, I2, I3, S0, S1);
 mux4x1_dataflow u2 (Y_dataflow, I0, I1, I2, I3, S0, S1);
 mux4x1_structural u3 (Y_structural, I0, I1, I2, I3, S0, S1);
 mux4x1_behavioral u4 (Y_behavioral, I0, I1, I2, I3, S0, S1);
 initial begin
 $monitor("S1=%b S0=%b I0=%b I1=%b I2=%b I3=%b | Y_gate=%b Y_dataflow=%b Y_structural=%b Y_behavioral=%b", S1, S0, I0, I1, I2, I3, Y_gate, Y_dataflow,Y_structural, Y_behavioral);
 // Test cases
 {S1, S0} = 2'b00; {I0, I1, I2, I3} = 4'b1010; #10;
 {S1, S0} = 2'b01; {I0, I1, I2, I3} = 4'b1010; #10;
 {S1, S0} = 2'b10; {I0, I1, I2, I3} = 4'b1010; #10;
 {S1, S0} = 2'b11; {I0, I1, I2, I3} = 4'b1010; #10;
 $finish;
 end
 endmodule
```
 OUTPUT:
 ```
 1)gatelevel
 ![gatelevel](https://github.com/user-attachments/assets/d83ff5b2-3bfe-4cec-849e-01e90216ed0e)
2)dataflow
![dataflow](https://github.com/user-attachments/assets/6ca0b4b7-ec5b-4818-80dd-85b261449f80)
3)Behavioral 
![behav](https://github.com/user-attachments/assets/9dfaa082-fec4-4db0-b11b-4d3148ce3a1e)
4)Structural 
![structural](https://github.com/user-attachments/assets/06acd746-bb0a-42ec-a633-d49ca3af6563)
5)testbench
![WhatsApp Image 2024-09-25 at 9 46 43 PM](https://github.com/user-attachments/assets/7dccd872-76d0-459d-ab97-a711600e421c)


Sample Output

Time=0 | S1=0 S0=0 | Inputs: A=0 B=0 C=0 D=0 | Y_gate=0 | Y_dataflow=0 | Y_behavioral=0 | Y_structural=0
Time=10 | S1=0 S0=0 | Inputs: A=0 B=0 C=0 D=0 | Y_gate=0 | Y_dataflow=0 | Y_behavioral=0 | Y_structural=0
Time=20 | S1=0 S0=0 | Inputs: A=0 B=0 C=0 D=1 | Y_gate=0 | Y_dataflow=0 | Y_behavioral=0 | Y_structural=0
Time=30 | S1=0 S0=1 | Inputs: A=0 B=0 C=0 D=1 | Y_gate=0 | Y_dataflow=0 | Y_behavioral=0 | Y_structural=0
Time=40 | S1=1 S0=0 | Inputs: A=0 B=0 C=0 D=1 | Y_gate=0 | Y_dataflow=0 | Y_behavioral=0 | Y_structural=0
...

Conclusion:

In this experiment, a 4:1 Multiplexer was successfully designed and simulated using Verilog HDL across four different modeling styles: Gate-Level, Data Flow, Behavioral, and Structural. The simulation results verified the correct functionality of the MUX, with all implementations producing identical outputs for the given input conditions.



  
