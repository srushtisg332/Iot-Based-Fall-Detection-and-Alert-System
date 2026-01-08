# IoT-Based Fall Detection and Alert System
#
# Abstract
# Falls are one of the major health risks for elderly individuals and patients living alone.
# Immediate medical assistance after a fall can significantly reduce complications.
# This project presents an IoT-based fall detection and alert system using the ESP32
# microcontroller and MPU6050 motion sensor.
# The system continuously monitors body movements and detects fall events using
# acceleration thresholds, triggering real-time alerts through Wi-Fi using the
# Blynk IoT platform.
#
# Project Description
# The IoT-Based Fall Detection and Alert System is an embedded solution designed to
# automatically detect human falls and notify caregivers or family members without
# manual intervention.
# The ESP32 collects motion data from the MPU6050 accelerometer and gyroscope,
# processes the data using Embedded C logic, and determines whether a fall has occurred.
# Once detected, the system sends instant alerts over the internet, ensuring quick
# response and improved safety.
#
# System Architecture
# 1. MPU6050 captures acceleration and angular velocity data.
# 2. ESP32 processes sensor data and compares it against predefined threshold values.
# 3. Motion interrupt triggers fall detection logic.
# 4. Wi-Fi module in ESP32 sends alert notifications.
# 5. Blynk cloud platform receives and displays real-time data.
#
# Hardware Requirements
# - ESP32 Development Board
# - MPU6050 Accelerometer and Gyroscope Module
# - Connecting Wires
# - Power Supply
# - Wi-Fi Network
#
# Software Requirements
# - Arduino IDE
# - Embedded C
# - ESP32 Board Package
# - Adafruit MPU6050 Library
# - Blynk IoT Library
#
# Functional Workflow
# - Initialize ESP32 and establish Wi-Fi connection.
# - Configure MPU6050 motion detection and interrupt settings.
# - Continuously read accelerometer and gyroscope data.
# - Detect sudden abnormal acceleration exceeding the threshold.
# - Trigger fall detection event.
# - Send alert notification to the Blynk dashboard.
# - Display live sensor readings for monitoring.
#
# Key Features
# - Automatic fall detection using motion sensors
# - Real-time alert notification via IoT
# - Continuous monitoring of acceleration and gyro data
# - Cloud-based dashboard visualization
# - Reliable and low-cost embedded solution
#
# Applications
# - Elderly care and assisted living
# - Patient monitoring systems
# - Smart healthcare devices
# - Emergency alert systems
# - Wearable safety solutions
#
# Limitations
# - Threshold-based detection may cause false positives during sudden movements.
# - Requires continuous internet connectivity.
# - Not optimized for low-power wearable deployment.
#
# Future Scope
# - Integration of GPS for location tracking.
# - SMS and call alerts using GSM modules.
# - Machine learning-based fall classification.
# - Mobile application development.
# - Power optimization for battery-operated devices.
#
# Learning Outcomes
# - Practical experience with ESP32 and IoT communication.
# - Sensor interfacing and real-time data handling.
# - Embedded C programming for motion detection.
# - Working with cloud-based IoT platforms.
# - Debugging real-world embedded systems.
#
# Conclusion
# The IoT-Based Fall Detection and Alert System demonstrates the effective use of
# embedded systems and IoT technology in healthcare safety applications.
# By integrating the ESP32 with the MPU6050 sensor, the system accurately detects
# fall events and transmits real-time alerts using Wi-Fi and the Blynk platform.
# The project addresses real-world challenges such as sensor calibration, false
# detection reduction, and reliable communication.
# This solution highlights how affordable IoT-based systems can play a crucial role
# in improving emergency response and patient care.
#


## CODE
     B: SOURCE CODE
     #define BLYNK_TEMPLATE_ID "TMPL3vyg4Zcw5"
     #define BLYNK_TEMPLATE_NAME "Iot based fall detector"
     #define BLYNK_AUTH_TOKEN "9vgSFiLullfiTzHxdFeyRDhKWb6Rdb7m"   
     #define BLYNK_PRINT Serial
     #include <WiFi.h>
     #include <WiFiClient.h>
     #include <BlynkSimpleEsp32.h>
     #include <Adafruit_MPU6050.h>
     #include <Adafruit_Sensor.h>
     #include <Wire.h>
     Adafruit_MPU6050 mpu;
     char auth[] = BLYNK_AUTH_TOKEN;
     char ssid[] = "sindhu";
     char pass[] = "sindhu193";
     BlynkTimer timer;
     void sendSensor()
     {
     if(mpu.getMotionInterruptStatus()) {
    /* Get new sensor events with the readings */
    sensors_event_t a, g, temp;
    mpu.getEvent(&a, &g, &temp);
         /* Print out the values */
    Serial.print("AccelX:");
    Serial.print(a.acceleration.x);
    Serial.print(",");
    Serial.print("AccelY:");
    Serial.print(a.acceleration.y);
    Serial.print(",");
    Serial.print("AccelZ:");
    Serial.print(a.acceleration.z);
    Serial.print(", ");
    Serial.print("GyroX:");
    Serial.print(g.gyro.x);
    Serial.print(",");
    Serial.print("GyroY:");
    Serial.print(g.gyro.y);
    Serial.print(",");
    Serial.print("GyroZ:");
    Serial.print(g.gyro.z);
    Serial.println("");
    // You can send any value at any time.
    // Please don't send more that 10 values per second.
        if( a.acceleration.x >=5.0 )
    {
                  Blynk.logEvent("alert","Fall detected");
    }
    Blynk.virtualWrite(V0, a.acceleration.x);
    Blynk.virtualWrite(V1, a.acceleration.y);
    Blynk.virtualWrite(V2, a.acceleration.z);
    Blynk.virtualWrite(V3,g.gyro.x);
    Blynk.virtualWrite(V4,g.gyro.y);
    Blynk.virtualWrite(V5,g.gyro.z);
    delay(200);
          }
         }
        void setup()
         {   
          Serial.begin(115200);
              Blynk.begin(auth, ssid, pass);
               Blynk.logEvent("alert","Fall detected !");
           while (!Serial)
           delay(10); // will pause Zero, Leonardo, etc until serial console opens
            Serial.println("Adafruit MPU6050 test!");
        // Try to initialize!
         if (!mpu.begin()) {
           Serial.println("Failed to find MPU6050 chip");
           while (1) {
             delay(10);
           }
         }
         Serial.println("MPU6050 Found!");
       //setupt motion detection
         mpu.setHighPassFilter(MPU6050_HIGHPASS_0_63_HZ);
         mpu.setMotionDetectionThreshold(1);
         mpu.setMotionDetectionDuration(20);
         mpu.setInterruptPinLatch(true); // Keep it latched.  Will turn off when reinitialized.
         mpu.setInterruptPinPolarity(true);
         mpu.setMotionInterrupt(true);
         Serial.println("");
         delay(100);
         timer.setInterval(1000L, sendSensor);
       }
       void loop()
       {
         if(Blynk.connected())
         {
           Serial.println("Device is online");
         }
             else
             {
               Serial.println("Device is offline")
         }
             Blynk.run();
             timer.run();
        }
