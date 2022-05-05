# Matter nRF Connect Bridge Example

This lightening bridge example demonstrates a simple lighting bridge and the use of dynamic endpoints..

The Matter device(nRF5340) that runs the lighting bridge application is connected to the Zigbee device(nRF52840) over serial connection(UART). The Matter device is controlled by the Matter controller device over the Thread protocol. If a Zigbee dimmable lightbulb is commissioned into the Zigbee network, the Matter bridge application will add an endpoint for the bridged lightbulb device. The Matter OnOff cluster command will be interpreted as Zigbee ZCL command and sent to the corresponding bridged device.

## Requirement

* A nRF5340 DK as Matter Bridge.
* A nRF52840 DK as [Zigbee Shell](https://developer.nordicsemi.com/nRF_Connect_SDK/doc/1.9.0/nrf/samples/zigbee/shell/README.html).
* At least one [Zigbee Dimmable Lightbulb](https://developer.nordicsemi.com/nRF_Connect_SDK/doc/1.9.0/nrf/samples/matter/light_bulb/README.html).

## Build the example

1. This example is based on nRF Conenct SDK **v1.9.0**. Before building the example, install the NCS v1.9.0 first. See [Getting Started](https://developer.nordicsemi.com/nRF_Connect_SDK/doc/1.9.0/nrf/getting_started.html) for more information.

2. Clone the example to nrf/samples/matter/bridge:

        $ git clone https://github.com/lats1980/matter-light-bridge.git nrf/samples/matter/bridge

3. Build the example:

        $ west build -b nrf5340dk_nrf5340_cpuapp nrf/samples/matter/bridge

4. Flash the device:

        $ west flash

## User Interface

- LED 1:
Shows the overall state of the device and its connectivity. The following states are possible:

   - Short Flash On (50 ms on/950 ms off) - The device is in the unprovisioned (unpaired) state and is waiting for a commissioning application to connect.

   - Rapid Even Flashing (100 ms on/100 ms off) - The device is in the unprovisioned state and a commissioning application is connected over Bluetooth LE.

   - Solid On - The device is fully provisioned.

- Button 1: If pressed for six seconds, it initiates the factory reset of the device. Releasing the button within the six-second window cancels the factory reset procedure.

 - Button 2: Start Zigbee Network Steering commissioning.

## Run the example

1. Connect Matter bridge to Zigbee shell:

    | Matter Bridge (nRF5340) | Zigbee Shell (nRF52840) |
    |---------|----------|
    | P1.02   | P0.08    |
    | P1.03   | P0.06    |

2. Commission the Matter device

   To commission the device, go to the [Configuring Matter development environment guide](https://developer.nordicsemi.com/nRF_Connect_SDK/doc/1.9.0/nrf/ug_matter_configuring_env.html#ug-matter-configuring-env) and complete the steps for the Matter controller you want to use. The guide walks you through the following steps:

    - Configure the Thread Border Router.
    - Build and install the Matter controller.
    - Commission the device.
    - Send Matter commands.

3. Commission Zigbee light bulb devices

4. Control the Zigbee device by CHIP controller

   First, read the PartsList attribute of Descriptor cluster to check the endpoint that represents the bridged Zigbee lightbulb. Below example read PartsList from node ID 1234:

        chip-device-ctrl > zclread Descriptor PartsList 1234 0 0
        [1651798719.817210][510784:510790] CHIP:IN: Prepared secure message 0x2441878 to 0x00000000000004D2 (1)  of type 0x2 and protocolId (0, 1) on exchange 15233i with MessageCounter:8042472.
        [1651798719.817312][510784:510790] CHIP:IN: Sending encrypted msg 0x2441878 with MessageCounter:8042472 to 0x00000000000004D2 (1) at monotonic time: 121222085 msec
        [1651798720.091562][510784:510790] CHIP:EM: Received message of type 0x5 with protocolId (0, 1) and MessageCounter:4903607 on exchange 15233i
        [1651798720.094660][510784:510790] CHIP:IN: Prepared secure message 0x7fcedcb19190 to 0x00000000000004D2 (1)  of type 0x10 and protocolId (0, 0) on exchange 15233i with MessageCounter:8042473.
        [1651798720.094802][510784:510790] CHIP:IN: Sending encrypted msg 0x7fcedcb19190 with MessageCounter:8042473 to 0x00000000000004D2 (1) at monotonic time: 121222363 msec
        AttributeReadResult(path=AttributePath(nodeId=1234, endpointId=0, clusterId=29, attributeId=3), status=0, value=[2, 3])

   The AttributeReadResult shows two endpoints (2, 3) for bridged light device.

5. Send test command to control the bridged light device. Below example toggle light device of endpoint 3 of node ID 1234:

        zcl OnOff Toggle 1234 3 0