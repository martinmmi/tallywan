
![20230606_194919_bbbb](https://github.com/martinmmi/redtally_docs/assets/118231543/8ef63eed-c346-4ca2-a95e-5beae4328457)

# Welcome to official redtally support

## Introduction
Here you will find my official support for redtally. I develooped it durring and after my work at the east-german television crew durring my masters.
Its a small device where you can transmit tallys over long range via loraWAN-Technology. The Interfaces of this device are supported by relay closing contact and via TSL. For TSL, unconfortly supported controllers are the ross carbonite series. I planed to implement more interfaces, if the demand is there. 

> [!NOTE]
> The following name recipients is also named as tally or receiver. Its the same meaning. The basestation is also named transmitter. 


## Applications und Use-cases for the device
If you have a situation, where the production depends from a wireless connectivity, that solution is a perfect compromises between availability, independence and technical accuracy. A usesample is to use the redtally solution for a undended drone operator. He can move freely without having to worry about a cableconnection.

![image](https://github.com/martinmmi/redtally_docs/assets/118231543/e6170ac3-c57e-4a25-af0b-8f77c0a12d58)

## Product specification
Here are listed the main product specifications:

### Basestation
- one basestation with wireless functionality
- loraWAN 868 MHz transmission
- ethernet connectivity for access to system and IP-TSL-functionality
- poe with the norm IEEE 802.3af
- display with informations for the network access
- micro-usb and usb-c port for upate the device or power it
- SMA-Port for an antenna
- four xlr closing contacts for using it without IP-functionality
  
### Recipients
- max. four recipients at the same time
- completely wireless over loraWAN 868 MHz 
- possible to connect an other light on the xlr-port (worked as an relay-levler on pin 2 and 3)
- power-switch and reset-button
- 800 mAh Accumulator
- display for the remaing accumulator capacity, the signal strength, connection mode and address
- 1/4 Zoll thread for connectivty to a camera or an stativ
- micro-usb for upate the device or charge it
- SMA-Port for an antenna
- one WS2812 neopixel leds for the directly view of that tally
- multiple inputs at the same time are supported (for example in the mix mode from a switcher)

## Hardware
For the project i used two different microcontroller for it. The LilyGO-T-ETH-POE on the left site for the basestation and the LilyGO-LORA32 V2.1 one the right site for the tally-recipints. 

![image](https://github.com/martinmmi/redtally_docs/assets/118231543/43e292a9-aa7e-4fd7-8a1d-74053a3c38d3)

You can turn it on with a switch on the left site. The reset button is only to clear the EEPROM / the lora-transmission settings. For everything i designed und printed a 3d-case for a perfect use. 

![image](https://github.com/martinmmi/redtally_docs/assets/118231543/3beaa7b6-c6aa-4b22-b656-8bfe4a978812)

## Software
For a maximum useage I implement few different functions on a webserver. You can access to them via the ip-adress on the display of the basestation. Make sure that the local ip-parameters in your network fit with the redtally solution. If there are in the same network, you have the access to follow functions:
- overview of connected tallys
- input mode of gpio or tsl
- tsl assigment of channels
- energy save mode
- discover time
- lora transmission values
- user credentials
- network operation mode (dhcp or manual)
- static ip configuration
- wlan and wlan credentials
- tsl application port
- tsl-udp diagramm support
- post authenification via sha-keying

### Communication Pattern
For all of the functionality and wireless communication it was nessesary to build an communication frame for it. Its represented by an byte frame, with is sended every time when an package is sended. This developed frame is listed here, 
but is of no further importance for the actualuse:

![267080081-c72ac58c-95e3-4d46-8063-0e43385acab2](https://github.com/martinmmi/redtally_docs/assets/118231543/03d81ffc-9cf1-4a04-aafb-fa7df5ba01d2)

At the next is a diagram, which shows you different communication modes:

![image](https://github.com/martinmmi/redtally_docs/assets/118231543/eb5f3abf-4db6-4825-ad8c-441ccd8c2efd)

The first, when everything is just turned on, the basestation sends an **discover-message** via broadcast to every recipient. The recipients responds the base station with an **offer-message** one after the other via unicast. Its nessary to connect minimum one recipient. Otherwiese the state will be not finished. Once a tally is connected, you still have 2 minutes to connect another one. This time increases again and again until eventually all four are connected. That time can be changed in the settings of base station. Is everythink connected, the basestation change after the discover-time automaticly to the **request-mode**. From here are only unicast sended by the basestation and the recipients. An Requests will be sended by the basestation, when the input-information are coming in. That message is responed by the recipient by an **acknowledge-message**. Durring the process, its importan to control the recipients that are already online over the time. For this the basestation will send an **control-message** to every recipients in an interval after 5-10 minutes. Thats also responed with an **acknowledge-message** by the recipient. If the recipient is not responding after two trys, maybe in case of the accumulator is empty or the recipient is out of range, the basestation will turn the respective tally as offline. The recipient will show it also with the yellow constant led state.

### Input-Modes
The are two modes availble. One with an *gpio close contact* (named GPIO in the system), which is provided as an voltage-devider. 0V will be turn the tally of and 3,3V will it turn on. The Input pins on the XLR-ports are pin 2 (+) and 3 (-). The other more advanced mode is the *tsl ip mode* (named TSL in the system). You transmit the tallydata via and TCP/UDP-package from your image mixer to the redtally basestation. 

![image](https://github.com/martinmmi/redtally_docs/assets/118231543/41909d93-b045-4c93-a177-2fe2b04aeaa6)

> [!NOTE]
> Here is a list by supported products for the TSL Mode at that time:
- ross carbonite series (TSL v3.1 via UPD)

I planed more interfaces depends from the demand:
- acuity
- vmix
- tricaster

### Led-States
You have different colors and states on the led from the recipient:

| Color | Frequency | Description |
| --- | --- | --- |
| red | constant | Shows you which tally is active in program |
| green | constant | Shows you which tally is active in preview |
| yellow | constant | The tally lost the connection and wait for auto-reconnection |
| yellow | slowly blinking | The tally is ready to connect with the basestation |
| yellow | fast blinking | The tally is in the registration process |
| off | constant | The tally is fully connected without any problems |

### TSL-Assigment of channels
Under ```Configuration```, ```More TSL-Configurations``` you will find an assignment for the tallys. Every tally has an unique id (for example bb). With this function you can assign tallys for a different input-channel (for example tally bb for input 6). You can assign there the input-channels of the tsl program-bus and preview-bus. Until now its only possible to use the channels until 8.
![image](https://github.com/martinmmi/redtally_docs/assets/118231543/ded70df8-c8b7-4349-8718-f8ccdc4a8c74)

### tsl application port
*coming soon*

### tsl-udp diagramm support
*coming soon*

### Energy Save Mode
Under ```Configuration``` you will find the energy save functionhe tallys. For this i turn of the display and put the recipients into a deep-sleep until shortly the next control message is comming. After them, its starts to sleep again. I implemented the function to save energy during an interruption or break. It receives also **request-messages** durring the deep-sleep. I dont know why, but i think it didnt turn off the loraMODEM.
> [!IMPORTANT]
> Please use this function as an unstable function.
![image](https://github.com/martinmmi/redtally_docs/assets/118231543/ded70df8-c8b7-4349-8718-f8ccdc4a8c74)

### Discover Time
Under ```Configuration``` you will find the discover time field. Durring the discovering of recipients, is that the time which is renewed when a recipient is founded after it starts to go to the request-mode.
![image](https://github.com/martinmmi/redtally_docs/assets/118231543/fc0bb483-7d84-481f-a44e-7e32ce8fd799)

### lora transmission values
*coming soon*

### user credentials
*coming soon*

### network operation mode (dhcp or manual)
*coming soon*

### static ip configuration
*coming soon*

### wlan and wlan credentials
*coming soon*

### post authenification via sha-keying
*coming soon*

## Throubleshooting
_______________________________________________________________________________________________________________________________
*The device didnt starts.* 

Are you shure that the accumulator is already charged and the power-switch is turned on? If noting works, you can put on an micro-usb-cable to charge the tally. You can control the charaing controller, if you open cautiou the lid from the tally. On the controller is an blue led and an red led on, if the controller is actually charing. The red led displays that the accumulator loads successfully. If the tally still doesn't start, contact me and send it to service.
_______________________________________________________________________________________________________________________________
*Durring the GPIO-Mode more than one tally are turned on after turing on only on tally* 

> [!WARNING]
> There is actually an problem by the hardware site with the shared ground-mass. The current from one input flows back over the other inputs, and that the reason because you can have that problem.
_______________________________________________________________________________________________________________________________
*I can no longer reach the base station after an incorrect manual IP configuration* 

> [!WARNING]
> Until now there is now way to clear the eeprom manualy with an hardware-button. You can clean it with a software tool or send it back to me for an service.
_______________________________________________________________________________________________________________________________
*Can it used via VPN?* 

Its possible to use the system via VPN. The Data stream is completely encapsulated by the VPN-client. I tested it from germany to the european foreign countries successful.
_______________________________________________________________________________________________________________________________



