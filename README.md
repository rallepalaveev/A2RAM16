# A2RAM16
This is an expansion card for Apple2 computers, aimed at those early machines which only have up to 48kB of RAM and are missing the upper 16kB, which are mapped to the 12kB ROM space $D000-$FFFF.
The cards that are available, AKA Language cards, not only occupy a slot, but also usually must have one of the DRAM chips removed from the motherboard and linked with a cable to that socket. They usually provide more than an additional 16kB.

However, I wanted to create a card which simply adds the missing 16kB, based on modern chips like SRAM, and not needing to have any additional cables to connect to the motherboard.

**IMPORTANT: It is very important to NOT use this card on computers which already have the 16kB available as there would be clashes between the card and the existing RAM and supporting logic.**

This project is based on a card with one 32kB SRAM chip (only half is used), two PLD logics to decode siganls and one 4-way line buffers chip. I might also do a variant with 2x 8kB SRAM chips for those who'd hate to have the unused 1/2 of the SRAM chip - it would be a simple change.

The logic of the card completely follows the design recommendations of the additional 16kB:
* Two banks of 4kB are mapped to $D000-$DFFF
* One bank of 8kB is mapped to $E000-$FFFF

Bank-switched RAM soft switches:

|Address  | Symbolic Name | Bank | Read From | Write to RAM? |
|:--------|:--------------|:-----|:----------|:--------------|
|$C080|READBSR2|2|RAM|No|
|$C081|WRITEBSR2|2|ROM|Yes*|
|$C082|OFFBSR2|2|ROM|No|
|$C083|RDWRBSR2|2|RAM|Yes*|
|$C088|READBSR1|1|RAM|No|
|$C089|WRITEBSR1|1|ROM|Yes*|
|$C08A|OFFBSR1|1ROM|No|
|$C08B|RDWRBSR1|1|RAM|Yes*|

Bank-switched RAM status locations:


|Address  | Symbolic Name | Description                      |
|:--------|:--------------|:---------------------------------|
|$C011|BSRBANK2|If this location is > = $80, then Bank2 of bankswitched RAM has been selected; if not, Bank1 has been selected.|
|$C012|BSRREADRAM|If this location is > = $80, then bank-switched RAM has been read-enabled; if not, the corresponding ROM locations are enabled.|



