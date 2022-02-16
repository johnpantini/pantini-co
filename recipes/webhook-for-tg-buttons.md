# Webhook для кнопок TG

В этом материале рассматривается вариант настройки ссылки webhook для бота в Telegram, где будут обрабатываться нажатия кнопок под сообщениями от сервисов **ppp**.

## Форматирование сообщений с кнопками

{% hint style="info" %}
Для отладки пользуйтесь тестовыми сообщениями в сервисах оповещений о торговых паузах.
{% endhint %}

Для примера разместим одну кнопку и одну ссылку под сообщением о стоп-торгах в один ряд. Ссылка предложит открыть инструмент в каталоге Тинькофф Инвестиций (ТИ) в браузере.

Кнопка подставит тикер инструмента в первый найденный на экране виджет заявки в открытом терминале ТИ с первой группой. Примерный вид сообщения будет таким:

![](<../.gitbook/assets/image (348).png>)

Установите новый или отредактируйте существующий сервис торговых пауз в **ppp**. У вас должен быть заранее добавлен бот, который будет обеспечивать работу сервиса. Для размещения кнопок под сообщением воспользуйтесь следующими функциями форматирования:

#### Для сервиса торговых пауз SPBEX

```javascript
/**
 * Функция форматирования сообщения о торговой паузе.
 *
 * @param {string} isin - ISIN инструмента.
 * @param {string} ticker - Тикер инструмента.
 * @param {string} name - Название инструмента.
 * @param {string} currency - Валюта инструмента.
 * @param {string} date - Дата и время сообщения от биржи.
 * @param {string} url - Ссылка на сообщение на сайте биржи.
 * @param {string} start - Время начала торговой паузы, MSK.
 * @param {string} finish - Время окончания торговой паузы, MSK.
 */
return {
  text: `‼️⏸ Приостановка торгов (SPBEX)
${'$'}${ticker || isin}
<b>${name}, ${isin}</b>
🕒 ${start} - ${finish}

<a href="${encodeURIComponent(url)}">Сообщение о приостановке торгов</a>
`,
  options: ticker ? {
    reply_markup: JSON.stringify({
      inline_keyboard: [
        [
          {
            text: '🟡',
            callback_data: JSON.stringify({
              e: 'ticker',
              t: ticker
            })
          },
          {
            text: `Open ${ticker} with Tinkoff`,
            url: `https://www.tinkoff.ru/invest/stocks/${ticker}`
          }
        ]
      ]
    })
  } : {}
};
```

#### Для сервиса торговых пауз NYSE/NASDAQ

```javascript
/**
 * Функция форматирования сообщения о торговой паузе.
 *
 * @param {string} halt_date - Дата начала торговой паузы (MM/DD/YYYY).
 * @param {string} halt_time - Время торговой паузы (Eastern Time).
 * @param {string} symbol - Тикер инструмента.
 * @param {string} name - Название инструмента.
 * @param {string} market - Площадка листинга инструмента.
 * @param {string} reason_code - Код торговой паузы.
 * @param {string} pause_threshold_price - Индикативная пороговая цена торговой паузы.
 * @param {string} resumption_date - Дата окончания торговой паузы (MM/DD/YYYY).
 * @param {string} resumption_quote_time - Время открытия книги заявок (Eastern Time).
 * @param {string} resumption_trade_time - Время открытия торгов (Eastern Time).
 */
