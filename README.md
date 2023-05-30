# mini_tutorials-RS485_ESP32
Setting up RS485 and then connecting it to ESP32 module
## What is RS485
TODO
## Usage of RS485
TODO
## Setting up RS485
### driver
https://learn.sparkfun.com/tutorials/how-to-install-ch340-drivers/linux
```Shell
sudo modprobe ch341
ls /dev/tty*
sudo dmesg | grep tty
sudo chown $USER /dev/ttyUSB0
```
### modbus_RTU
https://github.com/birdtechstep/modbus-esp


### connection 
UART
#### RS485 to RS485
A -> A <br>
B -> B <br>
#### UART to RS485

```
VCC --> 3V3
GND --> GND
---------
RO  --> RX2
DI  --> TX2
DE + RE --> Serial X
```

### modbus

## Connecting Two computers with RS485
TODO
## Connecting RS485 to computer
#### Connect ESP32 
```
sudo chown $USER /dev/ttyUSB0
```
#### Parity
#### Boot Button

### Example Codes as Slave


#### Read register
```ino
#include <ModbusRTU.h>

#define SLAVE_ID 1
#define FIRST_REG 0
#define REG_COUNT 10

ModbusRTU mb;

bool ToggleFlag= true;

void setup() {
  pinMode(2,OUTPUT);
  
  Serial.begin(115200);

  Serial2.begin(115200);
  mb.begin(&Serial2, 5);

  mb.slave(SLAVE_ID);
  mb.addCoil(FIRST_REG,0,REG_COUNT);
}

unsigned long int premillise = 0;

void loop() {

  if (millis() - premillise >= 5000) {
  
    

    Serial.print("Status Coil bit 8: ");
    
    Serial.println(mb.Coil(1));
    
//    digitalWrite(2,mb.Coil(8));
     
    premillise = millis();
  }

  mb.task();
  yield();
}
```
#### Write Integer
```ino
#include <ModbusRTU.h>

#define SLAVE_ID 1
#define FIRST_REG 0
#define REG_COUNT 10

ModbusRTU mb;

bool ToggleFlag= true;

void setup() {
  pinMode(2,OUTPUT);
  
  Serial.begin(115200);

  Serial2.begin(115200);
  mb.begin(&Serial2, 5);

  mb.slave(SLAVE_ID);
  mb.addIreg(FIRST_REG,1506,REG_COUNT);
}

unsigned long int premillise = 0;

void loop() {

  if (millis() - premillise >= 5000) {
  
    if(ToggleFlag == true){
      for(int i=0;i<=9;i++){
        mb.Ireg(i,888);    
      }
      ToggleFlag = false;
    }
    else if(ToggleFlag == false){
      for(int i=0;i<=9;i++){
        mb.Ireg(i,1506);    
      }
      ToggleFlag = true;
    }

     
    premillise = millis();
  }

  mb.task();
  yield();
}
```
