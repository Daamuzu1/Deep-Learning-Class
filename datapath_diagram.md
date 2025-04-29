# Data Path with Forwarding Unit Diagram

This diagram represents the data path implementation with forwarding unit as described in the Python code.

```mermaid
graph TD
    subgraph ForwardingUnit
        ID_Rs1 --> Compare_Rs1_ExRd
        ID_Rs2 --> Compare_Rs2_ExRd
        ID_Rs1 --> Compare_Rs1_MemRd
        ID_Rs2 --> Compare_Rs2_MemRd
        Ex_Rd --> Compare_Rs1_ExRd
        Ex_Rd --> Compare_Rs2_ExRd
        Mem_Rd --> Compare_Rs1_MemRd
        Mem_Rd --> Compare_Rs2_MemRd
        Compare_Rs1_ExRd --> Forward_Control_A
        Compare_Rs2_ExRd --> Forward_Control_B
        Compare_Rs1_MemRd --> Forward_Control_A
        Compare_Rs2_MemRd --> Forward_Control_B
        Ex_RegWrite --> Compare_Rs1_ExRd
        Ex_RegWrite --> Compare_Rs2_ExRd
        Mem_RegWrite --> Compare_Rs1_MemRd
        Mem_RegWrite --> Compare_Rs2_MemRd
    end

    subgraph InstructionDecode[Instruction Decode (ID)]
        IR --> ID_Rs1
        IR --> ID_Rs2
        RegFile[Register File] --> ReadData1_ID
        RegFile --> ReadData2_ID
    end

    subgraph Execute[Execute (EX)]
        IR --> Ex_Rd
        Ex_RegWrite[Ex RegWrite] --> Ex_Rd
        ALUResult_EX[ALU Result EX] --> Forward_Data_A
    end

    subgraph MemoryAccess[Memory Access (MEM)]
        IR --> Mem_Rd
        Mem_RegWrite[Mem RegWrite] --> Mem_Rd
        ALUResult_MEM[ALU Result MEM] --> Forward_Data_B
        ReadData_Memory[Memory Read Data] --> Forward_Data_C
    end

    Forward_Control_A --> Mux_ALU_A_Sel
    Forward_Control_B --> Mux_ALU_B_Sel
    Forward_Data_A --> Mux_ALU_A_Input1
    Forward_Data_B --> Mux_ALU_A_Input2
    Forward_Data_C --> Mux_ALU_A_Input3
    Forward_Data_A --> Mux_ALU_B_Input1
    Forward_Data_B --> Mux_ALU_B_Input2
    Forward_Data_C --> Mux_ALU_B_Input3
    ReadData1_ID --> Mux_ALU_A_Input0
    ReadData2_ID --> Mux_ALU_B_Input0
    Mux_ALU_A_Input0 --> Mux_ALU_A
    Mux_ALU_A_Input1 --> Mux_ALU_A
    Mux_ALU_A_Input2 --> Mux_ALU_A
    Mux_ALU_A_Input3 --> Mux_ALU_A
    Mux_ALU_B_Input0 --> Mux_ALU_B
    Mux_ALU_B_Input1 --> Mux_ALU_B
    Mux_ALU_B_Input2 --> Mux_ALU_B
    Mux_ALU_B_Input3 --> Mux_ALU_B
    Mux_ALU_A_Sel --> Mux_ALU_A
    Mux_ALU_B_Sel --> Mux_ALU_B
    Mux_ALU_A --> ALU_A
    Mux_ALU_B --> ALU_B
    ALU_A --> ALU
    ALU_B --> ALU
    ALU --> ALUResult_EX
```

## Component Descriptions

### Forwarding Unit
- **Inputs**: ID_Rs1, ID_Rs2, Ex_Rd, Mem_Rd, Ex_RegWrite, Mem_RegWrite
- **Outputs**: Forward_Control_A, Forward_Control_B
- **Function**: Compares source registers with destination registers to detect data hazards and generate forwarding control signals

### Instruction Decode (ID) Stage
- **Components**: Register file, instruction register (IR)
- **Outputs**: ReadData1_ID, ReadData2_ID, ID_Rs1, ID_Rs2
- **Function**: Decodes instructions and reads register values

### Execute (EX) Stage
- **Components**: ALU, control logic
- **Outputs**: ALUResult_EX, Ex_Rd
- **Function**: Performs ALU operations and generates destination register

### Memory Access (MEM) Stage
- **Components**: Memory interface
- **Outputs**: ALUResult_MEM, ReadData_Memory, Mem_Rd
- **Function**: Handles memory operations

### ALU Multiplexers
- **Inputs**: 
  - Mux_ALU_A: ReadData1_ID, ALUResult_EX, ALUResult_MEM, ReadData_Memory
  - Mux_ALU_B: ReadData2_ID, ALUResult_EX, ALUResult_MEM, ReadData_Memory
- **Control**: Forward_Control_A, Forward_Control_B
- **Outputs**: ALU_A, ALU_B
- **Function**: Selects the appropriate data source for ALU operands based on forwarding control signals

## Data Flow

1. Instructions are fetched and decoded in the ID stage
2. Register values are read from the register file
3. The forwarding unit compares source and destination registers
4. Forwarding control signals are generated
5. ALU multiplexers select the appropriate data sources
6. The ALU performs the operation with the selected operands
7. Results are written back to the register file or memory 