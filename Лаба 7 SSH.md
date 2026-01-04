## 1. Какой порт по умолчанию использует SSH
22/TCP
## 2. Можно ли его изменить?
меняется в конфиг файле ssh сервера
## 3. Какая служба отвечает за SSH-подключения?
Служба sshd 
## 4. Какой файл отвечает за настройку?
/etc/openssh/sshd_config
## 5. Подключаемся
``
```
[milfhntr@host-15 linux]$ ssh student@ternar.io -p 207
The authenticity of host '[ternar.io]:207 ([95.31.204.147]:207)' can't be established.
ED25519 key fingerprint is SHA256:/46rSP5LespYRxmt8Cmhl0cGhWs2Nj54zkIFp+HR4fI.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '[ternar.io]:207' (ED25519) to the list of known hosts.
student@ternar.io's password: 
Last login: Mon Nov 24 10:24:25 2025
```

## 6+7. Разрешаем подключение под рут + меняем колво попыток пароля
тут мы с божьей помощью  ввели PermitRootLogin yes и MaxAuthTries  в /etc/openssh/sshd_config
```
[milfhntr@host-15 linux]$ ssh student@ternar.io -p 207
student@ternar.io's password: 
Last login: Sun Jan  4 09:32:43 2026 from 95.104.180.144
[student@S-vm-207 ~]$ sudo vi /etc/openssh/sshd_config
[student@S-vm-207 ~]$ sudo sshd -t
[student@S-vm-207 ~]$
```

## 8. Создаем ssh-user

```
[student@S-vm-207 ~]$ sudo useradd -m ssh-user
useradd: пользователь «ssh-user» уже существует
[student@S-vm-207 ~]$ sudo passwd ssh-user
passwd: updating all authentication tokens for user ssh-user.

You can now choose the new password or passphrase.

A valid password should be a mix of upper and lower case letters, digits, and
other characters.  You can use a password containing at least 7 characters
from all of these classes, or a password containing at least 8 characters
from just 3 of these 4 classes.
An upper case letter that begins the password and a digit that ends it do not
count towards the number of character classes used.

A passphrase should be of at least 3 words, 11 to 72 characters long, and
contain enough different characters.

Alternatively, if no one else can see your terminal now, you can pick this as
your password: "couch$sage7fever".

Enter new password: 
Re-type new password: 
passwd: all authentication tokens updated successfully.
[student@S-vm-207 ~]$ ssh ssh-user@ternar.io -p 207
The authenticity of host '[ternar.io]:207 ([95.31.204.147]:207)' can't be established.
ED25519 key fingerprint is SHA256:/46rSP5LespYRxmt8Cmhl0cGhWs2Nj54zkIFp+HR4fI.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '[ternar.io]:207' (ED25519) to the list of known hosts.
ssh-user@ternar.io's password: 
[ssh-user@S-vm-207 ~]$
```

## 9+10. Ограничиваем бедолагу
в конфиге прописываем
`` DenyUsers ssh-user

## 11. Что хранится в файле know_hosts?

публичные ключи SSH-серверов к которым раньше подключался пользователь


## Конфигурируем


## 12.  Где хранятся пользовательские и системные настройки SSH-подключений?
~/.ssh/config
системые: /etc/openssh/ssh_config

## 13. Что за файл options?
Файл options — это пользовательский конфигурационный файл SSH, который может использоваться как альтернативное имя для файла настроек.

## 14+15+16. Отредактируйте файл options (config), чтобы можно было подключаться без ввода имени пользователя и порта

назначаем правильные права
chmod 600 ~/.ssh/config
```
ssh-user@ternar.io's password: 
[ssh-user@S-vm-207 ~]$ ssh: client_loop: send disconnect: Broken pipe
[milfhntr@host-15 linux]$ ssh student@ternar.io -p 207
student@ternar.io's password: 
ssh: Permission denied, please try again.
student@ternar.io's password: 
Last login: Sun Jan  4 09:52:27 2026 from 95.104.180.144
[student@S-vm-207 ~]$ vi ~/.ssh/config
[student@S-vm-207 ~]$ nano ~/.ssh/config
-bash: nano: команда не найдена
[student@S-vm-207 ~]$ sudo apt-get install nano
Чтение списков пакетов... Завершено
Построение дерева зависимостей... Завершено
Следующие НОВЫЕ пакеты будут установлены:
  nano
0 будет обновлено, 1 новых установлено, 0 пакетов будет удалено и 0 не будет обновлено.
Необходимо получить 607kB архивов.
После распаковки потребуется дополнительно 2664kB дискового пространства.
Получено: 1 http://ftp.altlinux.org Sisyphus/x86_64/classic nano 8.0-alt1:sisyphus+351044.100.1.1@1718541713 [607kB]
Получено 607kB за 0s (2481kB/s).
Совершаем изменения...
Подготовка...                           ############################### [100%]
Обновление / установка...
1: nano-8.0-alt1                        ############################### [100%]
y
Завершено.
[student@S-vm-207 ~]$ nano ~/.ssh/config
[student@S-vm-207 ~]$ chmod 600 ~/.ssh/config
[student@S-vm-207 ~]$ ssh lab207
student@ternar.io's password: 
Last login: Sun Jan  4 10:33:05 2026 from 95.104.180.144
```
## Ключики
## 1. ssh
SSH-ключи — это способ аутентификации в SSH без использования пароля.
## 2. Как создать SSH-ключи? 
Ключи создаются командой ssh-keygen.

## 3. Создаем ключи
ssh-keygen -t ed25519
- **Приватный ключ**:  ~/.ssh/id_ed25519
- Публичный: ~/.ssh/id_ed25519.pub
## 4.  Копирование ключа на сервер

ssh-copy-id -p 207 student@ternar.io
~/.ssh/authorized_keys
## 5. Подключение по ssh
пароль не запросило 
## 6. Запрещаем подключение с паролем для всех
sudo nano /etc/openssh/sshd_config
PasswordAuthentication no
PubkeyAuthentication yes
