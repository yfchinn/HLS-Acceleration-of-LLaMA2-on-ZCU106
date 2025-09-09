# HLS-Acceleration-of-LLaMA2-on-ZCU106

This project implements a hardware-accelerated Transformer forward pass.
It uses Vitis HLS to design computational kernels (kernel_forward.cpp) and an XRT-based host program (main.cpp) to manage FPGA execution. The system is deployed on the AMD Zynq UltraScale+ MPSoC ZCU106 board.

## Requirements

### Development Environment

- Ubuntu 22.04
- Vitis 2024.2
- Vivado 2024.2

### Execution Environment

- ZCU106 development board
- PetaLinux 2024.2

## Running on ZCU106

1. Copy the following files to the SD card:

- Llama2_host (host executable)
- binary_container_1.bin (FPGA kernel binary)
- Model weights (e.g., stories15M.bin)
- tokenizer.bin

2. Boot the ZCU106 in SD card mode.

3. Log into PetaLinux and check FPGA/XRT status:
```
xrt-smi examine -r all
```

4. Run the host application:
```
./Llama2_host stories15M.bin
```

## Results

| Environment | Tokens | Time (s) | Speed (tok/s) |
| ----------- | ------ | -------- | ------------- |
| ZCU106 PS   | 201    | 114.7    | 1.75          |
| ZCU106 Accelerated | 225    | 25.8     | 8.71          |

With FPGA acceleration, throughput **improves by 5x+ compared to PS-only execution**.
