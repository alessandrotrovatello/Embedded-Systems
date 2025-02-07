# Embedded System
Embedded System projects of Robotics Engineering at University of Genoa.

<p align="center">
  <img src="https://github.com/alessandrotrovatello/Embedded-Systems/blob/main/Images/mikroe-buggy.jpg" alt="Buggy" width="300">
</p>

### Hardware
The buggy used is a [Mikroe Buggy](https://www.mikroe.com/buggy).
### Software
All the code was developed in [MPLAB X IDE v6.20](http://www.microchip.com/mplab/mplab-x-ide) using [XC16 Compiler](http://www.microchip.com/mplab/compilers). The bootloader used is [mikroBootloader USB HID](https://www.mikroe.com/mikrobootloader) while for serial communication [HTERM](https://www.der-hammer.info/pages/terminal.html).


## Assignment
1) Simulate an algorithm that needs 7 ms for its execution, and
needs to work at 100 Hz. This is to emulate a real world scenario.
2) Acquire the three magnetometer axes at 25 Hz (set the data rate
to 0b110 in the 0x4C register).
3) Do the average of the last 5 measurements.
4) Send it to the UART at 5 Hz using the protocol `$MAG,x,y,z*`,
where x is the average value on the x-axis, y is the average value
of the y-axis, and z is the average z-axis value.
5) Compute the angle to the magnetic North using
atan2(y avg,x avg).
6) Send the computed angle at 5 Hz using the message `$YAW,x*`,
where x is the angle in degrees.

## Project Overview: Autonomous Buggy Control System
This project implements a **basic control system** for an **autonomous ground vehicle (buggy)**. The firmware is responsible for processing movement commands, controlling motors, handling obstacle detection, and monitoring the battery.

## Firmware Requirements

### Main Loop
- Runs at **1 kHz** frequency.
- Controls motors and reads the **IR sensor** at **1 kHz**.
- Starts in **"Wait for Start"** mode:
  - Motors are **off**.
  - LED **A0 and indicators blink at 1 Hz**.

- **Transition to "Execute" Mode:**  
  - Pressing **button RE8** moves the system to "Execute" mode.
  - The buggy processes **movement commands** received via UART.

### Command Processing
Commands follow the format:
`$PCCMD,x,t*`
  
  Where:
- `x` = Motion type  
  - 1 → Forward motion  
  - 2 → Counterclockwise rotation  
  - 3 → Clockwise rotation  
  - 4 → Backward motion  
- `t` = Duration (milliseconds)

The system stores commands in a **FIFO queue** (max **10** commands).
If the queue is **full**, it sends an error message:
`$MACK,0*`

If no commands are available, the buggy remains in the **"Execute"** state but stops moving.

### Motor Control
Uses **four PWM signals** (**10 kHz**) for motor control.
Pin assignments:

| Motion                  | Pin | Signal |
|-------------------------|----|--------|
| Left wheels forward     | RD2 | `left_pwm > 0` |
| Left wheels backward    | RD1 | `left_pwm < 0` |
| Right wheels forward    | RD4 | `right_pwm > 0` |
| Right wheels backward   | RD3 | `right_pwm < 0` |

### Obstacle Detection
- **IR sensor** prevents the buggy from getting closer than **20 cm** to obstacles.
- Located on **Mikrobus 1 or 2** (front of buggy).
- Uses **AN14/AN15** for reading and **RB9/RA3** for enabling.

### Battery Monitoring
- Battery voltage is read from **AN11**.
- Uses a **voltage divider** (200kΩ + 100kΩ resistors).

### UART Communication
- **Command Interface & Data Logging** via **UART → RS232**.
- The microcontroller sends the following data:
- **Battery voltage** (1 Hz): `$MBATT,v_batt*`
  
- **Distance sensor reading** (10 Hz): `$MDIST,distance*`
  
- Uses **RD0 (TX)** and **RD11 (RX)**.

## Evaluation Criteria
- Adherence to specifications.
- Correct **interrupt handling**.
- Proper **data sharing** and **buffer management**.
- Clean and efficient **code structure**.

## Pin Mapping

| Function               | Pin  |
|------------------------|------|
| Left-side lights       | RB8  |
| Right-side lights      | RF1  |
| Brakes                 | RF0  |
| Low-intensity lights   | RG1  |
| Beam headlights        | RA7  |
| Battery sensing        | AN11 |
| Left PWM (backward)    | RD1  |
| Left PWM (forward)     | RD2  |
| Right PWM (backward)   | RD3  |
| Right PWM (forward)    | RD4  |
| IR sensor voltage      | AN14/AN15 |
| IR sensor enable       | RB9/RA3 |
| UART TX                | RD0 |
| UART RX                | RD11 |

---

## Summary
This project implements an **embedded control system** for an **autonomous buggy**, processing real-time movement commands, monitoring the battery, and preventing collisions using **PWM signals, UART communication, and IR sensors**.


  



