# Wiring of the serial data link

The serial data link between the control computer and the individual transmitters and the phasing matrix uses differential serial bus [RS485](https://en.wikipedia.org/wiki/RS-485). The bus cable is plugged into the DB9 connector on the rear of each transmitter and
both the TX and RX signals go first into the transmitter via a set of pins and then out via a different set of pins to the next transmitter.

The transceiver IC used in the microcontroller boards is [SN75ALS176B](https://www.ti.com/product/SN75ALS176B)

| Pin | Description | Pin | Description |
| --- | --- | --- | --- |
| 1 | GND | 6 | RXB out |
| 2 | TXB out | 7 | RXA out |
| 3 | TXA out | 8 | RXB in |
| 4 | TXB in | 9 | RXA in |
| 5 | TXA in |

- [ ] The details of the termination of the bus is probably buried somewhere in the radar documentation
