# Открыть порт в Oracle Cloud

Авторизуйтесь в Oracle Cloud, затем перейдите в секцию **Instances **в разделе **Compute**:

![](<../.gitbook/assets/image (346).png>)

Выберите вашу виртуальную машину из списка. Откроется профиль машины, где нужно будет нажать на ссылку напротив Subnet:

![](<../.gitbook/assets/image (344).png>)

Далее откроется раздел с секцией **Security Lists**, где нужно будет выбрать элемент, начинающийся на **Default Security List**. Далее нажимайте на кнопку **Add Ingress Rule**.

![](<../.gitbook/assets/image (345).png>)

В примере выше открывается порт TCP 19999 под именем Netdata. Нажимайте **Add Ingress Rule** - на этом настройка завершается.
