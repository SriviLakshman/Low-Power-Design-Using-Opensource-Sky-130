# Low-Power-Design-Using-Opensource-Sky-130
A project on low power design techniques using opensource tools

# Introduction to Low Power Design 

Low power is critical to most chips in this age. In this project, an introduction to this topic is presented, along with the basic rules while designing low power systems. Design strategies are discussed and circuit simulations are done using these concepts. Some of the key concepts covered in this project include: 

 - Low Power Design vs. Power Management
 - Basic Rules of Power Management 
 - Balancing Density-Delivery-Leakage-Lifetime
 - Connecting softwatre level activity to device level issues
 - Voltage aware boolean equations
 - Island ordering 
 - Verifying low power designs
 - Impact of IOT on low power design


# Why Low Power Design?

1. **Economics of Power and Energy** 
   - When we look at the economics of power and energy, it is useful to first define these terms. 
   - Power is defined as the instantaneous draw while energy refers to the total sum of the V * I over a time T. Energy is directly dependant on the battery capacity which in turn directly affects weight and cost of the system
   -  It is also useful to look at the impact of Power v Energy : 
         - As power increases -> Heat dissipated increases as the junction temperature rises, the associated packaging/cooling cost rises, the frequency gets limited thereby reducing performance and the material degrades faster
         - For energy -> Can either be a finite resource (like a battery) or metered per use (wall outlets). As the energy increases, we will require a bigger battery (or risk low battery life, in case of wall outlets, the power bill will rise), energy leaks away and is a systemic metric as opposed to power being a run-time metric

2. **Portable vs. Mobile vs. Mobility**
   - Portable : Is generally a compact version of a bulky device. Eg: Laptop v Desktop
   - Mobile : A re-imagined version of the bulky device. Must be on battery Eg: Smartphone
   - Mobility: The change in services and use cases that come out of mobile -> This impacts power and energy in a big way. Eg: Most phones nowadays require to watch multimedia/make calls/gaming/apps/...etc. Requires changes in infrastructure (like 4G etc)
   - It is useful to note that mobile without mobility is useless

- Thus to conclude, we can see the motivations for low power are as follows:
      - Can increase performance by reducing power. And can provide this performance gain at higher cost again
      - Can reduce associated cooling/packaging costs -> improve profits

# Case Studies

1. **Thermal Runaways**
  - Thermal runaway can be described as a situation wherby an increase an temperature leads to a situation where there is futher increase in the temperature. In essence, it is a dangerous positive feedback loop with respect to temperature which pushes the temperature to dangerous maximums that cause damage.
  - We can consider the case of the Samsung Note phones where the batteries were exploding a while back. This is due to the thermal runaway phenomenon.
  - It is reasonable to think that the fault does not necessarily lie with the battery in this case. Samsung has many different phone models and only this particular model was affected. 
  - This leads to the conclusion that the battery is not really the trigger for these explosions. There might be thermal runaway issues with other componenets present in the phone that affect the battery. For instance, it is possible that the problem is really the SOC that consumes a lot of power which causes the thermal runaway and the subsequent rise in temperatures that manifest as the battery exploding

2. **Cooling Costs of CPUs**
  - Gaming enthusiasts are aware that in order to maximize the performance of your CPU, it is necessary to provide the best cooling solution which is caoable of wicking away the heat from the CPUs as fast as it is produced. The theory is simple, in that, while doing performance intensive tasks (like gaming), the CPU/GPU is under full load which causes it to heat rapidly. As CPUs generally cannot sustain these higher temperatures for long periods of time as it will damage the components, the performance is throttled in order to reduce the heat. Thus a well cooled CPU is capable of sustaining peak performance for longer periods of time. 
  - Modern cooling solutions are a plenty with a multitude of options. CPUs can be either air cooled (by using radiators) or water cooled (by using pumps and resevoirs) or even active cooling using thermoelectric devices (this is even present in some latest gaming phones). Costs for each of these components can range anywhere from $50 to $200 depending upon the configuration
  - Thus it is easily seen that there is a price to pay for sustained performance and this arises purely due to power considerations

