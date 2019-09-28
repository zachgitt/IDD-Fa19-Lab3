# Data Logger (and using cool sensors!)

*A lab report by Zachary Gittelman.*

## In The Report

Include your responses to the bold questions on your own fork of [this lab report template](https://github.com/FAR-Lab/IDD-Fa18-Lab2). Include snippets of code that explain what you did. Deliverables are due next Tuesday. Post your lab reports as README.md pages on your GitHub, and post a link to that on your main class hub page.

For this lab, we will be experimenting with a variety of sensors, sending the data to the Arduino serial monitor, writing data to the EEPROM of the Arduino, and then playing the data back.

## Part A.  Writing to the Serial Monitor
**a. Based on the readings from the serial monitor, what is the range of the analog values being read?** <br>
The readings range from 0 to 1023.
 
**b. How many bits of resolution does the analog to digital converter (ADC) on the Arduino have?** <br>
The values range from 0 is 1023. Thus there are 2^10 possible values which can be represented using 10 bits.

## Part B. RGB LED
**How might you use this with only the parts in your kit? Show us your solution.** <br>
[![RGB LED Video](https://github.com/zachgitt/IDD-Fa19-Lab3/blob/master/rgb_thumbnail.png)](https://youtu.be/8cb9lj_1cPc)

## Part C. Voltage Varying Sensors 
 
### 1. FSR, Flex Sensor, Photo cell, Softpot
**a. What voltage values do you see from your force sensor?** <br>
When resting the FSR is reading 0 on the analog and when pressed it reads upto 1023. Because the analog ontput on the serial monitor is linearly related to voltage the voltage when the FSR is resting is 0V and when pressed is nearly 5V. 

**b. What kind of relationship does the voltage have as a function of the force applied? (e.g., linear?)** <br>
When force is applied the voltage does not increase linearly. It increases at a smaller amount, therefore the square root of force is related to the voltage potential.

**c. Can you change the LED fading code values so that you get the full range of output voltages from the LED when using your FSR?** <br>
```
/*
  Fade

  This example shows how to fade an LED on pin 9 using the analogWrite()
  function.

  The analogWrite() function uses PWM, so if you want to change the pin you're
  using, be sure to use another PWM capable pin. On most Arduino, the PWM pins
  are identified with a "~" sign, like ~3, ~5, ~6, ~9, ~10 and ~11.

  This example code is in the public domain.

  http://www.arduino.cc/en/Tutorial/Fade
*/

int redPin = 11;           // the PWM pin the LED is attached to
int greenPin = 10;
int bluePin = 9;
int brightness = 0;    // how bright the LED is
int fadeAmount = 5;    // how many points to fade the LED by

// the setup routine runs once when you press reset:
void setup() {
  // declare pin 9 to be an output:
  pinMode(redPin, OUTPUT);
  pinMode(greenPin, OUTPUT);
  pinMode(bluePin, OUTPUT);
}

// the loop routine runs over and over again forever:
void loop() {
  // set the brightness of pin 9:
  analogWrite(redPin, brightness);
  analogWrite(greenPin, brightness);
  analogWrite(bluePin, brightness);

  // change the brightness for next time through the loop:
  brightness = brightness + fadeAmount;

  // reverse the direction of the fading at the ends of the fade:
  if (brightness <= 0 || brightness >= 255) {
    fadeAmount = -fadeAmount;
  }
  // wait for 30 milliseconds to see the dimming effect
  delay(30);
}
```

**d. What resistance do you need to have in series to get a reasonable range of voltages from each sensor?** <br>
With some experimentation, 10kOhm in series with 6kOhm produces around 600 on the analog. 10kOhm with 12kOhm produced around 400. 10kOhm with 18kOhm produced around 300.

**e. What kind of relationship does the resistance have as a function of stimulus? (e.g., linear?)** <br>
Given the relationship V=IR we know that resistance and voltage are linearly related.

### 2. Accelerometer
**a. Include your accelerometer read-out code in your write-up.** <br>
[![Accelerometer LED RGB](https://github.com/zachgitt/IDD-Fa19-Lab3/blob/master/acc_thumb.png)](https://youtu.be/X27SueGEUZA)
```
// Basic demo for accelerometer readings from Adafruit LIS3DH

#include <Wire.h>
#include <SPI.h>
#include <Adafruit_LIS3DH.h>
#include <Adafruit_Sensor.h>
#include <LiquidCrystal.h>

// Used for software SPI
#define LIS3DH_CLK 13
#define LIS3DH_MISO 12
#define LIS3DH_MOSI 11
// Used for hardware & software SPI
#define LIS3DH_CS 10

// software SPI
//Adafruit_LIS3DH lis = Adafruit_LIS3DH(LIS3DH_CS, LIS3DH_MOSI, LIS3DH_MISO, LIS3DH_CLK);
// hardware SPI
//Adafruit_LIS3DH lis = Adafruit_LIS3DH(LIS3DH_CS);
// I2C
Adafruit_LIS3DH lis = Adafruit_LIS3DH();

// LCD
LiquidCrystal lcd(12, 11, 5, 4, 3, 2);

// RGB
int redPin = 9;
int greenPin = 8;
int bluePin = 7;

void setup(void) {
#ifndef ESP8266
  while (!Serial);     // will pause Zero, Leonardo, etc until serial console opens
#endif

  // LCD columns and rows
  lcd.begin(16,2);

  Serial.begin(9600);
  Serial.println("LIS3DH test!");
  
  if (! lis.begin(0x18)) {   // change this to 0x19 for alternative i2c address
    Serial.println("Couldnt start");
    while (1);
  }
  Serial.println("LIS3DH found!");
  
  lis.setRange(LIS3DH_RANGE_4_G);   // 2, 4, 8 or 16 G!
  
  Serial.print("Range = "); Serial.print(2 << lis.getRange());  
  Serial.println("G");

  // RGB
  pinMode(redPin, OUTPUT);
  pinMode(greenPin, OUTPUT);
  pinMode(bluePin, OUTPUT);
}

void loop() {
  lis.read();      // get X Y and Z data at once
  // Then print out the raw data
  Serial.print("X:  "); Serial.print(lis.x); 
  Serial.print("  \tY:  "); Serial.print(lis.y); 
  Serial.print("  \tZ:  "); Serial.print(lis.z); 

  /* Or....get a new sensor event, normalized */ 
  sensors_event_t event; 
  lis.getEvent(&event);
  
  /* Display the results (acceleration is measured in m/s^2) */
  Serial.print("\t\tX: "); Serial.print(event.acceleration.x);
  Serial.print(" \tY: "); Serial.print(event.acceleration.y); 
  Serial.print(" \tZ: "); Serial.print(event.acceleration.z); 
  Serial.println(" m/s^2 ");

  Serial.println();

  // LCD, set cursor to column0, line1
  lcd.setCursor(0, 0);
  lcd.print("X: "); lcd.print(event.acceleration.x);
  lcd.print("Y: "); lcd.print(event.acceleration.y);
  lcd.setCursor(0, 1);
  lcd.print("Z: "); lcd.print(event.acceleration.z);
 
  delay(200); 

  // RGB
  if (event.acceleration.x > 8.0) {
    setColor(255, 0, 0); // red
  }
  if (event.acceleration.y > 8.0) {
    setColor(0, 255, 0); // green
  }
  if (event.acceleration.z > 8.0) {
    setColor(0, 0, 255); // blue
  }
}

void setColor(int red, int green, int blue)
{
  #ifdef COMMON_ANODE
    red = 255 - red;
    green = 255 - green;
    blue = 255 - blue;
  #endif
  analogWrite(redPin, red);
  analogWrite(greenPin, green);
  analogWrite(bluePin, blue);  
}
```

## Part E. Graphic Display
**Take a picture of your screen working insert it here!** <br>
![Graphic](https://github.com/zachgitt/IDD-Fa19-Lab3/blob/master/graphic.png)
The following was the code used to write the voltage value from the potentiometer to the graphic display. Note, the numbers were printed in reverse order.

```
/**************************************************************************
 This is an example for our Monochrome OLEDs based on SSD1306 drivers

 Pick one up today in the adafruit shop!
 ------> http://www.adafruit.com/category/63_98

 This example is for a 128x32 pixel display using I2C to communicate
 3 pins are required to interface (two I2C and one reset).

 Adafruit invests time and resources providing this open
 source code, please support Adafruit and open-source
 hardware by purchasing products from Adafruit!

 Written by Limor Fried/Ladyada for Adafruit Industries,
 with contributions from the open source community.
 BSD license, check license.txt for more information
 All text above, and the splash screen below must be
 included in any redistribution.
 **************************************************************************/

#include <SPI.h>
#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>

#define SCREEN_WIDTH 128 // OLED display width, in pixels
#define SCREEN_HEIGHT 32 // OLED display height, in pixels

// Declaration for an SSD1306 display connected to I2C (SDA, SCL pins)
#define OLED_RESET     4 // Reset pin # (or -1 if sharing Arduino reset pin)
Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, OLED_RESET);

#define NUMFLAKES     10 // Number of snowflakes in the animation example

#define LOGO_HEIGHT   16
#define LOGO_WIDTH    16
static const unsigned char PROGMEM logo_bmp[] =
{ B00000000, B11000000,
  B00000001, B11000000,
  B00000001, B11000000,
  B00000011, B11100000,
  B11110011, B11100000,
  B11111110, B11111000,
  B01111110, B11111111,
  B00110011, B10011111,
  B00011111, B11111100,
  B00001101, B01110000,
  B00011011, B10100000,
  B00111111, B11100000,
  B00111111, B11110000,
  B01111100, B11110000,
  B01110000, B01110000,
  B00000000, B00110000 };

void setup() {
  Serial.begin(9600);

  // SSD1306_SWITCHCAPVCC = generate display voltage from 3.3V internally
  if(!display.begin(SSD1306_SWITCHCAPVCC, 0x3C)) { // Address 0x3C for 128x32
    Serial.println(F("SSD1306 allocation failed"));
    for(;;); // Don't proceed, loop forever
  }

  // Show initial display buffer contents on the screen --
  // the library initializes this with an Adafruit splash screen.
  display.display();
  delay(2000); // Pause for 2 seconds

  // Clear the buffer
  display.clearDisplay();
}

void loop() {
  // Read analog, view on serial monitor
  int sensorValue = analogRead(A0);

  // print out the value you read:
  Serial.println(sensorValue);

  // Set position to write
  display.clearDisplay();
  display.setTextSize(1);      // Normal 1:1 pixel scale
  display.setTextColor(WHITE); // Draw white text
  display.setCursor(0, 0);     // Start at top-left corner
  display.cp437(true);         // Use full 256 char 'Code Page 437' font

  // Write voltage
  display.write(sensorValue);
  while (sensorValue > 0) {
    display.write('0' + sensorValue % 10);
    sensorValue /= 10;
  }

  display.display();
  delay(2000);
}
```

## Part F. Logging values to the EEPROM and reading them back
 
### 1. Reading and writing values to the Arduino EEPROM
**a. Does it matter what actions are assigned to which state? Why?** <br>
No, the actions assigned to each state is arbitrary. State0 clears, state1 reads, and state2 writes. However each of these states can do anything they are defined to do. However there is a predefined order in this example.

**b. Why is the code here all in the setup() functions and not in the loop() functions?** <br>
We have most of our code within setup for each state because we want it to only run once. Then each state has its own loop which does minimal amount of work for each loop.

**c. How many byte-sized data samples can you store on the Atmega328?** <br>
There are 1024 bytes to write to on the Atmega328.

**d. How would you get analog data from the Arduino analog pins to be byte-sized? How about analog data from the I2C devices?** <br>
Arduino analog pins are 1024 values or 10 bits, so to convert to bytes or 8 bits we divide the values into 4 bytes. The I2C devices already communicate with byte size so don't need to be converted.

**e. Alternately, how would we store the data if it were bigger than a byte? (hint: take a look at the [EEPROMPut](https://www.arduino.cc/en/Reference/EEPROMPut) example)** <br>
You can store data based on multiple addresses on the EEPROM. 

**Upload your modified code that takes in analog values from your sensors and prints them back out to the Arduino Serial Monitor.** <br>
[Potentiometer Analog to Serial Monitor with EEPROM](https://github.com/zachgitt/IDD-Fa19-Lab3/blob/master/SwitchState2.ino)
![EEPROM Logging](https://github.com/zachgitt/IDD-Fa19-Lab3/blob/master/eeprom.png)

### 2. Design your logger
 
**a. Insert here a copy of your final state diagram.**
![State Diagram](https://github.com/zachgitt/IDD-Fa19-Lab3/blob/master/state_diagram.jpeg)

## Part G. Create your data logger!
**a. Record and upload a short demo video of your logger in action.** <br>
[![Accelerometer Logger to Speaker](https://github.com/zachgitt/IDD-Fa19-Lab3/blob/master/thumbnail.png)](https://youtu.be/hzdhDT0dEWw)
