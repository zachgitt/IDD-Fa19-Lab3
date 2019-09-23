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
When resting the FSR is reading a little over 300 on the analog and when pressed it reads upto 1023. Because the analog ontput on the serial monitor is linearly related to voltage the voltage when the FSR is resting is 300/1023 = x/5. Therefore the voltage x = ~1.46V when the FSR is not pushed (aka lowest resistance), and 5V (highest resistance) when pushed. 

**b. What kind of relationship does the voltage have as a function of the force applied? (e.g., linear?)** <br>
The relationship between resistance and voltage applied is linear as the serial monitor shows this relationship.

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
With some experimentation, 10kOhm has a high enough resistance to produce analog values near 300-400. Additional experimentation was done with 200kOhm and 660kOhm but the analog values showed at 1023.

**e. What kind of relationship does the resistance have as a function of stimulus? (e.g., linear?)** <br>
Given the relationship V=IR we know that resistance and voltage are linearly related.

### 2. Accelerometer
**a. Include your accelerometer read-out code in your write-up.** <br>


### 3. IR Proximity Sensor

**a. Describe the voltage change over the sensing range of the sensor. A sketch of voltage vs. distance would work also. Does it match up with what you expect from the datasheet?**

**b. Upload your merged code to your lab report repository and link to it here.**

## Optional. Graphic Display

**Take a picture of your screen working insert it here!**

## Part D. Logging values to the EEPROM and reading them back
 
### 1. Reading and writing values to the Arduino EEPROM

**a. Does it matter what actions are assigned to which state? Why?**

**b. Why is the code here all in the setup() functions and not in the loop() functions?**

**c. How many byte-sized data samples can you store on the Atmega328?**

**d. How would you get analog data from the Arduino analog pins to be byte-sized? How about analog data from the I2C devices?**

**e. Alternately, how would we store the data if it were bigger than a byte? (hint: take a look at the [EEPROMPut](https://www.arduino.cc/en/Reference/EEPROMPut) example)**

**Upload your modified code that takes in analog values from your sensors and prints them back out to the Arduino Serial Monitor.**

### 2. Design your logger
 
**a. Insert here a copy of your final state diagram.**

### 3. Create your data logger!
 
**a. Record and upload a short demo video of your logger in action.**
