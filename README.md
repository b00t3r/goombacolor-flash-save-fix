# Goomba Color – Flash Save Fix

I created this fork because Lesserkuma’s batteryless-save support could not save progress on the reproduction cartridge I had on hand. My cartridge uses Flash for its save device instead of writable SRAM: saves could be loaded and appear to work, but the old save returned after a reboot.

This fork bypasses that save device and backs up Goomba’s internal Game Boy save image directly to unused ROM Flash, fixing the stale-save problem on this hardware.

When using in-game saves, save from the Game Boy game first, then open the Goomba menu and save with "A".
Save states capture the live emulator state and can be saved separately.

I used a code assistant in this project. The build was tested on real hardware.

## Using the built ROM

Append a legally dumped Game Boy ROM to `goomba.gba` to make a GBA ROM:

```sh
cat goomba.gba game.gb > game-goomba.gba
```

For a Game Boy Color game, use its `.gbc` file instead:

```sh
cat goomba.gba game.gbc > game-goomba.gba
```

On Windows PowerShell, use:

```powershell
cmd /c copy /b goomba.gba+game.gb game-goomba.gba
```

Flash the resulting `game-goomba.gba` to the cartridge.

## Original Lesserkuma documentation

### Goomba Color

Originally created by Flubba and Dwedit

Source code imported from https://www.dwedit.org/gba/goombacolor.php

#### Modifications

##### Batteryless saving

This allows Goomba Color to save its SRAM data on most modern reproduction/bootleg flash cartridges that have an SRAM chip but no battery installed. This works by writing the SRAM data into Flash ROM memory and restoring the data from Flash ROM into SRAM when booting.

The Flash ROM is updated in the following instances:

- Pressing L+R to bring up the main menu (with a prompt)
- Writing a Save State or Quick Save State by pressing R+SELECT
- Deleting a Save State or SRAM
- Exiting

The batteryless Goomba Color SRAM storage area in Flash ROM is dynamically determined and depends on the maximum ROM size of the flash cartridge. In case you need to extract/inject/edit something, you will find the data at ROM address `flash_size - 0x40000` and the length is 0x10000 bytes. Emulators should also be able to extract the SRAM data into a `.sav` file from a re-dumped compilation.

If you need to manually specify the save data location in ROM, you can append the compiled ROM with `53 56 4C 43` (SVLC) followed by the address in little endian (`00 00 0C 00` would be offset `0xC0000` on the flash chip). Make sure the address is correctly aligned to your flash chip’s sector size and that it’s accessible from the GBA ROM space (max. 32 MB).

Holding SELECT+UP+B on startup will wipe the save data in case something is not working right.

Tested repro cartridge flash chips: 128W30B, 256L30B, 29LV128DT, M36L0R7050T, M36L0R8060T, GL128S, M29W128FH, MSP55LV128M
