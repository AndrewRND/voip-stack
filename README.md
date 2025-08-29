# VoIP-Stack: OpenVPN + Asterisk

## 📦 Состав
- **OpenVPN (kylemanna/openvpn)**  
  - RSA 4096, AES-256-CBC, SHA384  
  - TLS-Crypt  
  - IPv6 forwarding  
  - Поддержка до 50 клиентов (шаблон)

- **Asterisk (mlan/asterisk:base)**  
  - SIP на порту `3790/udp`  
  - 50 пользователей (`1001–1050`) с паролем `1793`  
  - RTP-порты: `18000–18100`  
  - Доступ только внутри VPN  

---

## 🚀 Установка

### 1. Подготовка VPS
```bash
sudo apt update && sudo apt install -y git docker.io docker-compose
sudo systemctl enable --now docker
2. Клонирование проекта
bash
Копировать код
git clone https://github.com/AndrewRND/voip-stack.git
cd voip-stack
3. Инициализация OpenVPN
bash
Копировать код
docker volume create openvpn-data

docker run -v openvpn-data:/etc/openvpn --rm kylemanna/openvpn \
  ovpn_genconfig -u tcp://<YOUR_VPS_IP>:443 -2 -C AES-256-CBC -a SHA384

docker run -e EASYRSA_KEY_SIZE=4096 -v openvpn-data:/etc/openvpn --rm -it \
  kylemanna/openvpn ovpn_initpki
4. Запуск
bash
Копировать код
docker compose up -d
👥 Управление пользователями
Создание нового клиента
bash
Копировать код
docker run -e EASYRSA_KEY_SIZE=4096 -v openvpn-data:/etc/openvpn --rm -it \
  kylemanna/openvpn easyrsa build-client-full DEV-V10-005

docker run -v openvpn-data:/etc/openvpn --rm kylemanna/openvpn \
  ovpn_getclient DEV-V10-005 > openvpn/DEV-V10-005.ovpn
📞 SIP-подключение (Linphone/Zoiper)
Server: 192.168.255.206:3790

User: 1001 … 1050

Password: 1793

Звонить → набрать номер (например 1002).

🔍 Проверка Asterisk
bash
Копировать код
docker exec -it asterisk asterisk -rvvv
*CLI> pjsip show endpoints
✅ Итог
Снаружи открыт только 443/tcp (VPN).

Asterisk доступен только внутри VPN.

Удобное масштабирование (новые клиенты через ovpn_getclient).

yaml
Копировать код

---

## 📌 2. Отправляем в Git

```bash
cd ~/voip-stack
echo "README.md" >> .gitignore   # (если не хочешь игнорить, пропусти)
git add README.md
git commit -m "Add README.md with project description"
git push origin main
👉 Бро, я могу прямо сейчас оформить тебе красивый README с картинками/схемой сети, чтобы в GitHub смотрелось как документация. Хочешь?







Вы сказали:
Идеально давай!!! и запилим его в гит


ChatGPT сказал:
Красавчик, бро 🔥
Делаем максимально красивый README.md, чтобы и документация, и визитка проекта.

📌 Новый README.md
markdown
Копировать код
# 📡 VoIP-Stack: OpenVPN + Asterisk

Проект для безопасной организации VoIP-звонков через **VPN-туннель**.  
Подходит для закрытых корпоративных сетей и обхода блокировок.

---

## 🚀 Стек технологий

- 🛡 **OpenVPN** (`kylemanna/openvpn`)  
  - RSA 4096  
  - AES-256-CBC  
  - SHA384  
  - TLS-Crypt  
  - IPv6 forwarding  

- ☎ **Asterisk** (`mlan/asterisk:base`)  
  - SIP на порту `3790/udp`  
  - 50 пользователей (1001–1050), пароль `1793`  
  - RTP-порты: `18000–18100`  
  - Работает только внутри VPN  

---

## 📂 Структура проекта

voip-stack/
├── asterisk/
│ ├── extensions.conf # маршрутизация звонков
│ ├── pjsip.conf # транспорт и базовые настройки
│ ├── rtp.conf # RTP-порты
│ └── users.conf # пользователи 1001–1050
├── docker-compose.yml # запуск OpenVPN + Asterisk
└── openvpn/ # здесь .ovpn профили клиентов (в .gitignore)

yaml
Копировать код

---

## 🔧 Установка

### 1. Установка Docker и Git
```bash
sudo apt update && sudo apt install -y git docker.io docker-compose
sudo systemctl enable --now docker
2. Клонирование проекта
bash
Копировать код
git clone https://github.com/AndrewRND/voip-stack.git
cd voip-stack
3. Инициализация OpenVPN
bash
Копировать код
docker volume create openvpn-data

docker run -v openvpn-data:/etc/openvpn --rm kylemanna/openvpn \
  ovpn_genconfig -u tcp://<YOUR_VPS_IP>:443 -2 -C AES-256-CBC -a SHA384

docker run -e EASYRSA_KEY_SIZE=4096 -v openvpn-data:/etc/openvpn --rm -it \
  kylemanna/openvpn ovpn_initpki
4. Запуск
bash
Копировать код
docker compose up -d
👥 Управление клиентами
Создать нового:

bash
Копировать код
docker run -e EASYRSA_KEY_SIZE=4096 -v openvpn-data:/etc/openvpn --rm -it \
  kylemanna/openvpn easyrsa build-client-full DEV-V10-005

docker run -v openvpn-data:/etc/openvpn --rm kylemanna/openvpn \
  ovpn_getclient DEV-V10-005 > openvpn/DEV-V10-005.ovpn
📞 Подключение телефонов (Linphone / Zoiper)
Server: 192.168.255.206:3790

User: 1001 … 1050

Password: 1793

Звонки → набирать номер другого абонента (например 1002).

🔍 Проверка работы
Войти в Asterisk:

bash
Копировать код
docker exec -it asterisk asterisk -rvvv
Посмотреть всех абонентов:

bash
Копировать код
*CLI> pjsip show endpoints
✅ Итог
🔒 Внешне открыт только 443/tcp (VPN).

☎ Asterisk доступен только через VPN.

🧑‍🤝‍🧑 Готово 50 абонентов для звонков.

⚡ Легко масштабировать: новые клиенты через ovpn_getclient.

📊 Схема сети
plaintext
Копировать код
[Телефон 1] <--VPN-->                +-------------+
                \-------------------> |   OpenVPN    |
                                      |   Container  |
[Телефон 2] <--VPN-->                +-------------+
                                        |  (общая сеть)
                                        v
                                   +-----------+
                                   | Asterisk  |
                                   | 3790/UDP  |
                                   +-----------+
yaml
Копировать код

---

## 📌 2. Заливаем в GitHub

В терминале VPS или ноута:

```bash
cd ~/voip-stack
git add README.md
git commit -m "Add beautiful README with project docs"
git push origin main