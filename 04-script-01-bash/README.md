##    Домашнее задание к занятию "4.1. Командная оболочка Bash: Практические навыки" <br/> <br/>

**1.** Есть скрипт: <br/>

```shell
a=1
b=2
c=a+b
d=$a+$b
e=$(($a+$b))
```
Какие значения переменным c, d, e будут присвоены? Почему? <br/>

```shell
vagrant@ubuntu2004:~$ c=a+b
vagrant@ubuntu2004:~$ echo $c
a+b
vagrant@ubuntu2004:~$ d=$a+$b
vagrant@ubuntu2004:~$ echo $d
1+2
vagrant@ubuntu2004:~$ e=$(($a+$b))
vagrant@ubuntu2004:~$ echo $e
3
vagrant@ubuntu2004:~$
```


`c= a+b` - указали текст, а не переменные <br/>
`d= 1+2` - команда вывела значения переменных, но не выполнила арифметической операции так как по умолчанию это строки <br/>
`e= 3` - добавили скобки, дали команду на выполнение арифметической операции со значениями переменных <br/>
<br/>	

**2.** На нашем локальном сервере упал сервис и мы написали скрипт, который постоянно проверяет его доступность, записывая дату проверок до тех пор, пока сервис не станет доступным (после чего скрипт должен завершиться). В скрипте допущена ошибка, из-за которой выполнение не может завершиться, при этом место на Жёстком Диске постоянно уменьшается. Что необходимо сделать, чтобы его исправить: <br/>

```shell
while ((1==1)
do
	curl https://localhost:4757
	if (($? != 0))
	then
		date >> curl.log
	fi
done
```
Ваш скрипт:
??? 
<br/>

В условии нехватает закрывающей скобки `)` `while ((1==1)*` <br/>
Для исключения частых проверок сервера, нужно добавить `sleep $timeout` <br/>
Можно добавить проверку успешного выхода из цикла `else exit` <br/>

Скрипт:
```shell
#!/bin/bash
while ((1 == 1)); do
	curl http://localhost
	if (($? != 0)); then
		date >>curl.log
		sleep 1
	fi
done
```
<br/>

**3.** Необходимо написать скрипт, который проверяет доступность трёх IP: 192.168.0.1, 173.194.222.113, 87.250.250.242 по 80 порту и записывает результат в файл log. Проверять доступность необходимо пять раз для каждого узла. <br/>

Ваш скрипт:
???
<br/>
```shell
#!/bin/bash
ips=(192.168.0.1 173.194.222.113 87.250.250.24)
timeout=5
log=ip_log.log
check='curl -Is --connect-timeout'
for i in {1..5}; do
	date >>$log
	for host in ${ips[@]}; do
		$check $timeout http://$host >/dev/null

		if (($? == 0)); then
			status="http UP"
		else
			status="http DOWN"
		fi

		echo $host $status >>$log
	done
done
```
<br/>

**4.** Необходимо дописать скрипт из предыдущего задания так, чтобы он выполнялся до тех пор, пока один из узлов не окажется недоступным. Если любой из узлов недоступен - IP этого узла пишется в файл error, скрипт прерывается. <br/>

Ваш скрипт:
???
<br/>
```shell
#!/bin/bash
ips=(192.168.0.1 173.194.222.113 87.250.250.24)
timeout=5
log=ip_log.log
errorlog=ip_error.log
check='curl -Is --connect-timeout'
for i in {1..5}; do
	date >>$log
	for host in ${ips[@]}; do
		$check $timeout http://$host >/dev/null

		if (($? == 0)); then
			status="http UP"
			echo $host $status >>$log
		else
			status="http DOWN"
			echo $host $status >>$errorlog
			exit
		fi

	done
done
```
<br/>

**5**.* Мы хотим, чтобы у нас были красивые сообщения для коммитов в репозиторий. Для этого нужно написать локальный хук для git, который будет проверять, что сообщение в коммите содержит код текущего задания в квадратных скобках и количество символов в сообщении не превышает 30. Пример сообщения: [04-script-01-bash] сломал хук. <br/>

```shell
!#/bin/bash
test "" = "$(grep '^Signed-off-by: ' "$1" |
         sort | uniq -c | sed -e '/^[   ]*1[    ]/d')" || {
        echo >&2 Duplicate Signed-off-by lines.
        exit 1
}

commit='^(DevSys-[0-9])'
if ! grep -qE "$commit" "$1"; then
    echo "Commit message [DevSys-00-test]"
    exit 1
fi
```
<br/>