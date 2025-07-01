# Hand Gestured control car
I am making a car that can be moved by how I move my hand!!! Since this car cannot turn to I need to turn one side of the wheels to the opposite of the other side. I use an accelerometer to determine which way my hand is pointing at so it can send information to the 

You should comment out all portions of your portfolio that you have not completed yet, as well as any instructions:
```HTML 
<!--- This is an HTML comment in Markdown -->
<!--- Anything between these symbols will not render on the published site -->
```

| **Engineer** | **School** | **Area of Interest** | **Grade** |
|:--:|:--:|:--:|:--:|
| Khang L | The Athenian School | Robotics | Incoming Junior

**Replace the BlueStamp logo below with an image of yourself and your completed project. Follow the guide [here](https://tomcam.github.io/least-github-pages/adding-images-github-pages-site.html) if you need help.**

![Headstone Image](logo.svg)
  
# Final Milestone
<iframe width="560" height="315" src="https://www.youtube.com/embed/F7M7imOVGug" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

For your final milestone, explain the outcome of your project. Key details to include are:
- What you've accomplished since your previous milestone
- What your biggest challenges and triumphs were at BSE
- A summary of key topics you learned about
- What you hope to learn in the future after everything you've learned at BSE



# Second Milestone
<iframe width="951" height="535" src="https://www.youtube.com/embed/iNbLIMv6Etc" title="Khang L Milestone 2" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

- I was able to reconnect my two Bluetooth modules and make sure they connected perfectly. I also made the car run by moving my hand on the micro
- It was very easy to fix my problem of the Bluetooth module not returning anything
- My Bluetooth module did not return anything while in AT mode when it was supposed to
- Change the controller to joysticks and build the arm

# First Milestone
<iframe width="951" height="535" src="https://www.youtube.com/embed/BKX8kk_mlT8" title="Khang L  Milestone 1" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
- I have an Uno, which will be the main brain of my car; it will send out information for my motors to run. I have a microcontroller as the main brain for my glove, which will take information from the accelerometer, then send it to the Bluetooth. Uno will receive information from the Bluetooth module to run
- I built the car chassis and made it run
- I need to make the accelerometer work
- I will make the accelerometer work and make sure the Bluetooth connection is stable

# Schematics 
Here's where you'll put images of your schematics. [Tinkercad](https://www.tinkercad.com/blog/official-guide-to-tinkercad-circuits) and [Fritzing](https://fritzing.org/learning/) are both great resources to create professional schematic diagrams, though BSE recommends Tinkercad because it can be done easily and for free in the browser. 

# Code
Here's where you'll put your code. The syntax below places it into a block of code. Follow the guide [here]([url](https://www.markdownguide.org/extended-syntax/)) to learn how to customize it to your project needs. 

Here is the code for my hand using gesture control
```
#include <Wire.h>
const int MPU = 0x68; 
int16_t AcX, AcY, AcZ;

int moveable = 0;

void setup() {
 Serial.begin(38400);
 Serial1.begin(38400);

 Wire.begin();
 Wire.beginTransmission(MPU);
 Wire.write(0x6B);
 Wire.write(0);
 Wire.endTransmission(true);
 delay(500); 
}

void read_MPU() {
  Wire.beginTransmission(MPU);
  Wire.write(0x3B); 
  Wire.endTransmission(false);
  Wire.requestFrom(MPU, 6, true); 

  AcX = Wire.read() << 8 | Wire.read(); 
  AcY = Wire.read() << 8 | Wire.read(); 
  AcZ = Wire.read() << 8 | Wire.read(); 

  AcX = map(AcX, -17000, 17000, 0, 180);
  AcY = map(AcY, -17000, 17000, 0, 180);
  AcZ = map(AcZ, -17000, 17000, 0, 180);
}

void loop() {
  read_MPU();
  Serial.print(AcX);
  Serial.print(" ");
  Serial.print(AcY);
  Serial.print(" ");
  Serial.print(AcZ);
  Serial.println(" ");
  if (AcX < 60 && moveable == 0) { 
    moveable = 1;
    Serial1.write('f');
    Serial.println("f");
  }
  if (AcX > 130 && moveable == 0) {
    
    Serial1.write('b');
    Serial.println("b");
  }
  if (AcY < 60 && moveable == 0) {
    moveable = 1;
    Serial1.write('l');
    Serial.println("l");
  }
  if (AcY > 130 && moveable == 0) {
    moveable = 1;
    Serial1.write('r');
    Serial.println("r");
  }

  if ((AcX > 70) && (AcX < 120) && (AcY > 70) && (AcY < 120) && (moveable == 1)) {
    moveable = 0;
    Serial1.write('s');
    Serial.println("s");
  }
//  put your main code here, to run repeatedly:
 if (Serial1.available()) {
   Serial.print((char)Serial1.read());
 }
 if (Serial.available())
 {
   Serial1.write(Serial.read());
 }
}
```
Here is my code for my hand using joysticks
```
int xL, yR = 0;

int xL_center = 490;
int yR_center = 490;

void setup() { 
 Serial.begin(38400);
 Serial1.begin(38400); 
} 

void read_joystick(){
  xL = analogRead(A0);
  yR = analogRead(A5);
}
void loop(){
  read_joystick();
  if (!((xL_center - 30) < xL && (xL_center + 30) > xL)) {
    if (xL < 468) {
      moveable = 1;
      Serial.println("f");
      Serial1.write('f');
    }
    if (xL > 600) {
      moveable = 1;
      Serial.println("b");
      Serial1.write('b');  
    }
  }

  else if (!((yR_center - 30) < yR && (yR_center + 30) > yR)) {
    if (yR < 456) {
      moveable = 1;
      Serial1.write('r');
      Serial.println("r");
    }
    if (yR > 524) {
      moveable = 1;
      Serial1.write('l');
      Serial.println("l");
    }
  }

  else{
    Serial.println("s");
    Serial1.write('s'); 
  }
}
```
Here is my code for the car
```
#include <SoftwareSerial.h>

#define rx 2
#define tx 3

char input_hand;
const int A1A=11;
const int A1B=10;
const int A2A=5;
const int A2B=4;
const int A3A=7;
const int A3B=6;
const int A4A=9;
const int A4B=8;

SoftwareSerial configBt(rx, tx);
long tm,t,d;

void setup() {
  Serial.begin(38400);
  configBt.begin(38400);
  pinMode(tx, OUTPUT);
  pinMode(rx, INPUT);

  pinMode(A1A, OUTPUT);
  pinMode(A1B, OUTPUT);
  pinMode(A2A, OUTPUT);
  pinMode(A2B, OUTPUT);
  pinMode(A3A, OUTPUT);
  pinMode(A3B, OUTPUT);
  pinMode(A4A, OUTPUT);
  pinMode(A4B, OUTPUT);
}

void forward() {
  digitalWrite(A1A,LOW);
  digitalWrite(A1B,HIGH);
  digitalWrite(A2A,LOW);
  digitalWrite(A2B,HIGH);
  digitalWrite(A3A,LOW);
  digitalWrite(A3B,HIGH);
  digitalWrite(A4A,LOW);
  digitalWrite(A4B,HIGH);
  
}

void backward() {
  digitalWrite(A1A,HIGH);
  digitalWrite(A1B,LOW);
  digitalWrite(A2A,HIGH);
  digitalWrite(A2B,LOW);
  digitalWrite(A3A,HIGH);
  digitalWrite(A3B,LOW);
  digitalWrite(A4A,HIGH);
  digitalWrite(A4B,LOW);
}

void left() {
  digitalWrite(A1A,LOW);
  digitalWrite(A1B,HIGH);
  digitalWrite(A3A,LOW);
  digitalWrite(A3B,HIGH);
  digitalWrite(A2A,HIGH);
  digitalWrite(A2B,LOW);
  digitalWrite(A4A,HIGH);
  digitalWrite(A4B,LOW);
}

void right() {
  digitalWrite(A1A,HIGH);
  digitalWrite(A1B,LOW);
  digitalWrite(A3A,HIGH);
  digitalWrite(A3B,LOW);
  digitalWrite(A2A,LOW);
  digitalWrite(A2B,HIGH);
  digitalWrite(A4A,LOW);
  digitalWrite(A4B,HIGH);
}

void coasting() {
  digitalWrite(A1A,LOW);
  digitalWrite(A1B,LOW);
  digitalWrite(A3A,LOW);
  digitalWrite(A3B,LOW);
  digitalWrite(A2A,LOW);
  digitalWrite(A2B,LOW);
  digitalWrite(A4A,LOW);
  digitalWrite(A4B,LOW);
}

void loop() {
  if(configBt.available()) {
    input_hand = configBt.read();
  }

  if (configBt.available()) {
  Serial.print((char)configBt.read());
  }
  if (Serial.available())
  {
    configBt.write(Serial.read());
  }

  if (input_hand == 'f') {
    forward();
    Serial.println(input_hand);
  }
  if (input_hand == 'b') {
    backward();
    Serial.print(input_hand);
  }
  if (input_hand == 'l') {
    left();
    Serial.print(input_hand);
  }
  if (input_hand == 'r') {
    right();
    Serial.print(input_hand);
  }
  if (input_hand == 's') {
    coasting();
    Serial.print(input_hand);
  }
}
```
# Bill of Materials
Here's where you'll list the parts in your project. To add more rows, just copy and paste the example rows below.
Don't forget to place the link of where to buy each component inside the quotation marks in the corresponding row after href =. Follow the guide [here]([url](https://www.markdownguide.org/extended-syntax/)) to learn how to customize this to your project needs. 

| **Part** | **Note** | **Price** | **Link** |
|:--:|:--:|:--:|:--:|
| Adruino Uno | Logic control for car component | 16.99$ | <a href="https://www.amazon.com/ELEGOO-Board-ATmega328P-ATMEGA16U2-Compliant/dp/B01EWOE0UU/ref=sr_1_2_sspa?crid=3A6NCD2X9JEMJ&dib=eyJ2IjoiMSJ9.AcWZy-Yg4mDTnhzEHozxzPZdVC5-KUL2tW-OQewDKpBB4brSpD-p4bn74WcXiW3KarYertgpNaLJ0VHKx0qsPqolKAhiz1GRG5BwJQl73cEvrlXIXNmqlpSvU7uu2aRVSwAZi9Gj2AjSPLM3esW1Gzy9xEiQ9oiR5LCNjh4MlYDx5mTm5sI4rsD4CFTipJnF572qXlickl35FRcCj8oMXQotumgqI4yEIq0HobOtIlEnNhtVB51JMBHhqtmmF_PC9WeHJ4ySUVVcv_gq3_VeG1aAEbdm4NXmmT6NOYPw4Qo.1PFdgFT22oqO5Mg6-6j_aUL_EV8tUPuaFrB5N9oaEX0&dib_tag=se&keywords=elegoo+arduino&qid=1716856465&s=electronics&sprefix=elegoo+arduino%2Celectronics%2C99&sr=1-2-spons&sp_csd=d2lkZ2V0TmFtZT1zcF9hdGY&psc=1"> Link </a> |
| Adruino Micro | Logic control for hand component | 23.00$ | <a href="https://www.amazon.com/Arduino-Micro-Headers-A000053-Controller/dp/B00AFY2S56/ref=sr_1_1?crid=20YSC618RK7HK&dib=eyJ2IjoiMSJ9.KYXhMLqhDqOc2MMWNBuX3o6Z704f6o9819WeGW6BNwtfEbjqdXADHXUtkt6EZNGWaUmc1j5t8LCrgTUlUICAe3F4gmQmYWr2zHjWBZ5E1rm4gJ9VtQeQn77IZ3SsWkgSjHVy50jyiW6dKxiUlTeYJxEjK9ANHejH5zpE-bs88AmMyl3o2DJifFP0TvtZT3fc23pLfqvHz3kaNW4-uaZXEdMp0kH-7o7Niy5QILp0Rfk.ohT6Ufl_wpGGSgauw9W4Ep3oQFoZrW4GsOHYS-_ZQco&dib_tag=se&keywords=micro%2Barduino&qid=1751382686&sprefix=micro%2Baduri%2Caps%2C440&sr=8-1&th=1"> Link </a> |
| MPU-6050 | Acceleration measurement for hand component | 10.99$ | <a href="https://www.amazon.com/Pre-Soldered-Accelerometer-Raspberry-Compatible-Arduino/dp/B0BMY15TC4/ref=sr_1_2?crid=3JNFUNF55EMR1&dib=eyJ2IjoiMSJ9.nQ-HfKOFyZoszrV3cxLK6szL_dfkU7ZnseUB1MbsDUD_ExiEY3ljLKRh2b64oBeiBZvu3jXKXUihvCCHRXIhIBs4dKEBoT4YlTz80GUeV3Vy3269fCiyzUXX--Pb0oRCBDvZGKvq8FfAEyePBY9ZO66ntUqzlPySMsGLDwaEW0brUTrTfWfqzn_fq1psHZvSanLTyeokOF0MTRK7KaX7t2JD0kBqyChH2V1mIPpXeSU.LtBV6Dd2NSNr-MsM03xdwKwOamF0zGBQsNTTF1lytzY&dib_tag=se&keywords=mpu+6050&qid=1751382766&sprefix=mpu+%2Caps%2C1112&sr=8-2"> Link </a> |
| HC-05 | Bluetooth module | 9.99$ | <a href="https://www.amazon.com/DSD-TECH-HC-05-Pass-through-Communication/dp/B01G9KSAF6/ref=sr_1_1?crid=38TV8VB4T0X5S&dib=eyJ2IjoiMSJ9.VZL1p5RDGQw7c8DXaqrVkVxg4m258Oy2LeE1NiVg_8ME-lPLP26gSSpozOKtuZFLE7Mwg1CWAJ55vz6-95weGCunV5m0K2b177vaEO64WRvI56TcgcBCwQP2jWPBM45bhQyL8gQZ11qe-KZJ7v4GzavS3OehkOGmSylXJ51KF0YxWuJoC1syotWL1KAP0PhQ9IEKFL_X1oXzHNcH9fWAVh_jqlpoO3o1ZyUXBWwDY9Y.F8LLT4xTcfNJiAz6ws4-89XrfBR2MhSUxTHAB78T_u4&dib_tag=se&keywords=hc-05&qid=1751382793&sprefix=hc-%2Caps%2C550&sr=8-1"> Link </a> |
| Screwdriver set | tool set | 7.99$ | <a href="https://www.amazon.com/Small-Screwdriver-Set-Mini-Magnetic/dp/B08RYXKJW9/%22%3E"> Link </a> |
| Solder boards | Connecting wires |6.99$ | <a href="https://www.amazon.com/Pcs-MCIGICM-Points-Solderless-Breadboard/dp/B07PCJP9DY/ref=sr_1_1?dib=eyJ2IjoiMSJ9.Nzs7R0SjVHIxVvEThRpOz9sxqhs5KyJ1I0W1Q395qgDm2jNi6LZR2GOlHSegtwztu1VEI7IdXsQ39ee2bGw2s6xOSGXpOSg3W8GenR__BQoIAeu7e7y0FMRm0pZmx9MYg-zjc3Xd-tRj_28OCIyu3uEh880hk_qT1WxFUvmoQKOLhVDJatdmdKUQwZDIZvJhqs2HyR90n0G11XAma91KNTWZYK8dUGrDiWacK1dYJYA.Tw3JzksQ1vSqpMrk4sVYoH0U7SBYv1nwpYAwhur--fY&dib_tag=se&keywords=Solderless+Breadboard+Half+Size&qid=1751382956&sr=8-1"> Link </a> |
| Electronic kits | Electronic components |9.99$ | <a href="https://www.amazon.com/EL-CK-002-Electronic-Breadboard-Capacitor-Potentiometer/dp/B01ERP6WL4/ref=sr_1_6?crid=3G39AQI2SIIO9&dib=eyJ2IjoiMSJ9.aiOuEiBGo_Mju-lAIg683UwZOXbilvianje9P5S856QelgpdLhyvnuqDEkXY5r29FMFdQqW0gaGUKPx-3KGoE0as5WlUQUvsyc7QIpBScMfG9UsAM6tZbouaRzw4_GpaBrMtYWiJZ7-3u0jGxyuD97NhOjODYYoH76TYxpFOjbjhg-S4zqOVelINSWBw1gUcDZzGwMIDgTz6dQQKBtJPovaReLQIMYwtt4tDXuw54dI9fEuzR5s3ZwzjIak_PNVcwSdPLhapxt4ln0T_NQDNV1l8DGxGcWaAkMqF3VXnwzA.tI-iQ9_2GtK9cgTYYwFfFCypy9fN08db-_BzLWnnDms&dib_tag=se&keywords=electronic+kit&qid=1751385030&sprefix=electonic+k%2Caps%2C620&sr=8-6"> Link </a> |
| Electronic kits | Motors for main car |9.99$ | <a href="https://www.amazon.com/Gebildet-DC3V-12V-Aircraft-Robotic-Four-Wheel/dp/B08CMPBXNL/ref=sr_1_48?crid=25G6WBEIANO4J&dib=eyJ2IjoiMSJ9.ka9wQodTGdr6QcvxZaRbZd6mlmgSCzerVYNOy6D30O76UDbeBz-RBLZ8aQaDxJatshWTraJuynQG8X31CwYRJRf9znANs_lVR8GkGOHeu-qEpsjF93zeFLwaPUyyh61YrVxaVCffSCJ11q5aI50jJIla-P1BCl19b4R0p4HvZFq9-TIAcOOzN-cRNEBnl7q-RC_hbV4mxdMYZ942Qbu9NTTNHVddCwboKUhzM_zFbM6SftK2LH4OcbcYd4P44qohxwQsG2ThS5JKcCjEUgK6tf_r2eEQXDL868VAU32mj6Q.vZJ0juaKAVJJ24pYMgL-3__1ptscxPIX8ct-liCvwi4&dib_tag=se&keywords=DC%2BMotor%2C%2B12%2BV&qid=1751385258&sprefix=dc%2Bmotor%2C%2B12%2Bv%2Caps%2C698&sr=8-48&th=1"> Link </a> |
| 9V battery clip | battery clip |3.99$ | <a href="https://www.amazon.com/Battery-Connector-Plastic-Experiment-Equipment/dp/B08SL9X2YC/ref=sr_1_1?crid=ZUB1LKS87T73&dib=eyJ2IjoiMSJ9.lrvdqNYrwtnnoUa2K5IvkJpY7Ca8d9Ttfo1jsB6qdqMoDm8H4Mg5y1-B9VtJBEHVWVVE2d2ze0L1JojuKOmOVXIQPh2gMcNkp-EcpRO7RIeK93CYevfzaXeFEbnyl3TCkxHlZsdclCifVR5g9GPfc5dkwSZGBoYMkZZJ1Iw9j5gt4vtFsanpCK9HzcLeMptuaEMo3efqHRCideCUOMW2WgWzyZ3GMXGlX2gwhF0A_IY.IAmNqyICd7Cpqkq4rdTOHs0OIxd6-PfgRLJV5XGr-Ko&dib_tag=se&keywords=9v%2Bbattery%2Bclip&qid=1751385465&sprefix=9v%2Bbattery%2Bcli%2Caps%2C635&sr=8-1&th=1"> Link </a> |
| 9V battery | Powering car and hand |8.99$ | <a href="https://www.amazon.com/Amazon-Basics-Performance-All-Purpose-Batteries/dp/B00MH4QM1S/ref=sr_1_6?crid=43EZIADYZBH2&dib=eyJ2IjoiMSJ9.de-Op4ZbuQJmAOVj7eXm71jNVKBJW9CBRnvnu9KoyTuQ4dL_0_l4gmMTjMviAMDghQqZ70gN299jQvfhjfvzA6fPReaRi8XX5FIs401RCZlIhk1IkTuaGITwJvtAYctYxQJ36gQqCPV20fNi2eCw0Eu36jvtssIhaf3mC8XkxRKszfvkxjFOjtKILUvDghWMJMzvQh8sRUy5zX6CTzXlFZftBMOPxWYqrs7_lUrL3KlmfyOOH06S-uoib5ozJ-VKZ2ISNhsr9_PB4YQDqn1zQtWEQqR9-cA8O3FYOx3icEY.lj16fX4zGTQhD4QSn3wFQqH8rzeWMMiIPMFAMfqehhY&dib_tag=se&keywords=9V%2Bbattery&qid=1751385526&sprefix=9v%2Bbattery%2Caps%2C432&sr=8-6&th=1"> Link </a> |
| Car chasis | hold components of the main car |19.99$ | <a href="| 9V battery | Powering car and hand |8.99$ | <a href="https://www.amazon.com/Amazon-Basics-Performance-All-Purpose-Batteries/dp/B00MH4QM1S/ref=sr_1_6?crid=43EZIADYZBH2&dib=eyJ2IjoiMSJ9.de-Op4ZbuQJmAOVj7eXm71jNVKBJW9CBRnvnu9KoyTuQ4dL_0_l4gmMTjMviAMDghQqZ70gN299jQvfhjfvzA6fPReaRi8XX5FIs401RCZlIhk1IkTuaGITwJvtAYctYxQJ36gQqCPV20fNi2eCw0Eu36jvtssIhaf3mC8XkxRKszfvkxjFOjtKILUvDghWMJMzvQh8sRUy5zX6CTzXlFZftBMOPxWYqrs7_lUrL3KlmfyOOH06S-uoib5ozJ-VKZ2ISNhsr9_PB4YQDqn1zQtWEQqR9-cA8O3FYOx3icEY.lj16fX4zGTQhD4QSn3wFQqH8rzeWMMiIPMFAMfqehhY&dib_tag=se&keywords=9V%2Bbattery&qid=1751385526&sprefix=9v%2Bbattery%2Caps%2C432&sr=8-6&th=1"> Link </a> |"> Link </a> |


# Other Resources/Examples
One of the best parts about Github is that you can view how other people set up their own work. Here are some past BSE portfolios that are awesome examples. You can view how they set up their portfolio, and you can view their index.md files to understand how they implemented different portfolio components.
- [Example 1](https://trashytuber.github.io/YimingJiaBlueStamp/)
- [Example 2](https://sviatil0.github.io/Sviatoslav_BSE/)
- [Example 3](https://arneshkumar.github.io/arneshbluestamp/)

To watch the BSE tutorial on how to create a portfolio, click here.
