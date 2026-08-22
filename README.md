<p align="center">
  <img src="modbusscopSlave-logo-black.png" alt="ModbusScop Slave" width="520">
</p>

**ModbusScop Slave** is a free **Modbus slave / server simulator** with a modern,
dockable **Dear ImGui** interface. It serves requests over **Modbus TCP**,
**Modbus UDP**, and **Modbus RTU (serial)** — and can also serve **RTU-framed
requests over TCP/UDP** for terminal servers that bridge a network link to a
serial RTU bus. Answer as one or many simulated devices at once, so engineers can
exercise a Modbus master, HMI, SCADA, or gateway without any real field hardware.

Free to use and redistribute under the permissive **BSD 2-Clause License**.

Developed by **Carlos Nardi**.

[![Buy Me A Coffee](https://img.shields.io/badge/Buy%20Me%20A%20Coffee-support-yellow?logo=buy-me-a-coffee)](https://buymeacoffee.com/cnardi)

<p align="center">
  <img src="ModbusScop Slave Window 2.0.png" alt="ModbusScop Slave" width="1200">
</p>

## Concept

ModbusScop Slave is organized as a tree: **Channels → Devices → Maps**.

- A **Channel** is one server endpoint. It is either a **Network** channel — a
  **TCP** listener (serving several masters at once, up to a configurable limit)
  or a **UDP** datagram server — or a **Serial** channel (a Modbus RTU bus on a
  COM port). Network channels also carry a per-channel **framing** choice:
  standard *Modbus TCP (MBAP)* or *Modbus RTU (slave id + PDU + CRC, no MBAP)*.
  Each channel runs on its own I/O thread; start and stop them independently.
- A **Device** is one simulated unit (Modbus/slave ID) inside a channel. A single
  channel can host **many devices with different unit IDs**, and requests are
  routed to the matching device. A device can optionally **reply to any ID**
  (catch-all) for single-device setups.
- Each device exposes its data as **maps** in one of two models:
  - **RTU model** — the four classic Modbus tables (Coils, Discrete Inputs,
    Holding Registers, Input Registers) as separate, overlap-checked blocks.
  - **PLC model** — one continuous 65,536-word memory with automatic bit/word
    aliasing, so the same memory is reachable by both bit and register function
    codes.

## Features

- **TCP, UDP, and Serial** — serve masters over Modbus TCP, Modbus UDP, or Modbus
  RTU on a serial port, chosen per channel in the New Channel dialog (**Network /
  Serial**, and for Network **TCP / UDP**).
- **Modbus RTU over the network** — a per-channel framing selector lets a TCP or
  UDP channel accept and answer RTU-style frames (slave id + PDU + CRC16, no MBAP
  header), the way terminal servers present serial RTU devices over Ethernet.
- **Multi-device, multi-master simulation** — many channels, each with many
  devices, each answering on its own unit ID; TCP channels serve multiple masters
  concurrently.
- **RTU and PLC data models** — overlap-checked RTU blocks (FC 01–04) or a full
  64K PLC memory with bit/word aliasing and selectable bit order (LSB / MSB).
- **Editable map windows** — a Master-style grid with per-cell datatype
  (16 / 32 / 64-bit signed, unsigned, hex, binary, float, double), selectable
  byte order, per-cell aliases, Base-1 register numbering, configurable
  rows-per-column, and a per-bit editor. Cells flash when a master writes them
  (FC 05 / 06 / 15 / 16); the animation is configurable under **View → Animation
  Colors**.
- **Per-cell simulation** — drive individual cells with **Increment, Random, or
  Sine** (words) and **Toggle / Random** (bits), datatype-aware and paced by a
  per-cell period.
- **Request behavior / fault injection** — per-map **response delay**, **forced
  exception code**, and **no-reply** (silent drop); disabled regions answer
  Illegal Data Address (Exc 02).
- **Communication Monitor** — a timestamped, filterable log of every request and
  response frame (by device and by master), with an enable toggle, optional
  **log-to-file** with size-based rotation, and a line counter.
- **Status Messages** — a scrolling, timestamped log of channel start/stop,
  master connect/disconnect, workspace and map I/O events.
- **Dashboard** — live per-channel traffic counters (Rx / Tx / exceptions /
  bytes), connected-master count, and uptime, with a totals row.
- **Workspaces** — save and reload your entire setup: channels (including
  transport and framing), devices, RTU blocks and PLC memory, values, per-cell
  formats, aliases, simulations, and test behaviors. Individual maps can also be
  **exported and imported** on their own.
- **Themes** — dark / light / classic with a customizable accent color; layout
  and preferences are remembered between runs.

## Download & run

1. Go to the [**Releases**](../../releases) page and download the latest
   `ModbusScopSlave` archive for Windows.
2. Unzip it anywhere and run **`ModbusScopSlave.exe`** — no installation required.

**Requirements:** Windows 10/11 (64-bit).

**Rendering:** ModbusScop Slave uses the GPU by default; you can switch to **CPU
(software)** rendering under **View → Rendering** (handy over Remote Desktop or in
VMs). 

### Getting started

1. **+ Network** or **+ Serial** — create a channel.
   - **Network** — choose **TCP** or **UDP**, the **Framing** (*Modbus TCP
     (MBAP)* or *Modbus RTU over the network*), the bind IP (e.g. `127.0.0.1` or
     `0.0.0.0`), the port (default 502), and — for TCP — the maximum number of
     masters.
   - **Serial** — pick the COM port and set baud rate, data bits, parity, and stop
     bits.
2. **+ Device** — add one or more devices to the channel, each with its own unit
   ID, as **RTU** or **PLC**.
3. **+ Block** (RTU) or open **Memory** (PLC) — define the data, then edit values,
   formats, aliases, and simulations directly in the map window.
4. Press **Start** on the channel and point your Modbus master at the IP/port (or
   COM port). Watch the traffic in the **Communication Monitor** and the counters
   in the **Dashboard**.

Use **File → Save Workspace** (Ctrl+S) to keep the whole configuration and reload
it later with **Open Workspace** (Ctrl+O).

## Third-party libraries

ModbusScop Slave is built with these open-source components, each under its own
license:

| Library | Used for | License |
|---------|----------|---------|
| Dear ImGui (docking) | user interface | MIT |
| GLFW 3 | window / OpenGL context | Zlib/libpng |
| OpenGL 3 | rendering | — |
| Asio (standalone) | Modbus TCP / UDP / RTU server | Boost Software License 1.0 |
| stb_image | logo / splash decoding | MIT / public domain |

## License

ModbusScop Slave is released under the **BSD 2-Clause License**. It is provided
"as is", without warranty of any kind; the author is not responsible for any
damage or loss caused by its use.

```
BSD 2-Clause License

Copyright (c) 2026, Carlos Nardi
All rights reserved.

Redistribution and use in source and binary forms, with or without
modification, are permitted provided that the following conditions are met:

1. Redistributions of source code must retain the above copyright notice,
   this list of conditions and the following disclaimer.

2. Redistributions in binary form must reproduce the above copyright notice,
   this list of conditions and the following disclaimer in the documentation
   and/or other materials provided with the distribution.

THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS"
AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE
IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE
ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE
LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR
CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF
SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS
INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN
CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE)
ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE
POSSIBILITY OF SUCH DAMAGE.
```
