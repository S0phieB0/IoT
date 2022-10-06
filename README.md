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
Waarschijnlijk was er een oude code die er nog in zat. Ik heb later mijn arduino opnieuw in mijn computer gezet en de app opnieuw opgestart en toen werkte het wel.


### Mijn code:



/************************** Configuration ***********************************/

#include "config.h"

/************************ Example Starts Here *******************************/

#include "Adafruit_NeoPixel.h"

#define PIXEL_PIN     D5
#define PIXEL_COUNT   24
#define PIXEL_TYPE    NEO_GRB + NEO_KHZ800

Adafruit_NeoPixel pixels = Adafruit_NeoPixel(PIXEL_COUNT, PIXEL_PIN, PIXEL_TYPE);

// set up the 'color' feed
AdafruitIO_Feed *color = io.feed("color");

void setup() {

  // start the serial connection
  Serial.begin(115200);

  // wait for serial monitor to open
  while(! Serial);

  // connect to io.adafruit.com
  Serial.print("Connecting to Adafruit IO");
  io.connect();

  color->onMessage(handleMessage);

  // wait for a connection
  while(io.status() < AIO_CONNECTED) {
    Serial.print(".");
    delay(500);
  }

  // we are connected
  Serial.println();
  Serial.println(io.statusText());
  color->get();

  // neopixel init
  pixels.begin();
  pixels.show();

}

void loop() {

  io.run();

}

void handleMessage(AdafruitIO_Data *data) {

  // print RGB values and hex value
  Serial.println("Received HEX: ");
  Serial.println(data->value());

  long color = data->toNeoPixel();

  for(int i=0; i<PIXEL_COUNT; ++i) {
    pixels.setPixelColor(i, color);
  }

  pixels.show();

}


/************************ Adafruit IO Config *******************************/

#define IO_USERNAME  “*******”
#define IO_KEY       “******************”

/******************************* WIFI **************************************/



#define WIFI_SSID “********”
#define WIFI_PASS “*******”

#if defined(USE_AIRLIFT) || defined(ADAFRUIT_METRO_M4_AIRLIFT_LITE) ||         \
    defined(ADAFRUIT_PYPORTAL)
#if !defined(SPIWIFI_SS) // if the wifi definition isnt in the board variant
#define SPIWIFI SPI
#define SPIWIFI_SS 10 // Chip select pin
#define NINA_ACK 9    // a.k.a BUSY or READY pin
#define NINA_RESETN 6 // Reset pin
#define NINA_GPIO0 -1 // Not connected
#endif
AdafruitIO_WiFi io(IO_USERNAME, IO_KEY, WIFI_SSID, WIFI_PASS, SPIWIFI_SS,
                   NINA_ACK, NINA_RESETN, NINA_GPIO0, &SPIWIFI);
#else
AdafruitIO_WiFi io(IO_USERNAME, IO_KEY, WIFI_SSID, WIFI_PASS);
#endif


