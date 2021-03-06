# 🔑 GitHub

## Регистрация на ресурсе [GitHub.com](https://github.com)

Если аккаунта нет, регистрируемся по ссылке ниже. Понадобится указать имя пользователя и пароль.

{% embed url="https://github.com/join" %}

## Создание личной копии репозитория PPP

{% hint style="warning" %}
_Репозиторием_ называют место, где хранится код проекта. Для краткости будем также использовать краткое написание _репо_.

**GitHub** удобен для распространения исходного кода всего проекта между заинтересованными участниками. Внутри **GitHub** используется система контроля версий [**Git**](https://ru.wikipedia.org/wiki/Git).
{% endhint %}

Переходим по ссылке:

{% embed url="https://github.com/johnpantini/ppp" %}

В правой верхней части экрана нажимаем на кнопку **Fork**:

![](<../.gitbook/assets/image (250).png>)

{% hint style="info" %}
Там же есть кнопка-переключатель **Watch**. С её помощью можно подписаться на получение уведомлений обо всех действиях, которые будут происходить в репозитории.

Вы автоматически будете подписаны на свою копию репо, а для отслеживания моих изменений можете подписаться на оригинал.
{% endhint %}

Подождите немного, пока выполнится процедура, и продвигайтесь далее.

## Подключение GitHub Pages

**GitHub Pages** - бесплатный хостинг статических сайтов прямо из репозитория **GitHub**. Для активации переходим на вкладку **Settings** репозитория:

![Вкладка Settings подсвечена цветом](<../.gitbook/assets/image (276).png>)

Слева из боковой панели вкладок надо выбрать пункт **Pages**:

![Вкладка Pages подсвечена цветом](<../.gitbook/assets/image (237).png>)

В выпадающем списке **Source должна быть выбрана ветка main. Папку оставляем / (root)**, нажимаем **Save**:

![](<../.gitbook/assets/image (275).png>)

{% hint style="info" %}
**GitHub** начнёт первичную сборку сайта. **Стоит подождать минуту-другую** и обновить страницу, чтобы появилась такая же зелёная плашка, как на фрагменте снимка экрана выше.

Тему выбирать **не нужно**.
{% endhint %}

## Запуск приложения PPP в первый раз

Переходите по вашему личному адресу **GitHub Pages** из прошлого раздела (в зелёной плашке).

{% hint style="info" %}
Адрес имеет вид:`https://<имя пользователя>.github.io/ppp`

При открытии этой страницы вы будете перенаправлены в зависимости от вашего браузера либо на версию для настольных ПК, либо для мобильных устройств. Вы также можете вручную открыть нужную версию, используя следующие адреса:

`https://<имя пользователя>.github.io/ppp/desktop -`**для настольных ПК**

`https://<имя пользователя>.github.io/ppp/mobile -`**для мобильных устройств**
{% endhint %}

При первом запуске приложение попытается прочитать ключи, но это обернётся неудачей, потому что никакие настройки пока ещё не заданы. Вас перенаправят в раздел, где нужно будет ввести **сразу весь** минимально необходимый для работы комплект ключей 🔑.

{% hint style="info" %}
Пока все ключи не будут введены верно, приложение не сможет начать работу. Это удобно с той стороны, что вы гарантированно будете знать на месте, всё ли верно заполнили.
{% endhint %}

С этого момента инструкции будут рассматривать способы получения всех необходимых ключей (токенов, идентификаторов и т.д.).

## Получение персонального токена GitHub

Перейдите по ссылке:

{% embed url="https://github.com/settings/tokens" %}

Нажмите кнопку **Generate new token**. В появившейся форме выберите флажки **repo** и **user**, задайте желаемое время истечения (когда токен станет недействительным - в целях безопасности):

![](<../.gitbook/assets/image (84).png>)

Нажмите кнопку **Generate token** в нижней части страницы. После создания токена сразу скопируйте его - при покидании страницы он станет недоступным для просмотра.

![](<../.gitbook/assets/image (97).png>)

{% hint style="warning" %}
Полученный ключ 🔑 вводите в настройках облачных сервисов приложения PPP.
{% endhint %}
