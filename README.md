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

3. Monitor boot via UART:
    - Use Vitis → New Feature Preview → Serial Monitor Install
    - Connect to the board via UART and monitor the boot log
    - Set baud rate to 115200 bps and select the correct COM port
    - Verify system boot messages until Linux is up and running

4. Troubleshooting during boot:
    - If you see VFS: Cannot open root device:
      1. Press any key during boot to enter U-Boot CLI
      2. Update bootargs:
          ```
          setenv bootargs "root=/dev/mmcblk0p2"
          saveenv
          boot
          ```

5. Login after boot:
    - Default user: petalinux (set a new password at first login)
    - If unable to log in:
      1. Mount the SD card in Ubuntu
      2. Locate the ```/etc/``` directory in the system partition
      3. Reset password:
        ```
        sudo passwd root
        sudo nano /etc/shadow
        ```
      4. Ensure root line looks like:
        ```
        root:$6$abcd1234$......:15069:0:99999:7:::
        ```

6. Go to auto-mounted FAT32 partition:
  ```
  sudo -s
  cd /run/media/mmcblk0p1/
  ```

7. Run application:
  ```
  ./Llama2_host stories15M.bin
  ```

## Results

| Environment | Tokens | Time (s) | Speed (tok/s) |
| ----------- | ------ | -------- | ------------- |
| ZCU106 PS   | 201    | 114.7    | 1.75          |
| ZCU106 accelerated | 225    | 25.8     | 8.71          |

With FPGA acceleration, throughput **improves by 5x+ compared to PS-only execution**.
