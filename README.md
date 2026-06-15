#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>

#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64

const int potPin = A0;
const int buttonPin = 2;
const int ledPin = 7;
const int buzzerPin = 8;

void setup() {
  pinMode(buttonPin, INPUT_PULLUP);
  pinMode(ledPin, OUTPUT);
  pinMode(buzzerPin, OUTPUT);

  Serial.begin(9600);

  if (!display.begin(SSD1306_SWITCHCAPVCC, 0x3C)) {
    Serial.println("OLED Failed");
    while (true);
  }

  display.clearDisplay();
}

void loop() {

  int potValue = analogRead(potPin);

  float temperature = map(potValue, 0, 1023, 0, 100);

  bool vibrationDetected = (digitalRead(buttonPin) == LOW);

  display.clearDisplay();
  display.setTextSize(1);
  display.setTextColor(WHITE);
  display.setCursor(0, 0);

  display.print("Temp: ");
  display.print(temperature);
  display.println(" C");

  if (temperature > 50 || vibrationDetected) {

    digitalWrite(ledPin, HIGH);
    digitalWrite(buzzerPin, HIGH);

    display.println("");
    display.println("WARNING!");
    display.println("CHECK MOTOR");

    Serial.println("FAULT DETECTED");
  }
  else {

    digitalWrite(ledPin, LOW);
    digitalWrite(buzzerPin, LOW);

    display.println("");
    display.println("Motor Status");
    display.println("NORMAL");

    Serial.println("MOTOR NORMAL");
  }

  display.display();

  delay(500);
}
