#include <BleMouse.h>
#include <Wire.h>
#include <MPU6050_tockn.h>

BleMouse bleMouse("ESP32 Air Mouse", "OpenAI", 100);

MPU6050 mpu6050(Wire);

// Buttons
const int leftButton = 18;
const int rightButton = 19;

// Smooth variables
float smoothX = 0;
float smoothY = 0;

void setup() {

  Serial.begin(115200);

  // Buttons
  pinMode(leftButton, INPUT_PULLUP);
  pinMode(rightButton, INPUT_PULLUP);

  // I2C
  Wire.begin(21, 22);

  // MPU6050
  mpu6050.begin();

  Serial.println("Keep MPU6050 still...");
  delay(3000);

  mpu6050.calcGyroOffsets(true);

  Serial.println("MPU6050 Ready");

  // BLE Mouse
  bleMouse.begin();

  Serial.println("BLE Mouse Started");
}

void loop() {

  mpu6050.update();

  if (bleMouse.isConnected()) {

    // Read gyro
    float gx = -mpu6050.getGyroX();
    float gy = -mpu6050.getGyroY();

    // Reduce sensitivity
    gx = gx / 12.0;
    gy = gy / 12.0;

    // Smoothing filter
    smoothX = (smoothX * 0.85) + (gx * 0.35);
    smoothY = (smoothY * 0.85) + (gy * 0.35);

    // Convert to int
    int mouseX = (int)smoothX;
    int mouseY = (int)smoothY;

    // Dead zone
    if (abs(mouseX) < 2) mouseX = 0;
    if (abs(mouseY) < 2) mouseY = 0;

    // Move mouse
    bleMouse.move(mouseX, mouseY);

    // LEFT BUTTON
    if (digitalRead(leftButton) == LOW) {

      unsigned long pressTime = millis();

      while (digitalRead(leftButton) == LOW) {

        // LONG PRESS → SCROLL UP
        if (millis() - pressTime > 500) {

          bleMouse.move(0, 0, 1);

          delay(80);
        }
      }

      // SHORT PRESS → LEFT CLICK
      if (millis() - pressTime < 500) {

        bleMouse.click(MOUSE_LEFT);

        delay(200);
      }
    }

    // RIGHT BUTTON
    if (digitalRead(rightButton) == LOW) {

      unsigned long pressTime = millis();

      while (digitalRead(rightButton) == LOW) {

        // LONG PRESS → SCROLL DOWN
        if (millis() - pressTime > 500) {

          bleMouse.move(0, 0, -1);

          delay(80);
        }
      }

      // SHORT PRESS → RIGHT CLICK
      if (millis() - pressTime < 500) {

        bleMouse.click(MOUSE_RIGHT);

        delay(200);
      }
    }
  }

  delay(10);
}
