###              Домашнее задание по лекции "Компьютерные сети (лекция 1)"   <br/><br/>

**1.** Работа c HTTP через телнет. <br/>
- Подключитесь утилитой телнет к сайту stackoverflow.com `telnet stackoverflow.com 80`   <br/>
- отправьте HTTP запрос  <br/>
```shell
GET /questions HTTP/1.0 <br/>
HOST: stackoverflow.com <br/>
[press enter]
[press enter]
```
- В ответе укажите полученный HTTP код, что он означает? <br/><br/>



`Получили код 301. Постоянный редирект протокола http на https с таким же url` <br/>
[1.telnet](https://disk.yandex.ru/i/DK-4r7Z8rg0w9A)  <br/><br/>


**2.** Повторите задание 1 в браузере, используя консоль разработчика F12. <br/>
- откройте вкладку `Network` <br/>
- отправьте запрос http://stackoverflow.com <br/>
- найдите первый ответ HTTP сервера, откройте вкладку `Headers` <br/>
- укажите в ответе полученный HTTP код. <br/>
- проверьте время загрузки страницы, какой запрос обрабатывался дольше всего? <br/>
- приложите скриншот консоли браузера в ответ. <br/><br/>

`В ответ получили код 307 Temporary Redirect` <br/>
[307](https://disk.yandex.ru/i/DK-4r7Z8rg0w9A) <br/>
`Обработка дольше всего была у стартовой страницы сайта, время отклика 163мс` <br/>
[163мс](https://disk.yandex.ru/i/g6KFQJAgBTSAxg) <br/>



**3.** Какой IP адрес у вас в интернете? <br/>
```shell
vagrant@vagrant:~$ dig +short myip.opendns.com @resolver1.opendns.com 
77.51.152.xx
```
<br/>

**4.** Какому провайдеру принадлежит ваш IP адрес? Какой автономной системе AS? Воспользуйтесь утилитой `whois`  <br/>
```shell
vagrant@vagrant:~$ whois 77.51.152.xxx | grep ^descr
descr:          OJSC Rostelecom, Moscow Region Branch
descr:          DSL access network in  Moscow region
descr:          Rostelecom networks
```
[whois](https://disk.yandex.ru/i/28AXS94WLB_2Dw) 
```shell
vagrant@vagrant:~$ whois 77.51.152.xxx | grep ^origin
origin:         AS12389
```
`ip - OJSC Rostelecom` <br/>
`AS - AS12389`  <br/><br/>


**5.** Через какие сети проходит пакет, отправленный с вашего компьютера на адрес 8.8.8.8? Через какие AS? <br/> Воспользуйтесь утилитой `traceroute` <br/> 

`$ traceroute -An 8.8.8.8` <br/>
`$ mtr -zn 8.8.8.8` <br/> 
`Пакет проходит через AS 15169 12389 25515` <br/> [mtr](https://disk.yandex.ru/i/x2mYJorHNTDBrQ) <br/> [traceroute](https://disk.yandex.ru/i/WI7YMujyfu1XQw)

```shell
 vagrant@vagrant:~$ grep org-name <(whois AS12389)
org-name:       PJSC Rostelecom
vagrant@vagrant:~$ grep org-name <(whois AS25515)
org-name:       PJSC Rostelecom
vagrant@vagrant:~$ grep OrgName <(whois AS15169)
OrgName:        Google LLC
```
 <br/>


**6.** Повторите задание 5 в утилите `mtr`. На каком участке наибольшая задержка - delay? <br/>

`Задержка на хосте 9` <br/>
[mtr6](https://disk.yandex.ru/i/d-3XPL-A0Hg_1w) <br/>



**7.** Какие DNS сервера отвечают за доменное имя dns.google? Какие A записи? Воспользуйтесь утилитой `dig` <br/>

`NS` <br/> 
```shell
vagrant@vagrant:~$ dig +short NS dns.google
ns4.zdns.google.
ns2.zdns.google.
ns3.zdns.google.
ns1.zdns.google.
```
`А` <br/>
```shell
vagrant@vagrant:~$ dig +short A dns.google
8.8.4.4
8.8.8.8
```
<br/>

**8.** Проверьте PTR записи для IP адресов из задания 7. Какое доменное имя привязано к IP? Воспользуйтесь утилитой `dig` <br/>
```shell
vagrant@vagrant:~$ for ip in `dig +short A dns.google`; do dig -x $ip | grep ^[0-9].*in-addr; done
8.8.8.8.in-addr.arpa.   300     IN      PTR     dns.google.
4.4.8.8.in-addr.arpa.   300     IN      PTR     dns.google.
```