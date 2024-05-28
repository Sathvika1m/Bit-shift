# Traffic Signal Automation System with Ambulance Detection


## Introduction

The Traffic Signal Automation System with Ambulance Detection aims to enhance urban traffic management by ensuring that emergency vehicles, such as ambulances, can pass through intersections without delay. By using IR transmitters in ambulances and IR receivers at traffic signals, the system prioritizes the passage of emergency vehicles, significantly reducing response times and potentially saving lives. The system is built around the VSDSquadron Mini RISC-V development board, which controls the traffic lights and processes the signals from the IR receivers.

## Overview

The Traffic Signal Automation System with Ambulance Detection uses a combination of hardware and software to manage traffic signals dynamically. The key components include IR transmitters in ambulances, IR receivers at traffic lights, and the VSDSquadron Mini RISC-V development board. The system detects the presence of an ambulance approaching an intersection and changes the traffic lights to green to facilitate the ambulance's quick passage. Once the ambulance has passed, the system reverts to its default traffic signal operation. This project aims to provide a scalable and cost-effective solution to improve emergency response times in urban areas.


## Components Required

- VSDSquadron Mini RISC-V Development Board
- IR Transmitter LED
- IR Receiver LEDs
- LEDs - Red and Green color
- Connecting wires
- 3.7V Li ion Battery

## Circuit Connection Diagram

![circuit-diagram](https://github.com/Skandan-M-M/Bit-shift/assets/121422638/cd6f52d8-9074-4c5a-8dbb-b906b12ec676)

## Table for Pin Connection

| Component            | Pin on VSDSquadron Mini | Description                       |
|----------------------|-------------------------|-----------------------------------|
| IR Receiver LED 1    | PD5                  | Input from IR sensor 1            |
| IR Receiver LED 2    | PC7                  | Input from IR sensor 2            |
| IR Receiver LED 3    | PC0                  | Input from IR sensor 3            |
| IR Receiver LED 4    | PC5                  | Input from IR sensor 4            |
| Red LED 1              | PD3                  | Red light control of signal 1              |
| Green LED 1           | PD4                  | Green light control of signal 1             |
| Red LED 2              | PD1                  | Red light control of signal 2               |
| Green LED 2           | PD2                  | Green light control of signal 2             |
| Red LED 3              | PC1                  | Red light control of signal 3              |
| Green LED 3           | PC2                  | Green light control of signal 3             |
| Red LED 4              | PC3                  | Red light control of signal 4              |
| Green LED 4           | PC4                  | Green light control of signal 4             |




