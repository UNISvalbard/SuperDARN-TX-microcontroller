# AGC microcontroller command set and data format
The microcontroller is connected to the control computer via an RS485 serial link with data rate 9600, 8 bits, no parity and 1 stop bit. 
The control computer sends a command packet and, if no errors in the packet are detected, the microcontroller executes the command. The
microcontroller should respond within 20ms and the control computer should use a timeout mechanism to detect no responses.

The default state after the power up is the following:
- Relay: closed
- AGC loop: closed
- Cap 1: closed (in)
- Cap 2: open (out)
- Auto reset: disabled

# Command packet format

| Byte | 0   | 1    | 2   | 3   | 4   |
| ---  | --- | --- | --- | --- | --- |
|      | STX | Addr | len | Cmd | Bcs |

| Byte | Description |
| --- | --- |
| STX | The start of a command, always 0x55 |
| Addr | The microcontroller address in the range 1-254 decimal. Do not use 0x55 (decimal 85)|
| Len | The length of the data field. For command packets, this is always 1 |
| Cmd | The command number 1-13 decimal |
| Bcs | Block checksum: this is the least significant 8 bits of the sum of bytes 1, 2 and 3 |

For example, sending a command #1 (read status) to microcontroller #2 would be
```
0x55 0x02 0x01 0x01 0x04
```

# Command set

| Cmd | Name | Notes |
| --- |--- | --- |
| 1 | Read status | Returns the AGC status in a special packet |
| 2 | Reset relay | Closes the relay. The relay will trip again if the fault is still present |
| 3 | Trip relay | Opens the relay |
| 4 | Close loop | Closes the AGC loop |
| 5 | Open loop | Opens the AGC loop |
| 6 | Close Cap 1 | Put the damping capacitor 1 in the circuit |
| 7 | Open Cap 1 | Removes capacitor 1 from the circuit |
| 8 | Close Cap 2 | Put the damping capacitor 2 in the circuit |
| 9 | Open Cap 2 | Removes capacitor 2 from the circuit |
| 10 | Restart | Restarts the microcontroller. In practice, puts the microcontroller into an infinite loop and the watchdog then reset the system |
| 11 | Ping | The microcontroller responds with an ACK packet |
| 12 | Enable auto reset | See below |
| 13 | Disable auto reset | See below |

## Auto reset

If auto reset is enable, the microcontroller will attempt to reset the relay after a fault trip. There will be three attempts:
- the first attempt will take place 5 seconds after the trip
- the second attempt will be made 1 minute later
- the third attempt will be made 5 minutes later

If the third attempt fails, the relay will stay open until reset by command 2.

# Microcontroller responses

## Commands 2-13 
The microcontroller will respond with an ACK packet where Addr is the address of the microcontroller.

| Byte | 0   | 1    |
| ---  | --- | --- | 
|      | STX | Addr |

## Command 1
The microcontroller will respond with a packet

| Byte | 0  | 1  | 2  | 3  | 4  | 5  | 6  | 7  | 8  | 9  | 10  |
| ---  | --- | --- | --- | --- | --- | --- | --- | --- | --- | ---- | --- |
|      | STX | 0x00 | 0x0C (12) | Addr | ad0 | ad1 | ad2 | ad3 | ad4 | ad5 | ad6 |

| Byte | 11  | 12  | 13  | 14  | 15  |
| ---  | --- | --- | --- | --- | --- |
|      | ad7 | portA | portB | portC | Bcs |