3. **Battery degradation in the iPhone6**
 - It is well documented that Apple admitted to slowing down its iPhone6 through software updates to prevent the phones which had degraded batteries from completely shutting down when under high load
 - This seems to be a classic case of "delivery" problem, wherein the power delivery is not robust enough to supply the required voltage to the different componenets of the phone in order to keep working. 
 - It is possible that as the batteries grew weaker with age, it was not capable of delivering the required supply to keep the phone operational. This led Apple to slow the phone down so as to not tax the system and put the power into a state of high load. 

# Macro Perspective and Energy Principles
 - The fundamental principles that one has to be aware of while doing low power is to balance the **density,delivery,leakage,reliability**. In essence,
 - Cant pack too much power into one space (Density)
 - Need to deliver the load (Delivery)
 - Able to handle any fluctuations in the demand quickly
 - Product needs to be long lasting

# CMOS Refresher 
 - A CMOS is an example of a Voltage Controlled Current Source (VCCS), wherein Vg is applied to turn it on and the output current is proportional to the applied Vgs
 - Voltage Control has become the #1 choice for power management -> as we can control the voltage and the associated current
 - Some basic power management techniques:
       - Clock Gating : Reduces the switching capacitance (also activity factor)
       - Multi Threshold : Vary the speed and leakage of the circuit
  - Technology migration does not solve the power issue -> Smaller nodes have a huge leakage problem, also less reliable.

1. Operational Regions of CMOS
  - As opposed to the conventional thinking that the CMOS has only 2 states (an on/off), it is useful to consider it has 3 states for low power regions
  - The usual on and off states with a **standby** state which can be thought as a middle ground between off and on where there is state retention but we cannot do any operations on the same (insert LP-13)

2. The 7 degrees of voltage control (insert LP-14)
 - A CMOS has 7 degrees of voltage control. VDD, VSS, SLPP, SLPN, VBBP, VBBN, VRET

3. Range of voltage control techniques
     - Multi-VDD : Divide the entire design into different blocks each operating at a different fixed voltage. This is a **spatial** variation
     - Low VDD Standby : Blocks not in use currently are put in "Standby" mode at lower VDD, just enough to retain state but not shut off
     - MTCMOS Power Gating : Similar to Multi-VDD except we place some Power switches that is capable of shutting down the blocks when not in use. Called power gating as this technique requires power gating transistors (usually HVT to reduce leakage). This is a **spatial and temporal (as the same block turns on and off at different times)** variation. 
     - Retention : Similar to power gating but with some retention logic (as power gating will make you lose state) 
     - Back Bias : Tweaking bulk voltage to control Vt. Used for memories. Note: We either tweak BBN or BBP to adjust the Vt and hence try to control leakage
     - Dynamic Voltage Frequency Scaling : Changing voltage according to power needs dynamically

# Day 2

# Voltage Control Techniques
 - This is a deeper analysis of the techniques already discussed in the previous section. 
 - **Power Gating** 
       - Basically shutting down the power to inactive blocks
       - Use power switches to accomplish this
       - Take care to isolate the outputs/inputs with Iso cells/Level shifters to prevent corruption in ON region.
       - Costly shutdown and wakeup cycles
- **Retention**
       - Used in conjunction with power gating
       - Special retention registers are used to hold the data in a shadow latch when the main power is turned off
       - Can quickly restore state on power up
 - **Dynamic Voltage Scaling**
       - Dynamic variation of voltage as and when required
 - **Low-VDD Standby**
        - Put block in a low voltage just enough to retain state

# State Retention and Verification
    
   - Power gating without retention means that all registers in the system lose their state
   - Means they need to be re-initialized once powered up
   - This has both a time cost and an energy cost to re-acquire the context 
   - Thus it is advantageous to save and restore state -> but this comes at a cost also (area required/reponse latencies/standby power)
   - Retention Strategies can either be in hardware or software
        -**Software Retention Strategies**
             - Operating system intrusive
             - CPU can execute an architectural state save code -> Like windows "Hibernate" -> this dumps everything into memory and shuts down
         -**Hardware Retention Strategies**
              - SRPG : Registers can be replaced by dedicated state retention registers
              - Scan-based hibernate : Reuse scan chains to shift in/out
              - Voltage Scale or Body Bias logic and registers 
              - All require a defined "Sleep/Wake"request protocol. 
  
  # System Level State Retention Verification Challenges
  
  - When is it safe to stop and request state saving mode?
  - Who handles these processes? Is it the OS/CPU? 
  - Is the retained state integrity 100% guaranteed? -> Any corruption could lead to deadlock/errors
  - When is it safe to restore state? -> Need to ensure that the rails are stable and safe


