# Gateway ex Machina
Eclipse AGAIL extended with Thingsboard and ESP32

Gateway ex Machina extends and integrates [Eclpise AGAIL / Agile IoT](https://projects.eclipse.org/projects/iot.agail) technologies with [Thingsboard](https://github.com/thingsboard/thingsboard/) in an attempt to create the necessary software for an industrial grade robust gateway paired with [Espressif ESP32](https://github.com/espressif/arduino-esp32) wireless sensors that can be used in industrial IoT projects.

### Goals overview

1.  Utilize Eclipse Kura and connect to MODBUS smart meter devices.
1.  Integrate Eclipse Kura with Thingsboard.io and communicate telemetry from MODBUS devices on the field to the Thingsboard cloud server.
1.  Enable easy remote device management and OTA software update of GW software by utilizing dockerized Kura, resin.io and other AGAIL technologies.
1.  Develop an ESP32 mesh sensor network that is bridged with the GW, enabling sensor telemetry data to be forwarded to Thingsboard server.
1.  Add ESP32 firmware OTA capability via GW.


### Background

Thingsboard project includes a powerful gateway open-source software designed to run at the edge, connecting to the Thingsboard server over MQTT, as well as to local hardware sensors.




![alt_text](https://github.com/thingsboard/thingsboard-gateway/raw/master/img/tb-gateway.png?raw=true "image_tooltip")


[https://github.com/thingsboard/thingsboard-gateway](https://github.com/thingsboard/thingsboard-gateway)

The Gateway provides simple integration APIs, and encapsulates common Thingsboard related tasks: device provisioning, local data persistence and delivery, message converters/adaptors and other. It is designed with the assumption that application developers will connect to it through an external MQTT broker or an OPC-UA server, or by implementing custom extensions that support other protocols (e.g. MODBUS). It is based on Java Spring and its monolithic nature introduces various limitations for application developers who wish to extent it with custom protocols, plus it lacks device management features such as OTA software updates, self-healing agents etc. Therefore the combination of Eclipse Kura and other Agile IoT technologies with the Thingsboard gateway will bring multiple advantages to application developers.

After performing a thorough evaluation of Eclipse Kura, Resin.io and the Eclipse AGAIL project, a number of architecture approaches were identified, which are described below.


##


## Architecture approach

Thingsboard gateway (TB-GW) is an active project, where new features are constantly added, and it is important to ensure seamless upgradeability with future versions of the gateway. Therefore a loose coupled approach is preferred versus one that requires major refactoring of the TB-GW source code. A number of [architecture scenarios](AgileIoT-GatewayXM_D1_v0.3.pdf) has been explored with proof of concept implementations in order to conclude on the final most efficient architecture. So far most of the work is focused on a **lightweight Kura OSGi bundle that acts as a thingsboard gateway** and is documented in the repo bellow.

https://github.com/exmgr/Kura-Thingsboard-Bundle
