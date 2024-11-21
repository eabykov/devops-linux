# Задания по Linux (все файлы для выполнения заданий находятся в данном репозитории) 

### Перед началом выполнить комманды по очереди:

Для того чтобы при выполении команды `sudo ...` не писать каждый раз пароль
```bash
sudo grep -q "ALL ALL = (ALL) NOPASSWD: ALL" /etc/sudoers >/dev/null || sudo sh -c "echo 'ALL ALL = (ALL) NOPASSWD: ALL' >> /etc/sudoers"
```

Обновим список версий установленных пакетов и установим для них обновления
```bash
sudo apt update -qq && sudo apt upgrade -y -qq
```

Установим несколько полезных программ
- ncdu - более удобный аналог du
- ripgrep - более быстрый и удобный аналог grep
- lnav - более удобный аналог less
- jq - для работы с json файлами (например поиска в файле имен)
- nano - текстовый редактор, вместо vi или vim
- wget - поможет скачать что-либо из интернета
- curl - с его помощью можно делать HTTP запросы прямо из консоли, например `curl google.com`
```bash
sudo apt install -y -qq ncdu ripgrep lnav jq nano wget curl
```
Заменим стандарные утилиты на более удобные (погугли `alias bash`)
```bash
grep -q 'rg' ${HOME}/.bash_aliases || echo -e "alias grep='rg'" >> ${HOME}/.bash_aliases
grep -q 'ncdu' ${HOME}/.bash_aliases || echo -e "alias du='ncdu'" >> ${HOME}/.bash_aliases
grep -q 'lnav' ${HOME}/.bash_aliases || echo -e "alias less='lnav'" >> ${HOME}/.bash_aliases
```

<details>
  <summary>Все действия выше одним скриптом</summary>

```bash
#!/bin/bash

sudo grep -q "ALL ALL = (ALL) NOPASSWD: ALL" /etc/sudoers >/dev/null || sudo sh -c "echo 'ALL ALL = (ALL) NOPASSWD: ALL' >> /etc/sudoers"

sudo apt update -qq && sudo apt upgrade -y -qq

sudo apt install -y -qq ncdu ripgrep lnav jq nano wget curl

grep -q 'rg' ${HOME}/.bash_aliases || echo -e "alias grep='rg'" >> ${HOME}/.bash_aliases
grep -q 'ncdu' ${HOME}/.bash_aliases || echo -e "alias du='ncdu'" >> ${HOME}/.bash_aliases
grep -q 'lnav' ${HOME}/.bash_aliases || echo -e "alias less='lnav'" >> ${HOME}/.bash_aliases
```

</details>

### Первое задание

1. Перейди в `/tmp` директорию
2. Вернитесь обратно в директорию в которой были первоначально
3. Перейди в домашнюю директорию пользователя
4. Перейди в вышестоящую директорию
5. Перейди в корень (что такое корень?)
6. Выполни команду `cd ~`, куда вы попали?
7. Выведи на экран полный путь к директории где ты сейчас находишься

<details>
  <summary>Решение первого задания</summary>

```sh
#!/bin/sh

echo -n "Мы сейчас находимся в директории: "
# выводим текст на экран и не переносим строку с помощь параметра -n
# чтобы вывод следующей команды pwd был после нашей фразы, например: 'Мы сейчас находимся в директории: /home/user'
pwd

echo -n "1. Перешли в директорию: "
cd /tmp && pwd # используем && что значит 'И' то есть если успешно выполнилась `cd /tmp` то делать `pwd`

echo -n "2. Вернулись в директорию: "
cd -

echo -n "3. Перешли в директорию: "
cd && pwd

echo -n "4. Перешли в директорию: "
cd .. && pwd

echo -n "5. Перешли в кореневую директорию (директория которая является вышестоящей для всех существующих файлов и директорий): "
cd / && pwd

echo -n "6-7. Перешли в домашнюю директорию: "
cd ~ && pwd
```

</details>

### Второе задание

1. Создай два файла с именами `config` и `binary`
2. Создай директорию `app`
3. Перемести файлы `config` и `binary` в директорию `app`
4. Создай в директории `app` подпапки `temp/info/users` одной командой
5. Вернись на директорию выше и удали директорию `app`

<details>
  <summary>Решение второго задания</summary>

