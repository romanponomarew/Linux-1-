1)Задание 1 – Написать сервис, который будет раз в 30 секунд мониторить лог на предмет наличия ключевого слова. Файл и слово должны задаваться в /etc/sysconfig

.Для начала создаём файл с конфигурацией для сервиса в директории
/etc/sysconfig - из неё сервис будет брать необходимые переменные(SentOS)

Что такое service?
Это некоторая программа, которая выполняется в фоне и предоставляет полезную функциональность. К примеру, Apache веб сервер. 
Сервисы можно запускать и останавливать. Некоторые сервисы могут запускаться и останавливаться автоматически по определенным 
событиям (загрузка ОС, выгрузка ОС и тп). Так же их можно запускать/останавливать вручную. Сервис декларируется в 
/etc/systemd/system/my-name.service файлах (с суффиксом “.service”).

Systemd позволяет менять настройки сервисов без модификации оригинальных файлов. Для этого надо:

Создать директорию ниже «/etc/systemd/system/» или в «/lib/systemd/system/» названную «${unit}.d/»
Создать файл <something>.conf в «${unit}.d/» директории, содержащий параметры директив которые вы хотите обновить.

		

![Alt text](https://github.com/romanponomarew/Linux-1-/blob/master/LABA1/Screenshots/config_file(1).jpg)

.Затем создаем /var/log/watchlog.log и пишем туда строки на своё усмотрение,
плюс ключевое слово ‘ALERT’
		
![Alt text](https://github.com/romanponomarew/Linux-1-/blob/master/LABA1/Screenshots/watchlog_log(2).jpg)

.Создадим скрипт:
		
![Alt text](https://github.com/romanponomarew/Linux-1-/blob/master/LABA1/Screenshots/script(3).jpg)
Команда logger отправляет лог в системный журнал


.Создадим юниты для сервиса и для таймера:
		
![Alt text](https://github.com/romanponomarew/Linux-1-/blob/master/LABA1/Screenshots/Units(5).jpg)


Затем достаточно только запустить timer:
[root@nginx ~#] systemctl start watchlog.timer
И убедиться в результате:
[root@nginx ~#]tail -f /var/log/messages
		
![Alt text](https://github.com/romanponomarew/Linux-1-/blob/master/LABA1/Screenshots/start1(5%2C5).png)
![Alt text](https://github.com/romanponomarew/Linux-1-/blob/master/LABA1/Screenshots/start2(5%2C8).png)


2)Задание 2 – Из epel установить spawn-fcgi и переписать init-скрипт на unit-файл. Имя сервиса должно также называться.
		
![Alt text](https://github.com/romanponomarew/Linux-1-/blob/master/LABA1/Screenshots/2task.png)
		
		
![Alt text](https://github.com/romanponomarew/Linux-1-/blob/master/LABA1/Screenshots/spawn-fcgi(6).jpg)
		
Юнит-файл:
![Alt text](https://github.com/romanponomarew/Linux-1-/blob/master/LABA1/Screenshots/Spawn-service(7).jpg)
![Alt text](https://github.com/romanponomarew/Linux-1-/blob/master/LABA1/Screenshots/final2.png)
		
2)Задание 3 – Дополнить юнит-файл apache httpd возможностью запустить несколько инстансов сервера с разными конфигами.
			
![Alt text](https://github.com/romanponomarew/Linux-1-/blob/master/LABA1/Screenshots/httpd_service(8).jpg)
![Alt text](https://github.com/romanponomarew/Linux-1-/blob/master/LABA1/Screenshots/environment.png)
		
![Alt text](https://github.com/romanponomarew/Linux-1-/blob/master/LABA1/Screenshots/httpd_first(9).jpg)
![Alt text](https://github.com/romanponomarew/Linux-1-/blob/master/LABA1/Screenshots/httpd_second(10).jpg)
![Alt text](https://github.com/romanponomarew/Linux-1-/blob/master/LABA1/Screenshots/second_conf(11).jpg)
		
	

