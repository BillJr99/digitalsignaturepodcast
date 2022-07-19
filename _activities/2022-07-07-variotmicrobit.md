---
layout: activity
title: "Integrating the micro:bit with VarIOT via ThingsBoard"
permalink: /activities/variotmicrobit

gradelevel: secondary
category: microbit
subject: engineering

gitrepo: https://github.com/billjr99/variot-test

authors: "William M. Mongan"
date: 2022-07-07

summary: "In this activity, we will integrate the BBC micro:bit with a ThingsBoard gateway called VarIOT using a wireless radio."

thumbnail: "/images/activity-variotmicrobit/fig5.jpg"
---

In this article, we will integrate the [BBC micro:bit](https://microbit.org/) with a [ThingsBoard](https://thingsboard.io/) gateway called [VarIOT](https://drexel.edu/engineering/news-events/news/archive/2021/December/vip-project-helps-student-make-connections/).

If we have a VarIOT ThingsBoard gateway (which can be installed on a [Raspberry Pi](https://www.raspberrypi.org/)), we can connect it with a ThingsBoard server (one of which is hosted at Drexel Univesrity) to send telemetry data from IoT sensors that we can collect and evaluate using a micro:bit.  For example, we could send environmental data like temperature or humidity, or biomedical data like one's respiratory rate (or anything else you can imagine!).

### Configuring a VarIOT Gateway

You can install VarIOT and the ThingsBoard software on a Raspberry Pi by following [these instructions](https://github.com/drexelwireless/thingsboard-gateway/blob/master/docs/installation/tb-gateway.rst).  These steps also describe how to connect a gateway to a ThingsBoard server.  

#### Enabling Web Access to the Gateway

I edited the `~/thingsboard-gateway/thingsboard-gateway/thingsboard_gateway/config/tb-gateway.yaml` configuration file on my Raspberry Pi and un-commented the code in the REST block to allow REST access from the micro:bit.  

ThingsBoard provides [an article](https://thingsboard.io/docs/iot-gateway/getting-started/) describing how to configure the gateway and, specifically, how to [configure RESTful access](https://thingsboard.io/docs/iot-gateway/config/rest/).  You can add your own endpoints here following the template provided in the `~/thingsboard-gateway/thingsboard-gateway/thingsboard_gateway/config/rest.json` file. 

One thing that is required is to change the host name from `127.0.0.1` to `0.0.0.0` so that the endpoints are available from the outside world.  I did not enable SSL, since I don't have SSL built into the code of the HTTP extension driver.  For this reason, I'm also not using a username or password, since these would be sent in clear text, but these are prototyping practices only (and not for production use!).

#### Connecting to a VPN
If needed, you can VPN to the ThingsBoard server by running: `sudo openconnect -u <your user name> -b <vpn server name>` on your Gateway Raspberry Pi.  This will allow your gateway to connect to a server that is behind a VPN.

#### Testing the RESTful Configuration
In my `rest.json` file, I see that I have an endpoint called `my_devices`.  This is not the most descriptive name, since it's just a template, but rather than modify it, I'll use it for this demo so that we have a proof of concept with minimal configuration effort.  We can test this endpoint using a curl command from a terminal:

```
curl http://rpi4-variot:5000/my_devices -H "Content-Type: application/json" -X POST --data '{"temp": 25, "sensorName": "Mongan Gateway", "sensorType": "default", "sensorModel": "testmodel"}'
```

This endpoint is configured to map the `temp` parameter to the `temperature` telemetry value.  For fun, I also send an attribute called `model` that is mapped to the `sensorModel` parameter in the configuration, and I will send the value `testmodel`.  This can be left out.  When I configured my Gateway, I called my gateway sensor `Mongan Gateway`, and so I will configure this as the `sensorName` as well.

### Connecting the micro:bit to WiFi

Next, we will connect the micro:bit to WiFi so that we can connect to the Internet and send telemetry data to the gateway.  The instructions and diagrams provided by [these instructions](https://github.com/alankrantas/pxt-ESP8266_ThingSpeak) for a ThingSpeak board are exactly what we need.  I did not bother connecting an LED or the USB, so only the connections to the ESP8266 WiFi module was needed.

I used a breadboard, jumper wires, and an ESP8266 WiFi Module to do this, as well as a breadboard power supply.  I also used a micro:bit breakout board so that I can connect jumper cables to the pins of the micro:bit from the breadboard, rather than needing to use the allegator clips to clamp to the metal pads.  I'll include links to the items I used.

* [MB-102 Breadboard Power Supply](https://smile.amazon.com/dp/B08JYPMCZY?psc=1&ref=ppx_yo2ov_dt_b_product_details)
* [MB-102 Breadboard](https://smile.amazon.com/dp/B08DFWQBMB?psc=1&ref=ppx_yo2ov_dt_b_product_details)
* [Jumper Cables](https://smile.amazon.com/dp/B07GD2BWPY?psc=1&ref=ppx_yo2ov_dt_b_product_details)
* [Microbit Breakout Board](https://smile.amazon.com/Edge-Connector-Breakout-Board-Micro/dp/B082Z6H5LB/ref=sr_1_4?crid=994DJZFDCISO&keywords=microbit+breakout+board&qid=1656939978&s=industrial&sprefix=microbit+breakout+board%2Cindustrial%2C51&sr=1-4)

I connected the Rx pin of the WiFi module to P0 of the micro:bit, the Tx pin to P1, VCC and CH_EN to the 3V pin for power, and ground to the ground pin, as shown in the diagrams of the wiring diagram and pinout (shown below; image credit Alan Krantas via the [alankrantis/pxt-ESP8266_ThingSpeak](https://github.com/alankrantas/pxt-ESP8266_ThingSpeak) GitHub repository under an [MIT License](https://raw.githubusercontent.com/alankrantas/pxt-ESP8266_ThingSpeak/master/LICENSE)):

<img style="max-width:100%;" src="https://user-images.githubusercontent.com/44191076/57862847-9c235980-782b-11e9-9588-3e7fe76342ee.png" alt="Wiring diagram - Alan Krantas GitHub under an MIT License">

<img style="max-width:100%;" src="https://user-images.githubusercontent.com/44191076/50428909-fc097a00-08f5-11e9-91f1-921d1b957f29.png" alt="Pinout - Alan Krantas GitHub under an MIT License">

Following these diagrams, I wired my WiFi module to the micro:bit as follows:

<div style="text-align:center;">
<img style="max-width:100%;"  src="/images/activity-variotmicrobit/fig1.jpg" alt="Finished WiFi to micro:bit breadboard wiring figure 1">
<img style="max-width:100%;"  src="/images/activity-variotmicrobit/fig2.jpg" alt="Finished WiFi to micro:bit breadboard wiring figure 2">
<img style="max-width:100%;"  src="/images/activity-variotmicrobit/fig3.jpg" alt="Finished WiFi to micro:bit breadboard wiring figure 3">
<img style="max-width:100%;"  src="/images/activity-variotmicrobit/fig4.jpg" alt="Finished WiFi to micro:bit breadboard wiring figure 4">
<img style="max-width:100%;"  src="/images/activity-variotmicrobit/fig5.jpg" alt="Finished WiFi to micro:bit breadboard wiring figure 5">
</div>

### Writing the micro:bit Program
Finally, we can import this extension into a micro:bit project.  To do this, create a micro:bit project at [makecode.microbit.org](https://makecode.microbit.org) and click the settings cog at the top right.  Choose `Extensions`, and search for VarIOT.  Alternatively, you can import this URL directly to import the extension from GitHub: [https://github.com/BillJr99/pxt-ESP8266_VarIOT](https://github.com/BillJr99/pxt-ESP8266_VarIOT).

That's it!  You can now use VarIOT blocks to send data to the VarIOT gateway.  There are three basic steps (and blocks) to do this:

1. `on start`: add an `Initialize ESP8266` block.  The RX and TX pins should be set to P0 and P1, respectively (this seems backwards from the pinout wiring we did earlier, and that's OK, because the Rx of the WiFi is the Tx of the micro:bit, and vice-versa!).  Set your WiFi SSID and password here.
2. Also `on start`, but after connecting to WiFi, add a `Configure VarIOT gateway location` block and set the IP address and port of the VarIOT gateway.  For me, that is `rpi4-variot` and `5000`, but this will depend on the IP address of the Raspberry Pi and the port used when configuring the gateway above.
3. Whenever you'd like, add an `Upload data to VarIOT` block and fill in the details.  For me, I used the following settings from when I configured the gateway:
  * `Endpoint`: my_devices
  * `Device Name`: Mongan Gateway
  * `Label`: temp
  * `Value`: 55
  
That should do it!  When you view your gateway dashboard on the server, you should see this value appear.  

### Programming the micro:bit Extension to Connect to VarIOT
You don't have to do this step, but if you're interested, this section describes how I created the micro:bit extension blocks for use in your micro:bit program (to connect to VarIOT).

To do this, I forked the [alankrantas/pxt-ESP8266_ThingSpeak](https://github.com/alankrantas/pxt-ESP8266_ThingSpeak) repository into my own extension, which I call [BillJr99/pxt-ESP8266_VarIOT](https://github.com/BillJr99/pxt-ESP8266_VarIOT), under an MIT license.

All of the original repository's WiFi connection and TCP connection code applies here, so I was able to re-use that.  In effect, we will run our cURL command that we saw above to send telemetry data to the Gateway, but we'll get these values from a micro:bit!  I moved this code into a function called `doHTTP` so that I could create multiple post request endpoints, as you'll see in the code below.

### The Finished Project

Here's my finished product, available at [https://www.billmongan.com/variot-test/](https://www.billmongan.com/variot-test/):

<p align="center">
<img style="max-width:100%;" alt="The finished hide and seek project in the Microbit Makecode" src="https://github.com/billjr99/variot-test/raw/master/.github/makecode/blocks.png">
</p>

The plugin code is available at [https://github.com/BillJr99/pxt-ESP8266_VarIOT](https://github.com/BillJr99/pxt-ESP8266_VarIOT) and is shown below:

<p align="center">
<script src="https://gist.github.com/BillJr99/8eb548261f5cd9e2ae521695f099a3a5.js"></script>
</p>