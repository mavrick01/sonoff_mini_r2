# Sonoff Mini R2 - Tasmota Flashing

<h1> Tasmita Config </h1>
{"NAME":"Sonoff MINIR2","GPIO":[17,0,0,0,9,0,0,0,21,56,0,0,0],"FLAG":0,"BASE”:1}
backlog template WebPassword <passwd>; FriendlyName mini2; MqttClient mini2; hostname mini2; Topic mini2; MqttHost 192.168.<Y>.<X>;  MqttUser admin ;  MqttPassword <passwd> ;  Timezone 99;  TimeDst 1,1,10,1,2,660;  TimeStd 1,1,4,1,3,600

// This is the config for Melbourne Timezone
  
  <h2> Flashing with DIY Mode </h2>
  <b> Connect to your wifi </b>
Press Button for 8-10secs, till flashing blue
Find SSD ITEAD….Password 12345678
Connect to 10.10.7.1
Enter local SSID and Password
  <b> flash with tasmota image </b>
OSX CLI enter
SONOFF_IP=<X.X.X.X> //IP Address on the network
HASH = <ZZZZZ> // on http://http://sonoff-ota.aelius.com/ lookup the SHA256 hash for tasmota-latest-lite.bin  - Note you do not want to load minimal as this is the first flash, but it cannot take a full Tasmota. If you are wanting to work it out locally use “shasum -a 256 <filename>” 
Check OTA Lock Status: curl -XPOST --header "Content-Type: application/json" --data-raw '{"deviceid": "", "data": {}}' http://$SONOFF_IP:8081/zeroconf/info
Get something like this back:
“{"seq":3,"error":0,"data":{"switch":"off","startup":"off","pulse":"off","pulseWidth":500,"ssid”:”missed”,”otaUnlock":false,"fwVersion":"3.6.0","deviceid":"10011c0c7d","bssid":"70:3a:cb:95:ec:e1”,”signalStrength":-55}}”
Unlock OTA : curl -XPOST --header "Content-Type: application/json" --data-raw '{"deviceid": "", "data": {}}' http://$SONOFF_IP:8081/zeroconf/ota_unlock
Get something like this back: {"seq":3,"error":0}
Confirm OTA Lock Status: curl -XPOST --header "Content-Type: application/json" --data-raw '{"deviceid": "", "data": {}}' http://$SONOFF_IP:8081/zeroconf/info
Get something like this back:
“{"seq”:4,”error":0,"data":{"switch":"off","startup":"off","pulse":"off","pulseWidth":500,"ssid”:”missed”,”otaUnlock”:true,”fwVersion":"3.6.0","deviceid":"10011c0c7d","bssid":"70:3a:cb:95:ec:e1”,”signalStrength":-55}}”
Do the OTA Update : curl -XPOST --data "{\"deviceid\":\"\",\"data\":{\"downloadUrl\": \"http://sonoff-ota.aelius.com/tasmota-latest-lite.bin\", \"sha256sum\": \"$HASH\"} }" http://$SONOFF_IP:8081/zeroconf/ota_flash
// You can change the URL, just remember the hash must be correct.
Get something like this back:
“{"seq”:4,”error”:0}
// The secret is the error 0
NOW WAIT!!!
  
  <b> Connect to Tasmota </b>
It will reboot when done (flashing light). DO NOT DO LIKE I DID AND TRY RUN THIS MULTIPLE TIMES - YOU GET A SMALL BRICK
Look for the tasmota…. SSID, then you know you are successful.
Connect to that SSID and connect to 182.168.4.1 and connect it to you local SSID and password
Voila, you are working!!
  
  
