### Как сдавать задания

Вы уже изучили блок «Системы управления версиями», и начиная с этого занятия все ваши работы будут приниматься ссылками на .md-файлы, размещённые в вашем публичном репозитории.

Скопируйте в свой .md-файл содержимое этого файла; исходники можно посмотреть [здесь](https://raw.githubusercontent.com/netology-code/sysadm-homeworks/devsys10/04-script-01-bash/README.md). Заполните недостающие части документа решением задач (заменяйте `???`, ОСТАЛЬНОЕ В ШАБЛОНЕ НЕ ТРОГАЙТЕ чтобы не сломать форматирование текста, подсветку синтаксиса и прочее, иначе можно отправиться на доработку) и отправляйте на проверку. Вместо логов можно вставить скриншоты по желани.

---


# Домашнее задание к занятию "4.1. Командная оболочка Bash: Практические навыки"

## Обязательная задача 1

Есть скрипт:
```bash
a=1
b=2
c=a+b
d=$a+$b
e=$(($a+$b))
```

Какие значения переменным c,d,e будут присвоены? Почему?

| Переменная  | Значение | Обоснование |
| ------------- | ------------- | ------------- |
| `c`  | a+b  | переменной с присваивается символьная строка a+b |
| `d`  | 1+2  | переменной d присваивается символьная строка со значениями переменных $a и $b |
| `e`  | 3  | переменной 3 присваетвается результат математического действия 1+2 |


## Обязательная задача 2
На нашем локальном сервере упал сервис и мы написали скрипт, который постоянно проверяет его доступность, записывая дату проверок до тех пор, пока сервис не станет доступным (после чего скрипт должен завершиться). В скрипте допущена ошибка, из-за которой выполнение не может завершиться, при этом место на Жёстком Диске постоянно уменьшается. Что необходимо сделать, чтобы его исправить:
```bash
while ((1==1)
do
	curl https://localhost:4757
	if (($? != 0))
	then
		date >> curl.log
	fi
done
```

### Ваш скрипт:
```bash
#!/usr/bin/bash

s_s=1
echo $s_s
while (($s_s > 0))
do
  curl https://localhost:4757
  if (( $? !=0 ))
  then
   date >> curl.log
  else
   s_s=0
  fi
done
```

## Обязательная задача 3
Необходимо написать скрипт, который проверяет доступность трёх IP: `192.168.0.1`, `173.194.222.113`, `87.250.250.242` по `80` порту и записывает результат в файл `log`. Проверять доступность необходимо пять раз для каждого узла.

### Ваш скрипт:
```bash
t_ip=("192.168.0.1", "173.194.222.113", "87.250.250.242")

for v_ip in ${t_ip[@]}
do
  echo $v_ip
  for (( i=1; i <= 5; i++ ))
  do
   2>>1.log curl http://$v_ip:80
   if (($? != 0))
   then
    echo "попытка $i для ip $v_ip неуспешна" >> 1.log
   else
    echo "попытка $i для ip $v_ip успешна"  >> 1.log
   fi
  done
done
```

## Обязательная задача 4
Необходимо дописать скрипт из предыдущего задания так, чтобы он выполнялся до тех пор, пока один из узлов не окажется недоступным. Если любой из узлов недоступен - IP этого узла пишется в файл error, скрипт прерывается.

### Ваш скрипт:
```bash
t_ip=("192.168.0.1", "173.194.222.113", "87.250.250.242")

v_stop=0

while (( v_stop != 1 ))
do
        for v_ip in ${t_ip[@]}
        do
                echo " значение переменно v_stop $v_stop "
                if   (( v_stop != 1 ))
                then
                        echo $v_ip
                        2>>error.log curl http://$v_ip:80
                        if (( $? != 0 ))
                        then
                                v_stop=1
                                echo " ip $v_ip не доступен" >> error.log
                        fi
                else
                        break
                fi

        done
done
```

## Дополнительное задание (со звездочкой*) - необязательно к выполнению

Мы хотим, чтобы у нас были красивые сообщения для коммитов в репозиторий. Для этого нужно написать локальный хук для git, который будет проверять, что сообщение в коммите содержит код текущего задания в квадратных скобках и количество символов в сообщении не превышает 30. Пример сообщения: \[04-script-01-bash\] сломал хук.

### Ваш скрипт:
```bash
#!user/bin/bash
#
# An example hook script to check the commit log message.
# Called by "git commit" with one argument, the name of the file

# that has the commit message.  The hook should exit with non-zero
# status after issuing an appropriate message if it wants to stop the
# commit.  The hook is allowed to edit the commit message file.
#
# To enable this hook, rename this file to "commit-msg".

# Uncomment the below to add a Signed-off-by line to the message.
# Doing this in a hook is a bad idea in general, but the prepare-commit-msg
# hook is more suited to it.
#
# SOB=$(git var GIT_AUTHOR_IDENT | sed -n 's/^\(.*>\).*$/Signed-off-by: \1/p')
# grep -qs "^$SOB" "$1" || echo "$SOB" >> "$1"

# This example catches duplicate Signed-off-by lines.

#test "" = "$(grep '^Signed-off-by: ' "$1" |
#        sort | uniq -c | sed -e '/^[   ]*1[    ]/d')" || {
#       echo >&2 Duplicate Signed-off-by lines.
#       exit 1
#}

file=$1
test=$(cat $file)
length=${#test}

if (( length > 30 ))
then
        echo "длина комментария больше 30 символов"
        exit 1
elif  ((  length < 1 ))
then
        echo "комментарий отсутствует"
        exit 2
fi


if [[ $test == '['*']'* ]]
then
        echo  "проверка пройдена"
else
        echo "проверка не пройдена коммент - $test"
        exit 3
fi
```
