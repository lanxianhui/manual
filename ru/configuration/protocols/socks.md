# Socks

[![Английский](../../resources/english.svg)](https://www.v2ray.com/en/configuration/protocols/socks.html) [![Китайский](../../resources/chinese.svg)](https://www.v2ray.com/chapter_02/protocols/socks.html) [![Немецкий](../../resources/german.svg)](https://www.v2ray.com/de/configuration/protocols/socks.html) [![Русский](../../resources/russian.svg)](https://www.v2ray.com/ru/configuration/protocols/socks.html)

Socks - это реализация стандартного протокола SOCKS, совместимого с [ Socks 4 ](http://ftp.icm.edu.pl/packages/socks/socks4/SOCKS4.protocol), Socks 4а и [ Socks 5 ](http://ftp.icm.edu.pl/packages/socks/socks4/SOCKS4.protocol).

* Наименование: socks
* Тип: входящий / исходящий

## Конфигурация прокси для исходящего соединения

```javascript
{
  "servers": [{
    "address": "127.0.0.1",
    "port": 1234,
    "users": [
      {
        "user": "test user",
        "pass": "test pass",
        "level": 0
      }
    ]
  }]
}
```

Где:

* `servers`: Список socks серверов, в котором каждая запись это: 
  * `address`: Адрес сервера
  * `port`: Порт сервера
  * `users`: Список учетных записей пользователей: 
    * `user`: Логин
    * `pass`: Пароль
    * ` userLevel `: Пользовательский уровень.

Замечание:

* Если список пользователей не пустой, то socks будет использовать случайного пользователя для подключения к сервера.
* Поддерживаются только SOCKS5 сервера.

## Конфигурация прокси для входящего соединения

```javascript
{
  "auth": "noauth",
  "accounts": [
    {
      "user": "my-username",
      "pass": "my-password"
    }
  ],
  "udp": false,
  "ip": "127.0.0.1",
  "userLevel": 0
}
```

Где:

* `auth`: Метод аутентификации socks. Значение по умолчанию: `noauth`. Возможные варианты: 
  * `noauth`: Анонимная аутентификация
  * `password`: С использованием логина и пароля [RFC 1929](https://tools.ietf.org/html/rfc1929)
* `accounts`: Массив, в котором каждая запись содержит ` user` для логина и ` pass ` для пароля. Значения по умолчанию пустые. 
  * Используется только когда в значении `auth` используется `password`.
* `udp`: `true` для включения и `false` для выключения UDP. Значение по умолчанию: false.
* `ip`: Если UDP включен, этот IP адрес принимает пакеты UDP от клиента. Значение по умолчанию: `127.0.0.1`.
* ` userLevel `: Пользовательский уровень. Все подключения проходят через этот уровень.