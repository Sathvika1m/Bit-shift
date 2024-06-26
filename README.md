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


# Hardware Security Testing Using Fault Injection

## Fault Injection
Fault injection is a technique used to test the robustness and reliability of hardware systems by deliberately introducing faults and observing how the system behaves. It is a critical method for identifying vulnerabilities, ensuring reliability, and verifying the resilience of hardware against various types of faults and attacks.

## Purpose of Fault Injection
1. **Evaluate System Robustness:** To assess how well a system can handle unexpected faults and to ensure it fails gracefully.
2. **Identify Vulnerabilities:** To find potential security weaknesses that could be exploited by attackers.
3. **Improve Design:** To provide insights that help in refining and improving the design of hardware systems.
4. **Compliance and Standards:** To ensure that hardware meets specific industry standards and compliance requirements.
5. **Test Fault Tolerance Mechanisms:** To validate the effectiveness of fault tolerance and error correction mechanisms implemented in the hardware.

## Methods of Fault Injection
### Hardware-Based Fault Injection
- **Electromagnetic Interference (EMI):** Using electromagnetic fields to disrupt the normal operation of hardware.
- **Voltage Glitching:** Briefly lowering or raising the voltage to induce faults.
- **Clock Glitching:** Manipulating the clock signal to cause timing errors.
- **Laser Fault Injection:** Using focused laser beams to induce faults at specific locations on a chip.

### Software-Based Fault Injection
- **Code Insertion:** Inserting fault-inducing code into the software running on the hardware.
- **API Manipulation:** Altering API calls to simulate faults.
- **Exception Triggering:** Forcing exceptions to see how the hardware handles error conditions.

### Simulation-Based Fault Injection
- **Fault Simulators:** Using simulation tools to model and inject faults in a virtual environment.
- **Emulators:** Using hardware emulators to mimic real-world conditions and introduce faults.

## Importance of Fault Injection Testing
1. **Security Assurance:** Helps in uncovering vulnerabilities that could be exploited by attackers, ensuring better security.
2. **Reliability Improvement:** Identifies weaknesses that can be addressed to improve the overall reliability of the hardware.
3. **Risk Management:** Provides a way to understand and mitigate potential risks associated with hardware failures.
4. **Quality Assurance:** Ensures that the hardware meets the required quality standards and performs reliably under fault conditions.
5. **Compliance:** Helps in meeting regulatory and industry standards that require rigorous testing and validation.

## Challenges and Considerations
1. **Complexity:** Fault injection testing can be complex and requires a thorough understanding of both hardware and software systems.
2. **Cost:** Setting up fault injection testing environments, especially hardware-based methods, can be expensive.
3. **Skill Requirements:** Requires specialized knowledge and skills to design and execute effective fault injection tests.
4. **False Positives/Negatives:** Risk of producing misleading results, either by not triggering actual vulnerabilities (false negatives) or by triggering irrelevant faults (false positives).
5. **Impact on Hardware:** Physical fault injection methods can cause permanent damage to hardware, necessitating careful planning and consideration.
6. **Interpretation of Results:** Analyzing the results of fault injection tests can be challenging and requires expert judgment.
7. **Test Coverage:** Ensuring comprehensive coverage of all potential fault scenarios can be difficult.
8. **Safety:** Ensuring the safety of testers and equipment when using methods like high-voltage or laser fault injection.

## Fault Injection in Traffic Signal Automation System:
During the development of the traffic signal control system, we encountered a security issue where a different IR transmitter device could trigger the signal, causing unauthorized changes to the traffic lights. This fault injection posed a significant risk, as it could lead to traffic disruptions and potential accidents.

## Solution
To address this issue, we implemented a key-based authentication mechanism. The solution involves transmitting a specific code as a key using an IR transmitter. Only if the received code matches the predefined key, the signal will be changed. This is achieved by using an Arduino Uno as the key decoder, which is connected to the Minisquadron microcontroller. The Arduino receives the IR signal, decodes the key, and communicates the result to the Minisquadron to control the traffic lights.

