# Конфигурация виртуальной машины devops-vm

## 1. Параметры машины
- Виртуальная машина в Parallels Desktop на Apple Silicon (ARM64)
- Объем оперативной памяти: 2 ГБ (2048 МБ)
- Количество ядер процессора: 2
- Объем дискового накопителя: 64 ГБ (динамически расширяемый)
- ОС: Ubuntu Server 24.04 LTS (ARM64)

## 2. Сетевые интерфейсы
- `enp0s5` (Shared Network / NAT): IP выдается по DHCP (используется для доступа в интернет).
- `enp0s6` (Host-Only): IP 10.37.129.3/24 (используется для подключения с Mac и в следующих работах).
- Настройка сети выполнена в `/etc/netplan/50-cloud-init.yaml` (для обоих интерфейсов указан `dhcp4: true`).

## 3. Правило проброса портов
Не используется (в Parallels используется прямое подключение по IP-адресу Host-Only).

## 4. Учётные записи
- `student`: создан при установке, входит в группу sudo. Вход по паролю.
- `devops`: создан вручную, входит в группу sudo. Вход только по SSH-ключу.
- Прямой вход под `root` запрещен (`PermitRootLogin no`).

## 5. Служба SSH
- Порт: 2222
- Изменённые директивы (в `/etc/ssh/sshd_config.d/99-hardening.conf`): `Port 2222`, `PermitRootLogin no`, `PasswordAuthentication no`, `MaxAuthTries 3`, `AllowUsers devops`.
- Также закомментирована строка `PasswordAuthentication yes` в `/etc/ssh/sshd_config.d/50-cloud-init.conf`.

## 6. Правила межсетевого экрана (UFW)
- Политика по умолчанию: `deny (incoming)`, `allow (outgoing)`.
- Разрешённые порты: `2222/tcp` (SSH, с ограничением частоты LIMIT), `80/tcp` (HTTP), `443/tcp` (HTTPS).
- Логирование: `medium`.

## 7. Снимки состояния
- `01-clean-install` — чистая установка Ubuntu.
- `02-keys-configured` — настроен SSH-ключ для пользователя devops.
- `03-ssh-hardened` — изменен порт SSH, отключен вход по паролю и под root, настроен UFW.
