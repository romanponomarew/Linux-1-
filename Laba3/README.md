
#1)Посмотрим какие дисковые устройства и партиции есть в системе

	.lsblk - выводит список блочных устройств
	  Блочное устройство (block device) — вид файла устройств в UNIX/Linux-системах, обеспечивающий интерфейс к устройству, реальному или виртуальному,
 в      виде файла в файловой системе.
 	  С блочным устройством обеспечивается обмен данными блоками данных. Как правило, это устройства произвольного доступа, 
то      есть можно указать, из какого именно места должен быть прочитан или записан блок данных. Данные при чтении или записи на блочное устройство буферизуются.
	Типичные примеры блочных устройств: жёсткий диск, CD-ROM, НГМД.
	roman@romanPC:/etc$ lsblk
		sda      8:0    0  24,6G  0 disk 
		    └─sda1   8:1    0  24,6G  0 part /
	Какой размер дисков? = 24.6G
	Есть ли неразмеченное место на дисках? = нет
	Какой размер партиций? = 24.6G(1 партиция - sda1)
	Какая таблица партционирования используется? - 
	Какой диск, партция или лвм том смонтированы в / - sda1 

	.для просмотра UUID можно использовать утилиту blkid. Её необходимо запускать от имени суперпользователя, 
	но она показывает информацию только по нужному вам разделу. Например, чтобы посмотреть UUID раздела /dev/sda6 выполните: sudo blkid /dev/sda6
	
	.fdisk высвечивает информацию о разделах диска. Кроме того, fdisk создает и удаляет разделы диска, меняет активный раздел. 
	fdisk представляет расширенный набор функций по сравнению с одноименной командой MS-DOS. 
	fdisk обычно используется с меню в интерактивном режиме.
	Жесткий диск может иметь самое большее четыре раздела. Но в данный момент может быть активным только один раздел. 
	Коль скоро раздел сделан активным, операционная система этого раздела загружается автоматически при остановке текущей операционной системы.	
	
	.Часто нужно добавлять, удалять или изменять размер разделов, управлять их флагами или конвертировать файловые системы. 
	Для этого в Linux есть достаточно различных инструментов как с графическим интерфейсом, так и консольных.(parted -l)
		