# Day 3

# Deep dive into State Space

 1. **Power State Space, State Transitions and State Space Verification** 
    - The power intent in modern designs need to be verified dynamically as not all bugs can be caught using static verification techniques. Some examples of static verification techniques include checking the states of level-shifters + isolation cells in the netlist. 
    - However, these checks did not account for :
      - Power-on reset
      - Power state transition protocol
      - Functional Correctness through transition from one state space to another
      - Completeness of state retention 

  
  - For example, consider a rudimentary phone which has the following 3 modes (Insert LP-50). These modes can be :
      - Standby : For when there is no activity happening 
      - Phone : For when the user is either calling/ receiving a call
      - Multimedia: For when the user is watching multimedia content (photos/videos) 

  - The components of the phone can also be broken down as follows (Insert LP-51). 
  - So for example, when the phone is in Standby mode -> CPU is in standby, Video is off, Modem is in standby, Audio is off, Display is off
  - Similarly, when the phone is receiving a call -> CPU is in normal, Video is in off/normal, Modem is in normal, Audio is on and Display is off
  - And when the phone is in Multimedia mode -> CPU is in high-performance(HP), Video is in HP, Modem is standby, Audio is off/normal and Display is normal 
  - The real challenge is to verify what happens when the phone goes from one mode to another (For example, if the phone goes from Multimedia mode to Standby mode -> then we need to turn off all the componenents safely). This is the challenge of Dynamic verification -> especially as the number of components/states of each component increases

  - An example of a power state transition is shown below (Insert LP-55).It is seen that the system requires to go through a multitude of states. For eg: To go from standby to HP, it is first needed for the system to be restored to normal level (VDD-1.5V) and then stepped up to HP (VDD-3V). Also whenever the system steps-up/down between modes, there are also a lot of associated actions that need to happen (like the PLL stepping up/down, clock gating turning on/off etc). 
  - Thus, it is necessary to have some power management firmware to implement specific sequences (this also ensures that the total space is reduced -> can optimize for lesser variables)
  - Note however that the PM firmware must **NOT be modified** after being verified.   


2. **Elements of a Low Power Testbench**  
  - In order to develop a low power testbench for a DUT -> it is necessary to have some power aware components around it (Like a power mangement block -> which is capable of interacting with the DUT). A typical LP testbench is shown in the following figure (LP-58)
  - After the test bench is setup, we can move on to verifying the design 
    - Step 1: Verify the always ON part of the design first -> This follows the traditional verification principles and will leave only the power management issues for the low power verification.
    - This also allows using the testbench you have already generated to be used for LP verification (with some incremental modification to cover the missing areas)
    - Step 2: Verify each power mode (Eg: Only the phone mode/multimedia mode/standby mode)
    - Step 3: Verify wakeup from ALL OFF including Power-on reset. This is to ensure that the system is able to initialize into any one of the modes from total 0 (aka ALL OFF).By doing this we also ensure that all the islands are covered for on/off/DVFS. 
    - Step 4: Verify PMU firmware
    - Step 5: Verify key registers for retention. Corollary: Memory verification?

3. **Basics of Island Ordering**
  - In what order to wake up the islands. For example: Consider the case of a CPU and a Modem. And suppose we want to move CPU to HP and Modem to ON, what sequence of steps should we follow in order to reach the end? Should we move the CPU first or the Modem first, do we need to move them both to an intermediate step..etc

# Basics of MultiVoltage Terminology

