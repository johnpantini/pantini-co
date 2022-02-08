# Oracle: сетевой диск+VPN

## Создание блочного хранилища

В рамках бесплатного использования Oracle Cloud зарегистрированным пользователям предоставляется [блочное хранилище](https://docs.oracle.com/en-us/iaas/Content/FreeTier/freetier\_topic-Always\_Free\_Resources.htm) в сумме до 200 GB, а также до 5 бесплатных резервных копий хранилищ:

> All tenancies receive a total of 200 GB of [Block Volume](https://docs.oracle.com/en-us/iaas/Content/Block/Concepts/overview.htm#Overview\_of\_Block\_Volume) storage, and five volume backups included in the Always Free resources. These amounts apply to both boot volumes and block volumes combined. When you provision a compute instance, the instance automatically receives a 50 GB [boot volume](https://docs.oracle.com/en-us/iaas/Content/Block/Concepts/bootvolumes.htm#Boot\_Volumes) for storage.

&#x20;Если у вас уже есть виртуальные машины, то на каждую автоматически расходуется 50 GB хранилища, уменьшая доступные бесплатные ресурсы. Так, если вы настраивали **ppp** по инструкции и создали экземпляр машины под управлением [Oracle Linux](../ppp/oracle-cloud.md), то вам будет доступно не 200, а 150 GB бесплатного места (50GB займет сама машина).

Чтобы проверить личные лимиты и расходование хранилищ, перейдите в раздел [Limits, Quotas and Usage](https://cloud.oracle.com/limits) , затем выберите **Block Volume** из выпадающего списка **Service**:

![](<../.gitbook/assets/image (351).png>)

В колонке **Usage** можно увидеть, что мне доступно еще 4 резервные копии, а также 3 GB места хранилища (стоит округлять с шагом 5 GB, на деле бесплатного места больше нет).

Чтобы посмотреть детализацию по использованию места, перейдите по ссылке:

{% embed url="https://cloud.oracle.com/block-storage/volumes" %}

Пройдитесь по ссылкам **Block Volumes** и **Boot Volumes** в секции слева, чтобы увидеть расходование места.

Теперь вернитесь в раздел [Block Volumes](https://cloud.oracle.com/block-storage/volumes). Для создания нового хранилища нажмите **Create Block Volume**:

![](<../.gitbook/assets/image (348).png>)

Имя задаём произвольное, например, **ppp**. В разделе **Volume Size and Performance** выбираем **Custom** и вводим значение 150:

![Ava](<../.gitbook/assets/image (352).png>)

Далее нажимаем **Create Block Volume**. Затем, как хранилище будет создано, перейдите в него, нажав на ссылку с именем. Далее в разделе с ссылками слева нажмите A**ttached Instances**. Там нажмите на кнопку **Attach to Instance** (будет неактивна, если диск уже присоединён). Выбираем машину, присоединяем диск, после чего его нужно будет подключить и отформатировать.&#x20;

## Подключение хранилища
