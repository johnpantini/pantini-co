# Расписание торгов СПб/США

Расписание торгов по времени MSK на американских площадках + СПб (время летнее в NY, для зимы нужно прибавить **1 час**):

* 07:00 - открытие премаркета СПб для [ряда инструментов](https://spbexchange.ru/ru/stocks/inostrannye/raspisanie/morning/)
* 10:00 - открытие премаркета СПб, начало периода без дополнительной ликвидности на СПб, начало основной торговой сессии СПб
* 10:30 - pre-opening сессия на NYSE Arca
* 11:00 - открытие премаркета NYSE Arca и Nasdaq, Bats EDGX
* 13:00 - открытие внебиржевой площадки OTC Markets в США
* 13:30 - pre-opening сессия на NYSE, NYSE MKT, NYSE Chicago
* 14:00 - открытие премаркета Bats BYX, Bats BZX, Bats EDGA, NASDAQ BX и CHX
* 14:30 - синхронизация СПб с американским премаркетом, начало периода стандартной дополнительной ликвидности на СПб
* 15:00 - открытие FINRA/TRF (репортинг внебиржевых сделок), премаркета рыночных центров IEX и NSX
* 16:00 - открытие премаркета NASDAQ PSX
* 16:30 - открытие NYSE и NYSE MKT, основная торговая сессия в США
* 19:00 - начало дополнительной торговой сессии СПб, конец основной торговой сессии СПб
* 23:00 - закрытие NYSE и NYSE MKT, постмаркет (after hours)
* 00:00 - закрытие Bats BYX, Bats BZX, CHX, NASDAQ PSX и NSX, закрытие OTC Markets
* 00:30 - закрытие IEX
* 01:30 - закрытие FINRA/TRF
* 01:45 - конец дополнительной торговой сессии СПб
* 02:00 - закрытие NASDAQ BX
* 03:00 - закрытие Bats EDGA, Bats EDGX, NASDAQ и NYSE ARCA

Этот график касается бумаг, листингованных на NYSE и NASDAQ. Не применимо для бумаг TCS и бумаг, торгующихся на немецких биржах.

{% hint style="info" %}
[https://spbexchange.ru/ru/stocks/inostrannye/raspisanie/](https://spbexchange.ru/ru/stocks/inostrannye/raspisanie/)

По ссылке подробно расписан график торгов иностранными бумагами на бирже СПб.
{% endhint %}

{% file src="../../.gitbook/assets/nasdaq_sys_hours.pdf" %}
Часы работы систем Nasdaq
{% endfile %}

{% hint style="info" %}
Часы работы биржи NYSE и связанный с ней структур:

[https://www.nyse.com/markets/hours-calendars](https://www.nyse.com/markets/hours-calendars)
{% endhint %}

## Как получить список инструментов биржи СПб

#### Которые торгуются с 7:00 МСК

Переходим в список:

{% embed url="https://spbexchange.ru/ru/stocks/inostrannye/raspisanie/morning/" %}

Открываем инструменты разработчика (нажмите `Ctrl+Shift+I` или, если вы используете Mac, `Cmd+Opt+J`).

Введите код на вкладке Console и нажмите Enter:

```javascript
Array.from(document.querySelectorAll('td:nth-child(2)'))
  .map(td => td.textContent).join(' ')
```
