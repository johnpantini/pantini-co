# Oracle: сетевой диск+VPN

## Создание блочного хранилища

В рамках бесплатного использования Oracle Cloud зарегистрированным пользователям предоставляется [блочное хранилище](https://docs.oracle.com/en-us/iaas/Content/FreeTier/freetier\_topic-Always\_Free\_Resources.htm) в сумме до 200 GB, а также до 5 бесплатных резервных копий хранилищ:

> All tenancies receive a total of 200 GB of [Block Volume](https://docs.oracle.com/en-us/iaas/Content/Block/Concepts/overview.htm#Overview\_of\_Block\_Volume) storage, and five volume backups included in the Always Free resources. These amounts apply to both boot volumes and block volumes combined. When you provision a compute instance, the instance automatically receives a 50 GB [boot volume](https://docs.oracle.com/en-us/iaas/Content/Block/Concepts/bootvolumes.htm#Boot\_Volumes) for storage.

Если у вас уже есть виртуальные машины, то на каждую автоматически расходуется 50 GB хранилища, уменьшая доступные бесплатные ресурсы. Так, если вы настраивали **ppp** по инструкции и создали экземпляр машины под управлением [Oracle Linux](oracle-cloud.md), то вам будет доступно не 200, а 150 GB бесплатного места (50GB займет сама машина).

Чтобы проверить личные лимиты и расходование хранилищ, перейдите в раздел [Limits, Quotas and Usage](https://cloud.oracle.com/limits) , затем выберите **Block Volume** из выпадающего списка **Service**:

![](<../.gitbook/assets/image (351) (1) (1) (1).png>)

В колонке **Usage** можно увидеть, что мне доступно еще 4 резервные копии, а также 3 GB места хранилища (стоит округлять с шагом 5 GB, на деле бесплатного места больше нет).

Чтобы посмотреть детализацию по использованию места, перейдите по ссылке:

{% embed url="https://cloud.oracle.com/block-storage/volumes" %}

Пройдитесь по ссылкам **Block Volumes** и **Boot Volumes** в секции слева, чтобы увидеть расходование места.

Теперь вернитесь в раздел [Block Volumes](https://cloud.oracle.com/block-storage/volumes). Для создания нового хранилища нажмите **Create Block Volume**:

![](<../.gitbook/assets/image (348) (1) (1) (1) (1).png>)

Имя задаём произвольное, например, **ppp**. В разделе **Volume Size and Performance** выбираем **Custom** и вводим значение 150:

![Ava](<../.gitbook/assets/image (352) (1) (1) (1) (1).png>)

Далее нажимаем **Create Block Volume**. Затем, как хранилище будет создано, перейдите в него, нажав на ссылку с именем. Далее в разделе с ссылками слева нажмите **Attached Instances**. Там нажмите на кнопку **Attach to Instance** (будет неактивна, если диск уже присоединён). Выбираем машину, присоединяем диск, после чего его нужно будет подключить и отформатировать.&#x20;

## Подключение хранилища

Не покидая страницы хранилища (там, где нажимали на **Attach to Instance**), нажмите на три точки в строке со своей машиной:

![](<../.gitbook/assets/image (352) (1) (1) (1).png>)

В появившемся окне понадобятся две секции **Attach Commands** и **Detach Commands**.&#x20;

### Создание сервиса в ppp

Чтобы иметь удобный доступ к командам подключения и отключения устройства, сохраним их в сервисе на основе команд оболочки. Переходите в раздел установки сервисов, выбирайте нужный (Команды SSH) и нажимайте **Продолжить**:

![](<../.gitbook/assets/image (349) (1) (1) (1).png>)

Сразу добавьте один секрет, куда введите пароль для доступа к диску по сети в дальнейшем. Пароль придумайте самостоятельно. Выберите сервер Oracle, настроенный ранее, из списка, а также задайте имя сервиса на свое усмотрение, например, iSCSI SMB:

![](<../.gitbook/assets/image (351) (1) (1).png>)

Теперь перейдём к командам. Вернитесь в облако Oracle, скопируйте команды из секции **Attach Commands** и вставьте их в сервис ppp в область ввода **Команды создания**.

Аналогично содержимое секции **Detach Commands** из Oracle вставьте в сервис ppp в область ввода **Команды удаления**. Примерный вид команд следующий:

![](<../.gitbook/assets/image (353) (1) (1).png>)

**Сохраните сервис на этом этапе кнопкой Добавить сервис**. Убедитесь, что операция завершена успешно, после чего перейдите в раздел со списком сервисов, выберите только что сохранённый. Теперь он откроется уже в режиме редактирования, и нужно будет дополнить команды создания.

Итак, не удаляя старые команды создания, добавьте эти строчки следом за тремя уже имеющимися командами (можно не отделять пустой строкой):

```clike
sudo salt-call --local state.sls ppp
sudo dnf -y install samba samba-client samba-common
sudo sudo systemctl enable smb
sudo sudo systemctl enable nmb
sudo firewall-cmd --permanent --add-service=samba
sudo firewall-cmd --reload
sudo mkdir -p /mnt/ppp
sudo chmod -R 0770 /mnt/ppp
sudo chown -R root:ppp /mnt/ppp
sudo chcon -t samba_share_t /mnt/ppp
echo -ne "[%#(ctx.domSecrets[0].value.trim())%]\n[%#(ctx.domSecrets[0].value.trim())%]\n" | sudo smbpasswd -s -a pppuser
sudo salt-call --local xfs.mkfs /dev/sdb noforce=True
sudo grep -q '/dev/oracleoci/oraclevda' /etc/fstab || sudo sh -c "printf '/dev/oracleoci/oraclevda /mnt/ppp xfs defaults,_netdev,nofail 0 2\n' >> /etc/fstab"
sudo mount -a
sudo sh -c "printf '[global]\n\tworkgroup = WORKGROUP\n\tsmb ports = 445\t\n\tmin protocol = SMB2\n\tmax protocol = SMB3\n\tclient min protocol = SMB2\n\tclient max protocol = SMB3\n\tclient ipc min protocol = SMB2\n\tclient ipc max protocol = SMB3\n\tserver min protocol = SMB2\n\tserver max protocol = SMB3\t\n\tsecurity = user\n\tpassdb backend = tdbsam\n\tprinting = cups\n\tprintcap name = cups\n\tload printers = yes\n\tcups options = raw\n\n[homes]\n\tcomment = Home Directories\n\tvalid users = %%S, %%D%%w%%S\n\tbrowseable = No\n\tread only = No\n\tinherit acls = Yes\n\n[printers]\n\tcomment = All Printers\n\tpath = /var/tmp\n\tprintable = Yes\n\tcreate mask = 0600\n\tbrowseable = No\n\n[print$]\n\tcomment = Printer Drivers\n\tpath = /var/lib/samba/drivers\n\twrite list = @printadmin root\n\tforce group = @printadmin\n\tcreate mask = 0664\n\tdirectory mask = 0775\n\n[ppp]\n\tpath = /mnt/ppp\n\tvalid users = @ppp\n\tguest ok = no\n\twritable = yes\n\tbrowsable = yes\n' > /etc/samba/smb.conf"
sudo sudo systemctl restart smb
sudo sudo systemctl restart nmb
```

Сохраните сервис на этом этапе (кнопкой **Переустановить сервис**). Чтобы подключить сетевой диск, нужно создать защищённую сеть, после чего дополнить команды ещё раз.

## Создание SDN на базе ZeroTier

**SDN** (software-defined network, программно определённая сеть) - это виртуальная частная сеть (**VPN**), в которую мы поместим наши серверы и компьютеры. Узлы сети получат локальные адреса, невидимые из глобальной сети Интернет, и смогут взаимодействовать в защищенном режиме друг с другом. Это позволит, например, безопасно присоединить сетевой диск от Oracle на домашний или рабочий компьютер.

Для начала зарегистрируйтесь в **ZeroTier** через **GitHub**. Перейдите по ссылке и нажмите **Sign Up**:

{% embed url="https://www.zerotier.com" %}

Вас попросят указать почту, имя и фамилию, после чего перенаправят в [панель администрирования](https://my.zerotier.com/welcome). Нажимайте на кнопку, чтобы создать сеть:

![](<../.gitbook/assets/image (347) (1) (1).png>)

Сеть будет создана с уникальным числовым идентификатором и произвольным именем. Нажмите на строчку, перейдите в подробности, где можно поменять имя. Сеть должна быть в режиме **Private**:

![](<../.gitbook/assets/image (356) (1).png>)

Теперь установите клиент **ZeroTier** на свой компьютер:

{% embed url="https://www.zerotier.com/download" %}

После установки откройте клиент ZeroTier (найдите значок в системном лотке, где переключается язык), нажмите на него, чтобы открыть контекстное меню, и выберите пункт с адресом, это скопирует адрес компьютера в буфер обмена:

![Пример для Mac](<../.gitbook/assets/image (346) (1) (1) (1) (1).png>)

Вернитесь в браузер и вставьте адрес в поле **Manually Add Member**:

![](<../.gitbook/assets/image (358) (1).png>)

После добавления клиента убедитесь, что проставлен флажок **Auth**:

![](<../.gitbook/assets/image (357) (1).png>)

Откройте клиент ZeroTier на компьютере снова, присоединитесь к своей сети, введя её идентификатор, скопированный из панели управления ZeroTier в браузере (раздел **Basics**, поле **Network ID**):

![Пример для клиента Windows, пункт Open Control Panel... контекстного меню клиента](<../.gitbook/assets/image (346) (1) (1) (1).png>)

Теперь необходимо установить клиент ZeroTier на сервере Oracle и тоже присоединиться к сети. Вернитесь в ppp и добавьте следующие команды в конец к уже существующим:

{% hint style="warning" %}
Замените `<числовой id сети>` на id вашей сети без скобок
{% endhint %}

```
curl -s https://install.zerotier.com | sudo bash
sudo zerotier-cli info
sudo zerotier-cli join <числовой id сети>
```

Нажмите **Переустановить сервис**. По завершении операции вы увидите в терминале строку вида:

`200 info 1234ed4ca3 1.8.4 ONLINE`

Здесь `1234ed4ca3` - адрес вашего сервера, добавьте его в сеть ZeroTier по аналогии с тем, как вы добавляли свой компьютер.

## Подключение сетевого диска

Сейчас, когда у нас есть настроенная сеть на две машины, можно подключить сетевой диск.

Необходимо выяснить локальный адрес машины Oracle. Это можно сделать в панели управления ZeroTier в браузере. В таблице Members найдите запись, соответствующую вашей машине Oracle по числовому адресу, который вы недавно добавляли. И скопируйте её IP-адрес из колонки **Managed IPs**. Пусть для примера это будет 10.242.42.42.

### Windows

В проводнике используем контекстное меню компьютера, пункт **Подключить сетевой диск...**:

![](<../.gitbook/assets/image (350) (1).png>)

Проставьте два флажка, введите адрес (см. ниже) и нажмите **Готово**:

![](<../.gitbook/assets/image (354) (1) (1).png>)

`\\10.242.42.42\ppp`

⬆️ Здесь замените адрес на актуальный для вашего сервера. Появится окно ввода пароля и имени пользователя. Вводите **pppuser** в имя пользователя. Пароль вы вводили в секрете, когда создавали сервис в ppp.

Если диск не подключается, можно попробовать предпринять следующие действия:

> Если не подключается сетевой диск с ошибкой "Пароль указан неверно", попробуйте запустить **gpedit.msc** (нажмите Windows + R) - это редактор локальной групповой политики, далее идите по пути Конфигурация компьютера - Конфигурация Windows - Параметры безопасности - Локальные политики - Параметры безопасности.&#x20;
>
> В окне справа находим политику "Сетевая безопасность: уровень проверки подлинности LAN Manager", в ней выбираем "Отправлять только NTLMv2 ответ" и сохраняем. Если **gpedit.msc** нет в системе, его нужно установить отдельно, инструкцию поискать в сети.

### Mac

Откройте **Finder**, нажмите **Command + K**. Заполняйте по образцу:

![](<../.gitbook/assets/image (354) (1).png>)

`smb://10.242.42.42/ppp`

Если хотите, чтобы после перезагрузки диск подключался автоматически:

Войдите в Системные настройки из меню Apple. Щёлкните «Пользователи и группы». Нажмите «Объекты входа». Нажмите кнопку +, чтобы добавить еще один элемент входа. Найдите сетевой диск, который вы ранее подключили, и нажмите «Добавить».

### Linux

Установите пакет `cifs-utils`. Используйте следующую команду:

`mount.cifs //10.242.42.42/ppp /mnt`

Чтобы диск монтировался и после перезагрузки, добавьте запись в `/etc/fstab`:

`//10.242.42.42/ppp /mnt cifs user,rw,credentials=/root/.smbclient 0 0`

Создайте файл `/root/.smbclient` с содержимым:

```
username=pppuser
password=<ваш пароль-секрет>
```

После этого проверьте правильность настроек командой `mount -a`
