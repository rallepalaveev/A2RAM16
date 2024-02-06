# A2RAM16
This is an expansion card for Apple2 computers, aimed at those early machines which only have up to 48kB of RAM and are missing the upper 16kB, which are mapped to the 12kB ROM space $D000-$FFFF.
The cards that are available, AKA Language cards, not only occupy a slot, but also usually must have one of the DRAM chips removed from the motherboard and be linked with a cable to that socket. They usually provide more than an additional 16kB.

However, I wanted to create a card for the standart 50-pin slots, which simply adds the missing 16kB, based on modern chips like SRAM, and not needing to have any additional cables to connect to the motherboard.

**IMPORTANT: It is very important to NOT use this card on computers which already have the 16kB available as there would be clashes between the card and the existing RAM and supporting logic.**

This project is based on a card with one 32kB SRAM chip (only half is used), two PLD logics to decode siganls and one 4-way line buffers chip. I might also do a variant with 2x 8kB SRAM chips for those who'd hate to have the unused 1/2 of the SRAM chip - it would be a simple change.

The logic of the card completely follows the design recommendations of the additional 16kB:
* Two banks of 4kB are mapped to $D000-$DFFF
* One bank of 8kB is mapped to $E000-$FFFF

Bank-switched RAM soft switches:

| Address | Symbolic Name | Bank | Read From | Write to RAM? |
|:--------|:--------------|:-----|:----------|:--------------|
|$C080|READBSR2|2|RAM|No|
|$C081|WRITEBSR2|2|ROM|Yes*|
|$C082|OFFBSR2|2|ROM|No|
|$C083|RDWRBSR2|2|RAM|Yes*|
|$C088|READBSR1|1|RAM|No|
|$C089|WRITEBSR1|1|ROM|Yes*|
|$C08A|OFFBSR1|1|ROM|No|
|$C08B|RDWRBSR1|1|RAM|Yes*|

Bank-switched RAM status locations:

| Address | Symbolic Name | Description                      |
|:--------|:--------------|:---------------------------------|
|$C011|BSRBANK2|If this location is > = $80, then Bank2 of bankswitched RAM has been selected; if not, Bank1 has been selected.|
|$C012|BSRREADRAM|If this location is > = $80, then bank-switched RAM has been read-enabled; if not, the corresponding ROM locations are enabled.|

Operation:

The card houses the 3 softswitches above which upon reset are configured in the default configuration:

* Bank 2 is selected
* ROM is readable
* RAM is writeable

The card decodes the full A0-A15 address and if a memory location is selected from $D000 to $FFFF, it responds based on the 3 softswitches and R/W - it activates the RAM for writing or reading, while disabling the ROM by using the /INH signal.

For the innitial prototype design I used a transistor with open collector to drive the INH line. However, I quickly realized that I can do this with one of the 2 extra line drivers (LS125), by using the high ipmedance state when INH is not active and pulling it to GND when INH is activated. I also added 3 LEDs which show the status of the 3 soft switches BANK, WRRAM, RDRAM.

A test to be made for quick check of functionality:

]CALL-151           # Enter monitor

*F800<F800.FFFFM    # Copy monitor to RAM

*C083               # Set RAM for reading

This sequence should not lead to the computer freezing with a responce "$C083-", because the monitor is copied to the RAM and continues to work from RAM.

The benefit of the card is that programs which require 64kB of RAM can be used - primarily ProDOS and programs which can run with 64kB of RAM. It works from any 50-pin slot.

