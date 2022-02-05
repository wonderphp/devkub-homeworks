# Домашнее задание к занятию "12.2 Команды для работы с Kubernetes"
Кластер — это сложная система, с которой крайне редко работает один человек. Квалифицированный devops умеет наладить работу всей команды, занимающейся каким-либо сервисом.
После знакомства с кластером вас попросили выдать доступ нескольким разработчикам. Помимо этого требуется служебный аккаунт для просмотра логов.

## Задание 1: Запуск пода из образа в деплойменте
Для начала следует разобраться с прямым запуском приложений из консоли. Такой подход поможет быстро развернуть инструменты отладки в кластере. Требуется запустить деплоймент на основе образа из hello world уже через deployment. Сразу стоит запустить 2 копии приложения (replicas=2). 

Требования:
 * пример из hello world запущен в качестве deployment
 * количество реплик в deployment установлено в 2
  ![image](https://user-images.githubusercontent.com/30965391/152504703-7101433f-3283-4cfd-b164-d2f8becdb512.png)

 * наличие deployment можно проверить командой kubectl get deployment
 ![image](https://user-images.githubusercontent.com/30965391/152504775-4b99e7df-fbf1-4252-ad92-fcbc8e3a0d7a.png)

 * наличие подов можно проверить командой kubectl get pods
 
  на первом скрине

## Задание 2: Просмотр логов для разработки
Разработчикам крайне важно получать обратную связь от штатно работающего приложения и, еще важнее, об ошибках в его работе. 
Требуется создать пользователя и выдать ему доступ на чтение конфигурации и логов подов в app-namespace.
![image](https://user-images.githubusercontent.com/30965391/152552402-e45d0263-ec3f-4cc0-b47a-19d2a3cd5475.png)

Требования: 
 * создан новый токен доступа для пользователя
kubectl config set-credentials viewer --token=$TOKEN
$TOKEN получил
kubectl get secret viewer-token-ggxcp -o yaml

 * пользователь прописан в локальный конфиг (~/.kube/config, блок users)
 ![image](https://user-images.githubusercontent.com/30965391/152551992-966b13c5-4b48-4376-a407-026464220769.png)

 * пользователь может просматривать логи подов и их конфигурацию (kubectl logs pod <pod_id>, kubectl describe pod <pod_id>)
![image](https://user-images.githubusercontent.com/30965391/152551784-f2c223e9-ae0f-4f3f-963a-2fb51e995b74.png)
+созданы еще разные роли и забинжены. Очистил скрин уже не сниму как делал.

**ДОРАБОТКА**  
 еще раз прочитал ДЗ - не показал что можно логи читать пользователю viewer
 ![image](https://user-images.githubusercontent.com/30965391/152658576-10c10390-a79f-4b1a-af5b-14a04f391735.png)
Рад что работает.

![image](https://user-images.githubusercontent.com/30965391/152658033-9cb2bde8-4e0e-4451-8ef9-b37c4aa74da7.png)
Биндил так:
![image](https://user-images.githubusercontent.com/30965391/152658158-2ed5e349-73af-4d36-ae21-990fd0bfc1da.png)
Часть команд сохарнилась, часть видимо с пробелом в начале была
![image](https://user-images.githubusercontent.com/30965391/152658219-8354341b-fb82-4838-85d9-58fc31805ae8.png)
![image](https://user-images.githubusercontent.com/30965391/152658265-652417db-4d12-4fe4-bedb-a75be239a5e4.png)
![image](https://user-images.githubusercontent.com/30965391/152658275-2c67ed8c-dab0-485d-9da0-f92deea18fe5.png)



```
pixel@kopilka:~$ kubectl get ClusterRole  system:node -n default -o json
{
    "apiVersion": "rbac.authorization.k8s.io/v1",
    "kind": "ClusterRole",
    "metadata": {
        "annotations": {
            "rbac.authorization.kubernetes.io/autoupdate": "true"
        },
        "creationTimestamp": "2022-02-03T23:09:40Z",
        "labels": {
            "kubernetes.io/bootstrapping": "rbac-defaults"
        },
        "name": "system:node",
        "resourceVersion": "99",
        "uid": "ad98c3e3-210f-4693-81e9-3fd687c11cea"
    },
    "rules": [
        {
            "apiGroups": [
                "authentication.k8s.io"
            ],
            "resources": [
                "tokenreviews"
            ],
            "verbs": [
                "create"
            ]
        },
        {
            "apiGroups": [
                "authorization.k8s.io"
            ],
            "resources": [
                "localsubjectaccessreviews",
                "subjectaccessreviews"
            ],
            "verbs": [
                "create"
            ]
        },
        {
            "apiGroups": [
                ""
            ],
            "resources": [
                "services"
            ],
            "verbs": [
                "get",
                "list",
                "watch"
            ]
        },
        {
            "apiGroups": [
                ""
            ],
            "resources": [
                "nodes"
            ],
            "verbs": [
                "create",
                "get",
                "list",
                "watch"
            ]
        },
        {
            "apiGroups": [
                ""
            ],
            "resources": [
                "nodes/status"
            ],
            "verbs": [
                "patch",
                "update"
            ]
        },
        {
            "apiGroups": [
                ""
            ],
            "resources": [
                "nodes"
            ],
            "verbs": [
                "patch",
                "update"
            ]
        },
        {
            "apiGroups": [
                ""
            ],
            "resources": [
                "events"
            ],
            "verbs": [
                "create",
                "patch",
                "update"
            ]
        },
        {
            "apiGroups": [
                ""
            ],
            "resources": [
                "pods"
            ],
            "verbs": [
                "get",
                "list",
                "watch"
            ]
        },
        {
            "apiGroups": [
                ""
            ],
            "resources": [
                "pods"
            ],
            "verbs": [
                "create",
                "delete"
            ]
        },
        {
            "apiGroups": [
                ""
            ],
            "resources": [
                "pods/status"
            ],
            "verbs": [
                "patch",
                "update"
            ]
        },
        {
            "apiGroups": [
                ""
            ],
            "resources": [
                "pods/eviction"
            ],
            "verbs": [
                "create"
            ]
        },
        {
            "apiGroups": [
                ""
            ],
            "resources": [
                "configmaps",
                "secrets"
            ],
            "verbs": [
                "get",
                "list",
                "watch"
            ]
        },
        {
            "apiGroups": [
                ""
            ],
            "resources": [
                "persistentvolumeclaims",
                "persistentvolumes"
            ],
            "verbs": [
                "get"
            ]
        },
        {
            "apiGroups": [
                ""
            ],
            "resources": [
                "endpoints"
            ],
            "verbs": [
                "get"
            ]
        },
        {
            "apiGroups": [
                "certificates.k8s.io"
            ],
            "resources": [
                "certificatesigningrequests"
            ],
            "verbs": [
                "create",
                "get",
                "list",
                "watch"
            ]
        },
        {
            "apiGroups": [
                "coordination.k8s.io"
            ],
            "resources": [
                "leases"
            ],
            "verbs": [
                "create",
                "delete",
                "get",
                "patch",
                "update"
            ]
        },
        {
            "apiGroups": [
                "storage.k8s.io"
            ],
            "resources": [
                "volumeattachments"
            ],
            "verbs": [
                "get"
            ]
        },
        {
            "apiGroups": [
                ""
            ],
            "resources": [
                "serviceaccounts/token"
            ],
            "verbs": [
                "create"
            ]
        },
        {
            "apiGroups": [
                ""
            ],
            "resources": [
                "persistentvolumeclaims/status"
            ],
            "verbs": [
                "get",
                "patch",
                "update"
            ]
        },
        {
            "apiGroups": [
                "storage.k8s.io"
            ],
            "resources": [
                "csidrivers"
            ],
            "verbs": [
                "get",
                "list",
                "watch"
            ]
        },
        {
            "apiGroups": [
                "storage.k8s.io"
            ],
            "resources": [
                "csinodes"
            ],
            "verbs": [
                "create",
                "delete",
                "get",
                "patch",
                "update"
            ]
        },
        {
            "apiGroups": [
                "node.k8s.io"
            ],
            "resources": [
                "runtimeclasses"
            ],
            "verbs": [
                "get",
                "list",
                "watch"
            ]
        }
    ]
}
```

## Задание 3: Изменение количества реплик 
Поработав с приложением, вы получили запрос на увеличение количества реплик приложения для нагрузки. Необходимо изменить запущенный deployment, увеличив количество реплик до 5. Посмотрите статус запущенных подов после увеличения реплик. 

Требования:
 * в deployment из задания 1 изменено количество реплик на 5
 * проверить что все поды перешли в статус running (kubectl get pods)
![image](https://user-images.githubusercontent.com/30965391/152552844-82ee15c7-4d07-4a9d-aec4-2b8efaabcc5e.png)

---

### Как оформить ДЗ?

Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

---
