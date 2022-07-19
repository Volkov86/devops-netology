##                        ДЗ к занятию "3.4. Операционные системы (лекция 2)" <br/> <br/>

**1.** На лекции мы познакомились с node_exporter. В демонстрации его исполняемый файл запускался в background. <br/> 
Этого достаточно для демо, но не для настоящей production-системы, где процессы должны находиться под внешним управлением. <br/> 
Используя знания из лекции по systemd, создайте самостоятельно простой unit-файл для node_exporter:

Поместите его в автозагрузку,
предусмотрите возможность добавления опций к запускаемому процессу через внешний файл (посмотрите, например, на systemctl cat cron),
удостоверьтесь, что с помощью systemctl процесс корректно стартует, завершается, а после перезагрузки автоматически поднимается.


С помощью `systemctl` процесс корректно стартует, завершается, а после перезагрузки автоматически поднимается: <br/>

```shell
root@vagrant:~# ps -e |grep node_exporter
   9699 ?   00:00:00 node_exporter   
```
[node_exporter](https://disk.yandex.ru/i/z35ZvFXKGTwsXQ)  
[service](https://disk.yandex.ru/i/mrhVXq75VsKs4Q) <br/> <br/>

```shell
vagrant $  systemctl stop node_exporter
==== AUTHENTICATING FOR org.freedesktop.systemd1.manage-units ===
Authentication is required to stop 'node_exporter.service'.
Authenticating as: vagrant
Password: 
==== AUTHENTICATION COMPLETE ===  
```
[stop_node](https://disk.yandex.ru/i/cn-JkV1QUZe42A) <br/> <br/> 

```shell
 vagrant $  ps -e |grep node_exporter
 vagrant $  systemctl start node_exporter
==== AUTHENTICATING FOR org.freedesktop.systemd1.manage-units ===
Authentication is required to start 'node_exporter.service'.
Authenticating as: vagrant
Password:
==== AUTHENTICATION COMPLETE === 
```
```shell
 vagrant $  ps -e |grep node_exporter
   9824 ?        00:00:00 node_exporter
```

[start_node](https://disk.yandex.ru/i/2kO_NxqepIrffg) <br/> 

***конфигурационный файл:***

```shell
  _# /etc/systemd/system/node_exporter.service 
 [Unit] 
Description=Node Exporter Service 
After=network.target 
[Service] 
User=nodeusr 
Group=nodeusr 
Type=simple 
ExecStart=/usr/local/bin/node_exporter 
ExecReload=/bin/kill -HUP $MAINPID 
Restart=on-failure 
[Install] 
WantedBy=multi-user.target     
```
[config](https://disk.yandex.ru/i/Alapk4m8y0svvA)  <br/>

***переменная окружения:***
```shell
vagrant $  sudo cat /proc/9824/environ
LANG=en_US.UTF-8PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/binHOME=/home/nodeusrLOGNAME=nodeusrUSER=nodeusrINVOCATION_ID=4b8d5d3fe0ae45d897c72c0745f0c14fJOURNAL_STREAM=9:132702
```
<br/>

**2.** Ознакомьтесь с опциями node_exporter и выводом /metrics по-умолчанию. Приведите несколько опций, которые вы бы выбрали для базового мониторинга хоста по CPU, памяти, диску и сети.


**CPU:** <br/>
```shell
node_cpu_seconds_total{cpu="0",mode="idle"} 137695.56
node_cpu_seconds_total{cpu="0",mode="system"} 157.86
node_cpu_seconds_total{cpu="0",mode="user"} 90.15
```
**Memory:** <br/>
```shell
node_memory_MemAvailable_bytes 5.72854272e+08
node_memory_MemFree_bytes 1.384448e+08
```
**Disk:** <br/>
```shell
node_disk_io_time_seconds_total{device="sda"} 720.992
node_disk_read_bytes_total{device="sda"} 1.331976192e+09
node_disk_read_time_seconds_total{device="sda"} 207.744
node_disk_write_time_seconds_total{device="sda"} 1981.4850000000001
```

**Network:** <br/>
```shell
`node_network_receive_bytes_total{device="eth0"} 2.95894006e+08
`node_network_receive_errs_total{device="eth0"} 0
`node_network_transmit_bytes_total{device="eth0"} 7.022474e+06
`node_network_transmit_errs_total{device="eth0"} 0
```
<br/>

**3.** Установите в свою виртуальную машину `Netdata`. <br/> Воспользуйтесь готовыми пакетами для установки (sudo apt install -y `netdata`). 
После успешной установки: <br/>
в конфигурационном файле `/etc/netdata/netdata.conf` в секции [web] замените значение с `localhost` на `bind to = 0.0.0.0`, <br/>
добавьте в Vagrantfile проброс порта `Netdata` на свой локальный компьютер и сделайте vagrant reload: <br/>
`config.vm.network "forwarded_port", guest: 19999, host: 19999`
После успешной перезагрузки в браузере на своем ПК (не в виртуальной машине) вы должны суметь зайти на `localhost:19999`. <br/> 
Ознакомьтесь с метриками, которые по умолчанию собираются `Netdata`, и с комментариями, которые даны к этим метрикам. <br/>

[web](https://disk.yandex.ru/i/ZyorlUMzWmpbMg)  <br/><br/>


**4.** Можно ли по выводу `dmesg` понять, осознает ли ОС, что загружена не на настоящем оборудовании, а на системе виртуализации? <br/>

```shell
vagrant $  dmesg | grep -i virtual
[    0.000000] DMI: innotek GmbH VirtualBox/VirtualBox, BIOS VirtualBox 12/01/2006
[    0.014141] CPU MTRRs all blank - virtualized system.
[    0.107067] Booting paravirtualized kernel on KVM
[    1.199105] Performance Events: PMU not available due to virtualization, using software events only.
[   16.010493] systemd[1]: Detected virtualization oracle.
```
<br/>


**5.** Как настроен sysctl fs.nr_open на системе по-умолчанию? Узнайте, что означает этот параметр. Какой другой существующий лимит не позволит достичь такого числа `(ulimit --help)`? <br/>



`vagrant $  sysctl fs.nr_open` <br/>
`fs.nr_open = 1048576` <br/>


 `vagrant $  ulimit -n` <br/>
`1024`

*Параметр означает максимальное число открытых дескрипторов для ядра (системы)* <br/>
*Число задается кратное 1024, в данном случае* =1024*1024. <br/>


`vagrant $  cat /proc/sys/fs/file-max`
`9223372036854775807`

*максимальный предел ОС.* <br/>


**6.** Запустите любой долгоживущий процесс (не ls, который отработает мгновенно, а, например, sleep 1h) в отдельном `неймспейсе` процессов; покажите, что ваш процесс работает под PID 1 через `nsenter`. <br/> Для простоты работайте в данном задании под root (sudo -i). <br/> Под обычным пользователем требуются дополнительные опции (--map-root-user) и т.д. <br/>


**pts/1:** <br/>

`root@vagrant:~# unshare -f --pid --mount-proc sleep 2m` <br/>

**pts/0:** <br/>

`root@vagrant:~# ps -e | grep sleep` <br/>
   `2178 pts/1    00:00:00 sleep` <br/>

```shell
root@vagrant:~# nsenter --target 2202 --mount --uts --ipc --net --pid ps aux
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root           1  0.0  0.0   5476   520 pts/1    S+   18:14   0:00 sleep 2m
root           2  0.0  0.3   8892  3384 pts/0    R+   18:15   0:00 ps aux
```
<br/>



**7.** Найдите информацию о том, что такое:`(){ :|:& };:`. Запустите эту команду в своей виртуальной машине Vagrant с Ubuntu 20.04 (это важно, поведение в других ОС не проверялось). <br/> Некоторое время все будет "плохо", после чего (минуты) – ОС должна стабилизироваться. <br/> Вызов `dmesg` расскажет, какой механизм помог автоматической стабилизации. <br/> Как настроен этот механизм по-умолчанию, и как изменить число процессов, которое можно создать в сессии? <br/>



`:(){ :|:& };:` <br/>

*эта команда является **fork-bomb**. Она оперирует определением функции с именем ‘:‘, которая вызывает сама себя дважды: один раз на переднем плане и один раз в фоне. Она продолжает своё выполнение снова и снова, пока система не зависнет.* <br/>
```shell
root@vagrant:~# dmesg | grep fork
[ 7997.408346] cgroup: fork rejected by pids controller in /user.slice/user-1000.slice/session-9.scope
```
[fork-bomb](https://disk.yandex.ru/i/L3yhs6h3I1srYw) <br/>
