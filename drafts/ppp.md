---
description: Виджеты с кнопками торговли
---

# ⭐️ PPP

Сначала выполните инструкцию по созданию приложения **MongoDB Realm** по ссылке:

[https://pantini.gitbook.io/pantini-co/terminal/github-and-cloud#mongodb-realm](https://pantini.gitbook.io/pantini-co/terminal/github-and-cloud#mongodb-realm)

{% embed url="https://pantini.gitbook.io/pantini-co/terminal/github-and-cloud#mongodb-realm" %}

## Торговля через Alor

#### Портфель Alor

У вас должен быть открыт счёт и профинансированы портфели фондового рынка бирж **MOEX **(для списания комиссии) и **SPBEX **(для торговли).

Узнать номер портфеля можно в личном кабинете Алор после авторизации:

{% embed url="https://lk.alor.ru/main" %}

Портфель будет указан в первой вкладке:

![](<../.gitbook/assets/image (287).png>)

Для примеров в этом материале взят номер портфеля **D71111**.

#### Торговый токен Alor

Теперь необходимо получить торговый токен. **Регистрируемся **по ссылке:

{% embed url="https://alor.dev/register" %}

После регистрации подтверждаем учетную запись через почту. Далее входим в портал для разработчиков через [https://alor.dev/login](https://alor.dev/login) и авторизуемся.

Далее переходим в раздел с токенами доступа по ссылке [https://alor.dev/open-api-tokens](https://alor.dev/open-api-tokens)

Вводите логин и пароль от личного кабинета Alor, далее нажимайте кнопку **Связать аккаунт разработчика с торговым счетом**. Далее нажимайте кнопку **Выписать токен**. 

{% hint style="danger" %}
Полученный токен нужно хранить в секрете. Он равносилен паролю.
{% endhint %}

Документация к Open API располагается по ссылке [https://alor.dev/docs](https://alor.dev/docs)

## Торговля через Tinkoff

#### Торговый токен Tinkoff

Получите торговый токен для торговли в настройках по ссылке (внизу страницы):

{% embed url="https://www.tinkoff.ru/invest/settings/" %}

{% hint style="danger" %}
Полученный токен нужно хранить в секрете. Он равносилен паролю.
{% endhint %}

#### Выбор счёта для торгов

{% hint style="danger" %}
**В настоящий момент в Tinkoff Open API поддерживается только торговля с основного брокерского счета и ИИС.**
{% endhint %}

Находясь на вкладке с терминалом, откройте [инструменты разработчика браузера](https://learn.javascript.ru/devtools).

Перейдите на вкладку **Console**, вставьте следующий код и нажмите **Enter**:

```javascript
console.table((await (await fetch(`https://api-invest.tinkoff.ru/trading/user/broker_accounts?appName=invest_terminal&appVersion=2.0.0&sessionId=${new RegExp('psid[^;]+')
.exec(document.cookie).toString().replace(/^[^=]+./, '')}`, {method: 'POST'})).json()).payload.accounts.map((a) => ({id: a.brokerAccountId, name: a.name})));
```

Будет выведен список счётов с названиями и номерами в виде таблицы. Вам лишь остаётся выбрать нужный счёт (**только основной брокерский или ИИС**), запомнив его номер. Для примеров в этом материале возьмём субсчёт с номером **000000000**.

## Serverless-функция торговли

### Общая часть

В панели управления **MongoDB Realm** выбираем вкладку **Realm**, затем нажимаем на карточку приложения:

![](<../.gitbook/assets/image (289).png>)

В боковой панели выберите пункт **3rd Party Services**, затем нажмите **Add a Service**:

![](<../.gitbook/assets/image (300).png>)

На следующем этапе выбираем вариант **HTTP**, в поле с названием сервиса вводим **ppp**. Далее нажимайте кнопку **Add Service**:

![](<../.gitbook/assets/image (309).png>)

На следующем экране будет предложено создать новый **Webhook**. Это постоянная ссылка, при активации которой будет выполняться заложенная нами бизнес-логика покупок/продаж. Реализация зависит от брокера, теперь смотрите разделы ниже под вашу ситуацию.

### Торговля через Alor

Для начала настроим учётные данные.

Для примера будем использовать портфель **D71111** и токен **988d5822-be2e-42ee-a92a-6ded32a452b3**. В левой панели выберите раздел **Values**, далее нажмите на кнопку **Create New Value**:

![](<../.gitbook/assets/image (291).png>)

Введите значение портфеля (в двойных кавычках) под именем `alorSPBEXPortfolio`, далее нажимайте **Save Draft**.

![Укажите ваш портфель фондового рынка Alor SPBEX](<../.gitbook/assets/image (293).png>)

Теперь нужно создать ещё одно значение `alorRefreshToken`, на этот раз типа **Secret**:

![Укажите ваш токен](<../.gitbook/assets/image (294).png>)

Нажимаем **Save Draft** и создаём дополнительное значение `alorRefreshTokenValue` типа **Value**, к которому привязываем секрет `alorRefreshToken`:

![](<../.gitbook/assets/image (295).png>)

Нажимаем **Save Draft**, нас перенесут на таблицу со всеми значениями.

Теперь можно сохранить предварительные изменения, нажав на кнопку **REVIEW DRAFT & DEPLOY** в верхней части страницы:

![](<../.gitbook/assets/image (299).png>)

В появившемся окне нажимаем кнопку **Deploy**.

Теперь возвращаемся в раздел **3rd Party Services** в боковой панели, выбираем ранее созданный сервис **ppp**, нажимаем кнопку **Add Incoming Webhook**:

![](<../.gitbook/assets/image (302).png>)

Вводим название **ppp-alor**:

![](<../.gitbook/assets/image (308).png>)

В разделе Webhook Settings будет сформирована постоянная ссылка. **Скопируйте её, она понадобится для вставки в бота позднее.**

![](<../.gitbook/assets/image (305).png>)

В разделе **Authorization **выберите вариант **Require Secret** и укажите ваш токен (начинается с `JP-`) из бота [https://t.me/pantini_warden_bot](https://t.me/pantini_warden_bot)

![](<../.gitbook/assets/image (306).png>)

{% hint style="warning" %}
В дальнейшем, если понадобится перевыпустить токен Pantini Warden, не забудьте обновить функцию соответственно.
{% endhint %}

Теперь нажмите **Save Draft**. Вас перенесут в редактор кода, удалите всё содержимое в нём и вставьте следующий код:

```javascript
const fetchAlorAccessToken = async ({ alorRefreshToken }) => {
  const https = require('https');

  return new Promise(async (resolve, reject) => {
    const req = https.request(
      {
        hostname: 'oauth.alor.ru',
        port: 443,
        path: `/refresh?token=${alorRefreshToken}`,
        method: 'POST'
      },
      async (res) => {
        try {
          let responseText = '';

          for await (const chunk of res) {
            responseText += chunk;
          }

          resolve(JSON.parse(responseText || 'null'));
        } catch (error) {
          reject(error);
        }
      }
    );

    req.end();
  });
};

const fetchAlorOpenAPI = async ({
  path,
  body = '{}',
  token,
  portfolio,
  method = 'POST'
}) => {
  const https = require('https');

  return new Promise(async (resolve, reject) => {
    const req = https.request(
      {
        hostname: 'api.alor.ru',
        port: 443,
        path,
        method,
        headers: {
          'Content-Type': 'application/json',
          'X-ALOR-REQID': `${portfolio};${Date.now()}`,
          'Content-Length': body.length,
          Authorization: `Bearer ${token}`
        }
      },
      async (res) => {
        try {
          let responseText = '';

          for await (const chunk of res) {
            responseText += chunk;
          }

          resolve(JSON.parse(responseText || 'null'));
        } catch (error) {
          reject(error);
        }
      }
    );

    if (body) req.write(body);

    req.end();
  });
};

exports = async function (payload, response) {
  const body = EJSON.parse(payload.body.text());

  if (body.check === true) {
    response.setStatusCode(200);
    response.setBody('200 OK');
  } else {
    const ticker = body.t.replace('.', ' ');
    const price = +body.p;
    const volume = body.v;
    const direction = body.d;

    if (ticker && volume > 0 && price > 0) {
      const alorRefreshToken = context.values.get('alorRefreshTokenValue');

      if (!alorRefreshToken) {
        response.setStatusCode(404);
        response.setBody('Не задан refresh-токен Alor Open API');
      } else {
        const portfolio = context.values.get('alorSPBEXPortfolio');

        if (!portfolio) {
          response.setStatusCode(404);
          response.setBody('Не задан портфель ФР Alor');
        } else {
          const tokenRes = await fetchAlorAccessToken({ alorRefreshToken });

          if (!tokenRes.AccessToken) {
            response.setStatusCode(403);
            response.setBody('Refresh-токен неверный, заблокирован либо истёк');
          } else {
            const res = await fetchAlorOpenAPI({
              path: `/commandapi/warptrans/TRADE/v2/client/orders/actions/limit`,
              method: 'POST',
              body: JSON.stringify({
                instrument: {
                  symbol: ticker,
                  exchange: 'SPBX'
                },
                quantity: volume,
                side: direction,
                price,
                user: {
                  account: 'L01-00000F00',
                  portfolio
                }
              }),
              portfolio,
              token: tokenRes.AccessToken
            });

            if (res.message === 'success') {
              response.setStatusCode(200);
              response.setBody('200 OK');
            } else {
              console.log(JSON.stringify(res));

              response.setStatusCode(400);
              response.setBody(res.message);
            }
          }
        }
      }
    } else {
      response.setStatusCode(400);
      response.setBody('Некорректные данные в заявке');
    }
  }
};
```

Снова нажмите **Save Draft**, а затем **REVIEW DRAFT & DEPLOY** в верхней части страницы, а потом **Deploy**. Дождитесь успеха операции, затем переходите в раздел настройки бота Pantini Warden.

### Торговля через Tinkoff

Для примера будем использовать торговый токен **t.xxx** и номер субсчёта **000000000**. По аналогии с настройкой данных для **Alor **(см. выше) создадим три значения:

1. `tinkoffAccountId`- номер субсчёта
2. `tinkoffToken `- секрет с торговым токеном
3. `tinkoffTokenValue` - значение секрета `tinkoffToken`

![Укажите номер субсчёта](<../.gitbook/assets/image (296).png>)

![Укажите торговый токен Tinkoff](<../.gitbook/assets/image (297).png>)

![](<../.gitbook/assets/image (298).png>)

Нажимаем **Save Draft**, нас перенесут на таблицу со всеми значениями.

Теперь можно сохранить предварительные изменения, нажав на кнопку **REVIEW DRAFT & DEPLOY** в верхней части страницы:

![](<../.gitbook/assets/image (299).png>)

В появившемся окне нажимаем кнопку **Deploy**.

Теперь возвращаемся в раздел **3rd Party Services** в боковой панели, выбираем ранее созданный сервис **ppp**, нажимаем кнопку **Add Incoming Webhook**:

![](<../.gitbook/assets/image (302).png>)

Вводим название **ppp-tinkoff**:

![](<../.gitbook/assets/image (303).png>)

В разделе Webhook Settings будет сформирована постоянная ссылка. **Скопируйте её, она понадобится для вставки в бота позднее.**

![](<../.gitbook/assets/image (305).png>)

В разделе **Authorization **выберите вариант **Require Secret** и укажите ваш токен (начинается с `JP-`) из бота [https://t.me/pantini_warden_bot](https://t.me/pantini_warden_bot)

![](<../.gitbook/assets/image (306).png>)

{% hint style="warning" %}
В дальнейшем, если понадобится перевыпустить токен Pantini Warden, не забудьте обновить функцию соответственно.
{% endhint %}

Теперь нажмите **Save Draft**. Вас перенесут в редактор кода, удалите всё содержимое в нём и вставьте следующий код:

```javascript
const fetchTIOpenAPI = async ({ path, body = '', token, method = 'POST' }) => {
  const https = require('https');

  return new Promise(async (resolve, reject) => {
    const req = https.request(
      {
        hostname: 'api-invest.tinkoff.ru',
        port: 443,
        path,
        method,
        headers: {
          'Content-Type': 'application/json',
          'Content-Length': body.length,
          Authorization: `Bearer ${token}`
        }
      },
      async (res) => {
        if (res.statusCode === 401) {
          resolve({
            status: 'Error',
            payload: {
              message: 'Неверный торговый токен'
            }
          });
        } else {
          try {
            let responseText = '';

            for await (const chunk of res) {
              responseText += chunk;
            }

            resolve(JSON.parse(responseText || 'null'));
          } catch (error) {
            reject(error);
          }
        }
      }
    );

    if (body) req.write(body);

    req.end();
  });
};

exports = async function (payload, response) {
  const body = EJSON.parse(payload.body.text());

  if (body.check === true) {
    response.setStatusCode(200);
    response.setBody('200 OK');
  } else {
    const ticker = body.t.replace(' ', '.');
    const price = +body.p;
    const volume = body.v;
    const direction = body.d;
    const figi = body.f;

    if (ticker && figi && volume > 0 && price > 0) {
      const tinkoffToken = context.values.get('tinkoffTokenValue');

      if (!tinkoffToken) {
        response.setStatusCode(404);
        response.setBody('Не задан торговый токен');
      } else {
        const accountId = context.values.get('tinkoffAccountId');

        if (!accountId) {
          response.setStatusCode(404);
          response.setBody('Не задан номер счёта');
        } else {
          const res = await fetchTIOpenAPI({
            path: `/openapi/orders/limit-order?figi=${figi}&brokerAccountId=${accountId}`,
            method: 'POST',
            body: JSON.stringify({
              lots: volume,
              operation: direction === 'buy' ? 'Buy' : 'Sell',
              price
            }),
            token: tinkoffToken
          });

          if (
            res &&
            res.status.toLowerCase() === 'ok' &&
            res.payload.status.toLowerCase() !== 'rejected'
          ) {
            response.setStatusCode(200);
            response.setBody('200 OK');
          } else {
            console.log(JSON.stringify(res));

            response.setStatusCode(400);
            response.setBody(res ? res.payload.message : 'Запрос не выполнен');
          }
        }
      }
    } else {
      response.setStatusCode(400);
      response.setBody('Некорректные данные в заявке');
    }
  }
};
```

Снова нажмите **Save Draft**, а затем **REVIEW DRAFT & DEPLOY** в верхней части страницы, а потом **Deploy**.

## Настройка бота Pantini Warden

Сейчас необходимо сообщить боту [https://t.me/pantini_warden_bot](https://t.me/pantini_warden_bot) ссылку на **Webhook**, чтобы он мог с ней взаимодействовать. Для этого отправьте команду боту личным сообщением, передав ранее скопированную ссылку:

```bash
# /setpppwebhook <ссылка на WebHook>
# Пример (это тест, вставлять нужно свою ссылку!):

/setpppwebhook https://eu-central-1.aws.webhooks.mongodb-realm.com/api/client/v2.0/app/ppp/service/ppp/incoming_webhook/ppp-tinkoff
```

Если всё в порядке, бот примет **Webhook **и сообщит об этом:

![](<../.gitbook/assets/image (310).png>)

{% hint style="info" %}
Бот попробует активировать функцию в холостом режиме для проверки корректности ссылки.
{% endhint %}

Теперь виджет с кнопками должен начать работать.
