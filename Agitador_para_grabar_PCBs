/*  Proyecto: Aguitador para placas de circuito impreso.
 *  
 *  Desarrollado por: Alberto Angel Bautista y Cesar Anselmo Lopez
 *                    17 de Julio 2017
 *                    
 *  Comentarios: Este dispositivo se desarrollo para ahorrar tiempo en el grabado 
 *               de circuitos impresos con cloruro ferrico. Agitar la bandeja cuando 
 *               se graba algun PCB es peligroso por el gas que expulsa este proceso y 
 *               tambien quita tiempo. 


   ENCODER  Pin A       to digital pin 2
   ENCODER  Pin B       to digital pin 3
   ENCODER  Pin SWITCH  to digital pin 0

   BUTTON   I/O Pin     to digital pin A5

   BUZZER   Pin         to digital pin 4

   SERVO    Pin PWM     to PWM     pin 5

   LCD VSS  Pin to GND
   LCD VCC  Pin to 5V
   LCD VEE 1K resistor to GND
   LCD RS   Pin         to digital pin 8
   LCD R/W  Pin to GND
   LCD E    Pin         to digital pin 7
   LCD D4   Pin         to digital pin 9
   LCD D5   Pin         to digital pin 10
   LCD D6   Pin         to digital pin 11
   LCD D7   Pin         to digital pin 12
*/
#include <LiquidCrystal.h>
#include <Servo.h>
#include "Event.h"
#include "Timer.h"
#include "MsTimer2.h"

#define EncoderPinA   2
#define EncoderPinB   3
#define EncoderSW     0
#define StartPauseSW  A5
#define BuzzerPin     4
#define PWM           5
#define E             7
#define RS            8
#define D4            9
#define D5            10
#define D6            11
#define D7            12



#define FINISHED_MSG      "  Finished!"
#define STOP_MSG          "   STOP!"
#define TIME_MSG          "Time: "

#define SPEED_MSG         "Speed: "
#define SPEED_FAST_MSG    "Fast  "
#define SPEED_MEDIUM_MSG  "Medium"
#define SPEED_LOW_MSG     "Low   "
#define SPEED_FAST_VAL    5
#define SPEED_MEDIUM_VAL  10
#define SPEED_LOW_VAL     15

void RefreshLCD(void);
void EncoderRead(void);
void Setting_Seconds(void);
void DecreaseASecond(void);
void RunServo(void);

uint8_t dataAux = 0, dataRead = 0;
int x = 0, minutes = 0, seconds = 0, secAux, speed_num = 0;
bool lim, state = false, state2 = false, working = false;
bool set_seconds = true, set_minutes = false, set_speed = false;
char* speed_msg = SPEED_MEDIUM_MSG;
int speed_val = SPEED_MEDIUM_VAL;
uint8_t select = 0, blinking = 0;

Servo myservo;
LiquidCrystal lcd(RS, E, D4, D5, D6, D7);
Timer t;

