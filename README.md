# JTAGscan

JTAGscan allows you to find JTAG ports without buying expensive hardware like the JTAGulator.
All you need is a Arduino compatible board and optionally a logic level shifter.

## How does it work?

JTAGscan iterates through configured set of pins to find the JTAG TMS, TCK, TDO and TDI lines
using two approaches:
- by reading ID CODE to find TMS, TCK and TDO, TDI is found by shifting bits in BYPASS mode
- by shifting bits in BYPASS mode to find TMS, TCK, TDO and TDI
The first approach is quicker and thus preferred.

Unfortunately not all chips support ID CODE retrieval so BYPASS mode scan will be needed for them.

## How to use it?

- hook up your Arduino to the target
- use a logic level shifer between Arduino and target if required
- adjust PIN_MASK and PIN_MAX for your setup
- select proper build target
- build the project and upload it to your board
- establish serial connection with baud rate of 115200
- ammend debug options if required
- use option _h_ for help
- use option _a_ to automatically enumerate pins using available methods

## How does it look like?

- help
```
> h
---------------------------------
-- JTAGscan Jtag Pinout Finder --
---------------------------------
 a - Automatically find all pins
 i - IDCODE search for pins
 b - BYPASS search for pins
 t - TDI-only BYPASS search 
 d - set debug level: 0
 p - adjust max pins used: 8
 h - print this help
---------------------------------
```
- automatic enumeration on a Kinetis based board
```
> a
     Automatically searching
--- Starting with IDCODE scan ---
| TCK | TMS | TDO |      IDCODE |
---------------------------------
|   2 |   3 |   0 |    cba00477 |
------------ SUCCESS ------------
    TCK, TMS, and TDO found.
--- BYPASS searching, just TDI --
| TCK | TMS | TDO | TDI | Width |
---------------------------------
|   2 |   3 |   0 |   1 |    31 |
------------ SUCCESS ------------
```

- toggle debug level to 1, then ID CODE enumeration of TCK, TMS and TDO lines
```
> d
Debug level set to 1
> i
------- IDCODE searching --------
| TCK | TMS | TDO |      IDCODE |
---------------------------------
|   0 |   1 |   2 |           0 |
|   0 |   1 |   3 |           0 |
|   0 |   1 |   4 |           0 |
|   0 |   1 |   5 |           0 |
|   0 |   2 |   1 |           0 |
|   0 |   2 |   3 |           0 |
|   0 |   2 |   4 |           0 |
|   0 |   2 |   5 |           0 |
|   0 |   3 |   1 |           0 |
|   0 |   3 |   2 |           0 |
|   0 |   3 |   4 |           0 |
|   0 |   3 |   5 |           0 |
|   0 |   4 |   1 |           0 |
|   0 |   4 |   2 |           0 |
|   0 |   4 |   3 |           0 |
|   0 |   4 |   5 |           0 |
|   0 |   5 |   1 |           0 |
|   0 |   5 |   2 |           0 |
|   0 |   5 |   3 |           0 |
|   0 |   5 |   4 |           0 |
|   1 |   0 |   2 |           0 |
|   1 |   0 |   3 |           0 |
|   1 |   0 |   4 |           0 |
|   1 |   0 |   5 |           0 |
|   1 |   2 |   0 |           0 |
|   1 |   2 |   3 |           0 |
|   1 |   2 |   4 |           0 |
|   1 |   2 |   5 |           0 |
|   1 |   3 |   0 |           0 |
|   1 |   3 |   2 |           0 |
|   1 |   3 |   4 |           0 |
|   1 |   3 |   5 |           0 |
|   1 |   4 |   0 |           0 |
|   1 |   4 |   2 |           0 |
|   1 |   4 |   3 |           0 |
|   1 |   4 |   5 |           0 |
|   1 |   5 |   0 |           0 |
|   1 |   5 |   2 |           0 |
|   1 |   5 |   3 |           0 |
|   1 |   5 |   4 |           0 |
|   2 |   0 |   1 |           0 |
|   2 |   0 |   3 |           0 |
|   2 |   0 |   4 |           0 |
|   2 |   0 |   5 |           0 |
|   2 |   1 |   0 |           0 |
|   2 |   1 |   3 |           0 |
|   2 |   1 |   4 |           0 |
|   2 |   1 |   5 |           0 |
|   2 |   3 |   0 |    4ba00477 |
------------ SUCCESS ------------
| TCK | TMS | TDO |      IDCODE |
------- IDCODE complete ---------
```

- enumeration of TDI following a ID CODE scan
```
> t                
--- BYPASS searching, just TDI --
| TCK | TMS | TDO | TDI | Width |
---------------------------------
|   2 |   3 |   0 |   1 |    31 |
------------ SUCCESS ------------
```

- full identification of lines using BYPASS mode
```
> b
------- BYPASS searching --------
| TCK | TMS | TDO | TDI | Width |
---------------------------------
|   0 |   1 |   2 |   3 |     0 |
|   0 |   1 |   2 |   4 |     0 |
|   0 |   1 |   2 |   5 |     0 |
|   0 |   1 |   3 |   2 |     0 |
|   0 |   1 |   3 |   4 |     0 |
|   0 |   1 |   3 |   5 |     0 |
|   0 |   1 |   4 |   2 |     0 |
|   0 |   1 |   4 |   3 |     0 |
|   0 |   1 |   4 |   5 |     0 |
...
|   2 |   1 |   4 |   3 |     0 |
|   2 |   1 |   4 |   5 |     0 |
|   2 |   1 |   5 |   0 |     0 |
|   2 |   1 |   5 |   3 |     0 |
|   2 |   1 |   5 |   4 |     0 |
|   2 |   3 |   0 |   1 |    31 |
------------ SUCCESS ------------
| TCK | TMS | TDO | TDI | Width |
------- BYPASS complete ---------
```

## No Platformio?

No problem, just copy the contents of src/main.cpp to a new Arduino project, remove the Arduino.h import from the first line and you're ready to go.