const mappings = {
  T1: 'Halt - News Pending. Ожидаются новости.',
  T2: 'Halt - News Released. Эмитент начинает процесс распространения новостей в соответствии с требованиями о добросовестном раскрытии информации (SEC Regulation FD).',
  T5: 'Single Stock Trading Pause in Effect. Цена инструмента изменилась более, чем на 10% (включительно) в течение 5 минут.',
  T6: 'Halt - Extraordinary Market Activity. Необычная рыночная активность. Срабатывает, если NASDAQ обнаруживает проблемы (которые могут привести к изменению цены) с котированием, с репортингом в ленту сделок, или проблемы соединения.',
  T8: 'Halt - Exchange-Traded-Fund (ETF). Срабатывает в ETF, если обнаружены проблемы в базовых активах.',
  T12: 'Halt - Additional Information Requested by NASDAQ. Срабатывает, если NASDAQ ожидает дополнительную информацию (эмитенту направляются вопросы, на которые тот должен дать ответ).',
  H4: 'Halt - Non-compliance. Несоответствие требованиям листинга NASDAQ.',
  H9: 'Halt - Not Current. Компания не опубликовала актуальный отчёт в регулирующие органы (SEC).',
  H10: 'Halt - SEC Trading Suspension. SEC приостановила торги на неопределенное время.',
  H11: 'Halt - Regulatory Concern. Торги приостановлены в другом рыночном центре по требованию регулирующих органов. Длительность приостановки может исчисляться днями или неделями.',
  O1: 'Operations Halt, Contact Market Operations. Проблемы с маркет-мейкингом (проблемы с выставлением котировок и обработкой заявок участников торгов).',
  IPO1: 'HIPO Issue not yet Trading. Проблемы на торгах инструментов, выходящих на IPO (в первый день торгов). Торги IPO начинаются позже начала основной сессии на несколько часов.',
  M1: 'Corporate Action. Корпоративное событие.',
  M2: ' Quotation Not Available. Нет котировок по инструменту.',
  LUDP: 'Volatility Trading Pause. Торговая пауза в связи с волатильностью.',
  LUDS: 'Volatility Trading Pause - Straddle Condition. Пауза, связанная с выходом котировки bid или ask за установленные пределы.',
  MWC1: 'Market Wide Circuit Breaker Halt. Остановка торгов из-за срабатывания глобальной стоп-защиты рынка.',
  MWC2: 'Market Wide Circuit Breaker Halt. Остановка торгов из-за срабатывания глобальной стоп-защиты рынка.',
  MWC3: 'Market Wide Circuit Breaker Halt. Остановка торгов из-за срабатывания глобальной стоп-защиты рынка.',
  MWC0: 'Market Wide Circuit Breaker Halt. Остановка торгов из-за срабатывания глобальной стоп-защиты рынка.',
  T3: 'News and Resumption Times. Эмитент закончил процесс распространения новостей, вскоре ожидаются торги.',
  T7: 'Single Stock Trading Pause/Quotation-Only Period. См. код T5.',
  R4: 'Qualifications Issues Reviewed/Resolved; Quotations/Trading to Resume. См. код H4.',
  R9: 'Filing Requirements Satisfied/Resolved; Quotations/Trading To Resume. См. код H9.',
  C3: 'Issuer News Not Forthcoming; Quotations/Trading To Resume. Публикация новостей отменена. См. коды T2 и T3.',
  C4: 'Qualifications Halt Ended; Maintenance Requirements Met. См. коды H4 и R4.',
  C9: 'Qualifications Halt Concluded; Filings Met; Quotes/Trades To Resume. См. коды H9 и R9.',
  C11: 'Trade Halt Concluded By Other Regulatory Auth.; Quotes/Trades Resume. См. код H11.',
  R1: 'New Issue Available. См. код T1.',
  R2: 'Issue Available. См. код T2.',
  IPOQ: 'IPO security released for quotation. Для инструментов в день IPO - начало котирования. Только NASDAQ.',
  IPOE: 'IPO security - positioning window extension. Расширение периода подачи заявок в перекрёстной сессии NASDAQ для инструментов в день IPO.',
  MWCQ: 'Market Wide Circuit Breaker Resumption. Снятие глобальной стоп-защиты рынка.',
  M: 'Volatility Trading Pause. Торговая пауза в связи с волатильностью.',
  D: 'Security deletion from NASDAQ / CQS. Инструмент удалён с торгов (делистинг).'
};
const formatDateTime = (dateString) => {
  const [date, timeZ] = new Date(`${dateString} GMT-8`)
    .toISOString()
    .split(/T/);
  const [y, m, d] = date.split(/-/);
  const [time] = timeZ.split(/\./);

  return `${d}.${m}.${y} ${time} MSK`;
};

