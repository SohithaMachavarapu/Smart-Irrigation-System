# Smart-Irrigation-System
#include <LiquidCrystal.h>

// LCD pin setup: RS, E, D4, D5, D6, D7
LiquidCrystal lcd(7, 8, 9, 10, 11, 12);

// Pin definitions
const int soilSensorPin = A0;    // Soil sensor analog pin
const int relayPin = 2;          // Relay to control water pump
const int buzzerPin = 3;         // Buzzer for alert
const int ledPin = 4;            // LED to show watering status

// Threshold for soil dryness (0 = wet, 1023 = dry)
const int drynessThreshold = 600;

void setup() {
  // Start serial monitor for debugging
  Serial.begin(9600);

  // Setup pin modes
  pinMode(soilSensorPin, INPUT);
  pinMode(relayPin, OUTPUT);
  pinMode(buzzerPin, OUTPUT);
  pinMode(ledPin, OUTPUT);

  // Start LCD
  lcd.begin(16, 2);
  lcd.print("Smart Irrigation");
  delay(2000);
  lcd.clear();
}

void loop() {
  // Read soil moisture value
  int soilValue = analogRead(soilSensorPin);
  Serial.print("Soil Value: ");
  Serial.println(soilValue);

  // Display moisture level on LCD
  lcd.setCursor(0, 0);
  lcd.print("Moisture: ");
  lcd.print(soilValue);

  // Check if the soil is dry

  if (soilValue > drynessThreshold) {
    lcd.setCursor(0, 1);
    lcd.print("Watering plants ");

    digitalWrite(relayPin, HIGH);  // Turn ON water pump
    digitalWrite(ledPin, HIGH);    // Turn ON LED
    tone(buzzerPin, 1000);         // Beep buzzer

    delay(5000);                   // Water for 5 seconds

    digitalWrite(relayPin, LOW);   // Turn OFF pump
    digitalWrite(ledPin, LOW);     // Turn OFF LED
    noTone(buzzerPin);             // Stop buzzer
  } else {
    lcd.setCursor(0, 1);
    lcd.print("Soil is moist   ");
    digitalWrite(relayPin, LOW);   // Keep pump OFF
    digitalWrite(ledPin, LOW);     // LED OFF
    noTone(buzzerPin);             // Buzzer OFF
  }

  delay(3000); // Wait before next reading
  lcd.clear();
}
