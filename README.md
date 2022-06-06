
# Xiaomi Mi Robot Vacuum - Domoticz Python plugin

This plugin uses the [Python-miio](https://github.com/rytilahti/python-miio) library.

This plugin only works with the following container: https://github.com/Wizzard72/mirobot-miio-server. Create container before this guide.


## How it works

Plugin provides: Status, Control, Fan Level, Battery, Care status devices

**Status**: show current status in readable layout of switch. Status updates by polls 
(interval) and when you click Control device (for instant status change).

**Control**: for sending commands.

**Fan Level**: for adjusting suction power. (MiHome app related: Quiet=38, Balanced=60, Turbo=77, Max=90)

**Battery**: since ```0.0.4``` as new device

**Care**: since ```0.1.0``` new 5 devices (care status + reset tool)

>Since ```0.1.0``` plugin uses wrapper-server for python-miio lib. It helps to use this plugin in Domoticz without blocking mode.

>Since ```0.1.2``` wrapper-server (MIIO Server) runs outside plugin due limits of domoticz python plugin system.

## Installation

Before installation plugin check the `python3`, `python3-dev`, `pip3` is installed for Domoticz plugin system:

```sudo apt-get install python3 python3-dev python3-pip```

Make sure you have libffi and openssl headers installed, you can do this on Debian-based systems (like Rasperry Pi) with:

```sudo apt-get install libffi-dev libssl-dev```.

### Container
  
  Make sure the container is running
  
  ```docker ps```
  
  Output look like this:
  
  ```
CONTAINER ID   IMAGE                           COMMAND                  CREATED        STATUS        PORTS                                                                                  NAMES
3afb120b7e11   miio-mirobot:latest             "sh -c './miio_serve…"   27 hours ago   Up 27 hours   0.0.0.0:22222->22222/tcp, :::22222->22222/tcp                                          miio-mirobot
```
### Install plugin

  SSH login to Domoticz with Putty for example
  
  ```
  cd domoticz/plugins
  git pull https://github.com/Wizzard72/domoticz-mirobot-plugin
  cd domoticz-mirobot-plugin 
  pip3 install msgpack-python
  sudo systemctl stop domoticz.service
  sudo systemctl start domoticz.service
  ```
  
  Test theconnectivity to the vacuum cleaner
  ```
  chmod +x test.py
  ./test.py
  ```
  
  Output:
  ```
  test: trying connect to 127.0.0.1:22222
  test: sent request to server [status]
  test: reading response...
  test: got server reply {'error': None, 'state_code': 8, 'battery': 100, 'fan_level': 102, 'clean_seconds': 14, 'clean_area': 0.155, 'cmd': 'status'}
```

Also you can run MIIO Server manually and look log output:
```
sudo ./miio_server.py 192.168.1.12 476e6b70343055483230644c53707a12 --host 127.0.0.1 --port 22222

# then you can run test
sudo ./test.py
```

If server and test is ok, time to restart the Domoticz:
```
sudo service domoticz.sh restart
```

Now go to **Setup** -> **Hardware** in your Domoticz interface and add type with name **Xiaomi Mi Robot Vacuum**.

| Field | Information|
| ----- | ---------- |
| Data Timeout | Keep Disabled |
| MIIOServer host:port | by default 127.0.0.1:22222 |
| Update interval | In seconds, this determines with which interval the plugin polls the status of Vacuum. Suggested is no lower then 5 sec due timeout in python-mirobo lib, but you can try any.  |
| Fan Level Type | ```Standard``` - standard set of buttons (values supported by MiHome); ```Slider``` - allow to set custom values, up to 100 (in standard Max=90) (values not supported by MiHome) |
| Debug | When set to true the plugin shows additional information in the Domoticz log |

After clicking on the Add button the new devices are available in **Setup** -> **Devices**.

If you want to change ```Fan Level Type``` just disable hardware, update type and enable again. Old device can be deleted manually in Devices menu.

## How to update plugin

```
cd domoticz/plugins/xiaomi-mirobot
git pull
```

Restart the Domoticz service
```
sudo service domoticz.sh restart
```

## Screenshots

![status_unit](https://user-images.githubusercontent.com/93999/29568433-0da95692-8759-11e7-8706-344c02536d6a.png)
![control_unit](https://user-images.githubusercontent.com/93999/29568435-13645e10-8759-11e7-92d8-5fe130912c78.png)

![fan_level](https://user-images.githubusercontent.com/93999/29668575-6906ea22-88e9-11e7-8508-8f0ff48e2f78.png)
![fan_level2](https://user-images.githubusercontent.com/93999/29713051-86cd023c-89a5-11e7-83cc-5953b8cbbfa5.png)

![care1](https://user-images.githubusercontent.com/93999/32418537-08d3c918-c27d-11e7-89e9-10daf79bcdb4.png)
![care2](https://user-images.githubusercontent.com/93999/32418538-08ef7e10-c27d-11e7-9ff8-8dfff1c20377.png)

![bat](https://user-images.githubusercontent.com/93999/29769383-c8202814-8bf2-11e7-86b2-3629bfc63dc0.png)


### How to obtain device Token

Check the [instruction](https://github.com/rytilahti/python-miio#finding-the-token)
