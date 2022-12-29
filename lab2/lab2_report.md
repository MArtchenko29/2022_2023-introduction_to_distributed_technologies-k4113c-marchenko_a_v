University: [ITMO University](https://itmo.ru/ru/)

Faculty: [FICT](https://fict.itmo.ru)

Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)

Year: 2022/2023

Group: K4113c

Author: Marchenko Artem Vyacheslavovich Lab: Lab2

Date of create: 29.12.2022

Date of finished: 


### Лабораторная работа №2 "Развертывание веб сервиса в Minikube, доступ к веб интерфейсу сервиса. Мониторинг сервиса"

#### Описание

##### В данной лабораторной работе вы познакомитесь с развертыванием полноценного веб сервиса с несколькими репликами. 

#### Цель работы

##### Ознакомиться с типами "контроллеров" развертывания контейнеров, ознакомиться с сетевыми сервисами и развернуть своё веб приложение

#### Ход работы

* ##### Создаём контейнер frontend-container. 
Первым делом, необходимо скачать образ itdt-contained-frontend командой `docker pull ifilyaninitmo/itdt-contained-frontend:master `


![Скачивание образа](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab2/screen/1.PNG)


Проверяем появление скаченного образа с помощью команды `docker images`


![Проверка появления образа](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab2/screen/2.PNG)


Следующим шагом создадим контейнер на основе скаченного образа. В терминале введём команду `docker run -d --name frontend-container ifilyaninitmo/itdt-contained-frontend:master`.


Как и со скаченным образом, необходимо проверить появление самого контейнера на его основе. Команда: `docker ps -a`.


![Создание контейнера на основе образа](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab2/screen/3.PNG)


![Проверка появления контейнера](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab2/screen/4.PNG)


* ##### Создание Deployment

На третьем шаге запускаем minikube. Для этого нам понадобится команда `minikube start`. И опять проверка! Проверяем появление ноды командой `kubectl get nodes`.


![Запуск minikube](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab2/screen/5.PNG)


![Проверка появления ноды](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab2/screen/6.PNG)


Теперь можем создать manifest file. Для этого создадим файл с расширением .yaml (`touch frontend-deployment.yaml`).


Откроем его с помощью редактора nano (`nano frontend-deployment.yaml`).


Пример манифеста для развертывания:


![Содержимое файла](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab2/screen/7.PNG)


Сохраним файл (комбинация Ctrl+o) и выйдем из редактора nano (комбинация Cntrl+x).


Для того, чтобы запустить два экземпляра пода, необходимо использовать свойство `replicas: 2`. Шаблон пода задаётся в объекте `Template`. С помощью свойства `env` объявляем внутри подов переменные окружения `REACT_APP_USERNAME` и `REACT_APP_COMPANY_NAME` со значениями `Artem` и `ITMO`, соответственно.


Теперь неободимо перейти в папку с .yaml-файлом и выполнить команду `kubectl create -f frontend-deployment.yaml` :


![Создание развертывания](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab2/screen/8.PNG)


Для того, чтобы проверить, появлось ли развертывание, используем команду: `kubectl get deployments`


![Проверка развертывания](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab2/screen/9.PNG)


* ##### Создание сервиса frontend-service
Нам нужно создать сервис для доуступа к развертыванию. Команда `minikube kubectl -- expose deployment frontend --port=3000 --target-port=3000 --name=frontend-service --type=LoadBalancer`:


![Создание сервиса](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab2/screen/10.PNG)


Здесь, `LoadBalancer` - это тип сервиса, который решает задачу балансировки нагрузки между подами


Теперь необходимо перебросить локальный порт на порт контейнера. Для этого используем команду `minikube kubectl -- port-forward service/frontend-service 3000:3000`


![Перебрасывание портов](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab2/screen/11.PNG)


Заметьте, что для дальнейшей работы необходимо будет открыть второй терминал (Ctrl+Alt+t). Первый останется работать в интерактивном режиме (пока висит - перебрасывает, остановишь - перестанет)
Последним шагом в этом пункте будет открытие страницы: `http://localhost:3000`


![Открытие страницы](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab2/screen/12.PNG)


* ##### Логи подов


Для того, чтобы посмотреть список всех подов, необходима команда `minikube kubectl get pods`. Как и должно быть, мы видим, что deployment запустил два пода:


![Просмотр подов](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab2/screen/13.PNG)


Посмотрим логи первого и второго пода:


![Логи](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab2/screen/14.PNG)


Для того, чтобы удалить развертывание, используем команду: `kubectl delete deployments/frontend`


![Удаление](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab2/screen/15.PNG)


Так как работа выполнена,  проверяем - `kubectl get deployments` останавливаем minikube командой `minikube stop`:


![Последняя проверка](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab2/screen/16.PNG)


![Остановка](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab2/screen/17.PNG)


* ##### Диаграмма
Диаграмма нарисована в draw.io


![Диаграмма](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab2/screen/18.PNG)


* ##### Проблемы с которыми столкнулся
При переброске локального порта на порт контейнера возникла следующая проблема: 


![Проблема](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab2/screen/19.PNG)


Решение простое - я пересоздал сервис для доступа к развертыванию. После этого, переброска локального порта на порт контейнера выполнилась успешно.




