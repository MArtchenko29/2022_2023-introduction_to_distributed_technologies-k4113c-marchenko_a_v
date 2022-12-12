University: [ITMO University](https://itmo.ru/ru/)

Faculty: [FICT](https://fict.itmo.ru)

Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)

Year: 2022/2023

Group: K4113c

Author: Marchenko Artem Vyacheslavovich Lab: Lab1

Date of create: 10.12.2022

Date of finished: 


### Лабораторная работа №1 "Установка Docker и Minikube, мой первый манифест"

#### Описание

##### Это первая лабораторная работа в которой вы сможете протестировать Docker, установить Minikube и развернуть свой первый "под".

#### Цель работы

##### Ознакомиться с инструментами Minikube и Docker, развернуть свой первый "под".

#### Подготовка


Проверить, поддерживает ли виртуальная машина виртуализацию:


![Поддержка виртуализации](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab1/screen/26.PNG)


В случае не поддержки виртуализации, на хосте пропишите следующее:


![Поддержка виртуализации](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab1/screen/virtvirt.PNG)


Перед выполнением нам понадобится установить docker:


![Установка докера](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab1/screen/22.PNG)


Установить гипервизор:


![Установка гипервизора](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab1/screen/23.PNG)


Установить minikube:


![Установить minikube](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab1/screen/24.PNG)


И установить kubectl:


![Установить cubectl](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab1/screen/25.PNG)


#### Ход работы

* ##### Создаём контейнер vault. 
Для работы необходимо скачать образ контейнера vault. Для этого необходимо ввести команду `docker pull vault`. После необходимо провести проверку появления этого образа. Для этого введем команду `docker images`.


![Скачивание образа](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab1/screen/4.PNG)


![Проверка появления образа](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab1/screen/5.PNG)


Следующим шагом создадим контейнер на основе скаченного образа. В терминале введём команду `docker run -d --name vault vault`.


Как и со скаченным образом, необходимо проверить появление самого контейнера на его основе. Команда: `docker ps -a`.


![Создание контейнера на основе образа](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab1/screen/6.PNG)


![Проверка появления контейнера](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab1/screen/7.PNG)


* ##### Создание Pod

На третьем шаге запускаем minikube. Для этого нам понадобится команда `minikube start`. И опять проверка! Проверяем появление ноды командой `kubectl get nodes`.


![Запуск minikube](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab1/screen/8.PNG)


![Проверка появления ноды](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab1/screen/9.PNG)


Теперь можем создать manifest file, по которому будет работать pod. Для этого создадим файл с расширением .yaml (`touch vault_pod.yaml`):


![Создание yaml-файла](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab1/screen/10.PNG)


Откроем его с помощью редактора nano (`nano vault_pod.yaml`):


![Открытие редактором nano](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab1/screen/11.PNG)


На основе знания двух структур (спики и мапы) напишем наш манифест:


![Содержимое файла](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab1/screen/12.PNG)


Сохраним файл (комбинация Ctrl+o) и выйдем из редактора nano (комбинация Cntrl+x).
Теперь неободимо перейти в папку с .yaml-файлом:


![Папка с .yaml-файлом](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab1/screen/13.PNG)


Нам нужно создать Pod, запустить Pod и проверить появление Pod:

`kubectl create -f vault_pod.yaml` - создать

`kubectl apply -f /home/artem` - запустить

`kubectl get pods` - проверить


![Создание, запуск и проверка пода](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab1/screen/14.PNG)


Заметьте, что для того, чтобы узнать путь, можно использовать команду `pwd`.

* ##### Создание сервиса
Нам нужно создать сервис для доуступа к pod. Команда `minikube kubectl -- expose pod vault --type=NodePort --port=8200`:


![Создание сервиса](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab1/screen/15.PNG)


Теперь необходимо перенаправить трафик с pod на локальный. Для этого используем команду `minikube kubectl -- port-forward service/vault 8200:8200`


![Перебрасывание трафика](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab1/screen/16.PNG)


Заметьте, что для дальнейшей работы необходимо будет открыть второй терминал (Ctrl+Alt+t). Первый останется работать в интерактивном режиме (пока висит - перебрасывает, остановишь - перестанет)
Последним шагом в этом пункте будет открытие страницы авторизации Vault: [Страница авторизации](http://localhost:8200):


![Обращение к сервису извне](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab1/screen/17.PNG)


* ##### Поиск токена


Для авторизации нам необходим токен. Как было сказано выше, открываем второй терминал и с помощью команды `minikube kubectl -- logs service/vault` ищем его:


![логи](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab1/screen/19.PNG)


![root](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab1/screen/18.PNG)


Вводим root token в поле авторизации. На рисунке ниже результат входа:


![вход](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab1/screen/20.PNG)


Так как работа выполнена, останавливаем ноду командой `minikube stop`:


![стоп нода](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab1/screen/21.PNG)


* ##### Диаграмма
Диаграмма нарисована в draw.io


![Dia](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab1/screen/Dia.PNG)


* ##### Проблемы с которыми столкнулся 


При загрузке лабораторной №1 на github произошло следующее: загрузка происходила от root'а, и в процессе загрузки случайно (по глупости) были удалены все данные по лабораторной. Перед удалением, я успел отправить их в виде кэша на только что инициализированный git. Долго думая, как вернуть данные удаленные под root'ом обнаружил, что они хранятся на локальном git'e, а сама ссылка на репозиторий (с которым происходит связывание локального git'a) уже существует, но сам репозиторий был удален.

##### Решение:

1) Командой  `git status` убедился, что данные хранятся в виде кэша на инициализированном git'e. 
2) Создал новый репозиторий и скопировал его url (для дальнейшего связывания с локальным git'ом)
3) Командой `git remote set-url origin ?.git` переписал url на новый, где `?.git` - это новый url репозиторий.
4) С успехом "запушил" данные на github!


![save git.PNG](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab1/screen/save%20git.PNG)

