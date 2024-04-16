# switch_with_relay

https://github.com/iot201-2024-KimYunbae/switch_with_relay/assets/164156059/38a5778c-7a14-4931-b8c5-55098fc089e4




```python
# 릴레이 코드
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



# 스위치 코드
from IO7FuPython import Device, ConfiguredDevice
import json
import time
import ComMgr
from machine import Pin

led = Pin(2,Pin.OUT)
sw1 = Pin(19, Pin.IN, Pin.PULL_UP)
sw2 = Pin(23, Pin.IN, Pin.PULL_UP)


def pressed1(p):
    if not p.value():
        switch = "on"
        device.publishEvent('status', json.dumps({"d":{ "switch": switch }}))
        led.on()
        time.sleep(0.2)

def pressed2(p):
    if not p.value():
        switch = "off"      
        device.publishEvent('status', json.dumps({"d":{ "switch": switch }}))
        led.off()
        time.sleep(0.2)
             

nic = ComMgr.startWiFi('io7lux')
device = ConfiguredDevice()
device.setUserCommand(pressed1)
device.setUserCommand(pressed2)
device.connect()

sw1.irq(trigger=Pin.IRQ_FALLING, handler=pressed1)
sw2.irq(trigger=Pin.IRQ_FALLING, handler=pressed2)


try:
    while True:
        pass

except Exception as e:
        print("Error checking message:", e)    



