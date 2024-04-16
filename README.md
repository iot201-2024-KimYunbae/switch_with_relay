# switch_with_relay

https://github.com/iot201-2024-KimYunbae/switch_with_relay/assets/164156059/38a5778c-7a14-4931-b8c5-55098fc089e4




```python
from IO7FuPython import Device, ConfiguredDevice
import json
import time
import ComMgr
from machine import Pin

led = Pin(2, Pin.OUT)
relay = Pin(23, Pin.OUT)
lamp = {""}

def value(lamp):
    if lamp == "on":
        led.on()
        relay.on()
    elif lamp == "off":
        led.off()
        relay.off()

def ctrl(topic, msg):
    global lamp, lastPub
    #message = json.loads(str(msg,'utf8'))
    message = json.loads(msg)
    if ("lamp" in message['d']):
        lamp = message['d']['lamp']
        print(lamp)
        value(lamp)
        lastPub = - device.meta['pubInterval']
        

nic = ComMgr.startWiFi('io7device01')
device = ConfiguredDevice()
device.setUserCommand(ctrl)
device.connect()

lastPub = time.ticks_ms() - device.meta['pubInterval']
value(lamp)

while True:
    device.loop()
    if (time.ticks_ms() - device.meta['pubInterval']) > lastPub:
        lastPub = time.ticks_ms()
        device.publishEvent('status', json.dumps({'d':{'lamp': lamp}}))




ㅜ
