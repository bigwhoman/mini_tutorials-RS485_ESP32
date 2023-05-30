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

### connection 
A -> A <br>
B -> B <br>

### modbus

### diagslave

```
./diagslave -b 9600 -p none -m rtu /dev/ttyUSB0 
```

## Connecting Two computers with RS485
TODO
## Connecting RS485 to computer
TODO
