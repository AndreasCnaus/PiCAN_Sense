    
# PiCAN_Sense Project

The **PiCAN_Sense** is a system designed to monitor environmental data using two main components:

An **STM32WB** microcontroller and a **Raspberry Pi**, which are connected via the CAN bus. The microcontroller periodically reads the environmental data ​​from the BME280 sensor and puts them on the CAN bus. The Raspberry Pi acts as a central control unit. Using a custom developed device driver for the MCP2515 CAN-chip, it receives the environmental data from the microcontroller via the CAN bus and makes them available to the user-space application.

This two-part architecture is reflected in the repository structure, consisting of the following subrepositories:
 - **PiCAN**: Contains the Raspberry Pi's MCP2515 CAN-chip driver and user-space application for test and verification.
 - **STM32WB_CAN_Sense**: Contains the firmware and configurations for the STM32WB microcontroller, along with the necessary custom drivers for the MCP2515 CAN-chip and the BME280 sensor.

## System Overview:
           +----------------------------------+
           |     Raspberry Pi 4 / Linux       |
           |  +----------------------------+  |
           |  | Test Tool (User Space)     |  |
           |  |  using MCP2515 Driver      |  |
           |  +----------------------------+  |
           |                | (sys_calls)     |
           |  +----------------------------+  |
           |  | MCP2515 Driver             |  |
           |  +----------------------------+  |
           +---------------+------------------+
                           ^
                           |
                           | (SPI0)
                           |
                           v
           +-------------------------------+
           |      MCP2515 CAN-Shield(1)    |
           +-------------------------------+
                           ^
                           |
                           | (CAN-Bus)
                           |
                           v
           +-------------------------------+
           |      MCP2515 CAN-Shield(2)    |
           +-------------------------------+
                           ^
                           |
                           | (SPI)
                           |
                           v
           +-------------------------------+
           |       RF Breakout Board       |           +-------------------+
           |                               |           |                   |
           |   +-------------------------+ | <--I2C--> +   BME280-Sensor   |
           |   |        STM32WB          | |           |                   |	
           |   +-------------------------+ |           +-------------------+
           +-------------------------------+
           

The PiCAN_Sense system architecture is organized as follows:

1. **Raspberry Pi 4**:
    - **Operating System**: Raspbian GNU/Linux 11 with kernel 6.1.21
    - **Key Software Components**:
        - **Test Tool (User Space)**: A user-space application that interacts with the MCP2515 driver to receive and transmit CAN-Messages.
        - **MCP2515 Driver**: A device driver that facilitates communication with the MCP2515-Chip on the CAN-Shield via SPI0 interface and exposes read/write/ioctl interfaces to the user space by means of a **Character-Device**.

2. **MCP2515 CAN-Shield**:
    - **First CAN-Shield**:
        - Acts as an intermediary between the Raspberry Pi 4 and the CAN-Bus network.
        - Transmits data between CAN-Bus and Raspbbery Pi vis SPI interface.
    - **Second CAN-Shield**:
        - Acts as an intermediary between the RF Breakout Board and the CAN-Bus network.
        -  Transmits data between CAN-Bus and RF Breakout Board via SPI interface.

3. **RF Breakout Board** (see laso: [RF Breakout Board](https://github.com/AndreasCnaus/BLE_ESB/tree/main/hardware)):
    - **Key Components**:
        - **STM32WB Microcontroller**: Reads BME280 Sensor data via the I2C interface and puts them on the CAN Bus via the CAN-Shield.
        - **BME280 Sensor**: An environmental sensor connected via the I2C bus, measuring temperature, humidity, and pressure.

## Hardware Setup:

![Test Setup](/docs/PiCAN_Sense_Hw_Setup.jpg)


## License

This project is licensed under the MIT License. You can find the full license text in the [LICENSE](https://github.com/AndreasCnaus/PiCAN_Sense/blob/main/docs/LICENSE) file.

### Third-Party Software

This project uses STM32 libraries, which are distributed under the [BSD-3-Clause License](https://opensource.org/licenses/BSD-3-Clause). Please ensure compliance with their license terms if you use or distribute this software.

The licenses of any additional third-party components are provided in their respective documentation.


