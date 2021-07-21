# Sonoff Mini R2 - Tasmota Flashing

## Tasmita Config
```
{"NAME":"Sonoff MINIR2","GPIO":[17,0,0,0,9,0,0,0,21,56,0,0,0],"FLAG":0,"BASE”:1}`
backlog template WebPassword <passwd>; FriendlyName mini2; MqttClient mini2; hostname mini2; Topic mini2; MqttHost 192.168.<Y>.<X>;  MqttUser admin ;  MqttPassword <passwd> ;  Timezone 99;  TimeDst 1,1,10,1,2,660;  TimeStd 1,1,4,1,3,600
```

- This is the tasmota config for Melbourne Timezones
  
## Flashing with DIY Mode
**Connect to your wifi**
- Press Button for 8-10secs, till flashing blue
- Find SSD ITEAD…. Wifi Password 12345678
- Connect to 10.10.7.1
- Enter local SSID and Wifi Password

**Flash with tasmota image**

Using OSX CLI enter
- `SONOFF_IP=<X.X.X.X>` //IP Address on the network
- `HASH = <ZZZZZ>` // on http://http://sonoff-ota.aelius.com/ lookup the SHA256 hash for tasmota-latest-lite.bin  
  - Note you do not want to load the minimal image, as this is the 1st time flashing. 
  - Due to the OTA limit, you cannot use the full Tasmo taimage, hencing using the lite version. 
  - If you are wanting to work the hash out locally, use “shasum -a 256 <filename>” on OSX

  **Check OTA Lock Status** 
  
  `curl -XPOST --header "Content-Type: application/json" --data-raw '{"deviceid": "", "data": {}}' http://$SONOFF_IP:8081/zeroconf/info`

  Response:
`{"seq":3,"error":0,"data":{"switch":"off","startup":"off","pulse":"off","pulseWidth":500,"ssid”:”missed”,”otaUnlock":false,"fwVersion":"3.6.0","deviceid":"10011c0c7d","bssid":"70:3a:cb:95:ec:e1”,”signalStrength":-55}}`
  
  - Notice otaUnlock = false. We need to change this.

  **Unlock OTA** 
  
  `curl -XPOST --header "Content-Type: application/json" --data-raw '{"deviceid": "", "data": {}}' http://$SONOFF_IP:8081/zeroconf/ota_unlock`

  Response: `{"seq":3,"error":0}`

  **Confirm OTA Lock Status** 
  
  `curl -XPOST --header "Content-Type: application/json" --data-raw '{"deviceid": "", "data": {}}' http://$SONOFF_IP:8081/zeroconf/info`

  Response:
`{"seq”:4,”error":0,"data":{"switch":"off","startup":"off","pulse":"off","pulseWidth":500,"ssid”:”missed”,”otaUnlock”:true,”fwVersion":"3.6.0","deviceid":"10011c0c7d","bssid":"70:3a:cb:95:ec:e1”,”signalStrength":-55}}`

    - Notice otaUnlock = true. This is success.
  
  **Perform the OTA Update**
  
  `curl -XPOST --data "{\"deviceid\":\"\",\"data\":{\"downloadUrl\": \"http://sonoff-ota.aelius.com/tasmota-latest-lite.bin\", \"sha256sum\": \"$HASH\"} }" http://$SONOFF_IP:8081/zeroconf/ota_flash`

  - You can change the URL, just remember the hash must be correct.

  Response:
`{"seq”:4,”error”:0}`

-The secret is the error 0

  ***NOW WAIT!!!***
  
**Connect to Tasmota**
  
- It will reboot when done (flashing light). DO NOT DO LIKE I DID AND TRY RUN THIS MULTIPLE TIMES - YOU GET A SMALL BRICK
- Look for the tasmota…. SSID, then you know you are successful.
- Connect to that SSID
- Open your browser to 182.168.4.1 and enter your local SSID and Wifi password
- **Voila, you are working!!**
  
  