1. `touch config binary`, `touch ./config ./binary`, `touch config` вместе с `touch binary`
2. `mkdir app` или `mkdir ./app`
3. `mv config app/` вместе с `mv binary app/` и проверить что это так с помощью `ls -lah`
4. `cd app` и `touch config.overwrite`
5. `mkdir -p temp/info/users`
6. `cd ..` и `rm -rfv app`

</details>

### Третье задание

1. Создай пустой файл `file.txt` в директории `/tmp`
2. Скопируй `file.txt` файл в свою домашнюю директорию
3. Создай копию файла `file.txt` которая будет называться `txt.file`
4. Создай директорию `directory` и перемести в нее файлы `file.txt` и `txt.file`
5. Создай копию директории `directory` которая будет называться `next_directory`
6. Переименуй `next_directory` в `next`
7. Удали обе директории которые создал и все файлы одной командой

<details>
  <summary>Решение третьего задания</summary>

1. `cd /tmp` и `touch file.txt`
2. `cp -a file.txt ~/` или `cp -a file.txt $HOME/`
3. `cp -a file.txt txt.file`
4. `mkdir directory` и потом `mv *.* directory` (осторожно, если есть еще файлы с точкой то скопирует и их)
5. `cp -a ./directory ./next_directory`
6. `mv next_directory next`
7. `rm -rfv next directory`

</details>

### Четвертое задание

