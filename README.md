# devops-netology

добавил в файл .gitignore каталог /terraform закомитил файл .gitignore каталог terraform теперь не будет пушаться на сервер и каталог не виден при комманде git status соответственно и все файлы и подкаталоги папки terraform не будут сохраняться в проекте. Благодаря созданному файлу .gitignore будет игнорироваться локальная директория */.terraform/



Локальные каталоги .terraform */.terraform/

файлы .tfstate *.tfstate .tfstate.

файлы журнала сбоев crash.log crash.*.log

исключить все файлы .tfvars *.tfvars

Игноририровать предопределенные файлы override.tf override.tf.json *_override.tf *_override.tf.json

Игнорировать конфигурационные файлы CLI .terraformrc terraform.rс

