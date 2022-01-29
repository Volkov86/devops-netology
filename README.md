               ДЗ по лекции "Системы контроля версий"

                        О файлах .gitignore
  исключения:
  
 1. локальные терраформные каталоги*
 2. файлы .tfstate*
 3. журналы сбоев*
 4. override.tf (используется для локального переопределения ресурсов)*
 5. .terraformrc (конфигурационные файлы CLI)*               






                ДЗ по лекции "Работа в терминале (лекция 1)"

    

1. Установите средство виртуализации Oracle VirtualBox.

                    sudo apt install virtualbox


2. Установите средство автоматизации Hashicorp Vagrant.

                    *sudo apt-get install vagrant


3. В вашем основном окружении подготовьте удобный для дальнейшей работы терминал.

                     добавлено отображение даты и времени в командной строке, изменен цвет:
                     PS1="\e[33m \u@\e[32m \d \t\e[35m $ \e[m "

                     ![name of the vagrant_color ](https://disk.yandex.ru/i/rbvIkoudzkX0Ew) 

  
4. С помощью базового файла конфигурации запустите Ubuntu 20.04 в VirtualBox посредством Vagrant:

                     VM установлена


5. Ознакомьтесь с графическим интерфейсом VirtualBox, посмотрите как выглядит виртуальная машина, которую создал для вас Vagrant, какие аппаратные ресурсы ей выделены. Какие ресурсы выделены по-умолчанию?

                    RAM:1024mb
                    CPU:2cpu
                    HDD:64gb
                    video:4mb


6. Ознакомьтесь с возможностями конфигурации VirtualBox через Vagrantfile: документация. Как добавить оперативной памяти или ресурсов процессора виртуальной машине?

                    *добавлением комманд в VagrantFile:

                    config.vm.provider "virtualbox" do |vb|
                      vb.memory = "1024"
                      vb.cpu = "2"
                    end


7. Команда vagrant ssh из директории, в которой содержится Vagrantfile, позволит вам оказаться внутри виртуальной машины без каких-либо дополнительных настроек. Попрактикуйтесь в выполнении обсуждаемых команд в терминале Ubuntu.
    
                    *выполнено


8. Ознакомиться с разделами man bash, почитать о настройках самого bash:
какой переменной можно задать длину журнала history, и на какой строчке manual это описывается?
что делает директива ignoreboth в bash?

                    1. HISTSIZE - *размер списка хранящегося в памяти интерпретатора
                             *строка 937
                    2. HISTFILESIZE - *максимальное количество команд хранящихся в файле истории.
                             *строка 937

   что делает директива ignoreboth в bash?

                    *игнорирует команды, начинающиеся с пробела и вывод одинаковых команд.


  
9. В каких сценариях использования применимы скобки {} и на какой строчке man bash это описано?

                    для выражения переменной, списка, зарезервированных слов, ограничивает тело функции,
                    выполняет подстановку элементов из списка, например:
                    mkdir фото_{01,02,03,04,05,06,07,08,09,10,11,12}_2009
                   *строка 1244


10. С учётом ответа на предыдущий вопрос, как создать однократным вызовом touch 100000 файлов? Получится ли аналогичным образом создать 300000? Если нет, то почему?

             touch {000001..100000}.txt - создаст в текущей директории соответсвющее число текстовых фалов
             touch {000001..100000}.txt - создание не удалось, слишком дилинный список аргументов
             touch {000001..110200}.txt - максимальное число списка
 

11. В man bash поищите по /\[\[. Что делает конструкция [[ -d /tmp ]] 

                    [[ —  расширенный вариант от «[«, является зарезервированным словом, а не командой, его bash выполняет как один элемент с кодом возврата. Внутри «[[….]]» разрешается выполнение операторов &&, || которые приводят к ошибке в обычных скобках «[….]» тем самым вариант с двойной скобкой более универсален.

       
                    [[ -d /tmp ]]  -  Проверяет наличие директории tmp. 
					
					добавление скипта в файл конфигурации ~/.bashrc


                                      if [[ -d /tmp ]]
                                      then
                                          echo "каталог есть"
                                      else
                                          echo "каталога нет"
                                      fi 


12. Основываясь на знаниях о просмотре текущих (например, PATH) и установке новых переменных; командах, которые мы рассматривали, добейтесь в выводе type -a bash в виртуальной машине наличия первым пунктом в списке:
bash is /tmp/new_path_directory/bash
bash is /usr/local/bin/bash
bash is /bin/bash


                    mkdir /tmp/new_path_directory/
                    cp /bin/bash /tmp/new_path_directory/
                    PATH=/tmp/new_path_directory/:$PATH

             



13. Чем отличается планирование команд с помощью batch и at?

                    команда at - используется для назначения одноразового задания на заданное время
                    команда batch - для назначения одноразовых задач, которые должны выполняться, когда загрузка системы становится меньше 0,8.
                    для использования at и batch необходимо чтобы был установлен RPM-пакет
                    (на моей VM данный пакет не установлен)


14. Pавершите работу виртуальной машины чтобы не расходовать ресурсы компьютера и/или батарею ноутбука.
 
                   *vagrant suspend





                   Домашнее задание к занятию «2.4. Инструменты Git»
				   
				   

1.Найдите полный хеш и комментарий коммита, хеш которого начинается на aefea.


git show aefea
aefead2207ef7e2aa5dc81a34aedf0cad4c32545    Update CHANGELOG.md


2.Какому тегу соответствует коммит 85024d3?


git show 85024d3
commit 85024d3100126de36331c6982bfaac02cdab9e76 (tag: v0.12.23)


3.Сколько родителей у коммита b8d720? Напишите их хеши.

git show -s --format=%p b8d720
56cd7859e 9ea88f22f
или
git show -s --format=%p b8d720 | wc -w
или 
git show --pretty=format:' %P' b8d720



4.Перечислите хеши и комментарии всех коммитов которые были сделаны между тегами v0.12.23 и v0.12.24.

git log  v0.12.23..v0.12.24  --oneline
33ff1c03b (tag: v0.12.24) v0.12.24
b14b74c49 [Website] vmc provider links
3f235065b Update CHANGELOG.md
6ae64e247 registry: Fix panic when server is unreachable
5c619ca1b website: Remove links to the getting started guide's old location
06275647e Update CHANGELOG.md
d5f9411f5 command: Fix bug when using terraform login on Windows
4b6d06cc5 Update CHANGELOG.md
dd01a3507 Update CHANGELOG.md
225466bc3 Cleanup after v0.12.23 release

или 
git log --pretty=%H v0.12.23...v0.12.24
33ff1c03bb960b332be3af2e333462dde88b279e
b14b74c4939dcab573326f4e3ee2a62e23e12f89
3f235065b9347a758efadc92295b540ee0a5e26e
6ae64e247b332925b872447e9ce869657281c2bf
5c619ca1baf2e21a155fcdb4c264cc9e24a2a353
06275647e2b53d97d4f0a19a0fec11f6d69820b5
d5f9411f5108260320064349b757f55c09bc4b80
4b6d06cc5dcb78af637bbb19c198faff37a066ed
dd01a35078f040ca984cdd349f18d0b67e486c35
225466bc3e5f35baa5d07197bbc079345b77525e





5.Найдите коммит в котором была создана функция func providerSource, ее определение в коде выглядит так func providerSource(...) (вместо троеточего перечислены аргументы).

git log -S'func providerSource' --oneline
5af1e6234 main: Honor explicit provider_installation CLI config when present
8c928e835 main: Consult local directories as potential mirrors of providers




6.Найдите все коммиты в которых была изменена функция globalPluginDirs.
git log -S globalPluginDirs
или
git log -S globalPluginDirs --oneline
35a058fb3 main: configure credentials from the CLI config file
c0b176109 prevent log output during init
8364383c3 Push plugin discovery down into command package



7.
git log -S synchronizedWriters --pretty=%an
или
git log -S'func synchronizedWriters' --pretty=format:'%h - %an %ae'
bdfea50cc - James Bardin j.bardin@gmail.com
5ac311e2a - Martin Atkins mart@degeneration.co.uk