![trafficsignal](https://github.com/Skandan-M-M/Bit-shift/assets/121422638/c97c743a-f992-4b22-bdc1-0acda2eeec99)

## Final updated code:
```c 

#include <ch32v00x.h>
#include <debug.h>

// Define GPIO ports and pins for traffic signals
#define RED_LED_1_GPIO_PORT GPIOD
#define RED_LED_1_GPIO_PIN GPIO_Pin_3
#define GREEN_LED_1_GPIO_PORT GPIOD
#define GREEN_LED_1_GPIO_PIN GPIO_Pin_4

#define RED_LED_2_GPIO_PORT GPIOC
#define RED_LED_2_GPIO_PIN GPIO_Pin_0
#define GREEN_LED_2_GPIO_PORT GPIOC
#define GREEN_LED_2_GPIO_PIN GPIO_Pin_2

#define RED_LED_3_GPIO_PORT GPIOC
#define RED_LED_3_GPIO_PIN GPIO_Pin_3
#define GREEN_LED_3_GPIO_PORT GPIOC
#define GREEN_LED_3_GPIO_PIN GPIO_Pin_4

#define RED_LED_4_GPIO_PORT GPIOC
#define RED_LED_4_GPIO_PIN GPIO_Pin_5
#define GREEN_LED_4_GPIO_PORT GPIOC
#define GREEN_LED_4_GPIO_PIN GPIO_Pin_6

#define IR_1_GPIO_PORT GPIOD
#define IR_1_GPIO_PIN GPIO_Pin_6
#define IR_2_GPIO_PORT GPIOD
#define IR_2_GPIO_PIN GPIO_Pin_5
#define IR_3_GPIO_PORT GPIOD
#define IR_3_GPIO_PIN GPIO_Pin_2
#define IR_4_GPIO_PORT GPIOD
#define IR_4_GPIO_PIN GPIO_Pin_0
#define INPUT_GPIO_PORT GPIOC
#define INPUT_GPIO_PIN GPIO_Pin_1


// Enable clocks for the GPIO ports
#define RED_LED_1_CLOCK_ENABLE RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOD, ENABLE)
#define GREEN_LED_1_CLOCK_ENABLE RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOD, ENABLE)
#define RED_LED_2_CLOCK_ENABLE RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOC, ENABLE)
#define GREEN_LED_2_CLOCK_ENABLE RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOC, ENABLE)
#define RED_LED_3_CLOCK_ENABLE RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOC, ENABLE)
#define GREEN_LED_3_CLOCK_ENABLE RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOC, ENABLE)
#define RED_LED_4_CLOCK_ENABLE RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOC, ENABLE)
#define GREEN_LED_4_CLOCK_ENABLE RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOC, ENABLE)
#define IR_CLOCK_ENABLE RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOD, ENABLE)
#define INPUT_GPIO_CLOCK_ENABLE RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOC, ENABLE)

int flag = 0, i = 0;
int counter = 0;
int timer = 0;
int arr[4] = {0, 0, 0, 0};
int top = 0, itr = 0;
int size = 4;

void NMI_Handler(void) __attribute__((interrupt("WCH-Interrupt-fast")));
void HardFault_Handler(void) __attribute__((interrupt("WCH-Interrupt-fast")));
void ifamb();

int is_ambulance_detected(uint16_t ir_pin) {
    // Read the state of the IR receiver pin
    GPIO_TypeDef *ir_port;
    switch (ir_pin) {
        case IR_1_GPIO_PIN:
            ir_port = IR_1_GPIO_PORT;
            break;
        case IR_2_GPIO_PIN:
            ir_port = IR_2_GPIO_PORT;
            break;
        case IR_3_GPIO_PIN:
            ir_port = IR_3_GPIO_PORT;
            break;
        case IR_4_GPIO_PIN:
            ir_port = IR_4_GPIO_PORT;
            break;
        default:
            return 0; // Assuming no ambulance detected for invalid pin
    }
    uint8_t ir_state = GPIO_ReadInputDataBit(ir_port, ir_pin);

    // Check if IR receiver detects an ambulance (assuming active low)
    return ir_state;
}


int existnum(int a) {
    for (int i = 0; i < 4; i++)
        if (arr[i] == a)
            return 8;
    return 7;
}

int control_traffic_signals() {
    
uint8_t lane = GPIO_ReadInputDataBit(INPUT_GPIO_PORT, INPUT_GPIO_PIN);
    flag = 0;
    if (is_ambulance_detected(IR_1_GPIO_PIN)|| ~(lane)&1 ) {
        if(existnum(1)==7){
            arr[itr] = 1;
            itr++;
            itr%=size;
        }
        flag++;
    } else if (((!is_ambulance_detected(IR_1_GPIO_PIN) || (lane)&1)) && arr[top] == 1){
        arr[top] = 0;
        top++;
        top%=size;
    }
    if (is_ambulance_detected(IR_2_GPIO_PIN)) {
        if(existnum(2)==7){
            arr[itr] = 2;
            itr++;
            itr%=size; 
        }
        flag++;
    } else if ((!is_ambulance_detected(IR_2_GPIO_PIN)) && arr[top] == 2){
        arr[top] = 0;
        top++;
        top%=size;
    }
    if (is_ambulance_detected(IR_3_GPIO_PIN)) {
        if(existnum(3) == 7){
            arr[itr] = 3;
            itr++;
            itr%=size;
        }
        flag++;
    } else if((!is_ambulance_detected(IR_3_GPIO_PIN)) && arr[top] == 3){
        arr[top] = 0;
        top++;
        top%=size;
    }
    if (is_ambulance_detected(IR_4_GPIO_PIN) ) {
        if(existnum(4)==7){
            arr[itr] = 4;
            itr++;
            itr%=size;   
        }
        flag++;
    } else if ((!is_ambulance_detected(IR_4_GPIO_PIN)) && arr[top] == 4){
        arr[top] = 0;
        top++;
        top%=size;
    }
    if(flag>0)
        ifamb();
    lane =0;
    return flag;
}

void ifamb() {
    if (arr[top] == 1) {

        GPIO_SetBits(GREEN_LED_1_GPIO_PORT, GREEN_LED_1_GPIO_PIN);
        GPIO_ResetBits(RED_LED_1_GPIO_PORT, RED_LED_1_GPIO_PIN);
        GPIO_SetBits(RED_LED_2_GPIO_PORT, RED_LED_2_GPIO_PIN);
        GPIO_ResetBits(GREEN_LED_2_GPIO_PORT, GREEN_LED_2_GPIO_PIN);
        GPIO_SetBits(RED_LED_3_GPIO_PORT, RED_LED_3_GPIO_PIN);
        GPIO_ResetBits(GREEN_LED_3_GPIO_PORT, GREEN_LED_3_GPIO_PIN);
        GPIO_SetBits(RED_LED_4_GPIO_PORT, RED_LED_4_GPIO_PIN);
        GPIO_ResetBits(GREEN_LED_4_GPIO_PORT, GREEN_LED_4_GPIO_PIN);
        Delay_Ms(1000);
    } else if (arr[top] == 2) {
        GPIO_SetBits(RED_LED_1_GPIO_PORT, RED_LED_1_GPIO_PIN);
        GPIO_ResetBits(GREEN_LED_1_GPIO_PORT, GREEN_LED_1_GPIO_PIN);
        GPIO_SetBits(GREEN_LED_2_GPIO_PORT, GREEN_LED_2_GPIO_PIN);
        GPIO_ResetBits(RED_LED_2_GPIO_PORT, RED_LED_2_GPIO_PIN);
        GPIO_SetBits(RED_LED_3_GPIO_PORT, RED_LED_3_GPIO_PIN);
        GPIO_ResetBits(GREEN_LED_3_GPIO_PORT, GREEN_LED_3_GPIO_PIN);
        GPIO_SetBits(RED_LED_4_GPIO_PORT, RED_LED_4_GPIO_PIN);
        GPIO_ResetBits(GREEN_LED_4_GPIO_PORT, GREEN_LED_4_GPIO_PIN);
        Delay_Ms(1000);
    } else if (arr[top] == 3) {
        GPIO_SetBits(RED_LED_1_GPIO_PORT, RED_LED_1_GPIO_PIN);
        GPIO_ResetBits(GREEN_LED_1_GPIO_PORT, GREEN_LED_1_GPIO_PIN);
        GPIO_SetBits(RED_LED_2_GPIO_PORT, RED_LED_2_GPIO_PIN);
        GPIO_ResetBits(GREEN_LED_2_GPIO_PORT, GREEN_LED_2_GPIO_PIN);
        GPIO_SetBits(GREEN_LED_3_GPIO_PORT, GREEN_LED_3_GPIO_PIN);
        GPIO_ResetBits(RED_LED_3_GPIO_PORT, RED_LED_3_GPIO_PIN);
        GPIO_SetBits(RED_LED_4_GPIO_PORT, RED_LED_4_GPIO_PIN);
        GPIO_ResetBits(GREEN_LED_4_GPIO_PORT, GREEN_LED_4_GPIO_PIN);
        Delay_Ms(1000);
    } else if(arr[top]==4) {
        GPIO_SetBits(RED_LED_1_GPIO_PORT, RED_LED_1_GPIO_PIN);
        GPIO_ResetBits(GREEN_LED_1_GPIO_PORT, GREEN_LED_1_GPIO_PIN);
        GPIO_SetBits(RED_LED_2_GPIO_PORT, RED_LED_2_GPIO_PIN);
        GPIO_ResetBits(GREEN_LED_2_GPIO_PORT, GREEN_LED_2_GPIO_PIN);
        GPIO_SetBits(RED_LED_3_GPIO_PORT, RED_LED_3_GPIO_PIN);
        GPIO_ResetBits(GREEN_LED_3_GPIO_PORT, GREEN_LED_3_GPIO_PIN);
        GPIO_SetBits(GREEN_LED_4_GPIO_PORT, GREEN_LED_4_GPIO_PIN);
        GPIO_ResetBits(RED_LED_4_GPIO_PORT, RED_LED_4_GPIO_PIN);
        Delay_Ms(1000);
    }
    Delay_Ms(2000);
}



int main(void) {
    NVIC_PriorityGroupConfig(NVIC_PriorityGroup_2);
    SystemCoreClockUpdate();
    Delay_Init(); // Use the existing Delay_Init function provided by the framework

    GPIO_InitTypeDef GPIO_InitStructure = {0};

    // Initialize GPIO for LED 1
    RED_LED_1_CLOCK_ENABLE;
    GPIO_InitStructure.GPIO_Pin = RED_LED_1_GPIO_PIN;
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP;
    GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
    GPIO_Init(RED_LED_1_GPIO_PORT, &GPIO_InitStructure);

    GREEN_LED_1_CLOCK_ENABLE;
    GPIO_InitStructure.GPIO_Pin = GREEN_LED_1_GPIO_PIN;
    GPIO_Init(GREEN_LED_1_GPIO_PORT, &GPIO_InitStructure);

    // Initialize GPIO for LED 2
    RED_LED_2_CLOCK_ENABLE;
    GPIO_InitStructure.GPIO_Pin = RED_LED_2_GPIO_PIN;
    GPIO_Init(RED_LED_2_GPIO_PORT, &GPIO_InitStructure);

    GREEN_LED_2_CLOCK_ENABLE;
    GPIO_InitStructure.GPIO_Pin = GREEN_LED_2_GPIO_PIN;
    GPIO_Init(GREEN_LED_2_GPIO_PORT, &GPIO_InitStructure);

    // Initialize GPIO for LED 3
    RED_LED_3_CLOCK_ENABLE;
    GPIO_InitStructure.GPIO_Pin = RED_LED_3_GPIO_PIN;
    GPIO_Init(RED_LED_3_GPIO_PORT, &GPIO_InitStructure);

    GREEN_LED_3_CLOCK_ENABLE;
    GPIO_InitStructure.GPIO_Pin = GREEN_LED_3_GPIO_PIN;
    GPIO_Init(GREEN_LED_3_GPIO_PORT, &GPIO_InitStructure);

    // Initialize GPIO for LED 4
    RED_LED_4_CLOCK_ENABLE;
    GPIO_InitStructure.GPIO_Pin = RED_LED_4_GPIO_PIN;
    GPIO_Init(RED_LED_4_GPIO_PORT, &GPIO_InitStructure);

    GREEN_LED_4_CLOCK_ENABLE;
    GPIO_InitStructure.GPIO_Pin = GREEN_LED_4_GPIO_PIN;
    GPIO_Init(GREEN_LED_4_GPIO_PORT, &GPIO_InitStructure);

    INPUT_GPIO_CLOCK_ENABLE;
    GPIO_InitStructure.GPIO_Pin = INPUT_GPIO_PIN;
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_IPU;  // Input with pull-up
    GPIO_Init(INPUT_GPIO_PORT, &GPIO_InitStructure);


    while (1) {

        if (counter == 1)
            goto a;
        else if (counter == 2)
            goto b;
        else if (counter == 3)
            goto c;
        else if (counter == 4)
            goto d;

        // Signal 1: Green
        timer = 0;
    a:
        if (control_traffic_signals() != 0) {
            counter = 1;
            continue;
        }
        GPIO_SetBits(GREEN_LED_1_GPIO_PORT, GREEN_LED_1_GPIO_PIN);
        GPIO_ResetBits(RED_LED_1_GPIO_PORT, RED_LED_1_GPIO_PIN);
        GPIO_SetBits(RED_LED_2_GPIO_PORT, RED_LED_2_GPIO_PIN);
        GPIO_ResetBits(GREEN_LED_2_GPIO_PORT, GREEN_LED_2_GPIO_PIN);
        GPIO_SetBits(RED_LED_3_GPIO_PORT, RED_LED_3_GPIO_PIN);
        GPIO_ResetBits(GREEN_LED_3_GPIO_PORT, GREEN_LED_3_GPIO_PIN);
        GPIO_SetBits(RED_LED_4_GPIO_PORT, RED_LED_4_GPIO_PIN);
        GPIO_ResetBits(GREEN_LED_4_GPIO_PORT, GREEN_LED_4_GPIO_PIN);
        Delay_Ms(500);
        timer++;
        // Green for 10 seconds
        if (timer < 10)
            goto a;
        timer = 0;
    b:
        if (control_traffic_signals() != 0) {
            counter = 2;
            continue;
        }
        // Signal 1: Red, Signal 2: Green
        GPIO_SetBits(RED_LED_1_GPIO_PORT, RED_LED_1_GPIO_PIN);
        GPIO_ResetBits(GREEN_LED_1_GPIO_PORT, GREEN_LED_1_GPIO_PIN);
        GPIO_SetBits(GREEN_LED_2_GPIO_PORT, GREEN_LED_2_GPIO_PIN);
        GPIO_ResetBits(RED_LED_2_GPIO_PORT, RED_LED_2_GPIO_PIN);
        GPIO_SetBits(RED_LED_3_GPIO_PORT, RED_LED_3_GPIO_PIN);
        GPIO_ResetBits(GREEN_LED_3_GPIO_PORT, GREEN_LED_3_GPIO_PIN);
        GPIO_SetBits(RED_LED_4_GPIO_PORT, RED_LED_4_GPIO_PIN);
        GPIO_ResetBits(GREEN_LED_4_GPIO_PORT, GREEN_LED_4_GPIO_PIN);
        Delay_Ms(500);
        timer++; // Green for 10 seconds
        if (timer < 10)
            goto b;
        timer = 0;
    c:
        if (control_traffic_signals() != 0) {
            counter = 3;
            continue;
        }
        // Signal 2: Red, Signal 3: Green
        GPIO_SetBits(RED_LED_1_GPIO_PORT, RED_LED_1_GPIO_PIN);
        GPIO_ResetBits(GREEN_LED_1_GPIO_PORT, GREEN_LED_1_GPIO_PIN);
        GPIO_SetBits(RED_LED_2_GPIO_PORT, RED_LED_2_GPIO_PIN);
        GPIO_ResetBits(GREEN_LED_2_GPIO_PORT, GREEN_LED_2_GPIO_PIN);
        GPIO_SetBits(GREEN_LED_3_GPIO_PORT, GREEN_LED_3_GPIO_PIN);
        GPIO_ResetBits(RED_LED_3_GPIO_PORT, RED_LED_3_GPIO_PIN);
        GPIO_SetBits(RED_LED_4_GPIO_PORT, RED_LED_4_GPIO_PIN);
        GPIO_ResetBits(GREEN_LED_4_GPIO_PORT, GREEN_LED_4_GPIO_PIN);
        Delay_Ms(500); // Green for 10 seconds
        timer++;
        if (timer < 10)
            goto c;
        timer = 0;
    d:
        if (control_traffic_signals() != 0) {
            counter = 4;
            continue;
        }
        // Signal 3: Red, Signal 4: Green
        GPIO_SetBits(RED_LED_1_GPIO_PORT, RED_LED_1_GPIO_PIN);
        GPIO_ResetBits(GREEN_LED_1_GPIO_PORT, GREEN_LED_1_GPIO_PIN);
        GPIO_SetBits(RED_LED_2_GPIO_PORT, RED_LED_2_GPIO_PIN);
        GPIO_ResetBits(GREEN_LED_2_GPIO_PORT, GREEN_LED_2_GPIO_PIN);
        GPIO_SetBits(RED_LED_3_GPIO_PORT, RED_LED_3_GPIO_PIN);
        GPIO_ResetBits(GREEN_LED_3_GPIO_PORT, GREEN_LED_3_GPIO_PIN);
        GPIO_SetBits(GREEN_LED_4_GPIO_PORT, GREEN_LED_4_GPIO_PIN);
        GPIO_ResetBits(RED_LED_4_GPIO_PORT, RED_LED_4_GPIO_PIN);
        Delay_Ms(500); // Green for 10 seconds
        timer++;
        if (timer < 10)
            goto d;
        timer = 0;
        counter = 1;
    }
}

void NMI_Handler(void) {}
void NMI_Handler1(void);
void HardFault_Handler(void) {
    while (1) {
    }
}
````
## Aurdino Code 

```cpp
#include <IRremote.h>
 
// Define sensor pin
const int RECV_PIN = 7;
const int SEND_PIN = 13;
 
// Define IR Receiver and Results Objects
IRrecv irrecv(RECV_PIN);
decode_results results;
 
void setup(){
  // Serial Monitor @ 9600 baud
  Serial.begin(9600);
  // Enable the IR Receiver
  irrecv.enableIRIn();
  // Set SEND_PIN as output
  pinMode(SEND_PIN, OUTPUT);
}
 
void loop(){
  if (irrecv.decode(&results)){
    // Print Code in HEX
    Serial.println(results.value, HEX);
    
    // Check for specific IR code and set SEND_PIN accordingly
    if(results.value == 0xFFFFFFFF){  // Replace with the actual IR code you expect
      digitalWrite(SEND_PIN, LOW);
    }
    else{
       digitalWrite(SEND_PIN, HIGH);
    }
    // Send the state of SEND_PIN via Serial
  //  Serial.write(digitalRead(SEND_PIN));
    Serial.println(digitalRead(SEND_PIN));
    
    // Resume receiving IR signals
    irrecv.resume();
  }
}
````

##Tankyou
