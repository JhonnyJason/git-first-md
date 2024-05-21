# ESP32 LED Controller

ESP32 has PWM capabilities. 
We may go up to 16bit duty cycle resolution.
However to get higher frequencies we might sacrifice resolution.
We might want to go for 10bit duty cycle resolution. And get 20khz frequency, which should offer perfectly smooth PWM.

However our target LED strip requires 24V. So we need to switch it via our ESP32 over a MOSFET.

MIC4427 as Mosfet Gate driver
https://www.microchip.com/en-us/product/mic4427

Any reasonable alternatives?


IRF 5305 as Mosfet  = P-Channel
https://www.reichelt.de/mosfet-p-kanal-55-v-31-a-rds-on-0-06-ohm-to-220ab-irf-5305-p41612.html
IRL 540N as Mosfet = N-Channel
https://www.reichelt.de/mosfet-n-kanal-100-v-36-a-rds-on-0-044-ohm-to-220ab-irl-540n-p41761.html


Do we need p-channel or n-channel Mosfet?
Which one is most reasonable?


What other parts do we need?