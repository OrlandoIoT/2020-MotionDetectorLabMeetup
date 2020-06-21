# Orlando Code Camp 2020 IoT Lab - Room Light

## Introduction

During this lab you will create an IoT application that will use a sensor to detect movement and use that to control a light, similar to automatic lights in a conference room. You will wire up a breadboard and code an application to use a PIR sensor to turn the LED on or off. You will also create a Particle Function to send data from the cloud service to the board to overwrite the state of the light.

You should be able to complete this during the 50 minutes alloted for the session.

## Materials

- 1 Particle Photon Development Board
- 1 220 Ohm resistor
- 1 PIR Sensor
- 3 Wires (1 Red, 2 Black, and 1 Other)
- 1 Breadboard
- 1 LED
- 1 USB Cable

If you are missing any of these materials, ask one of the organizers for the missing component(s).

## Part 1: Hello World

In this section you will wire up your board and write a simple Hello World application, known as Blinky in the embedded space.

### Setting up your board

#### Particle Photon 101

For this lab we will be using a Particle Photon development board. Here are a few notes about it.

- For this lab, we will be referring to the top as the side with the USB port on it.
- A cheap kit on their website is $29: <https://store.particle.io/collections/wifi/products/photon-kit>

#### Breadboards 101

If you've never worked with a breadboard before, here are a few notes. Otherwise, move on to the next section.

- They make prototyping easy to allow you to plug in wires without soldering them onto something
- The lines on the left and right sides are all connected in parallel
- The lines in the middle are connected horizontally, with a gap in the middle
- When plugging wires into the board we'll be taking note of their color. But the color does not have any meaning other than what we choose to give it. It's just metal on the inside.

![Diagram of connected lines in a breadboard](https://raw.githubusercontent.com/OrlandoIoT/2020-MotionDetectorLabMeetup/master/Motion%20Detector%20Lab/Images/Breadboard_lines.png "Diagram of connected lines in a breadboard")

#### Wiring your board

1) Take your Particle Photon and plug it into the breadboard somewhere in the middle with the top-most pin (the one labeled 3V3) on line 10 of the breadboard.
1) Plug the resistor into a breadboard spot on the same line as D0 (the bottom-right pin on the Photon board). Place the other end of the resistor somewhere on line 27.
1) Plug in the LED  into the board. There are 2 pins coming out of it. Put the longer-length side into line 27 and the other end onto the far right of the board (in the section labeled -).
1) Take a black wire and connect one side to line 13, next to the pin labeled GND on the Photon (this is ground). Plug the other side into the top of the far-right of the bread board, the side labeled -.
1) Plug the USB cable into the Photon board and into your computer.

When done your setup should look like this:
![Wired board for Blinky app](/Images/blinky_wired_board.jpg "Wired board for Blinky app")

You have just setup a breadboard to blink and LED. In the next section you will write the code to enable this functionality.

### Claim the Particle Photon board as yours

1) Make sure the Photon is connected to your computer.
1) On your mobile device, open the Particle app.
1) Click the Plus icon in the bottom right, then select the option to add a Photon.
1) On the Photon board, hold down the `Reset` button for 3 seconds and then release it. The built in LED should blink blue.
1) Click `Ready` in the Particle app to search for your Photon.
1) When your Photon is found select it. You will get a popup asking to change the owner. Select the `Change Owner` option.
1) On the next screen, enter the Wi-Fi credentials to connect the Photon to the network.

- Note: If you get an error saying the board could not connect to the network, it may have still worked. Move on to the next step. If the board does not show up in your list of devices come back to this step and run through it again.

### Connect to the Particle Web IDE

1) Using a web browser, navigate to <https://build.particle.io/build> and login.
1) If you have not already created a Particle account, create one now. Sorry, but it's required to use the Web IDE.
1) Spend a few seconds familiarizing yourself with the UI.

### Write and Deploy your Code

1) Create a new App by typing the title `CodeCamp2020` into the `Current App` text box.
2) Type the following code into the `codecamp2020.ino` text editor.

```c
int ledPin = D0;

void setup() {
  pinMode(ledPin, OUTPUT);
}

void setLedOn(){
  digitalWrite(ledPin, HIGH);
}

void setLedOff(){
  digitalWrite(ledPin, LOW);
}

void blinkLed(){
  setLedOn();
  delay(250);

  setLedOff();
  delay(250);
}

void loop(){
    delay(1000);
    blinkLed();
}
```

