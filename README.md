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

## Part F. Logging values to the EEPROM and reading them back
 
### 1. Reading and writing values to the Arduino EEPROM

**a. Does it matter what actions are assigned to which state? Why?**

**b. Why is the code here all in the setup() functions and not in the loop() functions?**

**c. How many byte-sized data samples can you store on the Atmega328?**

**d. How would you get analog data from the Arduino analog pins to be byte-sized? How about analog data from the I2C devices?**

**e. Alternately, how would we store the data if it were bigger than a byte? (hint: take a look at the [EEPROMPut](https://www.arduino.cc/en/Reference/EEPROMPut) example)**

**Upload your modified code that takes in analog values from your sensors and prints them back out to the Arduino Serial Monitor.**

### 2. Design your logger
 
**a. Insert here a copy of your final state diagram.**

## Part G. Create your data logger!
**a. Record and upload a short demo video of your logger in action.** <br>
