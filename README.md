# Digital Timer
 
Include your responses to the bold questions below. Include snippets of code that explain what you did. Deliverables are due next Tuesday. Post your lab reports as README.md pages on your GitHub, and post a link to that on your main class hub page.

## Part A. Solder your LCD panel

**Take a picture of your soldered panel and add it here!**

![Board](IMG_0736%20copy.jpg)

## Part B. Writing to the LCD
 
**a. What voltage level do you need to power your display?**

5V

**b. What voltage level do you need to power the display backlight?**

3.3V
   
**c. What was one mistake you made when wiring up the display? How did you fix it?**

Accidentally wired the backlight to 5v because I had hooked all of my + rails up to 5v. I separated out the 5v and 3.3v outputs coming from the arduino by dedicating one rail to 5v and the other 3.3.v.

**d. What line of code do you need to change to make it flash your name instead of "Hello World"?**

lcd.print("hello, World!") 
 
**e. Include a copy of your Lowly Multimeter code in your lab write-up.**
```
// include the library code:
#include <LiquidCrystal.h>


// initialize the library by associating any needed LCD interface pin
// with the arduino pin number it is connected to
const int rs = 12, en = 11, d4 = 5, d5 = 4, d6 = 3, d7 = 2;
LiquidCrystal lcd(rs, en, d4, d5, d6, d7);

int sensorPin = A0;    // select the input pin for the potentiometer
int sensorValue = 0;  // variable to store the value coming from the sensor


void setup() {
  // set up the LCD's number of columns and rows:
  lcd.begin(16, 2);
  // Print a message to the LCD.
  lcd.print("hello, Dan!");
}

void loop() {
   // read the value from the sensor:
  sensorValue = analogRead(sensorPin);
  float voltage = sensorValue * (5.0 / 1023.0);
  // set the cursor to column 0, line 1
  // (note: line 1 is the second row, since counting begins with 0):
  lcd.setCursor(0, 1);
  // print the number of seconds since reset:
  lcd.print(String(voltage) + " Volts");
  delay(1000);

}
```

## Part C. Using a time-based digital sensor

**Upload a video of your working rotary encoder here.**

[encoder video](IMG_0738.MOV)

## Part D. Make your Arduino sing!

**a. How would you change the code to make the song play twice as fast?**
```
tone(8, melody[thisNote], noteDuration/2);
int pauseBetweenNotes = (noteDuration * 1.30)/2;
```
**b. What song is playing?**


## Part E. Make your own timer

**a. Make a short video showing how your timer works, and what happens when time is up!**

**b. Post a link to the completed lab report your class hub GitHub repo.**
