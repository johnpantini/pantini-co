# Первые шаги

В этом и последующих разделах опубликованы инструкции по развертыванию приложения PPP. Проходите по порядку, собирайте ключи 🔑.

## Установка отдельного браузера

Для этой цели подойдут браузеры [Google Chrome Canary](https://www.google.com/intl/ru/chrome/canary/) или новый [Microsoft Edge](https://www.microsoft.com/ru-ru/edge). Можно и любой [другой](https://vivaldi.com/ru/), построенный на базе Chromium. Главное - современный и обновляемый регулярно (т.н. [evegreen browser](https://www.techopedia.com/definition/31094/evergreen-browser)).

{% hint style="info" %}
Для Windows рекомендуется Microsoft Edge.
{% endhint %}

{% hint style="danger" %}
Важно использовать чистый, не обременённый расширениями или вкладками браузер, чтобы получить максимальную производительность.

Никаких блокировщиков рекламы, VPN и прочих утилит быть не должно.
{% endhint %}

{% embed url="https://www.google.com/intl/ru/chrome/canary/" %}

{% embed url="https://vivaldi.com/ru/" %}

Переходим по ссылке, нажимаем большую кнопку загрузки. Дожидаемся установки, запускаем.

## Получение ключей облачных сервисов

> Никогда не теряй терпения - это последний ключ, отпирающий двери.
>
> **Антуан де Сент-Экзюпери**

Теперь, когда браузер установлен и готов к работе, нужно собрать комплект ключей от ряда облачных сервисов. Везде потребуется создать учётную запись, но в целом, все процедуры не займут много времени.

Начните с клонирования рабочего репозитория **GitHub**:

{% content-ref url="github.md" %}
[github.md](github.md)
{% endcontent-ref %}

Рекомендуется также продублировать приложение на ресурсе **Cloudflare Pages,** что даст прирост скорости загрузки:

{% content-ref url="cloudflare-pages.md" %}
[cloudflare-pages.md](cloudflare-pages.md)
{% endcontent-ref %}

Затем создайте сервисную машину, настроив автоматическое обновление через **GitHub**:

{% content-ref url="service-machine.md" %}
[service-machine.md](service-machine.md)
{% endcontent-ref %}

Наконец, создайте приложение **ppp** в облачных сервисах **Auth0** и **MongoDB Realm**:

{% content-ref url="auth0.md" %}
[auth0.md](auth0.md)
{% endcontent-ref %}

{% content-ref url="mongodb.md" %}
[mongodb.md](mongodb.md)
{% endcontent-ref %}

После выполнения этих инструкций рекомендуется сразу подключить свой бесплатный личный сервер:

{% content-ref url="oracle-cloud.md" %}
[oracle-cloud.md](oracle-cloud.md)
{% endcontent-ref %}

{% hint style="warning" %}
Рекомендуется пользоваться [Cloudflare Pages](cloudflare-pages.md) для работы с приложением на постоянной основе.
{% endhint %}
