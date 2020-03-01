# Flash-ESP-01-firmware
ESP-01 firmware(mikroprogram) az AT parancsokra majd a MicroPython-ra.
> Az AT parancsokhoz

Mindenekelőtt telepíteni kell az **esptool** alkalmazást. Mivel Python alapú alkalmazás, legelőször a python.org -ról telepíteni kell a Pythont. Telepítéskor engedélyezni kell a patch hozzáférést. Ezutén lehet csak telepíteni az esptool-t, aminek a részletes leírása a következő hivatkozáson érhető el: 
https://github.com/espressif/esptool

Az ESP-t flash módba kell kötni. Ezt bármilyen UART soros kommunikációt támogató eszközzel megtehetjük. A file-ok között megtalálod az usb to serial bekötésére: esp_flash_mode.jpg-t, és az Arduino bekötésre: esp_arduino_flash_mode.png-t.
A gyártók legtöbbször a legújabb mikroprogrammal forgalmazzák az esp-t. Az esptool segítségével kiolvashatjuk mi van rajta. Nyissuk meg a parancssort(cmd) és írjuk a következő parancsot: **esptool flash_id**.

Valami ehhez hasonlót kell, hogy kiírjon:
```
esptool.py v2.8
Found 1 serial ports
Serial port COM5
Connecting....
Detecting chip type... ESP8266
Chip is ESP8266EX
Features: WiFi
Crystal is 26MHz
MAC: 5c:cf:7f:b8:16:98
Uploading stub...
Running stub...
Stub running...
Manufacturer: e0
Device: 4014
Detected flash size: 1MB
Hard resetting via RTS pin...
```
Ha hibát jelez akkor vagy rosszul van bekötve, esetleg nem kap megfelelő táplálást(legalább 500 mA-es táplálást kell biztosítani ), vagy szerencsétlenségünkre nincs a ROM-ba beleégetve a bootloder(vagyis nem tud kommunikálni a soros porton. Ha ilyen gond merül fel, akkor ugyan azon a hivatkozáson ahonnan letöltöttük az esptool.py-t).

Ezekből a modulokból készítettek 512 KByte és 1MByte -os verziókat. Az 512 KByte-os számunkra nem kielégítő. A hardware
követelményekről a MicroPython oldalán lehet informálódni: http://docs.micropython.org/en/latest/esp8266/tutorial/intro.html.

A filok között megtaláljátok az aktuális AT bin file-t, vagy töltsétek le innen: https://www.espressif.com/en/support/download/at .
Ezek után töröljük a flash-t a következő cmd paranccsal: **esptool erase_flash** , majd írjuk rá a firmware-t a következő paranccsal: **esptool.py --port COM5 write_flash 0x00000 v1.3.0.2 AT Firmware.bin** . 

