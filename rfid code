#include <SPI.h>
#include <MFRC522.h>
#include <Wire.h>
#include <LiquidCrystal_I2C.h>
#include <Adafruit_MLX90614.h>
#include <Servo.h>

#define RST_PIN            9
#define SS_PIN             10
#define BUZZER_PIN         7

#define CUT_OF_TEMPERATURE 36
#define MILD_FEVER_MIN     36
#define MILD_FEVER_MAX     38

byte readCard[4];
char* myTags[100] = {};
int tagsCount = 0;
String tagID = "";
boolean successRead = false;
const int buzzer = BUZZER_PIN; 
boolean correctTag = false;
int proximitySensor;
boolean doorOpened = false;
const int numReadings = 100; // number of temperature readings to store
float readings[numReadings]; // array to store the temperature readings
int index = 0; // index to keep track of the current position in the array

// Create instances
MFRC522 mfrc522(SS_PIN, RST_PIN);
LiquidCrystal_I2C lcd(0x27, 16, 2);
Adafruit_MLX90614 mlx = Adafruit_MLX90614();
Servo myServo; // Servo motor

void setup() {

  // Initiating
  Serial.begin(9600);
  Wire.begin();
  mlx.begin(0x5A);  // MLX90614 temp sensor
  SPI.begin();   // SPI bus
  mfrc522.PCD_Init();  //  MFRC522
  lcd.begin();   // LCD screen
  lcd.backlight();  // Increase readability
  myServo.attach(8);  // Servo motor
  myServo.write(0);  // Initial lock position of the servo motor 

  // Prints the initial message
  lcd.print("-No Master Tag!-");
  lcd.setCursor(0, 1);
  lcd.print("    SCAN NOW");

  pinMode(buzzer, OUTPUT); // buzzer working as output

  // Waits until a master card is scanned
  while (!successRead) {
    successRead = getID();
    if ( successRead == true) {
      // Sets the master tag into position 0 in the array
      myTags[tagsCount] = strdup(tagID.c_str()); 
      lcd.clear();
      lcd.setCursor(0, 0);
      lcd.print("Master Tag Set!");
      tagsCount++;
    }
  }

  successRead = false;
  printNormalModeMessage();

}

void loop() {


  int proximitySensor = analogRead(A0);
  
  // If door is closed...
  if (proximitySensor < 200) {
    // If a new PICC placed to RFID reader continue
    if ( ! mfrc522.PICC_IsNewCardPresent()) { 
      return;
    }
    // Since a PICC placed get Serial and continue
    if ( ! mfrc522.PICC_ReadCardSerial()) { 
      return;
    }
    tagID = "";

    // The MIFARE PICCs that we use have 4 byte UID
    for ( uint8_t i = 0; i < 4; i++) {  //
      readCard[i] = mfrc522.uid.uidByte[i];
      tagID.concat(String(mfrc522.uid.uidByte[i], HEX)); // Adds the 4 bytes in a single String variable
    }
    tagID.toUpperCase();
    mfrc522.PICC_HaltA(); // Stop reading

    correctTag = false;

    // Checks whether the scanned tag is the master tag
    if (tagID == myTags[0]) {
      lcd.clear();
      lcd.print("Program mode:");
      lcd.setCursor(0, 1);
      lcd.print("Add/Remove Tag");

      while (!successRead) {
        successRead = getID(); 
        if ( successRead == true) { 
          for (int i = 0; i < 100; i++) { 
            if (tagID == myTags[i]) {  
              myTags[i] = "";
              lcd.clear();
              lcd.setCursor(0, 0);
              lcd.print("  Tag Removed!");
              printNormalModeMessage();
              return;
            }
          }
          myTags[tagsCount] = strdup(tagID.c_str());
          lcd.clear();
          lcd.setCursor(0, 0);
          lcd.print("   Tag Added!");
          printNormalModeMessage();
          tagsCount++;
          return;
        }
      }
    }
    successRead = false;

    // Checks whether the scanned tag is authorized
    for (int i = 0; i < 100; i++) {
      if (tagID == myTags[i]) {
        lcd.clear();
        lcd.setCursor(0, 0);
        float temperature = mlx.readObjectTempC(); // Initiating MLX sensor
        Serial.print("Temperature: ");
        Serial.println(temperature);
        if (temperature < CUT_OF_TEMPERATURE) {
          lcd.print(" Access Granted!");
          myServo.write(90); // Unlocks the door
          tone(buzzer, 500, 500);
        } else if (temperature >= MILD_FEVER_MIN && temperature < MILD_FEVER_MAX) { 
          lcd.print("   Mild Fever   ");
          lcd.setCursor(0, 1);
          lcd.print("  Take Rest <3  ");
          tone(buzzer, 659, 1000); // lowTemperatureNote
        } else {
          lcd.print(" SERIOUS FEVER:(");
          lcd.setCursor(0, 1);
          lcd.print("  See A Doctor  ");
          tone(buzzer, 1000, 1000); // highTemperatureNote
        }

        printNormalModeMessage();
        correctTag = true;
      }
    }
    if (correctTag == false) {
      lcd.clear();
      lcd.setCursor(0, 0);
      lcd.print(" Access Denied!");
      printNormalModeMessage();
    }
  }

  // If door is open...
  else {
    lcd.clear();
    lcd.setCursor(0, 0);
    lcd.print(" Door Opened!");
    while (!doorOpened) {
      proximitySensor = analogRead(A0);
      if (proximitySensor > 200) {
        doorOpened = true;
      }
    }
    doorOpened = false;
    delay(500);
    myServo.write(10); // Locks the door
    printNormalModeMessage();
  }

}

uint8_t getID() {
  // Getting ready for Reading PICCs
  if ( ! mfrc522.PICC_IsNewCardPresent()) { //If a new PICC placed to RFID reader continue
    return 0;
  }
  if ( ! mfrc522.PICC_ReadCardSerial()) {   //Since a PICC placed get Serial and continue
    return 0;
  }
  tagID = "";
  for ( uint8_t i = 0; i < 4; i++) {  // The MIFARE PICCs that we use have 4 byte UID
    readCard[i] = mfrc522.uid.uidByte[i];
    tagID.concat(String(mfrc522.uid.uidByte[i], HEX)); // Adds the 4 bytes in a single String variable
  }
  tagID.toUpperCase();
  mfrc522.PICC_HaltA(); // Stop reading
  return 1;
}

void printNormalModeMessage() {
  delay(1500);
  lcd.clear();
  lcd.print("-Access Control-");
  lcd.setCursor(0, 1);
  lcd.print(" Scan Your Tag!");
}
