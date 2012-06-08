# A SleepProxyClient implementation (Wake on Demand)

## About

Wake on Demand (http://support.apple.com/kb/HT3774) is great.
It enables an unused device to go to sleep while keeping it's announced MDNS (Zeroconf) services alive and will be woken up on access again.

These scripts enables your Linux server to save energy by going to sleep if it's currently not in use.
But it will be instantly woken up again by the SleepProxyServer using Wake on Lan (WOL) if one of it's services is requested. See http://en.wikipedia.org/wiki/Bonjour_Sleep_Proxy for more details.

### Requierments
To get this running, a SleepProxyServer on your local network is required. If present, it will announce itself via MDNS as <code>_sleep-proxy._udp</code>. 
Such a server is included in many Apple devices like its network products "Time Capsule" and "AirPort Express". But an Apple TV or any Mac running 10.6 or above can be turned into a sleep proxy server too.


## Setup / Install

This little tool consist of three scripts:

1. spc.py   
	This script sends the actual DNS update request to the sleep proxy server.
	It can be easily used without the other two scripts too.


2. spc.sh   
	Allows to send the update request to all available sleep proxy servers by just specifying the network interface to use as parameter.


3. checkSleep.sh   
	will try to check if the host is currently in use. It will suspend the host after two successfully calls. This script is designed to be periodically called by a cronjob.


### Requirements

 - python
 - dnspython (http://www.dnspython.org)
 - avahi-browse (to find the sleep proxy)
 - pm-utils (pm-suspend is used to suspend the host)
 
 These dependencies can be easy installed on Debian/Ubuntu using the following command:
 <pre>apt-get install python python-dnspython avahi-utils</pre>
 
 In addition it have to be possible to suspend the host by <code>pm-suspend</code> and to awake it by sending a Wake on Lan packet to it.

### Configuration

#### Custom services

Up to now there is no service discovery to find the services to keep alive.
This requires to specify the services to be announced by the sleep proxy server by hand.
This can be done via the services file.
By default the services file contains already two services: ssh and afp.
Custom services can be added easily by specifying at least the service and the corresponding port.
In most cases it's totally sufficient to just specify the service type and port without further txt information.

#### Setup auto-sleep

The checkSleep script needs to be called periodically to suspend the host by it's own.
This can be done by creating a cronjob via crontab:
<pre>*/8 * * * * /bin/bash /path/to/sleepProxyClient/checkSleep.sh</pre>

This causes the checkSleep.sh to be called every 8 minutes. Since two successfully calls are required to suspend the host the time until the last activity and actually suspending will be at least 16 minutes.

#### Tuning some parameters

Some more parameters can be adjusted to fit your needs:

- TTL (Time to live)   
	The TTL controls the life time of the MDNS announcement. After this period the sleep client will be woken up be the sleep proxy server again. The default value is 24h and can be changed within <code>spc.py</code>.

- Device Model   
	The device model to be announced can also be changed. (The small icon besides the servers name within the finder sidebar). It defaults to RackMac an can by changed within <code>spc.py</code> too.

## Support this project

Just support this project by contributing some code, creating issues or just by flattring it - Thanks!.

<a href="http://flattr.com/thing/713748/aweinSleepProxyClient-on-GitHub" target="_blank">
<img src="http://api.flattr.com/button/flattr-badge-large.png" alt="Flattr this" title="Flattr this" border="0" /></a>