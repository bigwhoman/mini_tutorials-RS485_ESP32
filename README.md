# mini_tutorials-RS485_ESP32
Setting up RS485 and then connecting it to ESP32 module
## What is RS485
RS-485 is a standard for serial communication transmission of data. It is used in a wide range of applications, such as industrial automation, instrumentation, and control systems. RS-485 is a differential communication standard, which means that it uses two wires to transmit data while rejecting common-mode noise. This makes it more immune to noise than single-ended standards like RS-232.

RS-485 supports multi-drop communication, which means that multiple devices can be connected to the same bus, allowing them to communicate with each other. It also supports long-distance communication, with data rates of up to 10 Mbps over short distances and up to 100 kbps over long distances of up to 1200 meters. <br>
The image below demonstrates how RS485 works.<br>

![image](https://github.com/bigwhoman/mini_tutorials-RS485_ESP32/assets/79264715/a36be1b7-d303-4cdf-a6f8-6c481f991b14)

## Usage of RS485
RS-485 is often used in industrial settings where reliable and robust communication is required, such as in factory automation, process control, and building automation systems.
## Setting up RS485
### driver
First, we will need to download the ch340 driver (if needed) for our system. <br>

https://learn.sparkfun.com/tutorials/how-to-install-ch340-drivers/linux
```Shell
sudo modprobe ch341
ls /dev/tty*
sudo dmesg | grep tty
sudo chown $USER /dev/ttyUSB0
```

### connection 
UART
#### RS485 to RS485
```
A -> A <br>
B -> B <br>
```
#### UART to RS485

```
VCC --> 3V3
GND --> GND
---------
RO  --> RX2
DI  --> TX2
DE + RE --> Serial X
```

![image](https://github.com/bigwhoman/mini_tutorials-RS485_ESP32/assets/79264715/74151021-8bdd-4a32-b60c-9231c4629953)


## Modbus Poll
Now we will need some kind of interface to communicate with RS485, we will use <b>Modbus Poll</b> as our interface.<br>
This is a interface for windows operating system which could send or receive data as the master for the RS485 device.

### Download
Download your system version. <br>
https://www.modbustools.com/download.html
Since in our simulation we will be using the ESP32 as the slave and the computer as our master you will need the <b>Modbus Poll</b>.
You could also download the Modbus slave on another computer and connect them with two RS485 modules to show the Master-Slave data exchange.

### Modbus Poll Interface
First, we go to <b>Connection -> Connection Setup</b> to setup our connection.<br>

![image](https://github.com/bigwhoman/mini_tutorials-RS485_ESP32/assets/79264715/1e1e0297-3b7b-417a-8c25-429500138bbe)

<br>

Then we go to <b>Functions</b> to select the wanted modbus function. <br>

Modbus functions will be as below. 

![image](https://github.com/bigwhoman/mini_tutorials-RS485_ESP32/assets/79264715/0d34db7f-d0bb-4caf-ba19-a4db21d62f81)

Now we will set our wanted function. <br>

![image](https://github.com/bigwhoman/mini_tutorials-RS485_ESP32/assets/79264715/175c9835-d914-4576-8ca6-aaa0dc0e4207)


## Connecting Two computers with RS485

To do this, download the slave app on another computer and connect both computers using the RS485 module. <br>
Then, make sure that the buad rates on both computers are the same so that they could exchange the data. <br>
Now try to first, read the values of slave registers with <b>read coil</b> and then change them randomly. <br>
You should see the that the register values would change on the second PC.

## Connecting RS485 to ESP32
#### Connect ESP32 to PC
```
sudo chown $USER /dev/ttyUSB0
```

### modbus_RTU
This library is used to work with RS485 on ESP32 module. <br>
Donwload the whole .zip project and add it to arduino-IDE. <br>
https://github.com/birdtechstep/modbus-esp

### Example Codes as Slave

The first code sets up a slave on the ESP32 module with a 10 register count where the first register offset set as 0. <br>

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

#### Boot Button
Now upload the codes onto ESP32, if you get stuck during connecting phase, hold the <b>boot</b> button on the ESP32 board.

#### Parity
Make sure that the parity bits on the slave and master are the same, otherwise you would get parity error...
