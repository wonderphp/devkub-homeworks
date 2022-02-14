# Домашнее задание к занятию "13.1 контейнеры, поды, deployment, statefulset, services, endpoints"
Настроив кластер, подготовьте приложение к запуску в нём. Приложение стандартное: бекенд, фронтенд, база данных (пример можно найти в папке 13-kubernetes-config).

## Задание 1: подготовить тестовый конфиг для запуска приложения
Для начала следует подготовить запуск приложения в stage окружении с простыми настройками. Требования:
* под содержит в себе 3 контейнера — фронтенд, бекенд, базу;  
![image](https://user-images.githubusercontent.com/30965391/153847432-84bfddcb-0b92-47c6-98b1-7a8fc2defb4f.png)  

* регулируется с помощью deployment фронтенд и бекенд;  
![image](https://user-images.githubusercontent.com/30965391/153847564-7a5ee85f-6a5a-4983-ad5a-66c835f9068b.png)  
![image](https://user-images.githubusercontent.com/30965391/153847682-c28ae12c-a719-4c3b-afec-2f97c422baa2.png)  
Представим что 2 штуки образа hello world это и есть фронт и бэк. Поселены на разные порты:  
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: front-back-db
  namespace: stage

spec:
  replicas: 1
  selector:
    matchLabels:
      app: front-back
  template:
    metadata:
      labels:
        app: front-web
        back: back-web
spec:
   selector:
    containers:
       template:
         metadata:
          labels:
            app: front-web
            back: back-web
      - name: front
        image: "us-docker.pkg.dev/google-samples/containers/gke/hello-app:2.0"
      - name: back
        image: "us-docker.pkg.dev/google-samples/containers/gke/hello-app:2.0"
        env:
        - name: "PORT"
        value: "81"
```

* база данных — через statefulset.
![image](https://user-images.githubusercontent.com/30965391/153850127-928caf02-3b84-4c97-9f27-2a53cab505aa.png)  
![image](https://user-images.githubusercontent.com/30965391/153850348-abffec84-5da7-4482-9782-d6d0868311cf.png)  
![image](https://user-images.githubusercontent.com/30965391/153851672-8543e730-8817-4be2-833b-287b6e282b3e.png)


## Задание 2: подготовить конфиг для production окружения
Следующим шагом будет запуск приложения в production окружении. Требования сложнее:
* каждый компонент (база, бекенд, фронтенд) запускаются в своем поде, регулируются отдельными deployment’ами;
* для связи используются service (у каждого компонента свой);
* в окружении фронта прописан адрес сервиса бекенда;
* в окружении бекенда прописан адрес сервиса базы данных.

## Задание 3 (*): добавить endpoint на внешний ресурс api
Приложению потребовалось внешнее api, и для его использования лучше добавить endpoint в кластер, направленный на это api. Требования:
* добавлен endpoint до внешнего api (например, геокодер).

---

### Как оформить ДЗ?

Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

В качестве решения прикрепите к ДЗ конфиг файлы для деплоя. Прикрепите скриншоты вывода команды kubectl со списком запущенных объектов каждого типа (pods, deployments, statefulset, service) или скриншот из самого Kubernetes, что сервисы подняты и работают.

---
