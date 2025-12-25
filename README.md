# Отчёт по работе с SOPS и Age на Windows

## Цель работы

Целью данной работы является изучение и практическое освоение инструмента **SOPS (Secrets OPerationS)** для безопасного хранения конфиденциальных данных, а также системы шифрования **Age**

## Ход работы:
### Установка утилит:

Для выполнения работы были установлены утилиты **age** и **sops**:
```bash
sudo apt-get update
sudo apt-get install -y age

curl -LO https://github.com/getsops/sops/releases/download/v3.8.1/sops-v3.8.1.linux.amd64
sudo mv sops-v3.8.1.linux.amd64 /usr/local/bin/sops
chmod +x /usr/local/bin/sops
```

Корректность установки проверялась командами:
```
age --version
sops --version
```
<img width="570" height="138" alt="image" src="https://github.com/user-attachments/assets/846ea78e-cf4b-4e6b-8f20-da13f82e6c43" />

### Генерация ключей Age:
Для шифрования данных был сгенерирован ключ Age с помощью команды:

```
age-keygen -o keys.txt
```
<img width="974" height="203" alt="image" src="https://github.com/user-attachments/assets/eedc9c3a-7485-4d2b-9996-f28349d42844" />

В результате был создан файл `keys.txt`, содержащий приватный ключ, а также выведен публичный ключ, который используется для шифрования данных.

### Настройка конфигурации SOPS: 

В корне проекта был создан файл конфигурации `.sops.yaml`, содержащий правила шифрования:

```
creation_rules:
  - path_regex: 'secrets\\.*\.yaml$'
    age: 'age1...'
```
<img width="974" height="475" alt="image" src="https://github.com/user-attachments/assets/94591228-b434-4654-8b30-cb8c114b9045" />

### Создание незашифрованного файла с секретами:

Был создан файл `secrets/database/my_secret.yaml`, содержащий чувствительные данные (логин и пароль).

<img width="974" height="113" alt="image" src="https://github.com/user-attachments/assets/56f0468d-aca9-4d5e-aaa4-fdabdb5d84fb" />

Шифрование выполнялось командой:
```
sops --encrypt secrets\database.yaml > secrets\database.enc.yaml
```
<img width="974" height="55" alt="image" src="https://github.com/user-attachments/assets/2bca6a8f-fb19-45d5-9174-1a812df40b1f" />

В результате был получен зашифрованный файл `my_secret.enc.yaml`, содержащий зашифрованные значения и служебный блок `sops`.

### Просмотр зашифрованного файла:

<img width="974" height="530" alt="image" src="https://github.com/user-attachments/assets/b65fc1ad-e09c-4e75-b03a-e1141e4a31c3" />

### Расшифровка данных

Для расшифровки файла была предварительно задана переменная окружения, указывающая путь к приватному ключу Age:

```
$env:SOPS_AGE_KEY_FILE = "$PWD\keys.txt"
```

После этого расшифровка выполнялась командой:

```
sops --decrypt secrets/database/my_secret.enc.yaml
```
<img width="974" height="121" alt="image" src="https://github.com/user-attachments/assets/6b31fa91-9c35-4c36-809b-7c738a92911a" />

Данные были успешно восстановлены в исходном виде.

### Редактирование зашифрованных файлов

<img width="630" height="275" alt="image" src="https://github.com/user-attachments/assets/fb601061-ef05-4f52-afaa-1f1b335811b6" />

### Вывод

В ходе выполнения работы был успешно освоен инструмент SOPS совместно с Age для безопасного хранения конфиденциальных данных.
