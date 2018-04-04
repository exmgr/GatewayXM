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


## Kura - TB Gateway Architecture approaches

Thingsboard gateway (TB-GW) is an active project, where new features are constantly added, and it is important to ensure seamless upgradeability with future versions of the gateway. Therefore a loose coupled approach is preferred versus one that requires major refactoring of the TB-GW source code. Seven [architecture scenarios](AgileIoT-GatewayXM_D1_v0.4.pdf) have been explored so far with proof of concept implementations in order to conclude on the most efficient approach. 

### Lightweight TB gateway OSGi bundle ### [(Architecture scenario #2)](AgileIoT-GatewayXM_D1_v0.4.pdf)
To take full advantage of Kura’s modular architecture, we decided to create a new, **lightweight TB gateway OSGi bundle** that is not based on the original TB-GW Java Spring source, but instead is utilizing Eclipse Paho MQTT library for direct communication with TB server over the TB gateway API. Doing so we avoid the complexities in refactoring the original TB-GW Java Spring app in to an OSGi bundle, which requires the handling of external configuration files within the Kura environment. 

More inforation on the **lightweight TB gateway OSGi bundle** can be found here: https://github.com/exmgr/Kura-Thingsboard-Bundle

### TB-GW as a service, managed by a Kura OSGi bundle ### [(Architecture scenario #3)](AgileIoT-GatewayXM_D1_v0.4.pdf)
We came up with an alternative architectural approach, in which we take full advantage of the original TB-GW Java Spring app as is, but still have the ability to control it from kura. **TB-GW as a service, managed by a Kura OSGi bundle** is aiming to utilize the original TB-GW deployed as intended by its authors (i.e. as a service) to ensure maximum future proofness. For Kura to be able to manage an external service, a new custom Kura app / OSGi bundle is created, that provides an external managing ability for TB-GW service.

A first step towards this approach is to deploy the TB-GW service as normal and then have the a Kura bundle that can start/stop the service via Kura’s web ui. TB-GW is then configured to subscribe to Kura’s built-in Artemis MQTT broker to listen for devices publishing telemetry data, TB-GW converts the payload to a Thingsboard compatible JSON format and forwards it the Thingsboard server. As a final step in the setup process, a CloudService must be set up that will be used by the Kura Wires graphs to publish telemetry to the local ArtemisMQTT service. The user can then add his devices as Assets in Kura, configure their data inputs as channels, and set up Kura Wire graphs with a simple Timer -> Asset -> Publisher flow to publish their data to Thingsboard.

More information on the **TB-GW service, managed by a Kura OSGi bundle** can be found here: https://github.com/exmgr/Kura-Tb-Gateway-Manager



