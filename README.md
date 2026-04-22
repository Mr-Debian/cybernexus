CyberNexus
Универсальный портативный сервер на Orange Pi с веб-интерфейсом в хакерском стиле.

Работает от любого USB-питания (powerbank, ноутбук, зарядка). Автоматически переключается между Wi-Fi сетями, создаёт свою точку доступа, если знакомых сетей нет.

Возможности
Личные файлы — у каждого пользователя своё хранилище с лимитом

Общие файлы — публичное хранилище для всех

Чат — общение в реальном времени

Веб-терминал — полноценный bash прямо в браузере

Wi-Fi менеджер — сканирование, подключение, сохранение сетей

Установщик проектов — загрузка и запуск Python/Node.js проектов через веб

Telegram бот — привязка аккаунта, доступ к общим файлам

Админ-панель — управление пользователями и лимитами

Адаптивный дизайн — работает на ПК, планшете и телефоне

Быстрый старт
Установка Armbian на Orange Pi Zero 2W:

Скачай образ с официального сайта armbian.com, запиши на SD-карту через Rufus или Balena Etcher, вставь в Orange Pi и включи питание.

После загрузки подключись по SSH или используй монитор:

Логин: root
Пароль: 1234

Система попросит сменить пароль и создать обычного пользователя.

Установка CyberNexus:

sudo apt update && sudo apt upgrade -y
sudo apt install -y python3 python3-pip python3-venv git screen sqlite3 wireless-tools wpasupplicant avahi-daemon

cd /opt
sudo git clone https://github.com/yourusername/cybernexus.git
sudo chown -R $USER:$USER /opt/cybernexus
cd /opt/cybernexus

python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt

sudo mkdir -p /srv/cybernexus/{users,common,projects}
sudo chown -R $USER:$USER /srv/cybernexus

python app.py

Открой браузер и перейди по адресу:

http://orangepizero2w.local:5000

Или по IP:

http://192.168.1.100:5000

Логин: admin
Пароль: mr13

Автозапуск
Создай файл службы:

sudo tee /etc/systemd/system/cybernexus.service << 'EOF'
[Unit]
Description=CyberNexus Server
After=network.target

[Service]
Type=simple
User=orangepi
WorkingDirectory=/opt/cybernexus
Environment="PATH=/opt/cybernexus/venv/bin"
ExecStart=/opt/cybernexus/venv/bin/python /opt/cybernexus/app.py
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
sudo systemctl enable cybernexus
sudo systemctl start cybernexus

Проверить статус:

sudo systemctl status cybernexus

Структура проекта
/opt/cybernexus/
├── app.py # Основной код
├── cybernexus.db # База данных SQLite
├── requirements.txt # Python-зависимости
├── venv/ # Виртуальное окружение
└── templates/ # HTML шаблоны
├── login.html
├── register.html
├── dashboard.html
├── files.html
├── common.html
├── chat.html
├── projects.html
├── admin.html
├── admin-wifi.html
├── terminal.html
└── installer.html

/srv/cybernexus/
├── users/ # Личные файлы пользователей
├── common/ # Общие файлы
└── projects/ # Установленные проекты

Telegram бот
Бот интегрирован с CyberNexus и позволяет:

Привязать Telegram аккаунт к аккаунту CyberNexus

Просматривать общие файлы

Загружать файлы в общее хранилище

Получать уведомления о статусе системы

Команды бота:

/start — Главное меню
/help — Список команд
/link ЛОГИН ПАРОЛЬ — Привязать аккаунт
/unlink — Отвязать аккаунт
/files — Общие файлы
/stats — Статистика системы

Для работы бота нужен токен от BotFather. Создай файл .env в папке с ботом:

BOT_TOKEN=ваш_токен_бота
ADMIN_ID=ваш_telegram_id
CYBERNEXUS_URL=http://127.0.0.1:5000

Установщик проектов
Позволяет загружать и запускать проекты через веб-интерфейс.

Поддерживаемые форматы:

.py — Python-файл

.zip — Архив с проектом

.txt — Конспект проекта

Формат TXT-конспекта:

ФАЙЛЫ
main.py:
print("Hello World!")

ЗАВИСИМОСТИ
pip: flask requests

МЕТАДАННЫЕ
type: web
port: 8080
start_command: cd ~ && python3 main.py

После установки проект появляется в разделе «Мои проекты», где можно:

Запустить / Остановить / Перезапустить

Смотреть логи

Обновить

Открыть веб-интерфейс (если указан порт)

Wi-Fi менеджер
Позволяет управлять Wi-Fi подключениями:

Сканировать доступные сети с уровнем сигнала

Подключаться к сети с паролем

Сохранять известные сети

Видеть текущее подключение

Если Orange Pi не находит знакомых сетей, он создаёт свою точку доступа:

SSID: CyberNexus_AP
Пароль: cybernexus123
IP: 192.168.4.1

Подключись к ней и открой http://192.168.4.1:5000

Системные требования
Orange Pi Zero 2W (или любой Orange Pi с Armbian)
USB Wi-Fi адаптер (рекомендуются на чипах Mediatek MT7612U или Ralink RT5572)
SD-карта от 8 ГБ
Питание 5V/2A через Micro-USB

Устранение проблем
Не работает Wi-Fi сканирование:

sudo apt install -y wireless-tools wpasupplicant
sudo systemctl restart wpa_supplicant

Не создаётся точка доступа:

sudo apt install -y hostapd dnsmasq
sudo systemctl unmask hostapd
sudo systemctl start hostapd

Не работает mDNS (orangepizero2w.local):

sudo apt install -y avahi-daemon
sudo systemctl enable avahi-daemon
sudo systemctl start avahi-daemon

На Windows может потребоваться установка Bonjour или включение mDNS в реестре.

Лицензия
MIT License — делай что хочешь, сохраняй копирайт.

Автор
Создано для собственных нужд и сообщества Orange Pi.

Вопросы и предложения — в Issues на GitHub.

проект временно преостановлен(ищю замену orange pi очень устал от возни с драйверами)
