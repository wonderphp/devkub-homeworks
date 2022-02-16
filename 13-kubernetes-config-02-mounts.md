# Домашнее задание к занятию "13.2 разделы и монтирование"
Приложение запущено и работает, но время от времени появляется необходимость передавать между бекендами данные. А сам бекенд генерирует статику для фронта. Нужно оптимизировать это.
Для настройки NFS сервера можно воспользоваться следующей инструкцией (производить под пользователем на сервере, у которого есть доступ до kubectl):
* установить helm: curl https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | bash
* добавить репозиторий чартов: helm repo add stable https://charts.helm.sh/stable && helm repo update
* установить nfs-server через helm: helm install nfs-server stable/nfs-server-provisioner
![image](https://user-images.githubusercontent.com/30965391/154330482-1aa174e8-670e-47b1-b638-47d12f94039b.png)

В конце установки будет выдан пример создания PVC для этого сервера.

## Задание 1: подключить для тестового конфига общую папку
В stage окружении часто возникает необходимость отдавать статику бекенда сразу фронтом. Проще всего сделать это через общую папку. Требования:
* в поде подключена общая папка между контейнерами (например, /static);  
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: front-back
  namespace: stage

spec:
 replicas: 1
 selector:
     matchLabels:
       app: front-web
 template:
  metadata:
      labels:
        app: front-web
  spec:
    containers:
    - name: front
      image: "us-docker.pkg.dev/google-samples/containers/gke/hello-app:2.0"
      imagePullPolicy: IfNotPresent
      volumeMounts:
              - mountPath: /f-b-share
                name: share-disk
                readOnly: true
    - name: back
      image: "us-docker.pkg.dev/google-samples/containers/gke/hello-app:2.0"
      imagePullPolicy: IfNotPresent
      volumeMounts:
              - mountPath: /f-b-share
                name: share-disk
      env:
      - name: "PORT"
        value: "81"

    volumes:
        - name: share-disk
          emptyDir: { }
```
* после записи чего-либо в контейнере с беком файлы можно получить из контейнера с фронтом.  
![image](https://user-images.githubusercontent.com/30965391/154251548-3c95db4b-3586-4f16-bda2-bdda42607084.png)


## Задание 2: подключить общую папку для прода
Поработав на stage, доработки нужно отправить на прод. В продуктиве у нас контейнеры крутятся в разных подах, поэтому потребуется PV и связь через PVC. Сам PV должен быть связан с NFS сервером. Требования:
* все бекенды подключаются к одному PV в режиме ReadWriteMany;  

* фронтенды тоже подключаются к этому же PV с таким же режимом;


* файлы, созданные бекендом, должны быть доступны фронту.  


---

### Как оформить ДЗ?

Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

---