3) Click the lightning icon to deploy the code to your board.
4) If the flash is successful your LED should be blinking every second. If that is not happening your board may be wired incorrectly. Double and triple check the wiring.

## Part 2: Add the PIR Sensor to detect movement

A blinking LED is cool, but it's not useful. The next few steps will have you attach a PIR sensor to your breadboard to detect movement.

### Wire up the PIR Sensor

1) Take the PIR Sensor and hold it so you are looking at the side with the 3 pins facing you.
1) The left side is ground, middle is data, and right side is power.
1) Take a black wire and plug it into the left pin of the sensor. Then plug the other end into the breadboard on the far-right side (it was wired up to be ground earlier).
1) Take the data wire (the one that isn't red) and plug it into the center pin in the sensor. Plug the other end into line 17 of the breadboard (so it is connected to Pin D4 on the Photon).
1) Take the red wire and plug it into the right side pin of the sensor and plug the other end into line 10 of the breadboard (so it is connected to the 3V pin on the Photon).
![Wired PIR Sensor](/Images/Wired_PIR_Sensor.png "Wired PIR Sensor")

### Write and Deploy your Code to use the new sensor

1) Change the code in your `codecamp2020.ino` file in the web text editor to match the code below, then deploy it to your Photon.

```c
int ledPin = D0;
int pirPin = D4;
int knownPirState = LOW;

void setup() {
  pinMode(ledPin, OUTPUT);
  pinMode(pirPin, INPUT);
}

void setLedOn(){
  digitalWrite(ledPin, HIGH);
}

void setLedOff(){
  digitalWrite(ledPin, LOW);
}

void blinkLed(){
  setLedOn();
  delay(250);

  setLedOff();
  delay(250);
}

void loop(){
  int currentState = digitalRead(pirPin);

  if(knownPirState != currentState)
  {
    blinkLed();
    knownPirState = currentState;

    if(knownPirState){
      setLedOn();
    }
    else{
      setLedOff();
    }
  }
}
```

2) When movement is detected by the PIR sensor the LED will blink once and then stay on. WHen there is no longer movement detected the LED will blink once and then turn off.
3) Test your board by covering the LED with your hands, waiting a few seconds, then uncovering the sensor.

- Note: It is not constantly checking for movement. So there will be a couple seconds delay.

## Part 3: Send an event when the PIR state changes

1) In the final module you will add a Particle Function to your code which will allow you to call a function on the Photon board from the Web Console.
1) Modify your code to match the below code, and deploy it to your Photon board.

```c
int ledPin = D0;
int pirPin = D4;
int knownPirState = LOW;
bool ledForcedOn = false;

void setup() {
  pinMode(ledPin, OUTPUT);
  pinMode(pirPin, INPUT);

  Particle.function("setLedForcedValue", setLedForcedValue);
}

void setLedOn(){
  digitalWrite(ledPin, HIGH);
}

void setLedOff(){
  digitalWrite(ledPin, LOW);
}

void blinkLed(){
  setLedOn();
  delay(250);

  setLedOff();
  delay(250);
}

void loop(){
  int currentState = digitalRead(pirPin);

  if(knownPirState != currentState)
  {
    blinkLed();
    knownPirState = currentState;

    if(knownPirState){
      setLedOn();
    }
    else{
      setLedOff();
    }
  }
  
  if(ledForcedOn){
      setLedOn();
  }
}

// Cloud functions must return int and take one String
int setLedForcedValue(String tag) {
  ledForcedOn = tag == "true";
  return 0;
}
```

3) On the bottom left of the screen, click on the link to the Console (the icon that looks like a graph).
4) A new tab will be opened to the Particle Web Console.
1) On the left hand side of the screen, click on the icon for Devices.
1) Select your Photon device from the screen (which should be the only device that appears).
1) Find the `Functions` section on the bottom-right of the screen. This will have the one function you created on your board. The line of code in your `setup()` function `Particle.function("setLedForcedValue", setLedForcedValue);` sent an event to Particle to alert it that the `setLedForcedValue()` function exists on your Photon.
1) Enter the text `true` into the `Argument` text box for the function and click `Call`.
1) Notice your LED is now always on, but will still blink once when the PIR sensor detects movement or lack of movement.

