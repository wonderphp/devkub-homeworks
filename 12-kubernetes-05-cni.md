# Домашнее задание к занятию "12.5 Сетевые решения CNI"
После работы с Flannel появилась необходимость обеспечить безопасность для приложения. Для этого лучше всего подойдет Calico.
## Задание 1: установить в кластер CNI плагин Calico
Для проверки других сетевых решений стоит поставить отличный от Flannel плагин — например, Calico. Требования: 
* установка производится через ansible/kubespray;
![image](https://user-images.githubusercontent.com/30965391/153729404-e38123e6-ed56-46e3-81bd-77e390dfcc8f.png)

* после применения следует настроить политику доступа к hello world извне.
![image](https://user-images.githubusercontent.com/30965391/153730655-6ae4e31d-51c3-4cb6-8748-d8d50c51764b.png)
![image](https://user-images.githubusercontent.com/30965391/153730731-231662b2-2463-461f-908f-ce506ebc4a06.png)
![image](https://user-images.githubusercontent.com/30965391/153730758-d119d468-a4ba-40cc-ac97-b2c75631dfc2.png)
![image](https://user-images.githubusercontent.com/30965391/153730844-037c3501-5ef1-48c6-b33d-a670d3847a62.png)



## Задание 2: изучить, что запущено по умолчанию
Самый простой способ — проверить командой calicoctl get <type>. Для проверки стоит получить список нод, ipPool и profile.
Требования: 
* установить утилиту calicoctl;
* получить 3 вышеописанных типа в консоли.
![image](https://user-images.githubusercontent.com/30965391/153747388-e7ee205a-ac75-4549-be4e-910f5f572d99.png)

---

### Как оформить ДЗ?

Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

---
