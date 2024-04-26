---
title: nodemcu micropython
date: 2024-03-08 15:08:32
tags: 
 - esp8266
---

### 刷写firmware到开发板
``` bash
esptool.py --port /dev/cu.usbserial-0001 erase_flash

esptool.py --port /dev/cu.usbserial-0001 --baud 115200 write_flash --flash_size=detect -fm dout 0 PY_ESP8266_GENERIC-20240222-v1.22.2.bin

