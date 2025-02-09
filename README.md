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
Создадим диск и скопируем на него данные.  
```
sudo mkfs.ext4 /dev/sdc
sudo mkdir /mnt/01
sudo mount /dev/sdc /mnt/01/
sudo cp -r /var/log/* /mnt/01/
ll /mnt/01/
```
![diskwith data](https://github.com/user-attachments/assets/f520cbd2-44f1-4dfb-912b-4753b8895f95)  
Создадим RAID 10 из оставшихся 3х дисков, файловую систему, монтируем и переносим на него данные с первого диска.  
```
sudo mdadm --create /dev/md177 -l 10 -n 4 /dev/sd{d..f} missing
sudo mdadm -D /dev/md177
```
![raid10](https://github.com/user-attachments/assets/231ee3da-c76f-4473-9d64-62a16753576b)  
```
sudo mkfs.ext4 /dev/md177
sudo mkdir /mnt/02
sudo mount /dev/md177 /mnt/02
sudo cp -r /mnt/01/* /mnt/02/
ll /mnt/02
```
![2025-02-09_15-44-08](https://github.com/user-attachments/assets/4add38d4-6e7e-4cfe-87d6-6ba1463b23cf)  
Отмонтируем первый диск и добавим его в RAID 10
```
sudo umount /mnt/01
sudo mdadm /dev/md177 --add /dev/sdc
sudo mdadm -D /dev/md177
```
![2025-02-09_17-40-04](https://github.com/user-attachments/assets/8849dc82-ffa6-4729-8074-e3efc9819d67)  
Сломаем один диск в рейде, выведем его из RAID, добавим его обратно в RAID.  
```
cat /proc/mdstat
sudo mdadm /dev/md177 --fail /dev/sdf
cat /proc/mdstat
sudo mdadm /dev/md177 --remove /dev/sdf
mdadm -D /dev/md177
```
![2025-02-09_17-56-02](https://github.com/user-attachments/assets/3b576f74-d775-4342-ae0d-78f9d94e3793)  



