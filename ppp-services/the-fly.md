# Парсер The Fly

{% hint style="danger" %}
Материал подразумевает, что вы [установили приложение ppp](../ppp/getting-started.md) и настроили его (ввели все ключи облачных сервисов).
{% endhint %}

В материале рассматривается настройка парсера новостей с ресурса [https://thefly.com/news.php](https://thefly.com/news.php)

В приложении ppp перейдите в раздел **Установить сервис** из бокового меню, затем выберите вариант **Парсер с персистентностью**. Далее рассматривается заполнение полей по порядку их следования в форме.

**Название сервиса** впишите любое по желанию, например, The Fly SPBEX (в примере отслеживаются только тикеры СПБ Биржи).

**Профиль API Supabase** должен быть [создан заранее](../recipes/supabase.md). Выберите вашу базу данных из списка.

В поле **Ресурс** вставьте текст [`https://thefly.com/news.php`](https://thefly.com/news.php)``

Поле **Фрейм** оставьте пустым.

**Интервал опроса** задайте по желанию от 1 до 5 секунд.

**Глубина хранения** - 50.

**Поля таблицы состояния** - вставьте такое содержимое:

```sql
title text primary key,
tickers text,
topic text,
date text not null,
priority bool not null,
link text
```

**Статические данные** - здесь мы сформируем массив тикеров для отслеживания. Для примера возьмем тикеры долларовых бумаг из каталога одного из крупных российских брокеров:

```javascript
const instruments =
  JSON.parse(
    plv8.execute(
      `select content from http_get('https://api.tinkoff.ru/trading/stocks/list?sortType=ByName&orderType=Asc&country=All')`
    )[0].content
  ).payload.values || [];

const symbols = [];

for (const i of instruments) {
  if (i.symbol.currency === 'USD' && i.symbol.ticker !== 'TCS')
    symbols.push(i.symbol.ticker.replace('.', ' '));
}

symbols.push('$ECON');
symbols.push('SPY');
symbols.push('SPB');
symbols.push('CIAN');
symbols.push('OZON');
symbols.push('QIWI');
symbols.push('MTL');

return symbols;
```

**Функция парсинга** имеет такой код:

```javascript
const isDST = () => {
  const currentDate = new Date();
  const currentYear = currentDate.getFullYear();
  const firstOfMarch = new Date(currentYear, 2, 1);
  const daysUntilFirstSundayInMarch = (7 - firstOfMarch.getDay()) % 7;
  const secondSundayInMarch =
    firstOfMarch.getDate() + daysUntilFirstSundayInMarch + 7;
  const start = new Date(currentYear, 2, secondSundayInMarch);
  const firstOfNovember = new Date(currentYear, 10, 1);
  const daysUntilFirstSundayInNov = (7 - firstOfNovember.getDay()) % 7;
  const firstSundayInNovember =
    firstOfNovember.getDate() + daysUntilFirstSundayInNov;
  const end = new Date(currentYear, 10, firstSundayInNovember);

  return (
    currentDate.getTime() <= end.getTime() &&
    currentDate.getTime() >= start.getTime()
  );
};

const stories = [];
const fetch = plv8.find_function('ppp_fetch');
const html = fetch('[%#payload.url%]', {
  headers: {
    'User-Agent': '[%#navigator.userAgent%]'
  }
}).responseText;

for (const m of html.matchAll(
  /(<tr id="news_[\s\S]+?)<div class='newsContent'>/gi
)) {
  const story = m[1];
  const priority = /tr_noticia_prioridad/i.test(story);
  const [_, topic, link, title] = story
    .replace('<span class="importantHeadline">', '')
    .match(
      /data-topic="(.+?)"[\s\S]+<a[\s\S]+href='([\s\S]+?)'><span>([\s\S]+?)<\/span>/i
    );
  const [__, time, date] = story.match(
    /soloHora">([\s\S]+?)<div class="fpo_overlay_ticker">([\s\S]+?)<\/div>/i
  );

  const tickers = [...story.matchAll(/data-ticker='([\s\S]+?)'/gi)].map(
    (i) => i[1]
  );

  if (tickers.some((t) => consts.indexOf(t) > -1))
    stories.push({
      date: `${date} ${time} GMT-${isDST() ? '7' : '8'}`,
      tickers: tickers.join(','),
      priority,
      topic,
      link,
      title: title.replace(/&#039;/g, "'").replace(/&amp;#39;/g, "'")
    });
}

return stories;
```

Далее идёт секция триггеров - оставляем без изменений. Разворачиваем секцию **Уведомления в Telegram**, включаем флажок **Также отправлять уведомления в Telegram**. &#x20;

**Бот** должен быть добавлен в **ppp** в разделе **Боты Telegram**. Чтобы создать бота, отправьте команду `/newbot` боту [https://telegram.me/BotFather](https://telegram.me/BotFather)

В поле **Канал или группа** введите ID канала/группы/чата, куда будут отправляться уведомления. Бот должен обладать правами администратора, чтобы всё работало. Чтобы получить ID канала, напишите там любое сообщение, затем скопируйте на него ссылку из контекстного меню. Это будет ссылка вида [https://t.me/c/123459678/1](https://t.me/c/123459678/1)

Добавьте слева -100 к большому числу в ссылке, это и будет ID канала. Для примера это `-100123459678`.

Поле **Форматирование** задаёт код функции, определяющей итоговый вид оповещений в Telegram. Впишите туда следующий код:

```javascript
const formatDateTime = (pubDate) => {
  const [date, timeZ] = new Date(Date.parse(pubDate || new Date()))
    .toISOString()
    .split(/T/);
  const [y, m, d] = date.split(/-/);
  const [time] = timeZ.split(/\./);

  return `${d}.${m}.${y} ${time} MSK`;
};

const formatTitle = (record) => {
  let icon = '🐝';

  switch (record.topic) {
    case 'events':
      icon = '📅';

      break;
    case 'recomm':
      icon = '👍';

      break;
    case 'recDowngrade':
      icon = '⬇️';

      break;
    case 'recUpgrade':
      icon = '⬆️';

      break;
    case 'periodicals':
      icon = '📰';

      break;
    case 'options':
      icon = '🅾️';

      break;
    case 'general_news':
      icon = '🌎';

      break;
    case 'hot_stocks':
      icon = '🔥';

      break;
    case 'earnings':
      icon = '💰';

      break;
    case 'syndic':
      break;
    case 'technical_analysis':
      icon = '💹';

      break;
  }

  if (record.priority) icon = '‼️' + icon;

  if (record.tickers.trim())
    return (
      icon +
      ' ' +
      record.tickers
        .split(',')
        .map((ticker) => {
          if (ticker.startsWith('$')) return ticker;

          return '$' + ticker;
        })
        .join(' ')
    );

  return icon + ' The Fly';
};

const options = {
  disable_web_page_preview: true
};

if (record.tickers.trim()) {
  options.reply_markup = JSON.stringify({
    inline_keyboard: [
      record.tickers
        .split(',')
        .filter((ticker) => {
          return ticker !== '$ECON';
        })
        .slice(0, 5)
        .map((t) => {
          if (t === 'SPB') t = 'SPB@US';

          return {
            text: t,
            callback_data: JSON.stringify({
              e: 'ticker',
              t
            })
          };
        })
    ]
  });
}

return {
  text: `${formatTitle(record)}
⏰ ${formatDateTime(record.date)}
<b><a href="${encodeURIComponent(record.link)}">${encodeURIComponent(
    record.title
  )}</a></b>`,
  options
};
```

&#x20;В примере под сообщениями размещаются кнопки для быстрого перехода в терминале. Чтобы они работали, настройте бота [по инструкции](../recipes/webhook-for-tg-buttons.md).

Теперь создавайте сервис, **после чего перезапустите**.
