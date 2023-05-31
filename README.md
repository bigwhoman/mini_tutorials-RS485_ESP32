# mini_tutorials-RS485_ESP32
Setting up RS485 and then connecting it to ESP32 module
## What is RS485
RS-485 is a standard for serial communication transmission of data. It is used in a wide range of applications, such as industrial automation, instrumentation, and control systems. RS-485 is a differential communication standard, which means that it uses two wires to transmit data.
The image below demonstrates how RS485 works.<br>

![image](https://github.com/bigwhoman/mini_tutorials-RS485_ESP32/assets/79264715/9c25cefd-ca7d-41b1-b350-20da393280b1)


## Usage of RS485
RS-485 is often used in industrial settings where reliable and robust communication is required, such as in factory automation, process control, and building automation systems.
## Setting up RS485
### driver
First, we will need to download the ch340 driver (if needed) for our system. <br>
Download it from the link below and install it according to the link.
https://learn.sparkfun.com/tutorials/how-to-install-ch340-drivers
```Shell
sudo modprobe ch341
ls /dev/tty*
sudo dmesg | grep tty
sudo chown $USER /dev/ttyUSB0
```

### connection 
The connection uses UART for communication.
#### RS485 to RS485
Connect these pins on both RS485 modules.
```
A -> A
B -> B
```
#### UART to RS485
Now connect the left pins related to UART, to right pins related to ESP32.<br>
```
VCC --> 3V3
GND --> GND
---------
RO  --> RX2
DI  --> TX2
DE + RE --> Serial X
```

![image](https://github.com/bigwhoman/mini_tutorials-RS485_ESP32/assets/79264715/70198908-f42d-4f00-99fc-5a3477e2b66d)


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

![image](https://github.com/bigwhoman/mini_tutorials-RS485_ESP32/assets/79264715/11657666-627e-49e1-84b1-7394cba7a0d9)

<br>

Then we go to <b>Functions</b> to select the wanted modbus function. <br>

Modbus functions will be as below. 

![image](https://github.com/bigwhoman/mini_tutorials-RS485_ESP32/assets/79264715/a24a3dd1-2a7d-4005-81bc-2ac3f5e8f19f)
![image](https://github.com/bigwhoman/mini_tutorials-RS485_ESP32/assets/79264715/bc2eda4a-ed50-4553-b4db-ea4f0c1727df)


Now we will set our wanted function. <br>

![image](https://github.com/bigwhoman/mini_tutorials-RS485_ESP32/assets/79264715/4ab9c16b-36be-4a1f-8e83-a44594029ace)


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

#### Read Coil
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
<!-- #### Write Coil
```ino
``` -->

#### Boot Button
Now upload the codes onto ESP32, if you get stuck during connecting phase, hold the <b>boot</b> button on the ESP32 board.

#### Parity
Make sure that the parity bits on the slave and master are the same, otherwise you would get parity error.
