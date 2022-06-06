# Торговые паузы NYSE/NSDQ

{% hint style="info" %}
Парсер не работает по выходным.
{% endhint %}

Переходим в раздел **Установить сервис** из бокового меню **ppp**, выбираем вариант **Торговые паузы NYSE/NASDAQ**.

В поле **Название сервиса** вводим желаемое название сервиса.

Профиль API Supabase должен быть создан заранее по [инструкции](../recipes/supabase.md). В Supabase необходимо включить расширения PG\_CRON, PLV8, DBLINK, HTTP (см. инструкцию).

Интервал опроса определяет, как часто парсер будет запрашивать список торговых пауз с ресурса поставщика данных. Задаётся в секундах.

Бот должен быть добавлен в **ppp** в разделе **Боты Telegram**. Чтобы создать бота, отправьте команду `/newbot` боту [https://telegram.me/BotFather](https://telegram.me/BotFather)

Поле **Тикеры для отслеживания** задаёт код функции, которая должна вернуть список тикеров, по которым будут контролироваться торговые паузы. Функция по умолчанию использует публичный каталог иностранных акций СПБ Биржи одного из крупнейший брокеров РФ. Нажатие кнопки **Выполнить функцию** позволяет исполнить код на реальной базе Supabase в отладочных целях.

Поле **Форматирование** задаёт код функции, определяющей итоговый вид оповещений в Telegram. По умолчанию выводится базовая информация с кодом паузы, датой и временем, описанием (если доступно).

Для проверки отправляйте тестовые сообщения. Для добавления кнопок под сообщениями смотрите [инструкцию](../recipes/webhook-for-tg-buttons.md).

В поле **Канал или группа** введите ID канала/группы, куда будут отправляться уведомления. Бот должен обладать правами администратора, чтобы всё работало. Чтобы получить ID канала, напишите там любое сообщение, затем скопируйте на него ссылку из контекстного меню. Это будет ссылка вида [https://t.me/c/123459678/1](https://t.me/c/123459678/1)

Добавьте слева -100 к большому числу в ссылке, это и будет ID канала. Для примера это `-100123459678`.

Если вы создаёте сервис впервые, он будет **остановлен**. Запустите его сразу после создания.

{% hint style="danger" %}
Сервис начнёт следить за паузами спустя минуту после создания или перезапуска.
{% endhint %}