![Alt text](https://github.com/romanponomarew/Linux-1-/blob/master/Laba3/Screenshots/lsblk.png)
		
#2)Создадим сжатую файловую систему для чтения squashfs:
	 Когда создаются маленькие и встроенные Linux-системы, каждый байт запоминающего устройства (дискета, флэш-карта и т. д.) очень важен, 
	поэтому сжатие используется везде, где возможно. Также, сжатые файловые системы часто используются для целей архивирования. 
	Для огромных общих архивов, так же, как и для персональных медиа-архивов, это насущно необходимо.
	ShquashFS выводит всё это на новый уровень. Это файловая система только для чтения, которая позволяет вам сжимать целые файловые 
	системы или отдельные директории, переписывать их в другие устройства/разделы или в обычные файлы, а затем устанавливать 
	их непосредственно (если это устройство) или использовать закольцовку устройства (в случае с файлом). Модульный, компактный системный 
	дизайн SquashFS блестящ. Для целей архивирования SquashFS предоставляет вам гораздо большую гибкость и скорость исполнения, чем архивы .tar.gz.
	
Commands:
	git clone https://gitlab.com/erlong15/mai.git
	mksquashfs mai/* mai.sqsh
	sudo mkdir /mnt/mai
	sudo mount mai.sqsh /mnt/mai -t squashfs -o loop

![Alt text](https://github.com/romanponomarew/Linux-1-/blob/master/Laba3/Screenshots/squashfs.png)
		
## 3)Посмотрим информацию по файловым системам смонтированным в системе

```bash
df -h
df -i
mount
```
* Какая файловая система примонтирована в /  (/dev/sda1 on / type ext4 (rw,relatime,errors=remount-ro) )
* С какими опциями примонтирована файловая система в /  (rw,relatime,errors=remount-ro)
* Какой размер файловой системы приментированной в /mnt/mai  (128K)

![Alt text](https://github.com/romanponomarew/Linux-1-/blob/master/Laba3/Screenshots/FileSystem.png)

---

## 4)Попробуем создать файлик в каталоге /dev/shm
https://losst.ru/komanda-dd-linux
		dd if=/dev/zero of=/dev/shm/mai bs=1M count=100
  Довольно часто системным администраторам приходится копировать различные двоичные данные. Например, иногда может понадобиться сделать резервную 
копию жесткого диска, создать пустой файл, заполненный нулями для организации пространства подкачки или другой виртуальной файловой системы.
  Для решения всех этих задач используется утилита dd linux, которая просто выполняет копирование данных из одного места в другое на двоичном уровне. 
Она может скопировать CD/DVD диск, раздел на диске или даже целый жесткий диск. В этой статье мы рассмотрим что из себя 
представляет команда dd linux, основные ее опции и параметры, а также как ею пользоваться.

  Фактически, это аналог утилиты копирования файлов cp только для блочных данных. Утилита просто переносит по одному блоку данных указанного размера с 
одного места в другое. Поскольку в Linux все, в том числе, устройства, считается файлами, вы можете переносить устройства в файлы и наоборот.
С помощью различных опций утилиты можно повлиять на размер блока, а это, в свою очередь, уже влияет на скорость работы программы.
  Синтаксис утилиты достаточно необычен, но в то же время очень прост, после того как вы его запомните и привыкнете:

			$ dd if=источник_копирования of=место_назначения параметры
С помощью параметра if вам нужно указать источник, откуда будут копироваться блоки, это может быть устройство, например, /dev/sda или файл - disk.img. 
Дальше, с помощью параметра of необходимо задать устройство или файл назначения. Другие параметры имеют такой же синтаксис, как if и of. 


		Команда free предоставляет информацию об использованной и неиспользованной памяти, а так же о разделе подкачки (swap). 
		Вывод системной памяти:
		Если требуется узнать какое количество памяти и свопа используется, наберите команду free в терминале. Информация отобразится в килобайтах.

```bash
dd if=/dev/zero of=/dev/shm/mai bs=1M count=100
free -h
rm -f /dev/shm/mai
free -h
```
* Что такое tmpfs?  -  Tmpfs — временное файловое хранилище во многих Unix-подобных ОС. Предназначена для монтирования файловой системы, но размещается в 
			ОЗУ вместо физического диска. Подобная конструкция является RAM-диском. 
			Все данные в Tmpfs являются временными, в том смысле, что ни одного файла не будет создано на жёстком диске. 
			После перезагрузки все данные, содержащиеся в Tmpfs, будут утеряны.
* какая часть памяти изменялась?	((free, shared, buff, available) не swap и не та часть которая используется)

![Alt text](https://github.com/romanponomarew/Linux-1-/blob/master/Laba3/Screenshots/free-h.png)

---

## 5)Изучим процессы запущенные в системе

```bash
ps -eF
ps rx 
ps -e --forest
ps -efL
```

* Какие процессы в системе порождают дочерние процессы через fork - Все новые процессы в Linux порождаются клонированием какого-то уже имеющегося процесса, с 
	помощью вызова системных функций clone(2) и fork(2) (от forking - порождение). У нового (порожденного или дочернего) процесса 
	тоже окружение, что и у родителя, отличается только номер ID процесса (т.н. PID). 
* Какие процессы в системе являются мультитредовыми - 

---

## 6)Разберитесь что делает команда

```bash
ps axo rss | tail -n +2|paste -sd+ | bc
```

tail -n +2 = выводить указанное количество строк из конца файла

rss - переводится как постоянное потребление памяти (Resident Set Size) и показывает сколько в момент вывода команды занято в оперативной памяти.
ps -a - выбрать все процессы, кроме фоновых;
ps -o, (o) - позволяет определить свой формат вывода;
paste - Это очень простая команда, всего с двумя опциями, использующаяся для вывода бок о бок строк из двух и более файлов.
paste  -s, --serial - вместо того, чтобы выводить строки из файлов рядом друг с другом, они выводятся последовательно.
paste  -d, --delimiters=... - сменить разделитель TAB на другой (список) (+)
Команда bc используется для калькулятора командной строки. Это похоже на базовый калькулятор, 
	с помощью которого мы можем сделать основные математические вычисления.	

* Что подсчитывается этой командой
	ps axo rss - вывести постоянное потребление памяти активных процессов, затем tail -n +2  - вывести последние 2 строки, paste -sd + - вывести данные последовательно разделив их плюсом, bc - сложить полученные данные
	В итоге, вывести получившийся результат
* Почему цифра такая странная - сумма в килобайтах

---

## 7)Уставновим утилиту smem и проанализируем параметр PSS в ней
```bash
apt-get install smem -y
smem
```
* утилита работает какое то время, можно оставить ее в другом терминале

![Alt text](https://github.com/romanponomarew/Linux-1-/blob/master/Laba3/Screenshots/smem.png)

---

## 8)Запустим приложеннный скрипт и понаблюдаем за процессами
```bash
python myfork.py
```
* в другом терминале  отследите порождение процессов
		
![Alt text](https://github.com/romanponomarew/Linux-1-/blob/master/Laba3/Screenshots/zombie.png)
		
* отследите какие состояния вы видите у процессов:
		статус запуска процесса файлa myfork.py = s
		статус статус порожденных(child) процессов = zombie
* почему появляются процессы со статусам Z
		myfork.py порождает дочерние процессы, которые завершаются после вызова
		Проце́сс-зо́мби, зо́мби (англ. zombie process, англ. defunct process) — дочерний процесс в Unix-системе, завершивший своё выполнение, 
		но ещё присутствующий в списке процессов операционной системы, чтобы дать родительскому процессу считать код завершения.
	 
* какой PID у основного процесса
		10671
* убейте основной процесс ```kill -9 <pid>```
* какой PPID стал у первого чайлда
		child процессы удалены вместе c родительским и поэтому не отображаются через ps -efl
* насколько вы разобрались в скрипте и втом что он делает?
		переменная pid меняется в цикле с помощью функции os.fork() и каждую итерацию выводится на экран. = child's PID

---

## 9)Научимся корректно завершать зомби процессы
* запустим еще раз наш процесс
* убьем процесс первого чайлда
		
![Alt text](https://github.com/romanponomarew/Linux-1-/blob/master/Laba3/Screenshots/killchild1.png)
		
* проверим его состояние  и убедимся что он зомби
		
![Alt text](https://github.com/romanponomarew/Linux-1-/blob/master/Laba3/Screenshots/child1.png)
		
* остановим основной процесс
* расскоментируем строки в скрипте
```python
      pid, status = os.waitpid(pid, 0)
      print("wait returned, pid = %d, status = %d" % (pid, status))
```
* поторим все еще раз
* отследим корректное завершение чайлда
		
![Alt text](https://github.com/romanponomarew/Linux-1-/blob/master/Laba3/Screenshots/killchild2.png)
		

---
## 10)Научимся убивать зомби процессы
* запускаем процессс еще раз
```bash
gdb
> attach <parent_PID>
> call waitpid(zombie_PID, 0, 0) wait

> detach
> quit
```
![Alt text](https://github.com/romanponomarew/Linux-1-/blob/master/Laba3/Screenshots/error.png)

---
## 11)Проблемы при отмонтировании директории
```bash
cd /mnt/mai
# в другом терминале
sudo umount /mnt/mai

fuser -v /mnt/dir
```

![Alt text](https://github.com/romanponomarew/Linux-1-/blob/master/Laba3/Screenshots/mount.png)
* Напишите какие процессы мешают размонтировать директорию
* посмотрите ```lsof -p <pid>``` этих процессов
* убейте мешаюший процесс и размонтируйте директорию



---

## 12)Решаем загадку исчезновения места на диске
* создадим директорию ~/myfiles
* запустим файл test_write.py из ранее созданной директории
* проверим в другом терминале что в этой директории создался файл и он увеличивается в размере
* в первом терминали нажмем Ctrl+Z
* проверим статус файла
* выполним команды
```bash
jobs -l
fg
```
* eще раз остановим  процесс черерз CTRL+Z
* выполним команду ```bg```
* проверим размер файловой системы и каталога
```bash
df 
du -sh  ~/myfiles
```
* удалим файл
* повторно проверим размеры кталога и файловой системы
* Какую систуацию вы видите, как вы это объясните
* Подключитесь к процессу через ```strace -p <pid>``` и назовите дескриптор файла, куда пишет процесс
* проверим какие файлы открыты у нашего процесса через команду lsof -p <pid>
* убьем процесс
* еще раз прорим размер файловой системы и каталога
* Напишите свое объяснение, что произошло

		после удаления файла значение usage увеличилось на 4%, но процесс который записывает в память все еще жив и занимает память

---

## 13)Утилиты наблюдения
* C помощью утилит мониторинга 
* проверьте текущий LA 
* запишите top 3 процессов загружающих CPU
* запишите top 3 процессов загружающих память 
* запустите утилиту atop как сервис через systemd
* запустите dd на генерацию файла размер в 3 гигабайта
* удалите сгенеренный файл
* через atop скажите какой  pid был у процесса
* Проанализируйте нагрузку на диск через утилиты  iotop и iostat

![Alt text](https://github.com/romanponomarew/Linux-1-/blob/master/Laba3/Screenshots/nabludenie.png)





	
