// Define motor control pins
#define in1 9
#define in2 10
#define in3 11
#define in4 12

// Define LED pin
#define LED 13

// Variables
int command;          // Int to store app command state
int Speed = 204;      // Speed range: 0 - 255
int Speedsec;
int buttonState = 0;
int lastButtonState = 0;
int Turnradius = 0;   // Radius of a turn, range: 0 - 255 Note: Robot may malfunction if higher than Speed
int brakeTime = 45;
int brkonoff = 1;     // 1 for electronic braking system, 0 for normal

void setup() {
  // Initialize pin modes
  pinMode(in1, OUTPUT);
  pinMode(in2, OUTPUT);
  pinMode(in3, OUTPUT);
  pinMode(in4, OUTPUT);
  pinMode(LED, OUTPUT);

  // Initialize serial communication
  Serial.begin(9600);  // Set the baud rate to match your Bluetooth module
}

void loop() {
  // Check for incoming commands from Bluetooth module
  if (Serial.available() > 0) {
    command = Serial.read();
    Stop(); // Initialize with motors stopped

    // Execute command
    switch (command) {
      case 'F': forward(); break;
      case 'B': back(); break;
      case 'L': left(); break;
      case 'R': right(); break;
      case 'G': forwardleft(); break;
      case 'I': forwardright(); break;
      case 'H': backleft(); break;
      case 'J': backright(); break;
      case '0'...'9': Speed = (command - '0') * 13 + 100; break; // Adjust speed
      case 'q': Speed = 255; break;
    }
    Speedsec = Turnradius;
    if (brkonoff == 1) {
      brakeOn();
    } else {
      brakeOff();
    }
  }
}

// Movement functions
void forward() {
  digitalWrite(in1, HIGH);
  digitalWrite(in2, LOW);
  digitalWrite(in3, HIGH);
  digitalWrite(in4, LOW);
}

void back() {
  digitalWrite(in1, LOW);
  digitalWrite(in2, HIGH);
  digitalWrite(in3, LOW);
  digitalWrite(in4, HIGH);
}

void left() {
  digitalWrite(in1, LOW);
  digitalWrite(in2, HIGH);
  digitalWrite(in3, HIGH);
  digitalWrite(in4, LOW);
}

void right() {
  digitalWrite(in1, HIGH);
  digitalWrite(in2, LOW);
  digitalWrite(in3, LOW);
  digitalWrite(in4, HIGH);
}

void forwardleft() {
  digitalWrite(in1, LOW);
  digitalWrite(in2, LOW);
  digitalWrite(in3, HIGH);
  digitalWrite(in4, LOW);
}

void forwardright() {
  digitalWrite(in1, HIGH);
  digitalWrite(in2, LOW);
  digitalWrite(in3, LOW);
  digitalWrite(in4, LOW);
}

void backright() {
  digitalWrite(in1, LOW);
  digitalWrite(in2, LOW);
  digitalWrite(in3, LOW);
  digitalWrite(in4, HIGH);
}

void backleft() {
  digitalWrite(in1, LOW);
  digitalWrite(in2, HIGH);
  digitalWrite(in3, LOW);
  digitalWrite(in4, LOW);
}

void Stop() {
  // Stop the motors
  digitalWrite(in1, LOW);
  digitalWrite(in2, LOW);
  digitalWrite(in3, LOW);
  digitalWrite(in4, LOW);
}

void brakeOn() {
  // Electronic braking system
  buttonState = command;
  if (buttonState != lastButtonState) {
    if (buttonState == 'S') {
      if (lastButtonState != buttonState) {
        digitalWrite(in1, HIGH);
        digitalWrite(in2, HIGH);
        digitalWrite(in3, HIGH);
        digitalWrite(in4, HIGH);
        delay(brakeTime);
        Stop();
      }
    }
    lastButtonState = buttonState;
  }
}

void brakeOff() {
  // Future use: additional braking functionalities
}
