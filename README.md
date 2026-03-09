 # EXPERIMENT-05-IOT BASED SENSOR MONITORING AND DEVICE CONTROL USING RASPBERRY PI AND BLYNK

---

### **NAME:**  
### **DEPARTMENT:**  
### **ROLL NO:**  
### **DATE OF EXPERIMENT:**  

---

## **AIM:**  
To interface **IR and LDR sensors** with **Raspberry Pi 4** to monitor sensor values in the Blynk mobile application and control output devices such as **LED, buzzer, and relay** through the **Blynk platform**.

---

## **APPARATUS REQUIRED:**  
1.	Raspberry Pi 4
2.	IR Sensor Module
3.	LDR Sensor Module
4.	Relay Module
5.	Buzzer
6.	LED
7.	Resistors (220Ω)
8.	Breadboard
9.	Connecting Wires
10.	Power Supply
11.	Wi-Fi Internet Connection
12.	Blynk Mobile Application
13. Computer with Thonny IDE  

---

## **THEORY:**  
<img width="1293" height="744" alt="image" src="https://github.com/user-attachments/assets/3c04afa6-1517-45d2-88f1-e671d9ed1ffb" />

 ### FIGURE-01 RASPI PI 4 PINOUT DIAGRAM: 

The Raspberry Pi 4 Model B is built around a Broadcom BCM2711 system-on-chip that integrates a quad-core ARM Cortex-A72 (64-bit) CPU, VideoCore VI GPU, memory controller, and peripheral interfaces, forming a compact yet complete computer architecture where the SoC connects internally to RAM, USB 3.0 controller, Gigabit Ethernet, HDMI display, and wireless modules. Its 40-pin GPIO header provides a flexible pin configuration consisting of power pins (5 V and 3.3 V), multiple ground pins, and general-purpose input/output pins that operate at 3.3 V logic and can be programmed for digital I/O or alternate functions. Key alternate functions include I²C (SDA, SCL) for sensor communication, SPI (MOSI, MISO, SCLK, CS) for high-speed peripheral interfacing, UART (TX, RX) for serial communication, and PWM for control applications.  For communication, I2C (SDA, SCL), SPI (MOSI, MISO, SCK), and UART (TX, RX) interfaces are mapped across different GPIO pins, allowing seamless connectivity with sensors and peripherals. All GPIO pins support PWM (Pulse Width Modulation), making it useful for motor control, LED brightness adjustment, and sound applications. The BOOTSEL button enables USB mass storage mode for firmware flashing, while the DEBUG pins (SWD interface) provide debugging capabilities. With its low power consumption, flexible GPIO options, and rich interface support, the Raspberry Pi Pico is widely used for IoT, embedded systems, robotics, and automation projects.This architecture and pin multiplexing allow the Raspberry Pi 4 to act as both a general-purpose computing platform and an embedded controller, supporting rapid prototyping, hardware interfacing, and IoT applications.


The Internet of Things (IoT) allows devices to communicate and exchange data over the internet. In this experiment, Raspberry Pi 4 acts as the central processing unit that reads input signals from sensors and communicates with the Blynk cloud platform. The IR sensor detects the presence of objects, while the LDR sensor detects the intensity of light. These sensor values are transmitted to the Blynk mobile application for real-time monitoring. Based on the sensor data or manual commands from the Blynk app, Raspberry Pi controls output devices such as an LED, buzzer, and relay through its GPIO pins. This experiment demonstrates remote monitoring and control using IoT technology.

### **About MPU6050 Sensor:**  
The **MPU6050** is a **6-Axis Inertial Measurement Unit (IMU)** that includes:  
- **3-axis accelerometer** and **3-axis gyroscope**  
- **I2C communication protocol** for easy interfacing  
- **Operating Voltage:** 3.3V – 5V  
- **Accelerometer Range:** ±2g, ±4g, ±8g, ±16g  
- **Gyroscope Range:** ±250°/s, ±500°/s, ±1000°/s, ±2000°/s  

The **accelerometer** measures linear acceleration in **X, Y, Z axes**, while the **gyroscope** measures rotational velocity. The sensor communicates with the Raspberry Pi Pico via **I2C protocol**.

---

## **WORKING PRINCIPLE:**  
1. The **MPU6050 sensor** is connected to the **Raspberry Pi Pico** using the **I2C communication protocol**.  
2. The **Pico reads acceleration and gyroscope values** from the sensor registers.  
3. The data is **processed and displayed on the serial monitor**.  
4. The readings can be used for **motion tracking, tilt sensing, or gesture recognition**.

---

## **CIRCUIT DIAGRAM:**  
### **Connections:**  

| MPU6050 Pin | Raspberry Pi Pico Pin |
|------------|----------------------|
| VCC | 3.3V |
| GND | GND |
| SDA | GP20 |
| SCL | GP21 |

---

## **PROGRAM (MicroPython)**  
```python
from machine import Pin, I2C
import utime

# MPU6050 I2C address
MPU6050_ADDR = 0x68

# MPU6050 Registers
PWR_MGMT_1 = 0x6B
ACCEL_XOUT_H = 0x3B
GYRO_XOUT_H = 0x43

# Initialize I2C
sda = Pin(20)  # Define your SDA pin
scl = Pin(21)  # Define your SCL pin
i2c = I2C(1, scl=scl, sda=sda, freq=400000)  # Use I2C1

def mpu6050_init():
    i2c.writeto_mem(MPU6050_ADDR, PWR_MGMT_1, b'\x00')  # Wake up MPU6050

def read_raw_data(reg):
    data = i2c.readfrom_mem(MPU6050_ADDR, reg, 2)
    value = (data[0] << 8) | data[1]  # Combine high and low bytes
    if value > 32767:
        value -= 65536  # Convert to signed 16-bit
    return value

def get_sensor_data():
    accel_x = read_raw_data(ACCEL_XOUT_H) / 16384.0  # Convert to g
    accel_y = read_raw_data(ACCEL_XOUT_H + 2) / 16384.0
    accel_z = read_raw_data(ACCEL_XOUT_H + 4) / 16384.0
    
    gyro_x = read_raw_data(GYRO_XOUT_H) / 131.0  # Convert to deg/s
    gyro_y = read_raw_data(GYRO_XOUT_H + 2) / 131.0
    gyro_z = read_raw_data(GYRO_XOUT_H + 4) / 131.0
    
    return (accel_x, accel_y, accel_z, gyro_x, gyro_y, gyro_z)

# Initialize MPU6050
mpu6050_init()

while True:
    ax, ay, az, gx, gy, gz = get_sensor_data()
    print(f"Accel: X={ax:.2f}g, Y={ay:.2f}g, Z={az:.2f}g | Gyro: X={gx:.2f}°/s, Y={gy:.2f}°/s, Z={gz:.2f}°/s")
    utime.sleep(1)
```

---

## **OUTPUT:**  
When the above program is executed, the output on the serial monitor will display real-time acceleration and gyroscope values, such as:
```
Accel: X=0.02g, Y=-0.01g, Z=1.00g | Gyro: X=0.05°/s, Y=-0.02°/s, Z=0.01°/s
Accel: X=0.03g, Y=-0.02g, Z=1.01g | Gyro: X=0.06°/s, Y=-0.03°/s, Z=0.02°/s
...
```
---

## **RESULT:**  
The **MPU6050 sensor** was successfully interfaced with the **Raspberry Pi Pico**, and real-time **acceleration and gyroscope data** were read and displayed. The sensor values can be used for **motion tracking, tilt detection, and gesture control applications**.

---

