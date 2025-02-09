# Дисковая подсистема
Домашнее задание (далее ДЗ) по теме Дисковая подсистема
## Создание виртуальной машины
Виртуальная машина создается с помощью Vagrant. Используется Российский репозиторий Vagrant Box.  
Создаем в своем домашнем каталоге директорию под ДЗ и клонируем этот репозиторий из GitHub.
```
  mkdir ~/otus/
  cd ~/otus/
  git clone https://github.com/Mihail-Pavlov-AI/disk_subsystem.git
```
Переходим в каталог и запускаем установку виртуальной машины, используем Vagrant файл. Будет установелена виртуальная машина Ubuntu 22.4 с 4мя дисками для теста с программным RAID.
```
 cd ~/otus/disk_subsystem
 vagrant up 
```
## Подключаемся через терминальную сессию к виртуальной машине
```
 vagrant ssh 
```  
## Создаем RAID 10  
Смотрим какие диски у нас есть
```
 lsblk  
```
![lsblk](https://github.com/user-attachments/assets/7442d072-7f62-4554-920b-ba7fc88669b7)  
Преподаватель предложил немного усложнить задание. Сделать обычный диск, без RAID, положить на него данные и затем использовать остальные диски для добавления в RAID к этому диску. Но так сделать нельзя, сделаем немного по другому, создадим диск с данными, создадим RAID 10 без одного диска, скопируем данные с диска на RAID 10 и затем подключим этот диск к RAID10.

