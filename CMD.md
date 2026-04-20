### 🌐 1. Сеть и диагностика

|Команда|Назначение|Пример использования|
|---|---|---|
|`ipconfig /all`|Полная сетевая конфигурация (IP, шлюз, DNS, DHCP-сервер, MAC)|`ipconfig /all \| findstr "IPv4 DNS DHCP"`|
|`ipconfig /flushdns`|Очистка кэша DNS-клиента|`ipconfig /flushdns`|
|`ipconfig /release` & `/renew`|Освободить/получить IP от DHCP|`ipconfig /release` → `ipconfig /renew`|
|`ping`|Проверка доступности узла|`ping -t 10.0.0.1` (непрерывно), `ping -4 google.com`|
|`tracert`|Трассировка маршрута до узла|`tracert dc.corp.local`|
|`pathping`|Комбинация ping + tracert с потерей пакетов по узлам|`pathping 8.8.8.8` (ждите 1-2 мин)|
|`nslookup`|Проверка DNS-записей и работа с конкретным DNS-сервером|`nslookup`, затем `server 8.8.8.8` → `corp.local`|
|`netstat -ano`|Активные TCP/UDP-соединения + PID процессов|`netstat -ano \| findstr :443`|
|`arp -a`|Таблица ARP (сопоставление IP ↔ MAC в локальной сети)|`arp -a \| findstr 192.168.1`|
|`route print`|Таблица маршрутизации ОС|`route print`|
### 🔐 2. Домен, AD и Group Policy

|Команда|Назначение|Пример|
|---|---|---|
|`gpupdate /force`|Принудительное обновление групповых политик|`gpupdate /force`|
|`gpresult /r`|Текстовый отчёт о применённых GPO для текущего пользователя/ПК|`gpresult /r`|
|`gpresult /h report.html`|Детальный HTML-отчёт по политикам **(требует запуска от администратора для компьютерных политик)**|`gpresult /h C:\gpo.html`|
|`klist`|Просмотр/очистка Kerberos-билетов|`klist`, `klist purge`|
|`nltest`|Диагностика доверия домена, поиск DC, проверка репликации|`nltest /dsgetdc:corp.local`, `nltest /sc_query:corp.local`|
|`net user /domain`|Информация о пользователях домена|`net user ivan.petrov /domain`|
|`net group "Domain Admins" /domain`|Состав глобальной группы домена|`net group "Domain Admins" /domain`|
|`net localgroup "Administrators"`|Локальные администраторы текущей машины|`net localgroup Administrators`|
|`whoami /all`|Текущий SID, группы, привилегии, тип аутентификации|`whoami /all`|

---

### 💻 3. Система, процессы и службы

|Команда|Назначение|Пример|
|---|---|---|
|`systeminfo`|Полная сводка: ОС, железо, установленные обновления, время загрузки|`systeminfo \| findstr /B /C:"OS Name" /C:"System Boot Time"`|
|`tasklist`|Список запущенных процессов|`tasklist \| findstr svchost`|
|`taskkill`|Завершение процесса|`taskkill /PID 1234 /F`, `taskkill /IM notepad.exe /F`|
|`sc query`|Статус служб Windows|`sc query wuauserv`, `sc query type= service state= running`|
|`sc start / stop / config`|Управление службами|`sc stop "Spooler"`, `sc config "Spooler" start= disabled`|
|`shutdown`|Перезагрузка/выключение локально или удалённо|`shutdown /r /f /t 0`, `shutdown /m \\PC01 /r /t 30 /c "Обслуживание"`|

---

### 📁 4. Файлы, диски и целостность системы

|Команда|Назначение|Пример|
|---|---|---|
|`robocopy`|Надёжное копирование с логами, повторами, зеркальным режимом|`robocopy C:\data D:\backup /MIR /R:3 /W:5 /LOG:C:\robocopy.log`|
|`sfc /scannow`|Проверка и восстановление защищённых системных файлов|`sfc /scannow`|
|`dism`|Восстановление образа Windows (требует интернета/источника)|`dism /online /cleanup-image /restorehealth`|
|`chkdsk`|Проверка файловой системы на ошибки|`chkdsk C: /f /r` (требует перезагрузки)|
|`attrib`|Просмотр/изменение атрибутов файлов (скрытый, системный, только чтение)|`attrib +h +s C:\secret.txt`|

---

### 🛠️ Полезные приёмы работы в `cmd`

|Приём|Синтаксис|Пояснение|
|---|---|---|
|Справка по команде|`команда /?` или `help команда`|Встроенная документация|
|Запуск от администратора|`Win+X` → Терминал (администратор) или `Ctrl+Shift+Enter`|Многие команды требуют повышенных прав|
|Перенаправление вывода|`> файл.txt`|Перезаписать файл|
|Добавление в файл|`>> файл.txt`|Дописать в конец|
|Ошибки + вывод в один файл|`команда > out.txt 2>&1`|Логируются и стандартный вывод, и ошибки|
|Фильтрация строк|`\| findstr "текст"` или `\| find /i "текст"`|Регистронезависимый поиск|

---

### ⚠️ Важное примечание: `cmd` vs PowerShell

`cmd` всё ещё используется в legacy-скриптах, средах восстановления и для быстрых проверок. **Но современный стандарт администрирования Windows — PowerShell.**

|Задача|`cmd`|PowerShell (современно)|
|---|---|---|
|Сетевая информация|`ipconfig /all`|`Get-NetAdapter`, `Get-NetIPAddress`|
|Ping/Traceroute|`ping`, `tracert`|`Test-Connection`, `Test-NetConnection`|
|GPO отчёт|`gpresult /h`|`Get-GPResultantSetOfPolicy`, `Get-GPOReport`|
|Пользователи AD|`net user /domain`|`Get-ADUser -Filter *`|
|Процессы|`tasklist`|`Get-Process`|

💡 **Рекомендация:** Используйте `cmd` для быстрой диагностики и старых скриптов, но для автоматизации и глубокого анализа переходите на PowerShell.
