# Smart Home DIY ideas for Linux Enthusiasts

1. Homebridge. Control your smart devices through Siri/Apple Home, even if devices never supported Apple HomeKit.
2. Raspberry Pi weather probes. Log and graph the temperature/humidity data from your RPi Zero-W probes.
3. Your tesla log. Scrape the vehicle status via Tesla APIs, to see the trends over time.
4. Network probes. Test your network connectivity through home and to the cloud, Wi-Fi or wired. 
5. Make all the data available remotely. Bring up a VM in AWS or GCP, replicate your influx data there, expose it over https via reverse proxy and cloudflare.

## Hardware Platform

### Raspberry pi 3b plus. 

This one plays a server role in my home. It runs debian + influx + Grafana. Also doubles as a probe, but that is a secondary function. 
I did not get top fancy with this one, just got a [KanaKit 3b plus from Amazon](https://www.amazon.com/gp/product/B07BC7BMHY/). 
Got a 512Gb micro-SD card for it as well, data tends to use up the disk space. 

Does not have to be 3b plus, you can go with something more powerful, but I did not want my hardware to have forced cooling (I am with Steve Jobs there, coolers ruin everything). 

### Raspbery Pi Zero W. 

This is my basic network and weather probe. I have got ine running outside on the porch, in  my attic, and in several rooms around the house. 
I got the board with the headers (fancy!) from AdaFruit. It is better if the pins are already soldered to the board, I am not the master solderer (not patient enough). 

As for the case, for some of my PIs aI went with the basic case - it does not have to be too fancy in the attic (can;t see it there anyway). 
For other, I went with a combo of [iUniker USB Dongle](https://www.amazon.com/gp/product/B07NKNBZYG/) for the easy power and fancy LED lights, and transparent [Zebra Zero case](https://www.amazon.com/gp/product/B01F9SHIG6/). Looks beautiful, especially in the night. I kinda wish I used that one in the attic too, the lights are really cool - even if I see them once a year. Do not do this for your bedroom sensor though - the lights are bright, and you cannot control them programmatically. 

For the power supply, I actually used several USB bricks that I had as a leftover from various devices over the years, Pi Zero is not very picky when it comes to power, and USB power supply will do. I recommend Apple iPhone bricks, the quality is superb - but if you need a 90-degree twist so you PiZero stand upright, you will have to pick some other vendor. 

### temp sensors

I went with DHT-22 sensors, as they seem to provide a good balance between cost and quality. 

## Software platform

- I use vanilla Debian Linux across the board, and I manage it with Ansible. You do not have to do the Ansible - it was purely my preference. 
- I use influxdb, Grafana and envoy for my servers. 
- SSL certificates are provided by LetsEncrypt (free). I prefer to avoid the SSL CA warning, and I am frugal, so using LetsEnctypt was a logical idea. 
- I had to purchase a domain name to make LetsEncrypt work. I've got one in .ml TLD fro free from https://my.freenom.com/. Again, frugality. 
- I used cloudflare for my DNS hosting and frontend/CDN (agin, free). Did I need a CDN? Maybe not, but Cloudflare provides so may cool services for free. It was very hard to pass on. 

## Public cloud.

If you are planning to check on status of all your sensors when you are away, you probably want to get yourself an instance in the cloud (I really do not recommend publishing ports on your home router, this si s a very bad security practice). 

The overall set up is:
- Get a free tier instance eiother in AWS or in GCP. I recommnd GCP, as it does not expire. 
- Bring up influx, grafana, envoy|nginx. 
  - influx to keep a replica of the data you collect at home. 
  - grafana to visualize it
  - envoy or nginx as a reverse proxy
- publish a subset of data from your local influx instance to the cloud
   - push replication is secure, you are not opening your home fiorewall for this
   - you do need a local instance of influx so you can continue collecting data even if public internet connectivity is down

## Application.
- I user a bunch of python scripts to grab the data from various sources, and dump them into influxdb.
- I use IRTT for network tests. It is awesome.
- I am slowly replacing all the custom python scripts with Telegraf external modules. Why? Because I use Telegraf for monitoring anyway, so why not consolidate all the probes into one platform. Pugins created so far:
  - https://github.com/iAnatoly/telegraf-input-dht_sensor
  - https://github.com/iAnatoly/telegraf-input-net_irtt