void RefreshLCD(void) {
  /* set the cursor to column,row */
  lcd.setCursor(7, 0);
  lcd.print("  ");
  lcd.setCursor(7, 0);
  if ((set_minutes) && (!working) && (blinking)) {
    lcd.print("  ");
  }
  else {
    if (minutes < 10) {
      lcd.print("0");
      lcd.setCursor(8, 0);
    }
    lcd.print(minutes);
  }
  lcd.setCursor(10, 0);
  lcd.print("  ");
  lcd.setCursor(10, 0);
  if ((set_seconds) && (!working) && (blinking)) {
    lcd.print("  ");
  }
  else {
    if (seconds < 10) {
      lcd.print("0");
      lcd.setCursor(11, 0);
    }
    lcd.print(seconds);
  }
  lcd.setCursor(10, 1);
  if ((set_speed) && (!working) && (blinking))
    lcd.print("      ");
  else
    lcd.print(speed_msg);
}
void BlinkText(void) {
  blinking = blinking ^ 1;
}
void InitMessage(void) {
  lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print(TIME_MSG);
  lcd.setCursor(9, 0);
  lcd.print(":");
  lcd.setCursor(13, 0);
  lcd.print("min");
  lcd.setCursor(0, 1);
  lcd.print(SPEED_MSG);
}
void StopMessage(void) {
  lcd.clear();
  lcd.setCursor(3, 0);
  lcd.print(STOP_MSG);
}
void FinishedMessage(void) {
  lcd.clear();
  lcd.setCursor(3, 0);
  lcd.print(FINISHED_MSG);
}
void EncoderRead(void) {
  if (working != true) {
    Get_x_value();
    dataAux = dataRead;
    uint8_t A_set = digitalRead(EncoderPinA);
    uint8_t B_set = digitalRead(EncoderPinB);
    dataRead = (B_set << 1) | (A_set);
    if ((dataAux == 2) && (dataRead == 3)) {
      x--;
      if (x < 0) {
        x = 59;
      }
    }
    if ((dataAux == 3) && (dataRead == 2)) {
      x++;
      if (x > 59) {
        x = 0;
      }
    }
    Set_something();
  }
}
void Get_x_value(void) {
  if (set_seconds) {
    x = seconds;
  }
  else if (set_minutes) {
    x = minutes;
  }
  else if (set_speed) {
    x = speed_num;
  }
}
void Set_something(void) {
  if (set_seconds) {
    Setting_Seconds();
  }
  else if (set_minutes) {
    Setting_Minutes();
  }
  else if (set_speed) {
    Setting_Speed();
  }
}
void Setting_Seconds(void) {
  secAux = seconds;
  seconds = x;
  if ((secAux == 0) && (seconds == 59)) {
    minutes--;
    if (minutes < 0) {
      minutes = 59;
    }
  }
  if ((secAux == 59) && (seconds == 0)) {
    minutes++;
    if (minutes > 59) {
      minutes = 0;
    }
  }
}
void Setting_Minutes(void) {
  minutes = x;
}
void Setting_Speed(void) {
  if (x == 0) {
    speed_num = 0;
  }
  else {
    if (x % 4 == 0) {
      speed_num = 0;
    }
    else  {
      if (x % 2 == 0) {
        speed_num = 2;
      }
      else {
        speed_num = 1;
      }
    }
  }
  switch (speed_num) {
    case 0: speed_msg = SPEED_FAST_MSG;
      speed_val = SPEED_FAST_VAL;
      break;
    case 1: speed_msg = SPEED_MEDIUM_MSG;
      speed_val = SPEED_MEDIUM_VAL;
      break;
    case 2: speed_msg = SPEED_LOW_MSG;
      speed_val = SPEED_LOW_VAL;
      break;
    default: NULL;
      break;
  }
  speed_num = x;
}
void DecreaseASecond(void) {
  if (working == true) {
    x--;
    if (x < 0) {
      x = 59;
    }
    Setting_Seconds();
  }
}
void RunServo(void) {
  int pos = myservo.read();
  if (pos == 90)
    lim = true;
  if (pos == 180)
    lim = false;

  if ((pos < 180) && (lim == true)) {
    pos++;
    myservo.write(pos);
    delay(speed_val);
  }
  if ((pos > 90) && (lim == false)) {
    pos--;
    myservo.write(pos);
    delay(speed_val);
  }
}


void setup() {
  /* I/O Settings*/
  pinMode (EncoderPinA, INPUT_PULLUP);
  pinMode (EncoderPinB, INPUT_PULLUP);
  pinMode (EncoderSW, INPUT_PULLUP);
  pinMode (StartPauseSW, INPUT_PULLUP);
  pinMode (BuzzerPin, OUTPUT);
  /* Servo Settings */
  myservo.attach(PWM);
  myservo.write(90);
  /* LCD Settings*/
  lcd.begin(16, 2);
  //WelcomeMessage();
  //delay(2000);
  InitMessage();
  /* Interrupt Settings*/
  attachInterrupt(digitalPinToInterrupt(EncoderPinA), EncoderRead, CHANGE);
  attachInterrupt(digitalPinToInterrupt(EncoderPinB), EncoderRead, CHANGE);
  /* Event timer Settings */
  int SecondEvent = t.every(1000, DecreaseASecond);
  int BlinkEvent = t.every(400, BlinkText);
  /* Timer 2 Settings */
  MsTimer2::set(200, RefreshLCD);
  MsTimer2::start();
}


void loop() {
  /* Update Timer event */
  t.update();
  /* Reading Push Buttons */
  if (!digitalRead(StartPauseSW)) {
    int counter = 0;
    while (!digitalRead(StartPauseSW)) {
      delay(100);
      counter++;
      if (counter > 20 && ((minutes > 0) || (seconds > 0))) {
        x = 0;
        seconds = 0;
        minutes = 0;
        state = false;
        working = false;
        MsTimer2::stop();
        StopMessage();
        delay(2000);
        InitMessage();
        MsTimer2::set(200, RefreshLCD);
        MsTimer2::start();
      }
    }
    if ((counter < 20) && (state == false) && ((minutes > 0) || (seconds > 0))) {
      working = working ^ 1;
      x = seconds;
      state = true;
    }
  }
  else if (!digitalRead(EncoderSW)) {
    if (working == false) {
      delay(100);
      if (state2 == false) {
        select++;
        if (select >= 3) {
          select = 0;
        }
        state2 = true;
        switch (select) {
          case 0: set_seconds = true;
            set_minutes = false;
            set_speed = false;
            break;
          case 1: set_seconds = false;
            set_minutes = true;
            set_speed = false;
            break;
          case 2: set_seconds = false;
            set_minutes = false;
            set_speed = true;
            break;
          default: NULL;
            break;
        }
      }
    }
  }
  else {
    state = false;
    state2 = false;
  }
  /* To work or Not to work*/
  if (working == true) {
    RunServo();
    if (!minutes && !seconds) {
      working = false;
      t.oscillate(BuzzerPin, 1000, LOW, 10);
    }
  }
}
