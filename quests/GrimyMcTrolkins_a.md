Stand next to Frost Tower (as WLAN has only a certain range)
Use the WiFi Dongle (Items section)

Scan for open WLANs

```bash
elf@0fef79d62d9a:~$ iwlist wlan0 scan
wlan0     Scan completed :
          Cell 01 - Address: 02:4A:46:68:69:21
                    Frequency:5.2 GHz (Channel 40)
                    Quality=48/70  Signal level=-62 dBm  
                    Encryption key:off
                    Bit Rates:400 Mb/s
                    ESSID:"FROST-Nidus-Setup"
```

Connect to that WLAN

```bash
elf@0fef79d62d9a:~$ iwconfig wlan0 essid FROST-Nidus-Setup
** New network connection to Nidus Thermostat detected! Visit http://nidus-setup:8080/ to complete setup
(The setup is compatible with the 'curl' utility)
```

Trying to access the Thermostat via curl

```bash
elf@0fef79d62d9a:~$ curl http://nidus-setup:8080
elf@0fef79d62d9a:~$ curl http://nidus-setup:8080/apidoc
```

The API Doc show how to set the temperature via POST and sending a JSON payload

```bash
elf@0fef79d62d9a:~$ curl -XPOST -H 'Content-Type: application/json'   --data-binary '{"temperature": 10}'   http://nidus-setup:8080/api/cooler
{
  "temperature": 10.19,
  "humidity": 14.17,
  "wind": 21.49,
  "windchill": 7.48,
  "WARNING": "ICE MELT DETECTED!"
}
```
