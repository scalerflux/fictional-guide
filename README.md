# Week 6

Physical Design - the process of converting a software description of a chip (RTL code) into an actual manufacturable silicon chip (GDSII layout). This is what happens in real semiconductor companies before sending designs to a foundry like TSMC or Intel for fabrication.


The PDK contains:
  * libs.ref: Standard cell libraries (the building blocks - gates, flip-flops)
  * libs.tech: Tool configuration files (how Magic, Yosys, etc. work with this technology)
  * Layer definitions, design rules, device models

  <img width="1032" height="773" alt="Screenshot 2025-10-29 at 9 42 24 AM" src="https://github.com/user-attachments/assets/bc14099c-ba22-41fd-a701-21596153b4f4" />

step 1: Design Preparation 
```
prep -design picorv32a
```
This did several critical things:

**a) Configuration Loading**

- Read your `config.json`
- Validated all parameters
- Set environment variables

**b) PDK Integration**

- Selected sky130A process (130nm technology node)
- Chose sky130_fd_sc_hd standard cell library (high-density variant)
- This library contains ~400 pre-designed cells (NAND, NOR, DFF, etc.)

**c) Created Run Directory**
<img width="1295" height="757" alt="Screenshot 2025-10-29 at 9 52 46 AM" src="https://github.com/user-attachments/assets/4dc509ca-50f4-4a83-a7a9-bb57517e0817" />

**d) Multi-Corner LEF Preparation**

- **What**: Created three LEF (Library Exchange Format) files
- **Why**: Real chips must work across temperature and voltage ranges
- **How it helps**:
    - **nom**: Tests typical performance
    - **min**: Catches "hold time" violations (data arriving too fast)
    - **max**: Catches "setup time" violations (data arriving too slow)

This is **better than the workshop's single LEF** 
picorv32 is a complete, real-world CPU used in actual chips

* OpenLane read your config.json file and loaded all your design parameters (clock period, die area, core utilization, etc.).
<img width="1346" height="851" alt="Screenshot 2025-10-29 at 9 30 05 AM" src="https://github.com/user-attachments/assets/208b4fdb-1839-42df-b3c2-46203eec4d56" />

**Step 2: Ran `run_synthesis`**

This is where the magic happens - converting high-level code to actual hardware.

- Yosys read the RTL and built an internal representation
- Identified all modules: picorv32, picorv32_pcpi_mul, picorv32_axi, etc.

**Stage 2: Elaboration**

- Understood design hierarchy (which modules call which)
- Inferred sequential elements (flip-flops) from behavioral code
- Inferred combinational logic (AND, OR, MUX gates)

**Stage 3: Technology Mapping**

- Converted generic gates to actual sky130 standard cells
- Chose specific cells based on:
    - Drive strength (how much load they can handle)
    - Area/power tradeoffs
    - Timing requirements

**Stage 4: Optimization**

- Removed redundant logic
- Minimized area while meeting timing
- Balanced path delays

**Stage 5: Static Timing Analysis (STA)**

- Calculated delays through all paths
- Checked if design meets 10ns clock period (100 MHz)
- Analyzed across nom/min/max corners
- Generated timing reports


**Output Files Created:**
<img width="1421" height="376" alt="Screenshot 2025-10-29 at 10 41 35 AM" src="https://github.com/user-attachments/assets/d3a9feb5-9bc3-4c6c-9c83-bfb1a6df06b6" />

1. **picorv32.v** (Gate-level netlist)
2. **picorv32.sdf** (Timing delays)

**Reports** 

<img width="1324" height="1059" alt="Screenshot 2025-10-29 at 11 01 57 AM" src="https://github.com/user-attachments/assets/056d1da8-6d5d-4b6e-9bbc-336b14a6f474" />


**Before Synthesis:**

- You had: Software description of a CPU (3000 lines of Verilog)
- Abstract, no physical form

**After Synthesis:**

- You have: A netlist of 10,000-15,000 actual silicon components
- Each component is a real, physical sky130 standard cell
- Connected by wires (signals/nets)
- Timing analyzed and validated

**Analogy:** Like going from an architectural blueprint to a list of specific building materials with quantities and connections.

