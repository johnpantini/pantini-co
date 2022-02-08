# Публичный feed новостей рынков US

{% hint style="info" %}
У сервиса нет SLA, может работать нестабильно.
{% endhint %}

Сервис предоставляет поток новостей по всему рынку US. Каналы [SPBEX News](telegram-resources/spbex-news.md) и [SPBEX SEC](telegram-resources/spbex-sec.md) фильтруют новости по тикерам для СПБ Биржи, но используют этот же источник.

Для подключения используйте облачный сервис **PubNub**:

{% embed url="https://www.pubnub.com/docs/sdks" %}
Документация
{% endembed %}

&#x20;По ссылке ознакомьтесь с документацией под ваш язык/платформу.

{% hint style="danger" %}
**uuid** сгенерируйте самостоятельно. Используйте следующий ключ **subscribeKey** для подписки:

`sub-c-a33c1016-88d3-11ec-a04e-822dfd796eb4`
{% endhint %}

### Пример для Node.js

```javascript
const PubNub = require('pubnub');

const pubnub = new PubNub({
  subscribeKey: 'sub-c-a33c1016-88d3-11ec-a04e-822dfd796eb4',
  uuid: 'ddfa7c32-6914-4e25-a58d-c44ce27018d3'
});

pubnub.addListener({
  message: function (messageEvent) {
    console.log(messageEvent.message);
  }
});

pubnub.subscribe({
  channels: ['ppp']
});

```

Поля приходящего сообщения:

* `title`- заголовок новости
* `body`- подробности, полный текст новости. Это может быть файл с разметкой, может быть просто текст
* `date`- дата последнего обновления новости в формате UTC
* `meta`- метаданные, дополнительная информация
* `tickers` - массив тикеров, к которым относится новость
* `tags`- дополнительная информация
* `quotes`- котировки основной сессии для тикеров, к которым относится новость
* `teaser`- краткий текст с сутью новости, может совпадать с заголовком
* `partnerUrl`- ссылка на партнёрский материал

Все поля опциональны. У форм SEC будет задано поле `meta.SEC`.
