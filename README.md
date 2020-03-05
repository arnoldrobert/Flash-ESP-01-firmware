# Flash-ESP-01-firmware
ESP-01 firmware(mikroprogram) az AT parancsokra majd a MicroPython-ra.
> Az AT parancsokhoz

Mindenekelőtt telepíteni kell az **esptool.py** alkalmazást. Mivel Python alapú, legelőször a https://www.python.org -ról letöltjük a Pythont. Telepítéskor engedélyezni kell a path hozzáférést. Ezután lehet csak telepíteni az esptool.py-t, aminek a részletes leírása a következő hivatkozáson érhető el: https://github.com/espressif/esptool .

Az ESP-t flash módba kell kötni. Ezt bármilyen UART soros kommunikációt támogató eszközzel megtehetjük. A file-ok között megtalálod az usb to serial bekötésére: esp_flash_mode.jpg-t, és az Arduino bekötésre: esp_arduino_flash_mode.png-t.
 Az esptool segítségével kiolvashatjuk mi van rajta. Nyissuk meg a parancssort(cmd) és írjuk a következő parancsot: **esptool.py flash_id**.

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
Ha hibát jelez akkor vagy rosszul van bekötve, esetleg nem kap megfelelő táplálást (szomjas jószág, legalább 500 mA-os táplálást kell neki biztosítani, 1,65 W a 3,3 V-on ), vagy szerencsétlenségünkre nincs a ROM-ba beleégetve a bootloader (vagyis nem tud kommunikálni a soros porton. Ha ilyen gond merül fel, akkor ugyanazon az oldalon ahonnan letöltöttük az esptool.py-t találhatunk erre is leírást). Minden prompt() parancs előtt csatlakoztassuk le és fel (reseteljük) a modult és próbáljuk újra.

Sz ESP-01 modulokból készítettek 512 KByte és 1MByte -os verziókat. Az 512 KByte-os számunkra nem kielégítő. A hardware
követelményekről a MicroPython oldalán lehet informálódni: http://docs.micropython.org/en/latest/esp8266/tutorial/intro.html.

A file-ok között megtaláljátok az aktuális AT bin file-t, vagy töltsétek le innen: https://www.espressif.com/en/support/download/at .
Ezek után töröljük a flash-t a következő paranccsal: **esptool.py erase_flash** . Mielőtt ráírnánk az AT mikroprogramot én azt ajánlom, hogy nyissuk meg az Arduino IDE-t és egy üres oldalon válasszuk ki a portunkat, majd nyissuk meg a soros ablakot. Próbálgassuk a különböző kommunikációs sebességeknél (baud), hogy olvasható szöveget kapjunk. A következő képpen végezzük: beállítjuk pl. 115200 -ra majd le- és visszacsatlakoztatjuk a EN (CH_PD) lábat, amikor táplálást kap ki kell, hogy írjon olvashatóan egy szöveget a bootolással kapcsolatban. Nálam ennél a modulnál 74880 bps, ami egy nem megszokott sebesség (oco kínai). Azért kell ezt megnézni, mert ettől nem tehetjük nagyobb sebességre az esptoo.py-t ha írjuk rá a firmware-t. A prompt()-ban lépjünk abba a mappába ahova letöltöttük a v1.3.0.2_AT_Firmware.bin -t, majd a következő paranccsal írjuk a flash-re (nálam az USB to serial a COM5 porton van): **esptool.py --port COM5 --baud 74880 --no-stub write_flash -fs 1MB -fm qio 0x0 v1.3.0.2_AT_Firmware.bin** . 

Valami ehhez hasonlót kell, hogy kiírjon:
```
C:\Users\petof\Documents\ESP8266>esptool.py --port COM5 --baud 74880 --no-stub write_flash -fs 1MB -fm qio 0x0 v1.3.0.2_AT_Firmware.bin
esptool.py v3.0-dev
Serial port COM5
Connecting....
Detecting chip type... ESP8266
Chip is ESP8266EX
Features: WiFi
Crystal is 26MHz
MAC: bc:dd:c2:ed:a9:87
Enabling default SPI flash mode...
Configuring flash size...
Erasing flash...
Flash params set to 0x0320
Took 0.47s to erase flash block
Wrote 1044480 bytes at 0x00000000 in 220.6 seconds (37.9 kbit/s)...

Leaving...
Hard resetting via RTS pin...

C:\Users\petof\Documents\ESP8266>
```
**Soros kommunikáció a modullal**

Ezután kapcsoljuk ki-be a modult majd csatlakoztassuk le a GPIO0-t a GND(-)-ról. Az AT parancsokat bevihetjük az Arduino soros monitorjában, vagy akár a PuTTy-ba. Nyissuk meg a PuTTY-t, tegyük soros kommunikációra, állítsuk be a portot meg a sebességet 115200-ra, és nyissuk meg a soros kommunikációt. Írjuk be a következőt: AT+GMR nyomjunk egy entert majd ctrl+J. A következőhöz hasonlót kell, hogy kiírjon:
```
AT+GMR
AT version:0.40.0.0(Aug  8 2015 14:45:58)
SDK version:1.3.0
Ai-Thinker Technology Co.,Ltd.
Build:1.3.0.2 Sep 11 2015 11:48:04
OK
```
Az AT parancsok a következő hivatkozáson érhetők el: https://www.espressif.com/sites/default/files/documentation/4a-esp8266_at_instruction_set_en.pdf .
> A MicroPython parancsokhoz

Csatlakoztassuk vissza a GPIO0-t a GND(-)-ra és töröljük a flash-t a korábban leírtak szerint, utána írjuk rá az aktuális MicroPython bin file-t, amit a következő oldalról lehet elérni: http://micropython.org/download#esp8266 . Ha sikerült csatlakoztassuk le a GPIO0-t a GND(-)-ról és használhatjuk ismét a PuTTy-t a MicroPythonhoz. Amikor megnyílik a PuTTy nyomjunk egy entert, hogy megjelenjen a parancsértelmező és írhatjuk a MicroPython parancsokat :).
```
>>> print('Lazíthatsz, ha ezt látod SIKERULT ":)"')
Lazíthatsz, ha ezt látod SIKERULT ":)"
>>>
```
Remélem, hogy az ESP8266 modul firmware ráírása azok számára is érthető lesz, akik kevésbé járatosak ezekben a modulokban.
Az esptool.com-on kívül ajánlom még az **Adafruit ampy-t** ami a következő hivatkozáson érhető el: https://github.com/scientifichackers/ampy .
