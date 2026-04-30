
# 3x-ui-aio

## Запуск

### 1. Создайте два поддомена:
Например:
- `panel.example.com` - панель 3x-ui 
- `cloud.example.com` - xray/сайт-заглушка

A-записи этих поддоменов должны содержать IP вашего VPS

### 2. Установите Docker

Инструкции по установке Docker: [https://docs.docker.com/engine/install/](https://docs.docker.com/engine/install/)

### 3. Клонируйте репозиторий

```bash
git clone https://github.com/dima-savelev/3x-ui-aio.git && cd 3x-ui-aio
```

### 4. Отредактируйте файл `angie.conf`

#### Вариант 1: автоматически (рекомендуется)

Выполните команду:

```bash
sed -i \
    -e "s|<your_panel_domain>|panel.example.com|g" \
    -e "s|<your_xray_domain>|cloud.example.com|g" \
    angie.conf
```

Не забудьте заменить `panel.example.com` и `cloud.example.com` на свои поддомены.

#### Вариант 2: вручную

Откройте файл `angie.conf` в любом редакторе:

```bash
nano angie.conf
```

И вручную замените:
- `<your_panel_domain>` → на поддомен для панели (например, `panel.example.com`) 
- `<your_xray_domain>` → на поддомен для xray (например, `cloud.example.com`) 

Сохраните изменения после редактирования.

### 5. Запустите Docker Compose

```bash
docker compose up -d
```

### 6. Перейдите в панель

Откройте браузер и перейдите по адресу: [https://panel.example.com](https://panel.example.com)

**Дефолтные данные для авторизации:**  
- Логин: `admin`  
- Пароль: `admin`

> [!CAUTION]
> Обязательно измените дефолтный логин и пароль в разделе **Panel Settings -> Authentication**.

> [!WARNING]
> Для повышения безопасности рекомендуется изменить путь до панели в разделе **Panel Settings -> URI Path**.
> 
> Например, при изменении URI Path на `/my-secret-panel-path/` панель будет доступна только по адресу [https://panel.example.com/my-secret-panel-path/](https://panel.example.com/my-secret-panel-path/)

### 7. Добавьте inbound в панели 3x-ui
inbound уже добавлен, все что остается, изменить некоторый параметры:
- **External Proxy:** cloud.example.com:443 изменить на свой домен
- **SNI:** cloud.example.com изменить на свой домен
- **Short IDs:** сгенерировать short id
- **Public and private key:** сгенерировать публичный и приватный ключи
Также уже создан тестовый клиент, у него можно обновить **id** и **subscription**.

### 8. Подписка
> По желанию можно изменить корневой путь подписки. Он должен быть формата /sub(тут любой ваш текст)/
> Например, /sub-dss-hksdh/
> Для корректной работоспособности подписок 3x-ui, необходимо перейти в **Panel Settings -> Subscription** и настроить `Reverse Proxy URI` следующим образом:

<img width="863" height="69" alt="image" src="https://github.com/user-attachments/assets/d5356e6c-6994-4767-8a8d-a07f64183cf4" />

### 9. WARP
По умолчанию в панели настроен warp и через него идет весь трафик.Это можно изменить, зайдя в Настройки Xray - Исходящие подключения и выставив direct первым. Также можно настроить маршрутизацию зайдя в Расширенный шаблон - Маршрутизация.
Пример маршрутизации (все через warp, за исключением определенных сайтов):
```json
[
  {
    "type": "field",
    "inboundTag": [
      "api"
    ],
    "outboundTag": "api"
  },
  {
    "type": "field",
    "outboundTag": "blocked",
    "ip": [
      "geoip:private"
    ]
  },
  {
    "type": "field",
    "outboundTag": "blocked",
    "protocol": [
      "bittorrent"
    ]
  },
  {
    "type": "field",
    "domain": [
      "geosite:youtube",
      "geosite:meta",
      "geosite:telegram",
      "cp.cloudflare.com",
      "one.one.one.one",
      "speedtest.net"
    ],
    "outboundTag": "direct"
  },
  {
    "type": "field",
    "ip": [
      "91.108.56.0/22",
      "91.108.4.0/22",
      "91.108.8.0/22",
      "91.108.16.0/22",
      "91.108.12.0/22",
      "149.154.160.0/20",
      "91.105.192.0/23",
      "91.108.20.0/22",
      "185.76.151.0/24"
    ],
    "outboundTag": "direct"
  }
]
```
