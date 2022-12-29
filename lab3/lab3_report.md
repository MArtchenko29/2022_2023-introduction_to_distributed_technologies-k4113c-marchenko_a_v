University: [ITMO University](https://itmo.ru/ru/)

Faculty: [FICT](https://fict.itmo.ru)

Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)

Year: 2022/2023

Group: K4113c

Author: Marchenko Artem Vyacheslavovich Lab: Lab3

Date of create: 29.12.2022

Date of finished: 


### Лабораторная работа №3 "Сертификаты и "секреты" в Minikube, безопасное хранение данных"

#### Описание

##### В данной лабораторной работе вы познакомитесь с сертификатами и "секретами" в Minikube, правилами безопасного хранения данных в Minikube 

#### Цель работы

##### Познакомиться с сертификатами и "секретами" в Minikube, правилами безопасного хранения данных в Minikube

#### Ход работы

* ##### Создание ConfigMap 
Запускаем Minikube - `minikube start`.


![Старт](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab3/screen/1.PNG)


Пример манифеста для ConfigMap был взят из официальной документации.


![Конфигмап](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab3/screen/2.PNG)


По ключам `react_app_user_name` и `react_app_company_name` будут храниться значения для переменных REACT_APP_USERNAME и REACT_APP_COMPANY_NAME, соответственно.


Переходим в папку с .yaml файлом и выполняем команду `kubectl create -f frontend-configmap.yaml` 


![первая команда](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab3/screen/3.PNG)


Проверяем появление ConfigMap командой `kubectl get configmaps`


![Проверка появления контейнера](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab3/screen/4.PNG)


* ##### Создание ReplicaSet

Пример манифеста для ReplicaSet так же был взят из официальной документации


![ReplicaSet](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab3/screen/5.PNG)


Шаблон пода задается в объекте `Template`. С помощью свойства `env` объявляем внутри подов переменные окружения `REACT_APP_USERNAME` и `REACT_APP_COMPANY_NAME`. Их значения необходимо взять из ConfigMap frontend-configmap, созданный ранее.


Создаём контроллер командой `kubectl create -f frontend-repliset-manifest.yaml`:


![Создание контроллера](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab3/screen/6.PNG)


Проверяем появление контроллера командой `kubectl get rs`:


![Проверка](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab3/screen/7.PNG)


* ##### Создание сервиса 
Для создания Ingress ресурса потребуется сервис. Манифест пишется по шаблону из официальной документации.


![Ingres](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab3/screen/prop.PNG)


Доступны только порты 30000-32767, пусть будет 30333. Согласно официальной документации, Ingress - ресурс работающий с сервисами типа `NodePort` и `LoadBalancer`. Необходимо в поле `type` один из них.

Создаём сервис командой - `kubectl create -f frontend-service.yaml`:


![создание сервиса](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab3/screen/8.PNG)


Следующим шагом необходимо проверить появлся ли сервис - `kubectl get services`:


![Перебрасывание портов](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab3/screen/9.PNG)


* ##### Генерация TLS

Для генерации TLS сертификата необходимо скачать и установить OpenSSL. Чтобы сделать это, необходимо ввести следующую команду `sudo apt install openssl`


Генерируем приватный ключ RSA. Опция `-out` указывает на имя файла для сохранения ключа, а число `2048` - размер ключа в битах. По умолчанию 512.


![Генерируем приватный ключ](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab3/screen/10.PNG)


Для того, чтобы получить сертификат, который можно использовать, необходимо этот ключ подписать. Создаем запрос на подпись командой:


![Запрос на подпись](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab3/screen/11.PNG)


При создании запроса на подпись нужно указать необходимую информацию. Обязательное поле здесь - это Common Name, здесь мы указываем своё доменное имя (FQDN), то самое, по которому с помощью Ingress, будет происходить вход на сервер - `frontend-lab3.artem`

Имеется возможность подписать сертификат тем же ключом, с помощью которого он был создан:


![Подпись](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab3/screen/12.PNG)


* ##### Создание Secret

Необходимо создать секрет командой - `kubectl create secret tls lab3-tls --cert=lab3.crt --key=lab3.key`:


![Секрет](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab3/screen/13.PNG)


* ##### Создание Ingress

Необходимо подключить Ingress в minikube:
`minikube addons enable ingress`
`minikube addons enable ingress-dns`


![ingress](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab3/screen/14.PNG)


![ingress-dns](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab3/screen/15.PNG)


Манифест для Ingress пишем по шаблону из официальной документации:


![Manifest Ingress](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab3/screen/16.PNG)


В полях hosts и host указываем наш FQDN - `frontend-lab3.artem`. Следующим шагом необходимо добавить IP фдресс ingress и FQDN в hosts файл:


![Добавление](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab3/screen/21.PNG)


Далее создаём точку входа в кластере minikube командой - `kubectl create -f frontend-ingress.yaml`:


![Создание ингр](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab3/screen/17.PNG)


Следующим шагом проверяем поялвение Ingress - `kubectl get ingress`:


![Добавление](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab3/screen/18.PNG)


Последней командой - подключаемся к Ingress командой - `minikube tunnel`:


![tunnel](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab3/screen/19.PNG)


Готово, открываем страницу `https://frontend-lab3.artem`. Здесь мы сможем увидеть параметры, переданные ConfigMap:


![web](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab3/screen/22.PNG)


* ##### Диаграмма

Диаграмма нарисована в draw.io


![Диаграмма](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab3/screen/23.PNG)


* ##### Проблемы с которыми столкнулся
При создании сервиса `frontend-service.yaml`, выходила ошибка о том, что такое сервис другого типа уже существует:


![Проблема](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab3/screen/error.PNG)


Решение - необходимо удалить этот сервис с таким же названием, но другого типа:


![Решение](https://github.com/MArtchenko29/2022_2023-introduction_to_distributed_technologies-k4113c-marchenko_a_v/blob/master/lab3/screen/decision.PNG)







