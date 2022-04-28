##                        ДЗ к занятию "3.4. Операционные системы (лекция 2)" <br/> <br/> <br/>

**1.** На лекции мы познакомились с node_exporter. В демонстрации его исполняемый файл запускался в background. <br/> 
Этого достаточно для демо, но не для настоящей production-системы, где процессы должны находиться под внешним управлением. <br/> 
Используя знания из лекции по systemd, создайте самостоятельно простой unit-файл для node_exporter:

поместите его в автозагрузку,
предусмотрите возможность добавления опций к запускаемому процессу через внешний файл (посмотрите, например, на systemctl cat cron),
удостоверьтесь, что с помощью systemctl процесс корректно стартует, завершается, а после перезагрузки автоматически поднимается.


с помощью `systemctl` процесс корректно стартует, завершается, а после перезагрузки автоматически поднимается: <br/>

`root@vagrant:~# ps -e |grep node_exporter` <br/>
   `9699 ?`    `00:00:00 node_exporter`   [node_exporter](https://disk.yandex.ru/i/z35ZvFXKGTwsXQ)  [service](https://disk.yandex.ru/i/mrhVXq75VsKs4Q) <br/> <br/>


`vagrant $  systemctl stop node_exporter`  <br/>
`==== AUTHENTICATING FOR org.freedesktop.systemd1.manage-units ===`  <br/>
`Authentication is required to stop 'node_exporter.service'.`  <br/>
`Authenticating as: vagrant`  <br/>
`Password:`  <br/>
`==== AUTHENTICATION COMPLETE ===`     [stop_node](https://disk.yandex.ru/i/cn-JkV1QUZe42A) <br/> <br/> 

 `vagrant $  ps -e |grep node_exporter` <br/>
 `vagrant $  systemctl start node_exporter` <br/>
`==== AUTHENTICATING FOR org.freedesktop.systemd1.manage-units ===` <br/>
`Authentication is required to start 'node_exporter.service'.` <br/>
`Authenticating as: vagrant` <br/>
`Password:` <br/>
`==== AUTHENTICATION COMPLETE ===`   [start_node](https://disk.yandex.ru/i/2kO_NxqepIrffg) <br/> <br/>

 `vagrant $  ps -e |grep node_exporter` <br/>
   `9824 ?        00:00:00 node_exporter` <br/>

***конфигруационный файл:***


  _`# /etc/systemd/system/node_exporter.service` <br/>
 [Unit] <br/>
Description=Node Exporter Service <br/>
After=network.target <br/>
[Service] <br/>
User=nodeusr <br/>
Group=nodeusr <br/>
Type=simple <br/>
ExecStart=/usr/local/bin/node_exporter <br/>
ExecReload=/bin/kill -HUP $MAINPID <br/>
Restart=on-failure <br/>
[Install] <br/>
WantedBy=multi-user.target     [config](https://disk.yandex.ru/i/Alapk4m8y0svvA)  <br/><br/>


***переменная окружения:***

 `vagrant $  sudo cat /proc/9824/environ` <br/>
`LANG=en_US.UTF-8PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/binHOME=/home/nodeusrLOGNAME=nodeusrUSER=nodeusrINVOCATION_ID=4b8d5d3fe0ae45d897c72c0745f0c14fJOURNAL_STREAM=9:132702` <br/><br/>





**2.** Ознакомьтесь с опциями node_exporter и выводом /metrics по-умолчанию. Приведите несколько опций, которые вы бы выбрали для базового мониторинга хоста по CPU, памяти, диску и сети.


**CPU:** <br/>

`node_cpu_seconds_total{cpu="0",mode="idle"} 137695.56` <br/>
`node_cpu_seconds_total{cpu="0",mode="system"} 157.86` <br/>
`node_cpu_seconds_total{cpu="0",mode="user"} 90.15` <br/>

**Memory:** <br/>
`node_memory_MemAvailable_bytes 5.72854272e+08` <br/>
`node_memory_MemFree_bytes 1.384448e+08` <br/>

**Disk:** <br/>
`node_disk_io_time_seconds_total{device="sda"} 720.992` <br/>
`node_disk_read_bytes_total{device="sda"} 1.331976192e+09` <br/>
`node_disk_read_time_seconds_total{device="sda"} 207.744` <br/>
`node_disk_write_time_seconds_total{device="sda"} 1981.4850000000001` <br/>


**Network:** <br/>
`node_network_receive_bytes_total{device="eth0"} 2.95894006e+08` <br/>
`node_network_receive_errs_total{device="eth0"} 0` <br/>
`node_network_transmit_bytes_total{device="eth0"} 7.022474e+06` <br/>
`node_network_transmit_errs_total{device="eth0"} 0` <br/><br/>




**3.** Установите в свою виртуальную машину Netdata. <br/> Воспользуйтесь готовыми пакетами для установки (sudo apt install -y netdata). 
После успешной установки: <br/>
в конфигурационном файле /etc/netdata/netdata.conf в секции [web] замените значение с `localhost` на `bind to = 0.0.0.0`, <br/>
добавьте в Vagrantfile проброс порта Netdata на свой локальный компьютер и сделайте vagrant reload: <br/>
`config.vm.network "forwarded_port", guest: 19999, host: 19999`
После успешной перезагрузки в браузере на своем ПК (не в виртуальной машине) вы должны суметь зайти на `localhost:19999`. <br/> 
Ознакомьтесь с метриками, которые по умолчанию собираются Netdata и с комментариями, которые даны к этим метрикам. <br/>



[netdata_web](https://disk.yandex.ru/i/ZyorlUMzWmpbMg)  <br/><br/>


**4.** Можно ли по выводу dmesg понять, осознает ли ОС, что загружена не на настоящем оборудовании, а на системе виртуализации? <br/>



`vagrant $  dmesg | grep -i virtual` <br/>
`[    0.000000] DMI: innotek GmbH VirtualBox/VirtualBox, BIOS VirtualBox 12/01/2006` <br/>
`[    0.014141] CPU MTRRs all blank - virtualized system.` <br/>
`[    0.107067] Booting paravirtualized kernel on KVM` <br/>
`[    1.199105] Performance Events: PMU not available due to virtualization, using software events only.` <br/>
`[   16.010493] systemd[1]: Detected virtualization oracle.` <br/><br/>




**5.** Как настроен sysctl fs.nr_open на системе по-умолчанию? Узнайте, что означает этот параметр. Какой другой существующий лимит не позволит достичь такого числа (ulimit --help)? <br/>



`vagrant $  sysctl fs.nr_open` <br/>
`fs.nr_open = 1048576` <br/>


 `vagrant $  ulimit -n` <br/>
`1024`

*Параметр означает максимальное число открытых дескрипторов для ядра (системы)*  <br/>
*Число задается кратное 1024, в данном случае* =1024*1024. <br/>


`vagrant $  cat /proc/sys/fs/file-max`
`9223372036854775807`

*максимальный предел ОС.* <br/>


**6.** Запустите любой долгоживущий процесс (не ls, который отработает мгновенно, а, например, sleep 1h) в отдельном неймспейсе процессов; покажите, что ваш процесс работает под PID 1 через nsenter. <br/> Для простоты работайте в данном задании под root (sudo -i). <br/> Под обычным пользователем требуются дополнительные опции (--map-root-user) и т.д. <br/>


**pts/1:** <br/>

`root@vagrant:~# unshare -f --pid --mount-proc sleep 2m` <br/>

**pts/0:** <br/>

`root@vagrant:~# ps -e | grep sleep` <br/>
   `2178 pts/1    00:00:00 sleep` <br/>


`root@vagrant:~# nsenter --target 2202 --mount --uts --ipc --net --pid ps aux` <br/>
`USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND` <br/>
`root           1  0.0  0.0   5476   520 pts/1    S+   18:14   0:00 sleep 2m` <br/>
`root           2  0.0  0.3   8892  3384 pts/0    R+   18:15   0:00 ps aux` <br/><br/>





**7.** Найдите информацию о том, что такое :(){ :|:& };:. Запустите эту команду в своей виртуальной машине Vagrant с Ubuntu 20.04 (это важно, поведение в других ОС не проверялось). <br/> Некоторое время все будет "плохо", после чего (минуты) – ОС должна стабилизироваться. <br/> Вызов dmesg расскажет, какой механизм помог автоматической стабилизации. <br/>  Как настроен этот механизм по-умолчанию, и как изменить число процессов, которое можно создать в сессии? <br/>



`:(){ :|:& };:` <br/>

*эта команда является **fork-bomb**. Она оперирует определением функции с именем ‘:‘, которая вызывает сама себя дважды: один раз на переднем плане и один раз в фоне. Она продолжает своё выполнение снова и снова, пока система не зависнет.* <br/>

`root@vagrant:~# dmesg | grep fork` <br/>
`[ 7997.408346] cgroup: fork rejected by pids controller in /user.slice/user-1000.slice/session-9.scope` <br/>

[fork-bomb](https://disk.yandex.ru/i/L3yhs6h3I1srYw)

<br/><br/><br/>



##                        ДЗ к занятию "3.3. Операционные системы (лекция 1)" <br/> <br/> <br/>


**1.** Какой системный вызов делает команда cd? <br/> 
В прошлом ДЗ мы выяснили, что cd не является самостоятельной программой, это shell builtin, <br/> 
поэтому запустить strace непосредственно на cd не получится. <br/> 
Тем не менее, вы можете запустить strace на /bin/bash -c 'cd /tmp'. <br/> 
В этом случае вы увидите полный список системных вызовов, которые делает сам bash при старте. <br/> 
Вам нужно найти тот единственный, который относится именно к cd. <br/> 
Обратите внимание, что strace выдаёт результат своей работы в поток stderr, а не в stdout.


`chdir("/tmp")` <br/> <br/> 


**2.** Попробуйте использовать команду file на объекты разных типов на файловой системе. <br/> 
Например: <br/>
`vagrant@netology1:~$ file /dev/tty` <br/>
`/dev/tty: character special (5/0)` <br/>
`vagrant@netology1:~$ file /dev/sda` <br/>
`/dev/sda: block special (8/0)` <br/>
`vagrant@netology1:~$ file /bin/bash` <br/>
`/bin/bash: ELF 64-bit LSB shared object, x86-64` <br/>
Используя strace выясните, где находится база данных file на основании которой она делает свои догадки.


**Ищет поьзовательские файлы и файлы БД:** <br/>

`openat(AT_FDCWD, "/etc/magic", O_RDONLY) = 3` <br/>

`stat("/root/.magic.mgc", 0x7ffc52ffe2d0) = -1 ENOENT (No such file or directory)`<br/>
`stat("/root/.magic", 0x7ffc52ffe2d0)    = -1 ENOENT (No such file or directory)`<br/>
`openat(AT_FDCWD, "/etc/magic.mgc", O_RDONLY) = -1 ENOENT (No such file or directory)`<br/>
`stat("/etc/magic", {st_mode=S_IFREG|0644, st_size=111, ...}) = 0`<br/><br/>


**3.** Предположим, приложение пишет лог в текстовый файл. <br/>
Этот файл оказался удален (deleted в lsof), однако возможности сигналом сказать приложению переоткрыть файлы или просто перезапустить приложение – нет. <br/> 
Так как приложение продолжает писать в удаленный файл, место на диске постепенно заканчивается. <br/> 
Основываясь на знаниях о перенаправлении потоков предложите способ обнуления открытого удаленного файла (чтобы освободить место на файловой системе). <br/><br/>


нашёл большущий файл на много гигабайт, удалил его `rm ./testbig.log`

В итоге этот файл всё ещё открыт каким-то процессом и имеется открытый дескриптор этого файла и поэтому файл просто помечен как ***deleted***, но фактически не удалён. <br/>
Нужно Найти тот самый процесс и перезапустить его или закрыть дескриптор файла:

`lsof +L1` <br/>
или <br/>
`lsof | grep deleted`

`COMMAND  PID    USER   FD   TYPE DEVICE SIZE/OFF NLINK NODE NAME` <br/>
`top     2466 vagrant    0u   CHR  12136,1      0t0     0    4 /tmp/testbig.log (deleted)` <br/>

Видим, что процесс с **PID=2466** имеет файловый дескриптор **4** <br/> 
Смотрим информацию по процессу `ps -p 2466` <br/>

Освобождаем место на диске:
убиваем процесс `kill -9 2466` или обнуляем файл  `sudo truncate -s 0 /proc/2466/fd/4 or > /proc/1366/fd/4` <br/> <br/>


**4.** Занимают ли зомби-процессы какие-то ресурсы в ОС (CPU, RAM, IO)? <br/>


Зомби-процессы освобождают все свои ресурсы, но запись в таблице процессов остается. <br/><br/>


**5.** В `iovisor BCC` есть утилита `opensnoop`: <br/>
`root@vagrant:~# dpkg -L bpfcc-tools | grep sbin/opensnoop` <br/>
`/usr/sbin/opensnoop-bpfcc` <br/>

На какие файлы вы увидели вызовы группы open за первую секунду работы утилиты? <br/> 
Воспользуйтесь пакетом bpfcc-tools для Ubuntu 20.04. Дополнительные сведения по установке. <br/>


[opensnoop](https://disk.yandex.ru/i/g-1Yj9jCItbGJg) <br/><br/>


**6.** Какой системный вызов использует uname -a? Приведите цитату из man по этому системному вызову, где описывается альтернативное местоположение в `/proc`, где можно узнать версию ядра и релиз ОС. <br/>


`uname()` <br/> 
`Part of the utsname information is also accessible  via  /proc/sys/kernel/{ostype, hostname, 
osrelease, version, domainname}`. <br/><br/>




**7.** Чем отличается последовательность команд через ; и через && в bash? Например:

`root@netology1:~# test -d /tmp/some_dir; echo Hi` <br/>
`Hi` <br/>
`root@netology1:~# test -d /tmp/some_dir && echo Hi` <br/>
`root@netology1:~#`  <br/><br/>


**;** - позволяет разместить две и более команд в одной и той же строке, выполнение команды происходит последовательно. <br/>
**&&** - Командная оболочка будет интерпретировать последовательность символов && как логический оператор "И". <br/> 
При использовании оператора && вторая команда будет исполняться только в том случае, если исполнение первой команды успешно завершится (будет возвращен нулевой код завершения). <br/>
использование **&&** вместе с **set -e** не имеет смысла, так как при ошибке скрипта, выполнение команд прекратиться. <br/><br/>


**8.** Из каких опций состоит режим bash set -euxo pipefail и почему его хорошо было бы использовать в сценариях? <br/>

 - **e** - прекращает выполнение скрипта если команда завершилась ошибкой, выводит в stderr строку с ошибкой <br/>
 - **u** - прекращает выполнение скрипта, если встретилась несуществующая переменная <br/>
 - **x** - выводит выполняемые команды в stdout перед выполнением <br/>
 - **o** **pipefail** - прекращает выполнение скрипта, даже если одна из частей pipe завершилась ошибкой <br/>

При таком запуске скрипт получается безопасным, происходит автоматическая обработка ошибок. <br/><br/>



**9.** Используя `-o stat` для `ps`, определите, какой наиболее часто встречающийся статус у процессов в системе. <br/>
В `man ps` ознакомьтесь (/PROCESS STATE CODES) что значат дополнительные к основной заглавной буквы статуса процессов. <br/>
Его можно не учитывать при расчете (считать `S`, `Ss` или `Ssl` равнозначными). <br/><br/>



**Ss** - процесс, ожидающий завершения, лидер сессии <br/>
**R+** - процесс выполняется, фоновый процесс <br/>

**s** -   является лидером сессии <br/>
**+** -   находится в группе процессов переднего плана <br/>

**l** - является многопоточным <br/>
**R** - запущенный или доступный для выполнения (в очереди выполнения) <br/><br/><br/><br/>






##               ДЗ к занятию "3.2. Работа в терминале, (лекция 2)" <br/> <br/> <br/>




**1.** Какого типа команда cd? Попробуйте объяснить, почему она именно такого типа; опишите ход своих мыслей, если считаете что она могла бы быть другого типа. <br/> <br/>

`cd` (*это команда смены текущего каталога*) <br/>

Название расшифровывается как **change directory.** <br/>
Это команда оболочки **(sh/bash/...)**, а не отдельная программа или системный вызов. <br/> 
Если сделать ее внешней, то она не будет менять каталог в текущей оболочке. <br/> 
Без аргументов — переход в домашний каталог.<br/>
**cd -** возврат в предыдущий каталог. <br/> <br/> 



**2.** Какая альтернатива без pipe команде grep <some_string> <some_file> | wc -l? man grep поможет в ответе на этот вопрос. Ознакомьтесь с документом о других подобных некорректных вариантах использования pipe.

`grep <some_string> <some_file> -c` <br/> <br/> <br/> 

**3.** Какой процесс с PID 1 является родителем для всех процессов в вашей виртуальной машине Ubuntu 20.04?

`systemd(1)` <br/> 
[systemd(1)](https://disk.yandex.ru/i/G0B-7Kf0xszEUA)  <br/> <br/>


**4.** Как будет выглядеть команда, которая перенаправит вывод stderr ls на другую сессию терминала? <br/>

`pts/0:` <br/>
`vagrant@ Mon Feb $  ls -l \root 2>/dev/pts/1` <br/>
`vagrant@ Mon Feb $` <br/>
[pts0](https://disk.yandex.ru/i/FW1QpJtg_-62qA) <br/> <br/>
`pts/1:` <br/>
`vagrant@ Mon Feb 07 17:01:27 $  ls: cannot access 'root': No such file or directory` <br/>
[pts1](https://disk.yandex.ru/i/i-q8KGLa2k0YRQ) <br/><br/>


**5.** Получится ли одновременно передать команде файл на stdin и вывести ее stdout в другой файл?<br/> Приведите работающий пример. <br/>


`cat < file1.txt > file2.txt` <br/><br/>

**6.** Получится ли вывести находясь в графическом режиме данные из PTY в какой-либо из эмуляторов TTY?<br/> Сможете ли вы наблюдать выводимые данные?<br/>


*Вывести получится при использовании перенаправления вывода:*

`vagrant@ Wed Feb $  tty` <br/>
`/dev/pts/1` <br/>

`vagrant@ Wed Feb $   echo "Hello, World!" > /dev/pts/0` <br/>

`vagrant@ Wed $  tty` <br/>
`/dev/pts/0`<br/>

`vagrant@ Wed $  Hello, World!` <br/>

данные наблюдать не сможем. Чтобы их увидеть надо перейти в эмулятор терминала с помощью Ctrl + Alt + F1..F6 <br/> В моем случает это Ctrl + Alt + F1 <br/><br/>



**7.** Выполните команду bash 5>&1. К чему она приведет? Что будет, если вы выполните echo netology > /proc/$$/fd/5? <br/> Почему так происходит?


`bash 5>&1`  создаст дескриптор c **FD5** и перенаправит его в **stdout** <br/> `echo netology > /proc/$$/fd/5` выведет netology в дескриптор **"5"**, который был пернеаправлен в **stdout.** <br/>
В консоли увидим вывод ***netology*** <br/>
[fd5](https://disk.yandex.ru/i/8kj2OMFPLbtpxw) <br/><br/>

**8.** Получится ли в качестве входного потока для pipe использовать только stderr команды, не потеряв при этом отображение stdout на pty? <br/> Напоминаем: по умолчанию через pipe передается только stdout команды слева от | на stdin команды справа. <br/> Это можно сделать, поменяв стандартные потоки местами через промежуточный новый дескриптор, который вы научились создавать в предыдущем вопросе. <br/>


`ls -l /root 6>&2 2>&1 1>&6 |grep denied -c` <br/> 
1 

`6>&2` - новый дескриптор перенаправили в stderr <br/>
`2>&1` - stderr перенаправили в stdout <br/> 
`1>&6` - stdout - перенаправили в в новый дескриптор <br/> <br/>


**9.** Что выведет команда cat /proc/$$/environ? <br/> Как еще можно получить аналогичный по содержанию вывод?<br/><br/>

`cat /proc/$$/environ` будут выведены переменные окружения <br/> 
[env](https://disk.yandex.ru/i/Rj3XStlgoTQNPw) <br/> 

аналогичный вывод переменных окружения можно получить с помощью команд: <br/> 
`printenv` <br/> 
`env` <br/> <br/> 
так же, при использовании команды **printenv** можно запрашивать значения для отдельных переменных. <br/> 
для просмотра переменных оболочки применяют команду `set` <br/> <br/> 



**10.** Используя man, опишите что доступно по адресам /proc/<PID>/cmdline, /proc/<PID>/exe. <br/> 

`/proc/[pid]/cmdline` <br/> 
этот файл, доступный только для чтения, содержит полный путь до исполняемого файла процесса [PID], если только этот процесс не является зомби.  <br/> В данном примере в этом файле ничего нет, то есть
чтение этого файла вернет **0** символов. <br/>

`/proc/<PID>/exe` <br/>   содержащий фактический путь к выполняемой команде [PID]<br/> попытка открыть его приведет к открытию исполняемого файла. <br/> <br/> 


**11.** Узнайте, какую наиболее старшую версию набора инструкций SSE поддерживает ваш процессор с помощью /proc/cpuinfo.

`grep sse /proc/cpuinfo` <br/> 
`cat /proc/cpuinfo` <br/> 
`SSE 4.2` <br/> <br/> 

**12.** При открытии нового окна терминала и vagrant ssh создается новая сессия и выделяется pty. <br/>  Это можно подтвердить командой tty, которая упоминалась в лекции 3.2. <br/> Однако: <br/>
vagrant@netology1:~$ ssh localhost 'tty' <br/>
not a tty <br/>
Почитайте, почему так происходит, и как изменить поведение. <br/><br/>
**shell** при входе на удаленный хост предполагает что что соединение выполняется пользователем, вводящим команду с помощью клавиатуры. <br/>Для запуска добавляется флаг **-t** и тогда принудительно создается псевдотерминал. <br/><br/>


**13.** Бывает, что есть необходимость переместить запущенный процесс из одной сессии в другую. <br/> Попробуйте сделать это, воспользовавшись reptyr. Например, так можно перенести в screen процесс, который вы запустили по ошибке в обычной SSH-сессии. <br/><br/>


установим утилиту **reptyr:**(Debian/Ubuntu) <br/>
`sudo apt-get install reptyr` <br/>
`vagrant@ Thu Feb 10 $ top` <br/>
*приостановка процесса* **CTRL-Z** <br/>
`vagrant@ Thu Feb 10 $ bg`   (возобновить процесс в фоновом режиме) <br/>
`vagrant@ Thu Feb 10 $ jobs -l`  (отобразить выполняемые фоновые процессы PID) <br/>
`vagrant@ Thu Feb 10 $ disown 2699` (изолируем дочерний процесс от родительского процесса) <br/>
`vagrant@ Thu Feb 10 $ screen`   (запуск выбранного терминального мультиплексора, например: **tmux,screen**) <br/>
`vagrant@ Thu Feb 10 $ reptyr 2699` (повторное подключение к фоновому процессу) <br/>
`vagrant@ Thu Feb 10 $ proc/sys/kernel/yama/ptrace_scope` (получаем отказ в праве доступа) <br/>
*определяем активное значение с помощью* `cat /proc/sys/kernel/yama/ptrace_scope` <br/>
**1**  (значение разрешает отладку только родительского процесса) <br/>
из под **root** меняем значение `echo 0 > /proc/sys/kernel/yama/ptrace_scope` <br/>
далее процесс будет перехвачен в **screen**, и продолжил работу после закрытия терминала. <br/>
отсоединяем терминальный мультиплексор (например, **CTRL- Z**) и закроем **ssh** <br/>
снова подключаем **ssh**, подключаемся к мультиплексору (например **tmux,screen**) <br/> `screen attach` <br/>
 [screen](https://disk.yandex.ru/i/-eomqWW2doSueA) <br/>
[reptyr pr ](https://disk.yandex.ru/i/SmdQP4SFostk1g) <br/><br/>




**14.** sudo echo string > /root/new_file не даст выполнить перенаправление под обычным пользователем, так как перенаправлением занимается процесс shell'а, который запущен без sudo под вашим пользователем. <br/> Для решения данной проблемы можно использовать конструкцию echo string | sudo tee /root/new_file. <br/> Узнайте что делает команда tee и почему в отличие от sudo echo команда с sudo tee будет работать. <br/>


**tee**  читает из `input` и пишет в `output` и в файл <br/>
работает так как команда запущена от **sudo.** <br/> <br/> <br/>


<br/><br/>


##                 ДЗ по лекции "Работа в терминале (лекция 1)" <br/> <br/> <br/>


**1.** Установите средство виртуализации Oracle VirtualBox.

`sudo apt install virtualbox` <br/> <br/>


**2.** Установите средство автоматизации Hashicorp Vagrant.

`sudo apt-get install vagrant` <br/> <br/>

**3.** В вашем основном окружении подготовьте удобный для дальнейшей работы терминал.

добавлено отображение даты и времени в командной строке, изменен цвет:<br/>
`PS1="\e[33m \u@\e[32m \d \t\e[35m $ \e[m "` <br/>
[vagrant_color](https://disk.yandex.ru/i/rbvIkoudzkX0Ew) <br/> <br/>


**4.** С помощью базового файла конфигурации запустите Ubuntu 20.04 в VirtualBox посредством Vagrant:

VM установлена <br/> <br/>

**5.** Ознакомьтесь с графическим интерфейсом VirtualBox, посмотрите как выглядит виртуальная машина, которую создал для вас Vagrant, какие аппаратные ресурсы ей выделены. Какие ресурсы выделены по-умолчанию?

- RAM:1024mb
- CPU:2cpu
- HDD:64gb
- video:4mb <br/> <br/>

**6.** Ознакомьтесь с возможностями конфигурации VirtualBox через Vagrantfile: документация. Как добавить оперативной памяти или ресурсов процессора виртуальной машине?

*добавлением комманд в VagrantFile:*

```shell
config.vm.provider "virtualbox" do |vb|
vb.memory = "1024" 
vb.cpu = "2" 
end
``` 
<br/> <br/>

**7.** Команда vagrant ssh из директории, в которой содержится Vagrantfile, позволит вам оказаться внутри виртуальной машины без каких-либо дополнительных настроек. Попрактикуйтесь в выполнении обсуждаемых команд в терминале Ubuntu.
    
выполнено <br/> <br/>


**8.** Ознакомиться с разделами man bash, почитать о настройках самого bash: <br/> 
какой переменной можно задать длину журнала history, и на какой строчке manual это описывается? <br/>
что делает директива ignoreboth в bash?

- HISTSIZE - *размер списка хранящегося в памяти интерпретатора* <br/>
строка **937**
- HISTFILESIZE - *максимальное количество команд хранящихся в файле истории.* <br/>
строка **937**

что делает директива ignoreboth в bash?

*игнорирует команды, начинающиеся с пробела и вывод одинаковых команд.* <br/> <br/>

**9.** В каких сценариях использования применимы скобки {} и на какой строчке man bash это описано?

для выражения переменной, списка, зарезервированных слов, ограничивает тело функции,
выполняет подстановку элементов из списка, например:<br/>
`mkdir фото_{01,02,03,04,05,06,07,08,09,10,11,12}_2009` <br/>
строка **1244** <br/> <br/>


**10.** С учётом ответа на предыдущий вопрос, как создать однократным вызовом touch 100000 файлов? Получится ли аналогичным образом создать 300000? Если нет, то почему?

`touch {000001..100000}.txt` - создаст в текущей директории соответсвющее число текстовых фалов <br/>
`touch {000001..300000}.txt` - создание не удалось, слишком дилинный список аргументов <br/>(`-bash: /usr/bin/touch: Argument list too long`) <br/>
`touch {000001..110200}.txt` - максимальное число списка <br/> <br/>
 

**11.** В man bash поищите по /\[\[. Что делает конструкция [[ -d /tmp ]] 

[[ —  расширенный вариант от «[«, является зарезервированным словом, а не командой, его bash выполняет как один элемент с кодом возврата. <br/> 
Внутри «[[….]]» разрешается выполнение операторов &&, которые приводят к ошибке в обычных скобках «[….]» тем самым вариант с двойной скобкой более универсален.
[[ -d /tmp ]]  -  Проверяет наличие директории tmp. 
					
*добавление скипта в файл конфигурации ~/.bashrc*

```shell

if [[ -d /tmp ]]
then
    echo "каталог есть"
else
    echo "каталога нет"
fi 
```
<br/> <br/>

**12.** Основываясь на знаниях о просмотре текущих (например, PATH) и установке новых переменных; командах, которые мы рассматривали, добейтесь в выводе type -a bash в виртуальной машине наличия первым пунктом в списке: <br/>
**bash is /tmp/new_path_directory/bash** <br/>
**bash is /usr/local/bin/bash** <br/>
**bash is /bin/bash**


`mkdir /tmp/new_path_directory/` <br/>
`cp /bin/bash /tmp/new_path_directory/` <br/>
`PATH=/tmp/new_path_directory/:$PATH`

[PATH](https://disk.yandex.ru/i/Ru1EstEZgRc2YA)           

<br/> <br/>

**13.** Чем отличается планирование команд с помощью batch и at?

команда **at** - используется для назначения одноразового задания на заданное время <br/>
команда **batch** - для назначения одноразовых задач, которые должны выполняться, когда загрузка системы становится меньше 0,8. <br/>
для использования at и batch необходимо чтобы был установлен **RPM-пакет**
(на моей VM данный пакет не установлен)

<br/> <br/>

**14.** Завершите работу виртуальной машины чтобы не расходовать ресурсы компьютера и/или батарею ноутбука.
 
*vagrant suspend*

<br/> <br/> <br/>

<br/><br/>




##               Домашнее задание к занятию «2.4. Инструменты Git» <br/> <br/> <br/>
				   
				   

**1.** Найдите полный хеш и комментарий коммита, хеш которого начинается на aefea.


`git show aefea`<br/>
`aefead2207ef7e2aa5dc81a34aedf0cad4c32545    Update CHANGELOG.md`

<br/>

**2.** Какому тегу соответствует коммит 85024d3?


`git show 85024d3` <br/>
`commit 85024d3100126de36331c6982bfaac02cdab9e76 (tag: v0.12.23)`

<br/>

**3.** Сколько родителей у коммита b8d720? Напишите их хеши.

`git show -s --format=%p b8d720` <br/>
`56cd7859e 9ea88f22f` <br/>
или <br/>
`git show -s --format=%p b8d720 | wc -w` <br/>
или <br/>
`git show --pretty=format:' %P' b8d720`

<br/> <br/>

**4.** Перечислите хеши и комментарии всех коммитов которые были сделаны между тегами v0.12.23 и v0.12.24.

`git log  v0.12.23..v0.12.24  --oneline` <br/>
`33ff1c03b (tag: v0.12.24) v0.12.24` <br/>
`b14b74c49 [Website] vmc provider links` <br/>
`3f235065b Update CHANGELOG.md` <br/>
`6ae64e247 registry: Fix panic when server is unreachable` <br/>
`5c619ca1b website: Remove links to the getting started guide's old location` <br/>
`06275647e Update CHANGELOG.md` <br/>
`d5f9411f5 command: Fix bug when using terraform login on Windows` <br/>
`4b6d06cc5 Update CHANGELOG.md` <br/>
`dd01a3507 Update CHANGELOG.md` <br/>
`225466bc3 Cleanup after v0.12.23 release` <br/>

или

`git log --pretty=%H v0.12.23...v0.12.24` <br/>
`33ff1c03bb960b332be3af2e333462dde88b279e` <br/>
`b14b74c4939dcab573326f4e3ee2a62e23e12f89` <br/>
`3f235065b9347a758efadc92295b540ee0a5e26e` <br/>
`6ae64e247b332925b872447e9ce869657281c2bf` <br/>
`5c619ca1baf2e21a155fcdb4c264cc9e24a2a353` <br/>
`06275647e2b53d97d4f0a19a0fec11f6d69820b5` <br/>
`d5f9411f5108260320064349b757f55c09bc4b80` <br/>
`4b6d06cc5dcb78af637bbb19c198faff37a066ed` <br/>
`dd01a35078f040ca984cdd349f18d0b67e486c35` <br/>
`225466bc3e5f35baa5d07197bbc079345b77525e` <br/>

<br/> <br/>

**5.** Найдите коммит в котором была создана функция func providerSource, ее определение в коде выглядит так func providerSource(...) (вместо троеточего перечислены аргументы).

`git log -S'func providerSource' --oneline` <br/>
`5af1e6234 main: Honor explicit provider_installation CLI config when present` <br/>
`8c928e835 main: Consult local directories as potential mirrors of providers`

<br/> <br/>

**6.** Найдите все коммиты в которых была изменена функция globalPluginDirs.

`git log -S globalPluginDirs` <br/>
или <br/>
`git log -S globalPluginDirs --oneline` <br/>
`35a058fb3 main: configure credentials from the CLI config file` <br/>
`c0b176109 prevent log output during init` <br/>
`8364383c3 Push plugin discovery down into command package`

<br/> <br/>

**7.**
`git log -S synchronizedWriters --pretty=%an` <br/>
или <br/>
`git log -S'func synchronizedWriters' --pretty=format:'%h - %an %ae'` <br/>
`bdfea50cc - James Bardin j.bardin@gmail.com` <br/>
`5ac311e2a - Martin Atkins mart@degeneration.co.uk`



<br/><br/><br/><br/><br/>





##                    ДЗ по лекции "Системы контроля версий"

**О файлах .gitignore**<br/>
*исключения:*
  
 - *локальные терраформные каталоги*
 - *файлы .tfstate*
 - *журналы сбоев*
 - *override.tf (используется для локального переопределения ресурсов)*
 - *.terraformrc (конфигурационные файлы CLI)* <br/> <br/> <br/>
 



