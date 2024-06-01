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

![circuit-diagram](https://github.com/Skandan-M-M/Bit-shift/assets/121422638/fffcf63d-f8d6-4df3-8c84-76809e908019)


## Table for Pin Connection

| Component            | Pin on VSDSquadron Mini | Description                       |
|----------------------|-------------------------|-----------------------------------|
| IR Receiver LED 1    | PD6                  | Input from IR sensor 1            |
| IR Receiver LED 2    | PD5                  | Input from IR sensor 2            |
| IR Receiver LED 3    | PD2                  | Input from IR sensor 3            |
| IR Receiver LED 4    | PD0                  | Input from IR sensor 4            |
| Red LED 1              | PD3                  | Red light control of signal 1              |
| Green LED 1           | PD4                  | Green light control of signal 1             |
| Red LED 2              | PC0                  | Red light control of signal 2               |
| Green LED 2           | PC2                  | Green light control of signal 2             |
| Red LED 3              | PC3                  | Red light control of signal 3              |
| Green LED 3           | PC4                  | Green light control of signal 3             |
| Red LED 4              | PC5                  | Red light control of signal 4              |
| Green LED 4           | PC6                  | Green light control of signal 4             |

## Code :
```c
#include <ch32v00x.h>
#include <debug.h>

// Defining GPIO ports and pins for traffic signals
#define R1_PORT GPIOD
#define R1_PIN GPIO_Pin_3
#define G1_PORT GPIOD
#define G1_PIN GPIO_Pin_4

#define R2_PORT GPIOC
#define R2_PIN GPIO_Pin_0
#define G2_PORT GPIOC
#define G2_PIN GPIO_Pin_2

#define R3_PORT GPIOC
#define R3_PIN GPIO_Pin_3
#define G3_PORT GPIOC
#define G3_PIN GPIO_Pin_4

#define R4_PORT GPIOC
#define R4_PIN GPIO_Pin_5
#define G4_PORT GPIOC
#define G4_PIN GPIO_Pin_6

#define IR1_PORT GPIOD
#define IR1_PIN GPIO_Pin_6
#define IR2_PORT GPIOD
#define IR2_PIN GPIO_Pin_5
#define IR3_PORT GPIOD
#define IR3_PIN GPIO_Pin_2
#define IR4_PORT GPIOD
#define IR4_PIN GPIO_Pin_0

// Enabling clocks for the GPIO ports
#define R1_CLOCK_ENABLE RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOD, ENABLE)
#define G1_CLOCK_ENABLE RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOD, ENABLE)
#define R2_CLOCK_ENABLE RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOC, ENABLE)
#define G2_CLOCK_ENABLE RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOC, ENABLE)
#define R3_CLOCK_ENABLE RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOC, ENABLE)
#define G3_CLOCK_ENABLE RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOC, ENABLE)
#define R4_CLOCK_ENABLE RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOC, ENABLE)
#define G4_CLOCK_ENABLE RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOC, ENABLE)
#define IR_CLOCK_ENABLE RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOD, ENABLE)

int flag = 0;

void NMI_Handler(void) __attribute__((interrupt("WCH-Interrupt-fast")));
void HardFault_Handler(void) __attribute__((interrupt("WCH-Interrupt-fast")));

int is_ambulance_detected(uint16_t ir_pin) {
    // Reading the state of the IR receiver pin
    GPIO_TypeDef *ir_port;
    switch (ir_pin) {
        case IR1_PIN:
            ir_port = IR1_PORT;
            break;
        case IR2_PIN:
            ir_port = IR2_PORT;
            break;
        case IR3_PIN:
            ir_port = IR3_PORT;
            break;
        case IR4_PIN:
            ir_port = IR4_PORT;
            break;
        default:
            return 0; // Assuming no ambulance detected for invalid pin
    }
    uint8_t ir_state = GPIO_ReadInputDataBit(ir_port, ir_pin);

    // Checking if IR receiver detects an ambulance (assuming active low)
    return !ir_state;
}

int control_traffic_signals() {
    flag = 0;                   //using flag var to handle the cases
    if (is_ambulance_detected(IR1_PIN)) {
        GPIO_SetBits(R1_PORT, R1_PIN);
        GPIO_ResetBits(G1_PORT, G1_PIN);
        flag++;
    } else {
        GPIO_SetBits(G1_PORT, G1_PIN);
        GPIO_ResetBits(R1_PORT, R1_PIN);
    }
    if (is_ambulance_detected(IR2_PIN)) {
        GPIO_SetBits(R2_PORT, R2_PIN);
        GPIO_ResetBits(G2_PORT, G2_PIN);
        flag++;
    } else {
        GPIO_SetBits(G2_PORT, G2_PIN);
        GPIO_ResetBits(R2_PORT, R2_PIN);
    }
    if (is_ambulance_detected(IR3_PIN)) {
        GPIO_SetBits(R3_PORT, R3_PIN);
        GPIO_ResetBits(G3_PORT, G3_PIN);
        flag++;
    } else {
        GPIO_SetBits(G3_PORT, G3_PIN);
        GPIO_ResetBits(R3_PORT, R3_PIN);
    }
    if (is_ambulance_detected(IR4_PIN)) {
        GPIO_SetBits(R4_PORT, R4_PIN);
        GPIO_ResetBits(G4_PORT, G4_PIN);
        flag++;
    } else {
        GPIO_SetBits(G4_PORT, G4_PIN);
        GPIO_ResetBits(R4_PORT, R4_PIN);
    }

    return flag;
}

int main(void) {
    NVIC_PriorityGroupConfig(NVIC_PriorityGroup_2);
    SystemCoreClockUpdate();
    Delay_Init();  

    GPIO_InitTypeDef GPIO_InitStructure = {0};

    // Initializing GPIO for LED 1
    R1_CLOCK_ENABLE;
    GPIO_InitStructure.GPIO_Pin = R1_PIN;
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP;
    GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
    GPIO_Init(R1_PORT, &GPIO_InitStructure);

    G1_CLOCK_ENABLE;
    GPIO_InitStructure.GPIO_Pin = G1_PIN;
    GPIO_Init(G1_PORT, &GPIO_InitStructure);

    // Initializing GPIO for LED 2
    R2_CLOCK_ENABLE;
    GPIO_InitStructure.GPIO_Pin = R2_PIN;
    GPIO_Init(R2_PORT, &GPIO_InitStructure);

    G2_CLOCK_ENABLE;
    GPIO_InitStructure.GPIO_Pin = G2_PIN;
    GPIO_Init(G2_PORT, &GPIO_InitStructure);

    // Initializing GPIO for LED 3
    R3_CLOCK_ENABLE;
    GPIO_InitStructure.GPIO_Pin = R3_PIN;
    GPIO_Init(R3_PORT, &GPIO_InitStructure);

    G3_CLOCK_ENABLE;
    GPIO_InitStructure.GPIO_Pin = G3_PIN;
    GPIO_Init(G3_PORT, &GPIO_InitStructure);

    // Initializing GPIO for LED 4
    R4_CLOCK_ENABLE;
    GPIO_InitStructure.GPIO_Pin = R4_PIN;
    GPIO_Init(R4_PORT, &GPIO_InitStructure);

    G4_CLOCK_ENABLE;
    GPIO_InitStructure.GPIO_Pin = G4_PIN;
    GPIO_Init(G4_PORT, &GPIO_InitStructure);

    while (1) {
        // Signal 1: Green
        if (control_traffic_signals() != 4)
            continue;
        GPIO_SetBits(G1_PORT, G1_PIN);
        GPIO_ResetBits(R1_PORT, R1_PIN);
        GPIO_SetBits(R2_PORT, R2_PIN);
        GPIO_SetBits(R3_PORT, R3_PIN);
        GPIO_SetBits(R4_PORT, R4_PIN);
        Delay_Ms(1000);  // Green for 1 second (maybe 60secs in real-world application)

        if (control_traffic_signals() != 4)
            continue;
        // Signal 1: Red, Signal 2: Green
        GPIO_SetBits(R1_PORT, R1_PIN);
        GPIO_ResetBits(G1_PORT, G1_PIN);
        GPIO_ResetBits(R2_PORT, R2_PIN);
        GPIO_SetBits(G2_PORT, G2_PIN);
        Delay_Ms(1000);  // Green for 1 second

        if (control_traffic_signals() != 4)
            continue;
        // Signal 2: Red, Signal 3: Green
        GPIO_SetBits(R2_PORT, R2_PIN);
        GPIO_ResetBits(G2_PORT, G2_PIN);
        GPIO_ResetBits(R3_PORT, R3_PIN);
        GPIO_SetBits(G3_PORT, G3_PIN);
        Delay_Ms(1000);  // Green for 1 second

        if (control_traffic_signals() != 4)
            continue;
        // Signal 3: Red, Signal 4: Green
        GPIO_SetBits(R3_PORT, R3_PIN);
        GPIO_ResetBits(G3_PORT, G3_PIN);
        GPIO_ResetBits(R4_PORT, R4_PIN);
        GPIO_SetBits(G4_PORT, G4_PIN);
        Delay_Ms(1000);  // Green for 1 second

        // Signal 4: Red
        GPIO_SetBits(R4_PORT, R4_PIN);
        GPIO_ResetBits(G4_PORT, G4_PIN);
    }
}

void NMI_Handler(void) {}
void HardFault_Handler(void) {
    while (1) {
    }
}

````

## DEMO VIDEO:



https://github.com/Skandan-M-M/Bit-shift/assets/121422638/4ec98c27-f859-491b-bd36-7b52c1d8ea93





