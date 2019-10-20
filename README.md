Compulink is a feature present in JVC stereo components in the 80's (and possibly 90's). It is a multi-master low speed TTL serial bus that runs over mono 3.5mm cables. The bus lets the stereo components control each other. For example:
- When you press play on the tape deck, it will tell the amplifier to switch to the tape input. 
- When you switch the amplifier to the tape input, it will tell the tape deck to start playing. 
- When the CD player starts playing, it tells the tape deck and record player to stop.

## Electrical Interface
- Connection: 3.5mm tip-and-sleeve connector. Each component has 2 Compulink jacks to support daisy chaining. The data is connected to the tip of the 3.5mm connector. The sleeve is connected to ground only on some components. On others, it's not connected.
  - Components that do not have the sleeve connected to ground need to be grounded through RCA audio cables. Failure to ground the components together will result in ground loops that prevent any communication on the bus! It's best to make sure all components are connected together with RCA audio cables.
- Voltage range: 0-5V, normally low with pull down resistors

## Data Encoding
Each word contains 8 bits, transmitted from MSB to LSB. (The service manual of the SEA-RM20 remote control unit lists the command values, and I observed the commands being transmitted from MSB to LSB) The bit encoding is similar to the [NEC remote protocol](https://www.circuitvalley.com/2013/09/nec-protocol-ir-infrared-remote-control.html):

        0    1
    5V __  __
    
    0V   __  ______
       \  /\      /      
       10ms  20ms
- A "0" bit is encoded as 5ms of 5V followed by 5ms of 0V.
- A "1" bit is encoded as 5ms of 5V followed by 15ms of 0V.
- The word is ended with 5ms of 5V to allow the receiver to tell if the last bit is 0 or 1.

Example: 0x20 on the wire:

        0   0     1     0   0   0   0   0  END
    5V __  __  __      __  __  __  __  __  __
    
    0V   __  __  ______  __  __  __  __  __  

## Known Commands
Command values are from the SEA-RM20 service manual. I also verified each of them on an oscilloscope.

### Tape Deck
COMMAND        | VALUE
---------------|------
PLAY           | 0x20
PLAY*          | 0x21
STOP           | 0x22
FF             | 0x23
REW            | 0x24
PAUSE          | 0x25
REC MUTE       | 0x26
REC            | 0x29
TRACK SEARCH + | 0x2B
TRACK SEARCH - | 0x2C
REC PAUSE      | 0x2D
STOP*          | 0x2E

\* alternate codes, no functional difference on a TD-W30 tape deck.

### CD Player
COMMAND | VALUE
--------|------
PLAY    | 0x41
STOP    | 0x42
TRACK + | 0x43
TRACK - | 0x44

### Phono
COMMAND | VALUE
--------|------
PLAY    | 0x87
STOP    | 0x86

### Amplifier
COMMAND | VALUE
--------|------
PHONO   | 0xA1
TUNER   | 0xA2
CD      | 0xA3
TAPE    | 0xA4

Amplifier commands are used to switch input

### Tuner
COMMAND   | VALUE
----------|------
POWER ON  | 0xC0
POWER OFF | 0xC1
FM        | 0xC2
AM        | 0xC3
PRESET +  | 0xC6
PRESET -  | 0xC7
PRESET #0 | 0xD0
PRESET #1 | 0xD1
PRESET #2 | 0xD2
PRESET #3 | 0xD3
PRESET #4 | 0xD4
PRESET #5 | 0xD5
PRESET #6 | 0xD6
PRESET #7 | 0xD7
PRESET #8 | 0xD8
PRESET #9 | 0xD9

Note that the PRESET #0 to PRESET #9 commands are not used to directly select a preset. They are used to enter a preset number digit-by-digit.

## See Also
http://www.johnwillis.com/2018/09/av-compu-link-unmodulated-ir-daisy.html