let message = `‼️⏸ Приостановка торгов (${market})
${'$'}${symbol}
<b>${name}</b>
🕒 ${formatDateTime(`${halt_date} ${halt_time}`)}

`;

const description = mappings[reason_code];

if (description) message += `<b>Код ${reason_code}</b>: ${description}\n`;
else message += `<b>Ожидание кода</b>\n`;

if (resumption_quote_time)
  message += `\nОткрытие книги заявок: ${formatDateTime(
    `${resumption_date} ${resumption_quote_time}`
  )}`;

if (resumption_trade_time)
  message += `\nВозобновление торгов: ${formatDateTime(
    `${resumption_date} ${resumption_trade_time}`
  )}`;

message +=
  '\n\n<a href="https://www.nasdaqtrader.com/trader.aspx?id=TradeHalts">К списку торговых пауз</a>';

return {
  text: message,
  options: symbol ? {
    reply_markup: JSON.stringify({
      inline_keyboard: [
        [
          {
            text: '🟡',
            callback_data: JSON.stringify({
              e: 'ticker',
              t: symbol
            })
          },
          {
            text: `Open ${symbol} with Tinkoff`,
            url: `https://www.tinkoff.ru/invest/stocks/${symbol}`
          }
        ]
      ]
    })
  } : {}
};
```

Если сервис у вас уже был, сохраните его нажатием кнопки **Переустановить сервис**.

## Установка webhook для бота

{% hint style="danger" %}
Чтобы выполнить этот раздел, подключите Pusher в **ppp** [по инструкции](pusher.md).
{% endhint %}

Для этого раздела вам понадобится зафиксировать название вашего бота, а также название подключения к Pusher, потому что код webhook будет ссылаться на эти значения.

Для примера подключение к Pusher называется **Pusher**, а бот **Onaryx**. Далее в ppp перейдите в раздел со списком конечных точек HTTPS, добавьте новую кнопкой справа вверху.

**Маршрут** стоит хранить в секрете - сгенерируйте уникальное значение кнопкой. **HTTP-метод** укажите как POST. Название функции - любое, например, **onaryxWebhook**. Содержимое функции используйте следующее:

{% hint style="danger" %}
В 11 строке поменяйте имя подключения к Pusher на ваше, если нужно (внутри одинарных кавычек).

Аналогично в строке 55 поменяйте название бота на ваше.
{% endhint %}

```javascript
exports = function ({ query, headers, body }, response) {
  try {
    const payload = EJSON.parse(body.text());

    if (payload.callback_query) {
      const data = JSON.parse(payload.callback_query.data);

      try {
        if (data.e === 'ticker') {
          const pusherCredentials = [%#JSON.stringify(await (async () => {
            const api = await ctx.app.ppp.user.functions.findOne({collection:'apis'},{removed:{$not:{$eq:true}},name:'Pusher'},{iv:1,appid:1,key:1,secret:1,cluster:1});

            return {
              appid: api.appid,
              key: api.key,
              secret: await ctx.app.ppp.crypto.decrypt(api.iv, api.secret),
              cluster: api.cluster
            };
          })())%];

          const timestamp = Math.floor(Date.now() / 1000);
          const pusherBody = JSON.stringify({
            name: 'ticker',
            channel: 'telegram',
            data: JSON.stringify({ t: data.t })
          });
          const crypto = require('crypto');
          const bodyMd5 = crypto
            .createHash('md5')
            .update(pusherBody)
            .digest('hex');

          let params = `auth_key=${pusherCredentials.key}&auth_timestamp=${timestamp}&auth_version=1.0&body_md5=${bodyMd5}`;
          const authSignature = utils.crypto.hmac(
            ['POST', `/apps/${pusherCredentials.appid}/events`, params].join(
              '\n'
            ),
            pusherCredentials.secret,
            'sha256',
            'hex'
          );

          params += `&auth_signature=${authSignature}`;

          context.http.post({
            url: `https://api-${pusherCredentials.cluster}.pusher.com/apps/${pusherCredentials.appid}/events?${params}`,
            body: pusherBody,
            headers: {
              'Content-Type': ['application/json']
            }
          });
        }
      } finally {
        const token = `[%#(await (async () => {
          const bot = await ctx.app.ppp.user.functions.findOne({collection:'bots'},{removed:{$not:{$eq:true}},name:'Onaryx'},{iv:1,token:1});

          return await ctx.app.ppp.crypto.decrypt(bot.iv, bot.token);
        })())%]`;

        context.http.get({
          url: `https://api.telegram.org/bot${token}/answerCallbackQuery?callback_query_id=${payload.callback_query.id}&text=🆗`
        });
      }
    }

    response.setStatusCode(204);
  } catch (e) {
    response.setStatusCode(500);
  }
};
```

Сохраните конечную и снова вернитесь к списку. В колонке **Конечная точка MongoDB Realm** будет ссылка на MongoDB Realm, перейдите по ней.

Найдите ссылку в разделе **Operation Type**, скопируйте ее. Далее в **ppp** откройте вашего бота на редактирование, разверните секцию Webhook, вставьте скопированную ссылку и сохраните бота. На данном этапе бот будет обрабатывать нажатия на кнопки и отправлять уведомления в Pusher.

## Прием уведомлений в терминале ТИ

Установите расширение Tampermonkey в браузер:

{% embed url="https://chrome.google.com/webstore/detail/tampermonkey/dhdgffkkebhmkfjojejmpbldmpobfkfo?hl=ru" %}

Нажмите на значок расширения, затем **Создать новый скрипт...**:

![](<../.gitbook/assets/image (355).png>)

Вставьте следующий код, затем нажмите **Сохранить** в меню **Файл**:

```javascript
// ==UserScript==
// @name         Tinkoff Terminal Pusher Integration
// @namespace    https://github.com/johnpantini/ppp
// @version      1.0
// @author       johnpantini
// @match        https://www.tinkoff.ru/terminal/*
// @icon         https://www.tinkoff.ru/favicon.ico
// @require      https://js.pusher.com/7.0/pusher.min.js
// @noframes
// @grant        none
// ==/UserScript==

(function () {
  function setWidgetTicker(widget, ticker) {
    if (widget && ticker) {
      const key = Object.keys(widget).find((key) =>
        key.startsWith('__reactFiber$')
      );

      if (key) {
        const internalInstance = widget[key];

        if (internalInstance) {
          const selectSymbol =
            internalInstance.return?.memoizedProps?.selectSymbol;

          selectSymbol?.(ticker.toUpperCase());
        }
      }
    }
  }

  const pusher = new Pusher('Ваш App Id в Pusher', {
    cluster: 'Ваш кластер в Pusher'
  });
  const channel = pusher.subscribe('telegram');

  channel.bind('ticker', function (data) {
    setWidgetTicker(
      document
        .querySelector(
          '[data-widget-type="COMBINED_ORDER_WIDGET"] div[style*="color: rgb(255, 212, 80)"]'
        )
        .closest('[data-widget-type="COMBINED_ORDER_WIDGET"]'),
      data.t
    );
  });
})();

```

Вместо **Ваш App Id в Pusher** и **Ваш кластер в Pusher** вставьте реальный значения, которые можно найти в профиле **Pusher** в **ppp**.

На вкладке настроек проставьте следующие параметры:

![](<../.gitbook/assets/image (349).png>)
