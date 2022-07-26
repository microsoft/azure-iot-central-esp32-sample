# Introduction 
This is an example code which leverages Azure IoT Central for a smart farm use case. It collects temperature, humidity, and light data from sensors. Then send the data to Azure via Azure IoT Central via wireless network. Most of the code of this project is based on the [ESP32-Azure IoT Kit example](https://github.com/Azure/azure-sdk-for-c-arduino/tree/main/examples/Azure_IoT_Central_ESP32_AzureIoTKit) of Azure SDK for C on Arduino.
## Hardwares

Micro-controller
* [Adafruit HUZZAH32 – ESP32 Feather](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Flearn.adafruit.com%2Fadafruit-huzzah32-esp32-feather&data=04%7C01%7Cvivihung%40microsoft.com%7C31b9da68c61f43b65b7608da09329eed%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637832410263428282%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C3000&sdata=KqFYe%2Bgacy5JYxGX4C%2Fo8b4s4KcbYZqEL4nd1iIUNcM%3D&reserved=0)

Sensors
* Temperature and humidity sensors: DHT11
* Sunlight sensor: SI1145

# Statement of Purpose
This repository aims to demonstrate how to use an ESP32 board to collect data from sensors and send the data to Azure IoT Central. It is not intended to be a released product. Therefore, this repository is not for discussing Azure IoT Central or requesting new features.

# Getting Started
## Requirements
* Install [Arduino IDE](https://www.arduino.cc/en/software) (recommend the Windows executable than the Windows app version).
* Install [SiLabs CP2104 Driver](https://www.silabs.com/developers/usb-to-uart-bridge-vcp-drivers)
* Install [ESP32 package for Arduino IDE](https://docs.espressif.com/projects/arduino-esp32/en/latest/getting_started.html)
* Install `Azure IoT SDK for C` library in Arduino IDE
* Install libraries for the DHT sensor in Arduino IDE:
    * `Adafruit IO Arduino v2.4.1+`
    * `Adafruit Unified Sensor`
    * `DHT Sensor Library`
* Install libraries for the Sunlight sensor in Arduino IDE:
    * [Manually install]((https://learn.adafruit.com/adafruit-all-about-arduino-libraries-install-use/installing-a-library)) [Adafruit_SI1145](https://github.com/adafruit/Adafruit_SI1145_Library/releases) library from GitHub
* An Azure subscription and has owner permission to create resources.
* A WiFi network is required to run this example.

### Development environment
Although Arduino IDE is required for running this example, I found Visual Studio Code with Arduino extension (`vscode-arduino`) provides better integrated experience.

## Setup
### A. Clone the repository
```
git clone https://github.com/microsoft/azure-iot-central-esp32.git
```

### B. Setup Azure IoT Central
1. Go to [Azure IoT Central](https://apps.azureiotcentral.com/) and create a new IoT Central application.
2. Go to the device page and click `Connect`. Get the value of `ID scope`, `Device ID`. Select "Shared access signature (SAS)" as the Authentication type. Get the value of `Primary key`. We will use them in later steps.

### C. Setup devices

1. Wire hardwares.
![](images/dht11+SI1145.png)

2. Open `iot_configs.h`. Replace `YOUR_WIFI_SSID` and `YOUR_WIFI_PASSWORD` with your WiFi SSID and password.
    ```cpp
    #define IOT_CONFIG_WIFI_SSID              "YOUR_WIFI_SSID"
    #define IOT_CONFIG_WIFI_PASSWORD          "YOUR_WIFI_PASSWORD"
    ```

3. Connect with Azure IoT Central.

   a. Open `iot_configs.h`. Replace `YOUR_ID_SCOPE`, `YOUR_DEVICE_ID`, and `YOUR_DEVICE_KEY` with the `ID scope`, `Device ID` and the `Primary key` from Azure IoT Central setup step 6.

   ```cpp
   #define DPS_ID_SCOPE                      "YOUR_ID_SCOPE"
   #define IOT_CONFIG_DEVICE_ID              "YOUR_DEVICE_ID"
   #define IOT_CONFIG_DEVICE_KEY             "YOUR_DEVICE_KEY"
   ```

   b. In IDE, set the board to `Adafruit ESP32 Feather`. and pick the right port. Upload the code to the board. You should see the Wi-Fi connection status, MQTT connection status, and events in Serial Monitor.

### D. Create a device and modify data model in IoT Central.
After connected Azure IoT Central via the previous step, you will find a device template, "Espressif ESP32 Azure IoT Kit", that is automatically generated in Azure IoT Central portal. 
1. Create a new device in Azure IoT Central: On the left navigation menu, click on "Devices". Click on the "New" button on the top. Provide a name and an ID for the device. Select "Espressif ESP32 Azure IoT Kit" as the device template.
2. Modifiy the data model: In the device template menu, select `Model` -> `{} Edit DTDL`. Copy and paste the content of `Azure_IoT_Central\Device_Template\Model\Espressif ESP32 Azure IoT Kit.json`, click `Save`. Then click `Publish` on the top menu.
3. Validate device connection: Once the updated template is published, navigate to "Devices" -> "Espressif ESP32 Azure IoT Kit" -> "Adafruit Feature ESP32" -> The device you created in D-1. You should see the device status change to `Connected`. Click on the "Raw data" tab, you can see the data with the latest data model uploaded from the device.

# About the code
This code is modified from the `Azure_IoT_Central_ESP32` sample code of Azure SDK for C in Arduino library.
* `Azure_IoT_Central\`: Configurations of Azure IoT Central.
    * `Device_Template\Model\Espressif ESP32 Azure IoT Kit.json`: The DTDL file for the data model.
* `Azure_IoT_Central_ESP32`: The code for the ESP32.
    * `Azure_IoT_Central_ESP32.ino`: The main file.
    * `Azure_IoT_PnP_Template.cpp`: Where we fetch data from the sensors and prepare payload for sending to the cloud.
    * `iot_configs.h`: The secrets for the Azure IoT Central along with other configurations.

