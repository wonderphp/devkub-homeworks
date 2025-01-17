# Домашнее задание к занятию "12.3 Развертывание кластера на собственных серверах, лекция 1"
Поработав с персональным кластером, можно заняться проектами. Вам пришла задача подготовить кластер под новый проект.

## Задание 1: Описать требования к кластеру
Сначала проекту необходимо определить требуемые ресурсы. Известно, что проекту нужны база данных, система кеширования, а само приложение состоит из бекенда и фронтенда. Опишите, какие ресурсы нужны, если известно:

* база данных должна быть отказоустойчивой (не менее трех копий, master-slave) и потребляет около 4 ГБ ОЗУ в работе;
* кэш должен быть аналогично отказоустойчивый, более трех копий, потребление: 4 ГБ ОЗУ, 1 ядро;
* фронтенд обрабатывает внешние запросы быстро, отдавая статику: не более 50 МБ ОЗУ на каждый экземпляр;
* бекенду требуется больше: порядка 600 МБ ОЗУ и по 1 ядру на копию.

Требования: опишите, сколько нод в кластере и сколько ресурсов (ядра, ОЗУ, диск) нужно для запуска приложения. Расчет вести из необходимости запуска 5 копий фронтенда и 10 копий бекенда, база и кеш.

ответ

База:
primary+standby *3 = 6шт, 6*4=24ГБ на круг, на кластер бд 8Гб 3 ноды. +1гб на ноду для нужд dockerd. Диски от 100гб на ноду
8 ядер на инстанс.

Кэш:
3шт*4гб =12гб+ 1гб*4 на нужды докера
3 ядра

Фронтенд:
5 инстансов по 1гб на ноду, лиски по 10гб может быть в одном кластере

бэкэнд
10*1гб для подов,+10*гб для нужд докера диски по 10гб, 10 ядер.

![image](https://user-images.githubusercontent.com/30965391/152577312-c7e82cb5-1ad8-4c54-a737-2996e8ac41b8.png)

---

**Дораоботка**

Давайте исходить из того, что мы не контролируем как распределятся поды между нодами.

Посчитайте общую потребность приложений.  

1ГБ RAM на ноду Бэкэнда *10 **= 10ГБ RAM** 500гб диск, 10 ядер  
Кэш 4ГБ RAM *3 = **12Гб RAM**, 500гб диск, 6 ядер  
БД 4 *6 **= 24Гб** 600гб дииск, 64 ядра  
Фронтэнд-1Гб * 5 **= 5ГБ RAM**, 500Гб, 5 Ядер  

Добавьте 30-40% для надежности.   

Бэкэнд **= 16ГБ RAM** 800Гб диск, 14 ядер  
Кэш-ноды **12Гб RAM**, 800Гб диск, 8 ядер  
БД  **= 32Гб** 1Тб дииск, 64 ядра  
Фронтэнд-1Гб * 5 **= 5ГБ RAM**, 500Гб, 8 Ядер  

Распределите потребность между нодами.  

Нода бэкэда **= 16ГБ RAM** 800Гб диск, 14 ядер  
Кэш-нода **12Гб RAM**, 800Гб диск, 8 ядер  
Фронтэнд-1Гб * 5 **= 4ГБ RAM**, 500Гб, 8 Ядер  

БД - не в кубере.  
памяти и дисков хватит что бы еще проксировать.  

Добавьте потребность нод.
Так вы получите характеристики нод.

---

**Дораоботка2**

1 Нода бэкэда **= 16ГБ RAM** 800Гб диск, 14 ядер  
2 Кэш-нода **16Гб RAM**, 800Гб диск, 8 ядер  
3 Фронтэнд-~1Гб * 5 **= 4ГБ RAM**, 500Гб, 8 Ядер  
4 БД Нода1 прод  **= 16Гб** 0.5Тб диск, 32 ядра  
5 БД Нода2 стендбай **= 16Гб** 0.5Тб диск, 32 ядра  
6 Пусть отдельно для фронт/бэка/кэш: control-plane 2gb 2cpu  50gb диск  
7 Пусть отдельно для БД: control-plane 2gb 2cpu 50gb диск  
8 Еще одна  control-plane для устойчивости etcd  2gb 2cpu 50gb диск  

итого надо 74GB 3250gb диски и 100CPU

---

### Как оформить ДЗ?

Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

---
