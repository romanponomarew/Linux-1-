1)Создаем пользователей:
	useradd -m -s /bin/bash user1
	useradd -m -s /bin/bash user2 
	
	.Какие UID создались у пользователей?(посмотреть можно с помощью команды id или в файле /etc/passwd):
	  screenshot(UID_users.png)
	  UID_user1 = 1001 (user1:x:1001:1001::/home/user1:/bin/sh)
	  UID_user2 = 1003 (user2:x:1003:1003::/home/user2:/bin/sh)
	.Что означают опции -m и -s:	
	  -m - создавать домашний каталог пользователя, если он не существует;(/home/user1)
	  -s - командная оболочка для пользователя;(/bin/sh)

2)Создаем группу и добавляем туда пользователей:
	groupadd admins(добавили группу admins)
	gpasswd -a user1 admins(дали user1 права на добавление и удаление пользователей в группу admins)
	gpasswd -a user2 admins(дали user1 права на добавление и удаление пользователей в группу admins)
	
		![alt text](LABA2(AAA)/users_id.png)
		(screenshot users_id.png)
 	roman@romanPC:~/Desktop$ id user1
	  uid=1001(user1) gid=1001(user1) groups=1001(user1),1003(admins)
	roman@romanPC:~/Desktop$ id user2
	  uid=1003(user2) gid=1003(admins) groups=1003(admins)

	(*) Через usermod сделайте группу admins основной для  user1. Результат id приложить в README.md
	roman@romanPC:~/Desktop$ sudo usermod -g admins user1 (usermod для добавления пользователя в группу)
	roman@romanPC:~/Desktop$ id user1
	uid=1001(user1) gid=1003(admins) groups=1003(admins)
		(группа затералась, поэтому нужно использовать -a -g)
		(screenshot usermod.png)

3)Создать каталог от рута и дать права группе admins туда писать:
	roman@romanPC:~/Desktop$ su -
	Password: 
	root@romanPC:~# pwd
	/root
	root@romanPC:~# mkdir /opt/upload
	mkdir: невозможно создать каталог «/opt/upload»: Файл существует
	root@romanPC:~# cd /opt
	root@romanPC:/opt# chmod 770 /opt/upload (770 значит права на чтение, запись,выполнение для вся пользователя файла, группы файла. Для остальных ничего нельзя)
			chgrp admins /opt/upload - (поменять группу файла)
	(*) попробуйте сменить текущую группу пользователя  newgrp admins у пользователя user2 и создайте еще файл
			newgrp admins - (сменить текущую группу пользователя)
	
			screenshot(chmod1.png) screenshot(chmod2.png)

4)Создать пользователя user3 и дать ему права писать в /opt/uploads
	.Создайте пользователя user3
	.Попробуйте записать из под него файл в /opt/uploads. Должны получить ошибку
	.Считайте acl с каталога. Добавьте черерз  setfacl права на запись в каталог.
	getfacl - используется для просмотра установленных ACL.
	setfacl - используется для назначения, модификации и удаления ACL прав.
man getfacl
man setfacl
getfacl /opt/upload
setfacl -m u:user3:rwx /opt/upload
su - user3
touch /opt/upload/user3_file
ls -l /opt/upload/user3_file
		screenshot(setfacl.png)

5)Установить GUID флаг на директорию /opt/uploads:
	.chmod g+s /opt/upload
	 su - user3
	 touch /opt/upload/user3_file2
	 ls -l /opt/upload/user3_file2

	.Объяснить почему изменилась группа при создании:
		Когда применяется к каталогу, SGID может быть полезен, потому что 
		вы можете использовать его для установки владельца группы по умолчанию для файлов и подкаталогов, 
		созданных в этом каталоге. По умолчанию, когда пользователь создает файл, его эффективная первичная группа 
		устанавливается как владелец группы для этого файла. 
	Таким образом, назначили созданному файлу группу как у каталога
	.Приложить ls -l /opt/upload  в  README.md
		screenshot(GUID.png)

6)Установить  SUID  флаг на выполняемый файл:
	.В начале  попробуйте прочитать cat /etc/shadow  из под пользователя user3
	.Установим suid бит на просмотрщик cat:
	.Установить suid /bin/cat и прочитайте снова из под user3
		screenshot(SUID1.png)
		screenshot(SUID2.png)
	.Объясните почему: 
		Команда chmod u+s позволяет работать с файлом от имени владельца - root

7)Сменить владельца  /opt/uploads  на user3 и добавить sticky bit:
	chown user3 /opt/upload
	chmod +t /opt/upload		(sticky bit)
	su - user1
	touch /opt/upload/user1_file_test
	ls -l /opt/upload/user1_file_test
	su - user3
	rm -f  /opt/upload/user1_file_test

		screenshot(sticky1.png)
		screenshot(sticky2.png)

(Еще одно важное усовершенствование касается использования sticky-бита в каталогах.
 Каталог с установленным sticky-битом означает, 
что удалить файл из этого каталога может только владелец файла или суперпользователь. 
Другие пользователи лишаются права удалять файлы.
 Установить sticky-бит в каталоге может только суперпользователь. Sticky-бит каталога, в отличие от sticky-бита файла, 
остается в каталоге до тех пор, пока владелец каталога или суперпользователь не удалит каталог явно или не применит к нему chmod. 
Заметьте, что владелец может удалить sticky-бит, но не может его установить.

Восьмеричное значение stiky-бита: 1000
Символьное: +t
Установить sticky-бит на каталог можно используя команду chmod:

chmod 1755 allex - с заменой прав;
chmod +t allex - добавление к текущим правам.
Убрать sticky-бит на каталог можно:

chmod -t allex)

	.Объясните почему user3 смог удалить файл, который ему не принадлежит:
		C помощью команд(chown user3 /opt/upload, chmod +t /opt/upload)сказали что удалять может только владелец файла либо владелец каталога в котором файл
	.Создайте теперь файл от user1 и удалите его пользователем user1
	.Объясните результат: 
		user1 - владелец файла, поэтому можно по sticky bit удалять


8)Записи в sudoers
   .попробуйте из под user3 выполнить sudo ls -l /root
	roman@romanPC:~/Desktop$ sudo useradd -m -s /bin/bash user3
	roman@romanPC:~/Desktop$ sudo passwd user3
	Enter new UNIX password: 
	Retype new UNIX password: 
	passwd: password updated successfully
	user3@romanPC:/home/roman/Desktop$ sudo ls -l /root
	[sudo] password for user3: 
	user3 is not in the sudoers file.  This incident will be reported.
   .почему у вас не получилось?
	не хватает прав пользователя для user 3 нельзя использовать команду sudo. Нужно добавить пользователя в sudoers
   .добавить запись в sudoers, позволяющую пользователю user 3 выполнять команду ls без пароля:
	roman@romanPC:/etc$ su -
	Password: 
	root@romanPC:~# cd /etc
	root@romanPC:/etc# nano sudoers.d/user3
		user3	ALL=NOPASSWD:/bin/ls
	добавьте запись в /etc/sudoers.d/admins разрешающий группе admins любые команды с вводом пароля:
	root@romanPC:/etc/sudoers.d# nano admins	
		%admins  ALL=(ALL:ALL) ALL




 
