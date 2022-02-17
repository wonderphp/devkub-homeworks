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
![image](https://user-images.githubusercontent.com/30965391/154430225-0758f45a-dc79-47fb-9ca0-073056895a5c.png)


* файлы, созданные бекендом, должны быть доступны фронту.  
![image](https://user-images.githubusercontent.com/30965391/154430380-9b826bd4-aee6-4554-85b4-6a9678efc66a.png)

---
Манифесты:
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend
  namespace: production
  labels:
    app: frontend
spec:

  replicas: 1
  selector:
    matchLabels:
      app: frontend
  template:
    metadata:
      labels:
        app: frontend

    spec:
      containers:
      - name: front
        image: kopilka.ga:4443/my-ubuntu
        imagePullPolicy: IfNotPresent
        command: ["/bin/bash", "-c"]
        args: ["while true; do sleep 30; done;"]
        volumeMounts:
         - mountPath: /f-b-share
           name: vol

        env:
        - name: "PORT"
          value: "80"
      volumes:
        - name: vol
          persistentVolumeClaim:
            claimName: nfs-claim
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: backend
  namespace: production
  labels:
    app: backend
spec:

  replicas: 1
  selector:
    matchLabels:
      app: backend
  template:
    metadata:
      labels:
        app: backend

    spec:
      containers:
      - name: back
        image: kopilka.ga:4443/my-ubuntu
        imagePullPolicy: IfNotPresent
        command: ["/bin/bash", "-c"]
        args: ["while true; do sleep 30; done;"]
        volumeMounts:
         - mountPath: /f-b-share
           name: vol

        env:
        - name: "PORT"
          value: "80"
      volumes:
        - name: vol
          persistentVolumeClaim:
            claimName: nfs-claim
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
      name: nfs-claim
      namespace: production
spec:
  storageClassName: "nfs"
  accessModes:
        - ReadWriteMany:
  resources:
      requests:
        storage: 100Mi
```

---
---
**Как проходило выполнение ДЗ**  
Ужас.  нарезал диски для Vbox воркеров по 10гб. Это была ошибка. Планировщик кубера, по маркеру diskpressure начинал выселять поды.
Остановил все ВМ, переместил часть файлов дисков воркеров на ssd 30gb(раритет))) включил кеш IO, изменил размер. Далее sudo -E gparted через проброс X11 изменил партицию.
потом lvdisplay -получить путь лог волума, потом lvresize -L +1G путь волума(можно несколько раз пока не поширится до краев), потом resize2fs и бинго!

Из-за разного рода ошибок, например таких как не указания  флага  imagePullPolicy: IfNotPresent поймал лимит скачиваний с docker.io
Обходное решение:
Применять  imagePullPolicy: IfNotPresent + установил docker registry kopilkga.ga:4443 пробросил ее, далее пулю образы, тегирую на свой репозиторий, пушаю к себе, далее в манифестах использую уже свой репо.
Возможно пригодидтся для ДЗ или диплома где нужно взять дженкинс, для ci/cd

---
### Как оформить ДЗ?

Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

---
