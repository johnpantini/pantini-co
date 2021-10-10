# Сервисная машина

Переходим по ссылке [https://signup.heroku.com/](https://signup.heroku.com) и регистрируемся:

{% embed url="https://signup.heroku.com" %}

В поле **Role **можно указать **Professional Developer**, в поле **Primary development language** - **Node.js**.

На почту придёт письмо с подтверждением. Переходим по ссылке, вводим пароль для учётной записи. Далее переходим на страницу [https://dashboard.heroku.com/terms-of-service](https://dashboard.heroku.com/terms-of-service)

{% embed url="https://dashboard.heroku.com/terms-of-service" %}

Нажимаем **Accept**. Нас перенесут на страницу [https://dashboard.heroku.com/apps](https://dashboard.heroku.com/apps)

{% embed url="https://dashboard.heroku.com/apps" %}

Нажимаем **Create new app**. Вводим любое имя, регион указываем **Europe**. Нажимаем **Create app**.

Нас перенесут на вкладку **Deploy**. В секции **Deployment method** выбираем **GitHub**. Нажимаем **Connect to GitHub**. Далее во всплывающем окне нажимаем **Authorize heroku**.

После этого страница обновится, в поле **repo-name** вводим **ppp **и нажимаем кнопку **Search**. Снизу появится строка с названием репозитория, нажимаем **Connect**.

Далее нажимаем кнопки **Enable Automatic Deploys** и **Deploy Branch**. Теперь скопируйте ссылку на своё приложение, которую можно узнать, нажав на кнопку **Open app** в верхней части страницы.

Приложения **Heroku **уходят в спящий режим через 30 минут неактивности. Можно поддерживать активность с помощью сервиса, вставьте туда имя своего приложения:

{% embed url="https://kaffeine.herokuapp.com" %}
