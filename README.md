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

[encoder video](https://photos.app.goo.gl/UvyvG4jQqSNryJpi8)

## Part D. Make your Arduino sing!

**a. How would you change the code to make the song play twice as fast?**
```
tone(8, melody[thisNote], noteDuration/2);
int pauseBetweenNotes = (noteDuration * 1.30)/2;
```
**b. What song is playing?**

Star wars theme song.

## Part E. Make your own timer

**a. Make a short video showing how your timer works, and what happens when time is up!**

**b. Post a link to the completed lab report your class hub GitHub repo.**


Custom timer code
```
// include the library code:
#include <LiquidCrystal.h>
#define ENC_A 6 //these need to be digital input pins
#define ENC_B 7
#include "pitches.h"

// notes in the melody:
// note durations: 4 = quarter note, 8 = eighth note, etc.:
int melody[] = {
 NOTE_C2, NOTE_F3, NOTE_C3, NOTE_A2,
  NOTE_C3, NOTE_F3, NOTE_C3,
  NOTE_C3, NOTE_F3, NOTE_C3, NOTE_F3,
  NOTE_AS3, NOTE_G3, NOTE_F3, NOTE_E3, NOTE_D3, NOTE_CS3,
  NOTE_C3, NOTE_F3, NOTE_C3, NOTE_A2, // the same again
  NOTE_C3, NOTE_F3, NOTE_C3,
  NOTE_AS3, 0, NOTE_G3, NOTE_F3,
  NOTE_E3, NOTE_D3, NOTE_CS3, NOTE_C3};
 
int noteDurations[] = {
  4,    4,    4,    4,
  4,    4,          2,
  4,    4,    4,    4,
  3,   8, 8, 8, 8, 8,
  4,    4,    4,    4, // the same again
  4,    4,          2,
  4, 8, 8,    4,    4,
  4,    4,    4,    4,
  0};

// initialize the library by associating any needed LCD interface pin
// with the arduino pin number it is connected to
const int rs = 12, en = 11, d4 = 5, d5 = 4, d6 = 3, d7 = 2;
LiquidCrystal lcd(rs, en, d4, d5, d6, d7);

int sensorPin = A0;    // select the input pin for the potentiometer
int sensorValue = 0;  // variable to store the value coming from the sensor
long timer;
unsigned long lastScreenUpdate;
unsigned long lastInput;
int gameCountDown = 3;
bool gameStarted = false;
bool gameEnded = false;
long lastTimerCountdown;
bool themePlaying = false;
long timeOfLastNote;
int noteIndex = 0;

void setup() {
  pinMode(ENC_A, INPUT_PULLUP);
  pinMode(ENC_B, INPUT_PULLUP);
  Serial.begin (9600);
  timer = 0;
  
  // set up the LCD's number of columns and rows:
  lcd.begin(16, 2);
  // Print a message to the LCD.
  lastScreenUpdate = millis();
  lastInput = millis();
  print_time();

   for (int thisNote = 0; thisNote < 2; thisNote++) {

    // to calculate the note duration, take one second divided by the note type.
    //e.g. quarter note = 1000 / 4, eighth note = 1000/8, etc.
    int noteDuration = 1000 / noteDurations[thisNote];
    tone(8, melody[thisNote], noteDuration);

    // to distinguish the notes, set a minimum time between them.
    // the note's duration + 30% seems to work well:
//    int pauseBetweenNotes = noteDuration * 1.30;
     int pauseBetweenNotes = (noteDuration * 1.30);
    delay(pauseBetweenNotes);
    // stop the tone playing:
    noTone(8);
  }
  
}

void loop() {
   unsigned long timeSinceLastUpdate = millis() - lastScreenUpdate;
   unsigned long timeSinceLastInput = millis() - lastInput;
   unsigned long timeSinceLastTimerCountdown = millis() - lastTimerCountdown;
//   Serial.println("the game has started " + String(gameStarted));
//   Serial.println("the game has ended " + String(gameEnded));
    static unsigned int counter4x = 0;      //the SparkFun encoders jump by 4 states from detent to detent
    static unsigned int counter = 0;
    static unsigned int prevCounter = 0;
    int tmpdata;
    tmpdata = read_encoder();
  
  if (gameStarted == false && gameEnded == false) {
    if( tmpdata) {
      counter4x += tmpdata;
      counter = counter4x/4;
      if (prevCounter > counter){
        timer++;
        lastInput = millis();
      }
      if (prevCounter < counter && timer - 1 >= 0){
        timer--;
        lastInput = millis();
      }
//      Serial.println("The tmpdata is " + String(tmpdata));
      prevCounter = counter;
    }
    countdown(timeSinceLastInput); 
  }

  if (gameStarted == true && gameEnded == false && timer > 0 && timeSinceLastTimerCountdown > 1000) {
    Serial.println("The time since last update is " + String(timeSinceLastTimerCountdown));
    Serial.println("The timer is " + String(timer));
    timer --;
    lastTimerCountdown = millis();
  }

  if ( timeSinceLastUpdate > 100 && gameEnded == false ){
   print_time(); 
  }

  playTheme();
  endGame();
  restart(tmpdata);
}

void print_time() {
  lcd.setCursor(0, 0);
  lastScreenUpdate = millis();
  lcd.print("Timer: " + String((int)timer) + " ");
}

void countdown(long timeSinceLastInput) {
  long gameCountDownClock = (long) (gameCountDown + 2 - (timeSinceLastInput/1000));
  if (gameCountDownClock <= 0 && gameStarted == false) {
    gameStarted = true;
    themePlaying = true;
    lastTimerCountdown = millis();
    lcd.setCursor(0, 1);
    lcd.print("                 ");
  }
  else if (timeSinceLastInput < 2000) {
    lcd.setCursor(0, 1);
    lcd.print("                 ");
    }  
  else {
    lcd.setCursor(0, 1);
    lcd.print("Starting in: " + String(gameCountDownClock));
  }
}

void endGame() {
  if (gameStarted == true && timer <= 0 && gameEnded == false) {
    gameEnded = true;
    themePlaying = false;
    stopTheme();
    lcd.clear();
    tone(8, 128);
    lcd.print("Game over, man!");
    delay(2000);
    noTone(8);
  }
}

void restart(int tmpdata) {
  if (tmpdata && gameEnded == true) {
    gameEnded = false;
    gameStarted = false;
    timer = 0;
    lcd.clear();
    print_time();
  }
}

void playTheme() {
  if (noteIndex == sizeof(melody)) {
        noteIndex = 0;
  }
  int noteDuration = 2000/noteDurations[noteIndex];
  int pauseBetweenNotes = noteDuration * 1.30;
  
  if (themePlaying) {
    if ((millis() - timeOfLastNote) >= pauseBetweenNotes) {
      tone(8, melody[noteIndex],noteDuration * 0.9);
      noteIndex++;
      timeOfLastNote = millis();
    }
  }
 
//      int pauseBetweenNotes = noteDuration * 1.30;
//      if (timeOfLastNote >= pauseBetweenNotes) {
//        tone(8, melody[thisNote],noteDuration * 0.9);
//      }
//      delay(pauseBetweenNotes);
//    }
//    themePlaying = false;
//  }
}

void stopTheme() {
  noTone(8);
}

int read_encoder()
{
  static int enc_states[] = {
    0,-1,1,0,1,0,0,-1,-1,0,0,1,0,1,-1,0  };
  static byte ABab = 0;
  ABab *= 4;                   //shift the old values over 2 bits
  ABab = ABab%16;      //keeps only bits 0-3
  ABab += 2*digitalRead(ENC_A)+digitalRead(ENC_B); //adds enc_a and enc_b values to bits 1 and 0
  return ( enc_states[ABab]);
}
```
