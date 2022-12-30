University: [ITMO University](https://itmo.ru/ru/)

Faculty: [FICT](https://fict.itmo.ru)

Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)

Year: 2022/2023

Group: K4113c

Author: Marchenko Artem Vyacheslavovich Lab: Lab4

Date of create: 30.12.2022

Date of finished: 


### Лабораторная работа №4 "Сети связи в Minikube, CNI, и CoreDNS"

#### Описание

##### Это последняя лабораторная работа в которой вы познакомитесь с сетями связи в Minikube. Особенности Kubernetes заключается в том, что у него одновременно работают underlay и overlay сети, а управление может быть организиванно различными CNI.

#### Цель работы

##### Познакомиться с CNI Calico и функцией IPAM Plugin, изучить особенности работы CNI и CoreDNS.

#### Ход работы

* ##### Calico и Multi-Node Clusters

При запуске minikube необходимо установить плагин `CNI=calico`, режим работы `Multi-Node Clusters`, а так же развернуть `2 Node`. Всё это можно сделать одной командой: 


![Старт](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab4/screen/1.PNG)


Следующим шагом необходимо проверить запуск `2 Nodes`. Для этого существует команда: `kubectl get nodes`.


![2nodes](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab4/screen/2.PNG)


Для того, чтобы проверить работу CNI Calico, посмотрим Pod'ы с меткой `calico-node`:


![pod](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab4/screen/3.PNG)


* ##### calicoctl и IPPool

Для назначения IP-адресов в Calico необходимо написать манифест для IPPool ресурса. С помощью IPPool можно создать IP-pool (блок IP-адресов), который выделяет IP-адреса только для узлов с определенной меткой (label).

Для того, чтобы назначить метки узлам, необходимо использовать следующие команды:


![метки](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab4/screen/4.PNG)


Шаблон манифеста IPPool можно взять из официальной документации:


![айпипул](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab4/screen/5.PNG)


Чтобы применить манифест для IPPool, необходимо установить calicotl. Для этого скачиваем config-файл с официального репозитория и выполняем команду `kubectl create -f calicoctl.yaml`:


![config](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab4/screen/6.PNG)


![креате калико](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab4/screen/7.PNG)


Далее необходимо проверить созданные IPPool'ы по-умолчанию:


![айпипулы по умолчанию](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab4/screen/8.PNG)


Следующим шагом необходимо удалить IPPool'ы по-умолчанию:


![удаление](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab4/screen/9.PNG)


Теперь создадим IPPool'ы:


![создание IPPool](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab4/screen/10.PNG)


Убедимся, что появилось два pool'а:


![проверка два](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab4/screen/11.PNG)


* ##### Deployment и Service
Манифест для Deployment (его можно взять из второй лабораторной, немного подкорректировав):

![dep](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab4/screen/12.PNG)


Шаблон манифеста для сервиса типа Load-Balancer берется из официальной документации:


![создание сервиса](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab4/screen/13.PNG)


Необходимо перейти в папку с .yaml файлами и запустить команду: `kubectl apply -f lab4-deployment.yaml -f lab4-service.yaml`


![Перебрасывание портов](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab4/screen/14.PNG)


Еще раз убедимся, что появилось развертнывание и сервис:


![развертывание и сервис](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab4/screen/15.PNG)


Проверим IP созданных Pod'ов: `kubectl get pods -o wide`:


![Проверка IP](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab4/screen/16.PNG)


* ##### Проброс порта

Пробрасываем порт для подключения к сервису через браузер: `kubectl port-forward service/lab4-service 8200:3000`

И переходим по ссылке: `http://localhost:8200/`


![port prob](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab4/screen/17.PNG)


![config](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab4/screen/18.PNG)


* ##### Пинг
Пингуем с одного контейнера на другой и наоборот:


![пинг1](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab4/screen/19.PNG)


![пинг2](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab4/screen/20.PNG)


* ##### Диаграмма

Диаграмма нарисована в draw.io


![Диаграмма](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab4/screen/21.PNG)