В директори есть файл в котором написан список IP адресов, вычисли какой из них чаще всего повторяется (использовать файл `ip_list`

<details>
  <summary>Решение четвертого задания</summary>

1. `cut -d' ' -f1` выведем только IP адреса, без всего лишнего
2. `sort -n` отсортируем их тем самым сгрупировав одинаковые
3. `uniq -c` выведем количество одинаковых повторений IP
4. `cut -d' ' -f1 ip_list | sort -n | uniq -c` совместим все три команды передавая вывод одной в другую через `|`

</details>

### Пятое задание

Вывести только время когда была установлена последняя программа (не запись в `man-db`). История установки программ хранится в файле `/var/log/dpkg.log`

<details>
  <summary>Решение пятого задания</summary>

1. Перейдем в директорию с файлом `cd /var/log/`
2. Посмотрим содержимое файла `less /var/log/dpkg.log` в котором можно делать поиск нажав `/` и введя слово, например `installed`
3. `grep 'installed' dpkg.log | grep -v 'man-db' | tail -1 | cut -d ' ' -f2`
   - `grep 'installed' dpkg.log` - поиск строк с словом installed в файле лога
   - `grep -v 'man-db'` - убрать строки с обновлением man-db во всех страках с installed
   - `tail -1` - вывести 1 последнюю строку
   - `cut -d ' ' -f2` - вывести только время (можно еще и дату для удобства `cut -d ' ' -f1-2`)

</details>

### Шестое задание

1. Отсортировать `names.txt` в алфавитном порядке и убрать повторения
2. Записать отсортированный список имен в файл `names_sorted.txt`
3. Дописать в конец файла `names_sorted.txt` имена с файла `names_new.txt`

<details>
  <summary>Решение шестого задания</summary>

1. `sort | uniq`
   - `sort` сортируем в алфавитном порядке
   - `uniq` склеивает повторяющиеся строки в одну
2. `sort | uniq > names_sorted.txt`
   - `>` перезаписывает содержимое файла `names_sorted.txt` выводом команды `uniq`
3. `cat names_new.txt >> names_sorted.txt`
   - `>>` дописывает в конец файла `names_sorted.txt` вывод команды cat (содержимое файла `names_new.txt`)

</details>

### Восьмое задание

1. Вывести все файлы из папки `/etc` в которых есть имя вашего пользователя
2. Не выводить ошибки при поиске

<details>
  <summary>Решение восьмого задания</summary>

1. `grep -i 'Aider' /etc/*`
   - `/etc/*` поиск по всем файлам в папке `/etc`
   - `-i` означает не учитывать регистр (найдет: aider, Aider, AIDER, aIDEr, и тд)
2. `grep -i 'Aider' /etc/* 2>/dev/null`
   - `2>/dev/null` не выводить ошибки

</details>

### Девятое задание

1. Упаковать (сжать) в архив все файлы с именами из седьмого задания
   1. С помощью `tar`
   2. С помощью `zip`
2. Создать папку `unpack` и распаковать в нее содержимое архивов по очереди

<details>
  <summary>Решение девятого задания</summary>

Ответы на все вопросы тут https://losst.pro/arhivatsiya-v-linux советую пользоваться `zip` как самым простым или `tar` как уже установленым в большинстве unix систем (linux)

</details>

### Десятое задание

1. Обьяснить какие данные будут выведены на экран в команде `top`
2. Какой командой можно посмотреть использование оперативной памяти?
   1. В чем разница между `available` и `free` памятью?
3. Что такое память `buff/cache`?
4. Посмотреть сколько места занято на диске `/` и насколько он нагружен

<details>
  <summary>Решение десятого задания</summary>

1. top и htop выводят:
   - load average - мера потребности в ресурсах CPU и на дисковые ресурсы. Например если 1.5 то это значит что нужна была мощность равная 1 vCPU (ядру) и 0.5 ожидание записи на диск, в сумме получилось 1.5 вот тебе и нагрузка на систему. Выводится как три числа за 1, 5 и 15 минут
   - использование оперативной памяти (сколько свободно, сколько всего, сколько занято и тд в mem)
   - список процессов отсортированых по использованию CPU (процессора)
2. free (-m в мегабайтах или -h для людей)
   1. `available` доступная память часть которой может уже использоваться, но ничего страшного если ее "забрать" и использовать при надобности (например кэш или буфер), а `free` это которая вообще сейчас не используется ни для чего
3. Временные данные, кэш, которые могут быть очищены если оперативная память (RAM) будет нужна какому-то приложению. Например кэш нужен чтобы приложения быстрее запускались
4. `ps aux | grep python`
5. `sudo kill -9 PID` где PID это числовой индификатор процесса который мы узнали из прошлой команды (ps aux...)
6. `df -m` (или можно -h) узнать нагрузку на диск можно несколькими способами:
   - `iostat -dx DISK_NAME` где DISK_NAME взято из столбца `Filesystem` первой команды
   - `sudo iotop` - как top, только для дисков

</details>

### Одиннадцатое задание

1. Создать скрипт который:
   1. Может запускать только текущий пользователь
   2. Устанавит через `apt` (только если еще не установлены)
      - `curl` - выполнять http запросы
      - `git` - система для контроля за версиями, может заливать их в публичный репозиторий
      - `jq` - для работы с файлами json чтобы удобно искать в них или отображать

<details>
  <summary>Решение одиннадцатого задания</summary>

1. Создаем скрипт `touch install_apps.sh` и даем права на запуск только текущему пользователю `chmod u+x install_apps.sh`
   1. Сам скрипт:
   ```sh
   #!/bin/bash
   
   sudo apt install -y wget curl git jq
   ```
   2. Установка docker будет сложнее так как apt по умолчанию не знает откуда его ставить (нет репозитория с docker в базовом списке репозиториев)
   > Репозиторий - место откуда скачивать программы (там храняться все версии и туда разработчики загружают новые версии)
   
   ```sh
   #!/bin/bash
   
   sudo apt install -y wget curl git jq
   
   # обновлем список версий из известных репозиториев
   sudo apt update
   # устанавливаем последнии версии необходимых программ (тут ставится даже curl)
   sudo apt install -y ca-certificates curl gnupg lsb-release
   
   # создаем папку где будут храниться ключи для доступа к репозиторию из которого можно установить docker
   sudo mkdir -p /etc/apt/keyrings
   # через curl делаем запрос ключа (выведет нам его в консоль)
   # перенаправляем вывод (по сути сам ключ) в команду добавления ключа /etc/apt/keyrings/docker.gpg
   curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
   
   # записываем данные о репозитории которые будет исопльзовать apt в файл /etc/apt/sources.list.d/docker.list
   echo \
   "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
   
   # обновляем список программ из репозиториев (обновит список програм которые можно ставить и из нового репозитория docker)
   sudo apt update
   # устанавливаем последнюю версию docker вместе с утилитами которые он использует
   sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
   
   # добавляем нашего пользователя в группу docker для того чтобы команды docker работали без sudo
   sudo usermod -aG docker $USER
   
   # включаем автозапуск докера при старте компа
   sudo systemctl enable docker.service
   sudo systemctl enable containerd.service
   
   echo "Нужно перезапустить компьютер для начала работы с docker"
   sudo docker --version
   ```

</details>
