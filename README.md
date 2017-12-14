# SIM800L_MQTT Management of devices on the GSM modem SIM800L and Arduino using the MQTT protocol

Тестовая передача показаний с датчиков в топики

TOPIC `C5/ds0` - Температура 1

TOPIC `C5/ds1` - Температура 2

TOPIC `C5/vbat`- Напряжение

TOPIC `C5/uptime` - Время работы

с интервалом в 60 секунд.

Настройки брокера в скетче, смотрите в своем личном кабинете

`const char MQTT_user[10] = "drive2ru";`      User

`const char MQTT_pass[15] = "martinhol221";`  Password   

`const char MQTT_CID[15] = "CITROEN";`        имя устройства, придумать

`String MQTT_SERVER = "m23.cloudmqtt.com";`  Server

`String PORT = "10077";`                  


# Работа скетча

## Connection to MCTT Broker

`AT+SAPBR=3,1, "Contype","GPRS"` - connection setup, response `OK`

`AT+SAPBR=3,1, "APN","internet.mts.by"` - setting the access point of the cellular operator, the answer `OK` 

`AT+SAPBR=1,1` - setting up GPRS connection, reply `OK` 

`AT+SAPBR=2,1` - connection status request, response `+SAPBR: 1,1,"100.78.6.XXX"`

`AT+CIPSTART="TCP","m23.cloudmqtt.com",\"10077"` - connection to the broker, the answer `CONNECT OK`

`AT+CIPSEND` - data transfer command

> authorization package `MQTT_CON()` 

> publishing package `MQTT_PUB()`

> subscription package `MQTT_SUB()`

`1A` - end byte

## Состав пакета авторизации (MQTT_CON) 

`10 2D 00 06 4D 51 49 73 64 70 03 C2 00 3C 00 07 43 49 54 52 4F 45 4E 00 08 64 72 69 76 65 32 72 75 00 0C 6D 61 72 74 69 6E 68 6F 6C 32 32 31 `


`10` authorization package token

`2D` - total length of the entire authentication package 2D (HEX)  = 42 (DEC) byte, from the next byte to the end

`00` - constantly

`06` - protocol length 06 (HEX)  = 6 (DEC) byte

`4D 51 49 73 64 70` -the type of the protocol itself, ASCII  `MQIsdp`

`03 C2 00 3C 00` - constantly

`07` - device name length 07 (HEX)  = 7 (DEC)  byte

`43 49 54 52 4F 45 4E` - само имя устройства , оно же в ASCII  `CITROEN`

`00`  - constantly

`08` - length of login 08 (HEX)  = 8 (DEC)  byte

`64 72 69 76 65 32 72 75` - the login, it's in the ASCII  `drive2ru`

`00`  - constantly

`0C` - password length 0C (HEX)  = 12 (DEC) байт

`6D 61 72 74 69 6E 68 6F 6C 32 32 31`  - the password itself, it's in the ASCII  `martinhol221`

## Состав пакета публикации (MQTT_PUB) 

`30 0F 00 09 43 35 2F 73 74 61 74 75 73 4C 6F 61 64 `

`30` маркер пакета публикации

`0F` - общая длинна всего пакета авторизации 0F (HEX)  = 15 (DEC) байт, от следующего байта и до конца

`00` - постоянно

`09` - длинна топика 09 (HEX)  = 9 (DEC) байт

`43 35 2F 73 74 61 74 75 73` - сам топик, он же в ASCII  `C5/status`

`4C 6F 61 64` - сообщение в топике, оно же в ASCII  `Load`

## Состав пакета подписки (MQTT_SUB) 

`82 0E 00 01 00 09 43 35 2F 63 6F 6D 61 6E 64 00 `

`82` маркер пакета подписки

`0E` - общая длинна всего пакета авторизации 0E (HEX)  = 14 (DEC) байт, от следующего байта и до конца

`00 01 00` - постоянно

`09` - длинна топика 09 (HEX)  = 9 (DEC) байт

`43 35 2F 63 6F 6D 61 6E 64` - имя топика на которое подписывается устройство, в ASCII  `C5/comand`

`00` - постоянно

# Отправка данных раз в 60 секунд

`41 54 2B 43 49 50 53 45 4E 44 0D 0A    30 0D 00 06 43 35 2F 64 73 30 31 32 2E 33 30   30 0D 00 06 43 35 2F 64 73 31 31 35 2E 34 35   30 0D 00 07 43 35 2F 76 62 61 74 30 2E 30 30   30 0C 00 09 43 35 2F 75 70 74 69 6D 65 39   1A`

`41 54 2B 43 49 50 53 45 4E 44 0D 0A` - это `AT+CIPSEND` 

`30 0D 00 06 43 35 2F 64 73 30 31 32 2E 33 30` - публикуем  `C5/ds012.30` - 12.30 градусов в топик C5/ds0

`30 0D 00 06 43 35 2F 64 73 31 31 35 2E 34 35` - публикуем  `C5/ds115.45` - 15.45 градусов в топик C5/ds1

`30 0D 00 07 43 35 2F 76 62 61 74 30 2E 30 30` - публикуем  `C5/vbat12.10` - 12.10 вольт в топик C5/vbat

`30 0C 00 09 43 35 2F 75 70 74 69 6D 65 39 ` - публикуем  `C5/uptime9` - 9 сек в топик C5/uptime

`1A` - байт окончания передачи данных



Консоль для отладки и наглядного просмотра трафика идущего от устройства

![](https://github.com/martinhol221/SIM800L_MQTT/blob/master/other/mqtt-4.jpg)

Загружаем приложение [MQTT Dash (IoT, Smart Home)](https://play.google.com/store/apps/details?id=net.routix.mqttdash&hl=ru)

Вводим сервер, порт, логин и пароль полученный выше. Добовляем элемент типа "Текст":

*  Имя "любое"

* Топик `C5/ds0`

* шрифт крупный

![](https://github.com/martinhol221/SIM800L_MQTT/blob/master/other/mqtt-6.jpg)

и т д, получится так.

![](https://github.com/martinhol221/SIM800L_MQTT/blob/master/other/mqtt-5.jpg)

Расход трафика с постоянно открытой сессией, 10.5 кб/час х 24 часа х 30 дней = 7.6 Мб в месяц  

![](https://github.com/martinhol221/SIM800L_MQTT/blob/master/other/mqtttrafic.JPG)

Регистрация у броккера cloudmqtt.com и получение настроек

![](https://github.com/martinhol221/SIM800L_MQTT/blob/master/other/mqtt-3.jpg)