4. **Fundamentals of Rails, Multi-VDD and Islands**
   - From figure (LP-14), it is seen that there are 7 different signals to control a CMOS. These include the VDD,VSS,SLPP,SLPN,BBN,BBP,VRET (which is used for retaining the states upon restore) -> these can also be called as the **7-Rails Model of CMOS logic**. These are all-you-need to control the CMOS logic
   - Rail
     - Output of a voltage source (like a battery, charge pump, voltage regulator) used to drive logic
   - Multi-VDD :
      - This technique involves providing an external voltage supply to each of the components (like CPU/PMU/RTC) and setting each of them to operate at different voltages. For eg: We can set the CPU to run at 1V, the PMU to run at 0.8V etc. This gives us the ability to control them externally.
    - Spatial and Temporal Variation:
      - Spatial Variation -> Is when there are many different islands running at different voltages. Eg: CPU at 1V and Modem at 0.8V 
      - Temporal Variationn -> Is when an island itself can turn on or off at different intervals of time. Eg: CPU can go from 0V to 1.1V to 3.3V and so on
     - Islands:
        - An island is defined as area of a die uniquely connected to a set of rails. Basically if there any change in even 1 rail out of the 7, then the 2 different blocks are said to be different islands. **Note: Even if one rail is different, they will have different electrical properties**
 5. **Fundamentals of Domains, Well and MTCMOS PowerGating, Isolation**
     - Domain :
         -  It is defined as the drain of the driver. As in what charges/discharges the gate in question. Who (from which domain) causes a logic 1 on this gate
      - Well :
          - If the blocks have the same BBP/BBN rail -> then there are said to be same well. (Refer LP-68)
      - MTCMOS Power Gating :
         - Stands for Multi-threshold CMOS
         - Basically using a HVT header/footer cell in order to reduce the leakage when the block is off.
      - Isolation :
          - Using these logic gates to isolate the always on block from the signals coming from off domain. Ensures that the signals are at the correct logic levels. 
          - Iso gates can be either AND/NAND/OR/NOR etc
          - Not recommended to use just a pull down transistor -> tough for DFT -> tough to find any stuck at faults if the PD transistor fails
          - Also not recommended to use a latch to store the last known good state (LKGS) between domains -> impossible to verify and schdeule killer
  6. **Parking, Level Shifters and SRPG**
      - Parking :
          - Fundametally this is isolation into an OFF block. Whereby signals from an On block pass thru a parking gate (like an AND tied to 0) to the off block. This is useful for HFN signals like scan etc, there will be a large fanout and we are able to shut down all these logic easily by jusy gating it thru the parking gate. 
          - Advantages: Avoid Level-shifter and save power in OFF blocks
       - Level Shifting : 
            - Changing the level of the signal as it moves from different domains
            - There are ISO + LS gates nowadays
        - Retention : Shadow Latch
            - Where we tap the output of the original latch with the help of a small inverter loop (with maybe a save/restore control). This is advatabgeous as
            - When design is normally operational, there is no big burden of 4 LARGE transistors (requires more drive, more area, more power etc). So small inverter loop will suffice
            - Also when the design is off, we dont want the big capacitance of the original FF/latch to drain out the content of the storage latch. Possible improvements to the architecture done to give a Shadow latch with Vret -> whereby shadow latch is powered with a different VDD and high VT transistors (Vret). But distributing different rails (VDD and Vret was expensive). So layout further changed to SRPG -> State Retention Power Gating FFs
        
 # Day 4
 
 # Voltage Aware Booleans
 
 - It is instructive to simulate a few circuits to grasp the concept of the challenges involved with multi voltage designs. Let us consider an CMOS buffer made by chaining back-to-back inverters. Let us supply each inverter with a different Vdd and see if the output is able to track the input. 
 
 - Simulation Circuit and Parameters 
 ![Circuit Schematic](https://github.com/SriviLakshman/Low-Power-Design-Using-Opensource-Sky-130/blob/main/Images-LPWorkshop/1.PNG)
 
 - Simulation Parameters. We assume all Pmos and Nmos transistors have the same length. The channel width of the pmos is twice that of the nmos to approximate equal the rise and fall times of the inverter
 - Simulation is done using ngspice
 - Case 1: Let the first stage and the second stage use the same Vdd of 1.8V. The input signal is also 1.8V. We can see from the ouput graphs that the output follows the input and the voltages remain the same as well. This is the default expected behaviour of a CMOS buffer
 ![image](https://github.com/SriviLakshman/Low-Power-Design-Using-Opensource-Sky-130/blob/main/Images-LPWorkshop/BufferSameVdd.PNG)
 - Case 2: Let the first stage use Vdd = 1.8V and the second stage use Vdd = 3.0V. The input signal is also 1.8V. In this case too we can see that the output tracks the input. Note however that the output is at 3V as compared to the input signal at 1.8V. This in effect means that the output from the first inverter stage is within the noise margin for the second inverter stage and so samples the output correctly.
  ![image](https://github.com/SriviLakshman/Low-Power-Design-Using-Opensource-Sky-130/blob/main/Images-LPWorkshop/BufferCase2.PNG)
 - Case 3: Let us repeat Case 2 but with the input signal at 0.5V. We know that this voltage in generally corresponds to a logic 0 (As it is < 30% of 1.8V, which is the strongest 1). In this case, we see that the output is unable to track the input. This is most likely as the output voltage (logic 1) from the first stage is still too low compared to the voltage levels required for the second stage. Thus, there is an aberration here.
  ![image](https://github.com/SriviLakshman/Low-Power-Design-Using-Opensource-Sky-130/blob/main/Images-LPWorkshop/BufferCase3.PNG) 
  - Case 4: Let the first stage use Vdd = 3.0V and the second stage use Vdd = 1.8V. The input signal is 3.0V. This case is more straightforward as the signal is moving from high voltage domain to a low voltage domain. As the input signal is also at a high level, we can see the output tracks the input and we are able to get the full 1.8V at the output (corresponding to a strong logic 1).
    ![image](https://github.com/SriviLakshman/Low-Power-Design-Using-Opensource-Sky-130/blob/main/Images-LPWorkshop/BufferCase4.PNG) 
  - Case 5: Let us repeat Case 4 but with the input signal voltage Vin = 0.9V. This again corresponds to a logic 0 for stage 1 (as 0.9V = 30% of 3V). Again we see that the output is not able to track the input. This is because the output of stage 1 is a logic 1 (which is tracking around 3V, we see that the second stage is not really able to pull the voltage down as much we want it to in order to affect the logic), thus the overall output is a logic 0. Which is incorrect.
    ![image](https://github.com/SriviLakshman/Low-Power-Design-Using-Opensource-Sky-130/blob/main/Images-LPWorkshop/BufferCase5.PNG)
      ![image](https://github.com/SriviLakshman/Low-Power-Design-Using-Opensource-Sky-130/blob/main/Images-LPWorkshop/BufferCase5-2.PNG) 

- Thus multi-voltage circuits require the presence of elements such as level shifters that are able to step-up/step-down voltages to ensure that the voltages are translated properly. 
- Now, let us look at some other circuits 

# Common Power Management Schemes on ARM Based SOCs

- A typical mobile SOC consists of :
      - One or more ARM cores
      - Blocks that can be independently controlled
      - Hardware power management unit
      - Software driver for power management functions
      - Essentially a loop between System/Software, IC/SOC and the Voltage Regulators
  
 - Bug Classification
    - Structural Errors
        - Missing Isolation, level shifters
        - Devices in wrong domains
        - Wrong rail connects 
        - These can be found through simple static checks
      
     - Control Errors
         - Mistimes control signals
         - Incorrect control activation sequence
         - Incorrect gating/ungating in off/low power states
        
      - Architectural Errors
         - Incorrect partitions,policies
         - Incorrect scheduling of resources

   - Thus, it is seen that the power management increases the complexity of verification
    ![image](https://github.com/SriviLakshman/Low-Power-Design-Using-Opensource-Sky-130/blob/main/Images-LPWorkshop/LP-81.PNG)
    
    - It is also noted that multi-voltage verification coverage is different compared to to traditional verification
     ![image](https://github.com/SriviLakshman/Low-Power-Design-Using-Opensource-Sky-130/blob/main/Images-LPWorkshop/LP-82.PNG)

    - So in order to completely verify a MV design, it is necessary to have both **static and dynamic checks**. The following image shows an example of the different domains that need to be considered in verifying a rudimentary phone. It can be seen that as the complexity rises, the verification complexity also explodes.
   ![image](https://github.com/SriviLakshman/Low-Power-Design-Using-Opensource-Sky-130/blob/main/Images-LPWorkshop/LP-83.PNG)
   
   - So a modern Power aware flow can be done as shown in the figure below. **Multi voltage rule checks (MVRCs) and Multi voltage simulations (MVSIM) are done throughout the different stages of the flow** to ensure that the design is adequately covered for low power checks/issues. 
     ![image](https://github.com/SriviLakshman/Low-Power-Design-Using-Opensource-Sky-130/blob/main/Images-LPWorkshop/LP-84.PNG) 
