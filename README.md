# AXI4-Lite Slave Peripheral

A standalone implementation of an **AXI4-Lite Slave Peripheral** supporting memory-mapped read and write transactions through the AXI4-Lite protocol. The design implements independent read and write finite state machines (FSMs), a three-register memory map, byte-enable writes using `WSTRB`, and protocol-compliant VALID/READY handshaking. A self-checking testbench verifies the functionality through directed protocol tests.

---

## Features

- 32-bit Address Bus
- 32-bit Data Bus
- Three memory-mapped 32-bit registers
- Independent Read and Write FSMs
- AXI4-Lite compliant VALID/READY handshake
- Independent handling of Write Address (AW) and Write Data (W) channels
- Byte-enable write support using `WSTRB`
- OKAY and SLVERR response generation
- Self-checking testbench with directed verification

---

## Repository Structure

```
axi-lite-slave-verilog/
│
├── rtl/
│   └── axi_lite_slave.v
│
├── tb/
│   └── tb_axi_lite_slave.v
│
├── docs/
│   └── block_diagram.png
│
├── results/
│   ├── rtl_schematic.png
│   └── simulation_waveform.png
│
├── README.md
├── LICENSE
└── .gitignore
```

---

## Architecture

The AXI4-Lite slave is organized around two independent finite state machines.

### Write FSM

The write FSM manages the AXI write address, write data, and write response channels.

Its responsibilities include:

- Receiving write addresses
- Receiving write data
- Supporting independent arrival of AW and W channels
- Decoding register addresses
- Updating the register file
- Generating write responses

### Read FSM

The read FSM manages the AXI read address and read data channels.

Its responsibilities include:

- Receiving read addresses
- Decoding register addresses
- Reading register contents
- Returning read data and response signals

### Block Diagram

<p align="center">
<img src="docs/block_diagram.png" width="800">
</p>

---

## Register Map

| Address | Register | Access |
|----------|----------|--------|
| `0x00` | REG0 | Read / Write |
| `0x04` | REG1 | Read / Write |
| `0x08` | REG2 | Read / Write |

---

## AXI4-Lite Interface Signals

| Channel | Signal | Description |
|---------|---------|-------------|
| **Global** | `ACLK` | Global clock |
| | `ARESETn` | Active-low reset |
| **Write Address** | `AWADDR` | Write address |
| | `AWVALID` | Indicates a valid write address |
| | `AWREADY` | Slave ready to accept write address |
| **Write Data** | `WDATA` | Write data |
| | `WSTRB` | Byte-enable strobes |
| | `WVALID` | Indicates valid write data |
| | `WREADY` | Slave ready to accept write data |
| **Write Response** | `BRESP` | Write response |
| | `BVALID` | Write response valid |
| | `BREADY` | Master accepts response |
| **Read Address** | `ARADDR` | Read address |
| | `ARVALID` | Indicates a valid read address |
| | `ARREADY` | Slave ready to accept read address |
| **Read Data** | `RDATA` | Read data |
| | `RRESP` | Read response |
| | `RVALID` | Read data valid |
| | `RREADY` | Master accepts read data |

> **Note:** Each AXI4-Lite channel uses an independent VALID/READY handshake. A transfer occurs only when both `VALID` and `READY` are asserted during the same clock cycle.

---

## Protocol Operation

### Write Transaction

1. The master sends a write address on the AW channel.
2. The master sends write data on the W channel.
3. The slave independently accepts both channels.
4. Once both address and data are available, the corresponding register is updated.
5. The slave returns a write response on the B channel.

### Read Transaction

1. The master sends a read address on the AR channel.
2. The slave decodes the address.
3. The requested register value is returned on the R channel.
4. The transaction completes after the RVALID/RREADY handshake.

---

## Verification

The testbench performs protocol verification using reusable AXI master tasks.

Implemented tests include:

- Reset Test
- Register Read/Write
- Register Overwrite
- Full Register Map Verification
- Partial Write using `WSTRB`
- Invalid Address Access
- Back-to-Back Write Transactions
- Back-to-Back Read Transactions
- Independent AW and W Channel Arrival

Each test automatically checks the expected result and reports **PASS** or **FAIL**.

---

## Simulation Results

Simulation verifies:

- Correct VALID/READY handshaking
- Independent operation of read and write channels
- Correct address decoding
- Register read/write functionality
- Proper write and read response generation
- Correct byte-enable (`WSTRB`) operation

### RTL Schematic

<p align="center">
<img src="results/rtl_schematic.png" width="850">
</p>

### Simulation Waveform

<p align="center">
<img src="results/simulation_waveform.png" width="850">
</p>

---

## Skills Demonstrated

- AXI4-Lite Protocol Implementation
- RTL Design
- Finite State Machine Design
- Memory-Mapped Peripheral Design
- Register File Design
- Address Decoding
- Byte Enable (`WSTRB`) Handling
- Protocol Verification
- Self-Checking Testbench Development

---

## Future Enhancements

- Parameterizable register count
- Configurable address and data widths
- Additional memory-mapped peripherals
- Interrupt support
- AXI4-Full interface
