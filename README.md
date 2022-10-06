# IoT

## ToDo2Tech

### Stap 1: installeer Adafruit IO Arduino (problemen met instaleren)

Het idee was dat ik het gewoon kon downlaoden vanuit de library, maar dat werkte niet bij mij. Ik volgde de stappen van https://learn.adafruit.com/adafruit-all-about-arduino-libraries-install-use/installing-a-library-on-mac-osx en downloaded de zip van https://github.com/adafruit/Adafruit_IO_Arduino/releases.


### Stap 2: Adafruit IO Setup

- Ik heb een Adafruit IO account aangemaakt en een dashboard aangemaakt via https://io.adafruit.com/
- Klik op “Get Started for Free 
- In Adafruit IO: ga de gele sleutel in het menu en neem de tekst van je key- en username over voor Arduino


### Stap 3: Adafruit IO Feed en Colorpicker aanmaken

Ga naar Dashboards > New Dashboard (Maak een dashboard aan) > create New Block (via het tandwiel icoon)
 
Kies color Picker
Create de feed name: color
Create Block
Stel een kleur in met de Color Picker (Ik had #0200ff)


### Stap 4: Code aanpassen 😁
In Arduino: File > Examples > Adafruit IO Arduino > Adafruitio_14_neopixel
In tab ‘config.h’: plak je Adafruit IO username en Key in
In tab ‘config.h’: voer het wifi netwerk en wachtwoord in 
Ik heb de hotspot van mijn telefoon gebruikt

in de Tab adafruit_14_Neopixel.ino
Pas: #define PIXEL_PIN 5 aan naar #define PIXEL_PIN D5


### Stap 5: Code uploaden (of druk eerst op het vinkje =  Verify) (problemen met code)

Toen ik op het vinkje drukte kreeg ik een foutmelding: 

///

In file included from /Users/sophieb/Documents/Arduino/libraries/Adafruit_IO_Arduino_423/src/AdafruitIO.h:22,
                 from /Users/sophieb/Documents/Arduino/libraries/Adafruit_IO_Arduino_423/src/wifi/AdafruitIO_ESP8266.h:20,
                 from /Users/sophieb/Documents/Arduino/libraries/Adafruit_IO_Arduino_423/src/AdafruitIO_WiFi.h:46,
                 from /private/var/folders/ym/qhtcldxs6jg7dv4vd6lqy6n40000gn/T/.arduinoIDE-unsaved202295-86902-186euej.wti1/adafruitio_14_neopixel/config.h:36,
                 from /private/var/folders/ym/qhtcldxs6jg7dv4vd6lqy6n40000gn/T/.arduinoIDE-unsaved202295-86902-186euej.wti1/adafruitio_14_neopixel/adafruitio_14_neopixel.ino:18:
/Users/sophieb/Documents/Arduino/libraries/Adafruit_IO_Arduino_423/src/AdafruitIO_Feed.h:21:10: fatal error: Adafruit_MQTT.h: No such file or directory
   21 | #include "Adafruit_MQTT.h"
      |          ^~~~~~~~~~~~~~~~~
compilation terminated.

exit status 1

Compilation error: exit status 1

///

Ik heb geprobeerd om de bestanden nog een keer te downloaden, maar dat werkte niet. Vervolgens heb ik gezocht op "arduino ide No such file or directory"
Dit was het antwoord:
"When you see a "No such file or directory" error it almost always means you need to install the library that contains the missing file. Often the code you're compiling will come with documentation (either a comment or separate document) that tells you where to get the library dependencies" (bron: https://forum.arduino.cc/t/fatal-error-arduinoble-h-no-such-file-or-directory/922962)

Ik heb via https://github.com/adafruit/Adafruit_MQTT_Library/releases/tag/2.4.3 de zip gedownload en in de library gezet van arduino. Ik heb dit biij ArduinoHttpClient en Adafruit_MQTT gedaan.


Upload de code > Activeer de ‘Serial Monitor’ > Open de serial monitor > serial monitor op 115200 baud
De serial monitor is verbonden en geeft de kleuren door.
Nu heb ik het probleem dat mijn led de hele tijd knippert met verschillende kleuren ipv de kleur die ik heb geselecteerd bij Adafruit.
Helaas is het me tot nu toe niet gelukt om het klipperen te stoppen.


