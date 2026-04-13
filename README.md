# klipper-voron-2.4

## Firmware flashing procedures

### Toolhead board

1. `make menuconfig`
   
    - Enable extra low-level configuration options: enabled
    - Micro-controller Architecture: Raspberry Pi RP2040
    - Bootloader Offset: 16KiB bootloader
    - Communication interface: CAN Bus
    - CAN RX gpio number: 4
    - CAN TX gpio number: 5
    - CAN bus speed: 100000
    - GPIO pins to set at micro-controller startup: gpio26

2. `make clean && make`
3. `sudo service klipper stop`
4. Get CAN UUID: `python3 ~/katapult/scripts/flashtool.py -i can0 -q`
    Should read: `Detected UUID: aedcf1cbced7, Application: Katapult`
5. Flash firmware: `python3 ~/katapult/scripts/flashtool.py -i can0 -f ~/klipper/out/klipper.bin -u aedcf1cbced7`
6. Check status: `python3 ~/katapult/scripts/flashtool.py -i can0 -q`
    Should read: `Detected UUID: aedcf1cbced7, Application: Klipper`

### Main board

1. `make menuconfig`
   
    - Enable extra low-level configuration options: enabled
    - Micro-controller Architecture: STM32
    - Processor model: STM32H723
    - Bootloader Offset: 128KiB bootloader
    - Clock reference: 25 MHz crystal
    - Communication interface: USB to CAN bus bridge (PA11/PA12)
    - CAN bus interface: PD0/PD1
    - CAN bus speed: 1000000
    - GPIO pins to set at micro-controller startup: disabled
    
3. `make clean && make`
4. `sudo service klipper stop`
5. Set board to bootloader mode: `~/klippy-env/bin/python ~/katapult/scripts/flashtool.py -i can0 -u c8e5bd7c949e -r`
6. Flash firmware: `make flash FLASH_DEVICE=/dev/ttyACM0`
7. Start Klipper: `sudo service klipper start`
