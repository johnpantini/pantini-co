# Сервисная машина

## Через облачный сервис Northflank (предпочтительно)

{% hint style="info" %}
Машины в этом сервисе работают бесперебойно.

Для регистрации понадобится карта [Bankoff](https://www.bankoff.co) с нулевым балансом (только реквизиты). Получите её через [TG-бот](https://t.me/Bankoff\_Bo\_Bot).
{% endhint %}

Переходим по [ссылке регистрации](https://app.northflank.com/signup), снизу нажимаем на маленькую иконку **GitHub**. Когда будет предложено указать репозитории, доступ к которым запросит **Northflank**, выберите вашу личную копию **ppp**:

![](<../.gitbook/assets/image (358).png>)

{% hint style="warning" %}
Если попытка входа через GitHub завершается ошибкой, создайте аккаунт классическим способом, придумав логин и пароль.
{% endhint %}

Придумайте логин и пароль, после чего вас перенесут в панель управления. Может потребоваться подтверждение электронной почты. Также могут попросить выбрать тему оформления - выбирайте нужную и нажимайте на кнопку **Choose theme**.

Если появится такая форма, нажимайте Link GitHub, привязывайте свой аккаунт.

![](<../.gitbook/assets/image (357).png>)

Создавайте новый проект **ppp (Create free project)**:

![](<../.gitbook/assets/image (355).png>)

Имя **ppp**, регион **Европа**:

![](<../.gitbook/assets/image (356).png>)

Далее в проекте создавайте новый сервис из контекстного меню в правом верхнем углу страницы (либо кнопкой **Add new service** в панели управления проектом, если это ваш первый сервис):

![](<../.gitbook/assets/image (360).png>)

Тип сервиса **Combined**, имя **ppp**, репозиторий **ppp** (ваша копия), ветвь **main**, тип сборки **Buildpack**:

![](<../.gitbook/assets/image (359).png>)

Добавьте переменную окружения PORT со значением 3777 в секции **Environment variables**:

![](<../.gitbook/assets/image (347).png>)

В секции **Networking** добавьте порт HTTP 3777 под именем **dyno**:

![](<../.gitbook/assets/image (345).png>)

Нажимайте кнопку **Create service**. При создании сервиса могут потребовать ввести номер карты, используйте карту Bankoff.&#x20;

После завершения создания URL сервисной машины можно будет найти на странице сервиса в правой верхней части страницы. Он оканчивается на **code.run**

## Через облачный сервис Heroku

{% hint style="info" %}
Машины в этом сервисе уходят в спящий режим, если нет активности. Выход из спящего режима (холодный старт) занимает до 10 секунд.
{% endhint %}

Переходим по ссылке [https://signup.heroku.com/](https://signup.heroku.com) и регистрируемся:

{% embed url="https://signup.heroku.com" %}

В поле **Role** можно указать **Professional Developer**, в поле **Primary development language** - **Node.js**.

На почту придёт письмо с подтверждением. Переходим по ссылке, вводим пароль для учётной записи. Далее переходим на страницу [https://dashboard.heroku.com/terms-of-service](https://dashboard.heroku.com/terms-of-service)

{% embed url="https://dashboard.heroku.com/terms-of-service" %}

Нажимаем **Accept**. Нас перенесут на страницу [https://dashboard.heroku.com/apps](https://dashboard.heroku.com/apps)

{% embed url="https://dashboard.heroku.com/apps" %}

Нажимаем **Create new app**. Вводим любое имя, регион указываем **Europe**. Нажимаем **Create app**:

![](<../.gitbook/assets/image (343).png>)

Нас перенесут на вкладку **Deploy**. В секции **Deployment method** выбираем **GitHub**. Нажимаем **Connect to GitHub**. Далее во всплывающем окне нажимаем **Authorize heroku**.

После этого страница обновится, в поле **repo-name** вводим **ppp** и нажимаем кнопку **Search**. Снизу появится строка с названием репозитория, нажимаем **Connect**.

Далее нажимаем кнопки **Enable Automatic Deploys** и **Deploy Branch**:

![](<../.gitbook/assets/image (348) (1) (1) (1) (1).png>)
