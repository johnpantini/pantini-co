# Определение цен открытия/закрытия бумаг с Nasdaq

Материал подразумевает самостоятельную работу над неясными терминами.\
\
Рынки должны быть эффективными, прозрачными и ликвидными. Nasdaq был первым полностью электронным рынком США, где обкатывались новейшие технологии, начиная с момента его создания в 70-х годах прошлого века, чтобы обеспечить именно такое функционирование.\
\
Технология определения цены открытия/закрытия бумаги - сложный процесс, который реализуется в несколько этапов.\
\
Рассказ пойдёт и о цене закрытия, и о цене открытия вперемешку. Смотрите по контексту. Для цены открытия, можно сказать, меняется только время и название ордеров (буква C заменяется на О).\
\
Официальную цену закрытия мы видим в ленте принтов как сделку с флагом M в 16:00 по времени Нью-Йорка. Затем эта цена попадает на все ресурсы типа Webull, Investing, Yahoo и т. п. Лента принтов первична, график - вторичен.\
\
Пример такого принта можно найти в бумаге с тикером CARA:

![Лента сделок (репродукция)](<../../.gitbook/assets/image (23).png>)

В данном случае цена закрытия равна 14.95 $, а объем стоит совершенно нетипичный, слишком больше среднего. 17 410 акций.\
\
Это суммарный объем, расшифровку которого можно получить, если обратиться к истории сделок на сайте Nasdaq (сложите объёмы по таблице в колонке **NLS Share Volume**, какое число получилось?) :

{% embed url="https://www.nasdaq.com/market-activity/stocks/cara/latest-real-time-trades" %}

| NLS Time (ET) | NLS Price | NLS Share Volume |
| ------------- | --------- | ---------------- |
| 13:00:01      | $ 14.95   | 531              |
| 13:00:01      | $ 14.95   | 1,703            |
| 13:00:01      | $ 14.95   | 704              |
| 13:00:01      | $ 14.95   | 495              |
| 13:00:01      | $ 14.95   | 359              |
| 13:00:01      | $ 14.95   | 380              |
| 13:00:01      | $ 14.95   | 987              |
| 13:00:01      | $ 14.95   | 343              |
| 13:00:01      | $ 14.95   | 166              |
| 13:00:01      | $ 14.95   | 1,348            |
| 13:00:01      | $ 14.95   | 9                |
| 13:00:01      | $ 14.95   | 830              |
| 13:00:01      | $ 14.95   | 64               |
| 13:00:01      | $ 14.95   | 1,278            |
| 13:00:01      | $ 14.95   | 18               |
| 13:00:01      | $ 14.95   | 7                |
| 13:00:01      | $ 14.95   | 2,611            |
| 13:00:01      | $ 14.95   | 387              |
| 13:00:01      | $ 14.95   | 166              |
| 13:00:01      | $ 14.95   | 563              |

Продолжение на 2-й странице:

| NLS Time (ET) | NLS Price | NLS Share Volume |
| ------------- | --------- | ---------------- |
| 13:00:01      | $ 14.95   | 171              |
| 13:00:01      | $ 14.95   | 107              |
| 13:00:01      | $ 14.95   | 47               |
| 13:00:01      | $ 14.95   | 357              |
| 13:00:01      | $ 14.95   | 144              |
| 13:00:01      | $ 14.95   | 1,703            |
| 13:00:01      | $ 14.95   | 498              |
| 13:00:01      | $ 14.95   | 250              |
| 13:00:01      | $ 14.95   | 5                |
| 13:00:01      | $ 14.95   | 470              |
| 13:00:01      | $ 14.95   | 588              |
| 13:00:01      | $ 14.95   | 2                |
| 13:00:01      | $ 14.95   | 119              |

{% hint style="info" %}
NLS расшифровывается как Nasdaq Last Sale - сделка в рыночном центре Nasdaq.
{% endhint %}

Для определения цены Nasdaq использует механизм, известный под названием перекрестной или скрещивающей сессии (crossing session). Принт окончания перекрестной сессии обозначается флагом **X** после значка **@** (последний обозначает обычную сделку).

{% embed url="https://pantini.gitbook.io/pantini-co/faq/aurora#ts-nasdaq-flags" %}

Эта сессия длится 2 минуты. В целом же у NASDAQ есть 4 перекрёстные  сессии. Это сессия открытия, закрытия, IPO и приостановка торгов (trading halt). Цель у всех одна - определить цену, с которой начнется торговля дальше (или же закончится).\
\
Заявки продавцов и покупателей сводятся (скрещиваются) к "общему знаменателю" наиболее эффективным и недискриминирующим с точки зрения Nasdaq способом.\
\
Цена открытия или закрытия будет напрямую зависеть от заявок на покупку и продажу. Если есть перекос по заявкам в одну или другую сторону, то это называется имбалансом. Он обозначается буквой **B **- покупателей больше, чем продавцов,** S** - продавцов больше, **N** - нет имбаланса.

Есть несколько типов заявок, которые можно ставить на открытии или закрытии. Эти заявки как раз и нужны, чтобы повлиять на имбаланс.\
\
Вот их типы:

* Лимитные заявки по цене закрытия (**On-Close Orders**) или открытия (**On-Open Orders**) - они выполняются только в рамках перекрестной сессии, потому что им нужна та самая цена закрытия или открытия для исполнения. Такие лимитные ордера сокращённо называют **LOC **и **LOO **соответственно.
* Имбалансные заявки - нужны для поддержания ликвидности, ставятся дилерами NASDAQ.
* Есть и рыночные заявки на закрытии и открытии (**MOC** и **MOO**) .

За две минуты до начала основной торговой сессии запускается перекрёстная сессия открытия (The Nasdaq Opening Cross) - в 16:28 МСК. NASDAQ начинает рассчитывать имбаланс на основе всех ордеров, поставленных ранее и продолжающих поступать в реальном времени. Расчет ведется в виде индикатора, который называется **NOII **- **Net Order Imbalance Indicator**. Картинка идёт ниже. На закрытии проводится аналогичная сессия **Closing Cross**.

![Индикатор NOII](<../../.gitbook/assets/image (22).png>)

Там красным шрифтом написано, что информация об имбалансе начнет распространяться трейдерам с 9:28 AM ET - 16:28 МСК.\
\
Описание некоторых колонок. **Paired Shares** - это количество акций, которые могут быть сопоставлены сейчас (то есть где спрос встречает предложение) по текущей опорной цене.  **Imbalance Shares** - разница в акциях (продажа-покупка по модулю), которые не могут быть сопоставлены. Далее идет сторона имбаланса (**imbalance side**) - кого больше, продавцов или покупателей. **Current Reference Price** - текущая опорная цена. Эта цена определяется алгоритмически так, чтобы количество **Paired Shares** было максимальным, а **Imbalance Shares** - минимальным, если бы в данный момент мы свели всех продавцов с покупателями. Решается задача оптимизации (в математическом смысле). Индикатор **NOII **обновляется каждые 5 секунд.\
\
Известен алгоритм, по которому рассчитывается диапазон, куда попадёт цена открытия или закрытия. В описании ниже цены bid и ask берутся только по рыночному центру Nasdaq (Q) - перекрёстная сессия проходит только там.\
\
Берется 10% (но не ниже 0,5$) от среднего значения между bid и ask и добавляется к ask, вычитается из bid. Например, если bid равен $5, ask равен $5.1, то 10% будет = (5+5.1) / 2 \*0.1 = 0.505$. Цена, таким образом, будет в диапазоне  5-0.505 / 5.1+0.505 = 4.495$ bid/5.605$ ask.\
\
Пока длятся эти две минуты, Nasdaq сводит вместе все ранее поданные и текущие заявки воедино и определяет финальную цену.\
\
Колонка на картинке **Near Indicative Clearing Price** - ближняя индикативная цена - цена, при которой будет сопоставлено наибольшее количество акций по заявкам по цене открытия/закрытия и рыночным заявкам, поступающим сейчас.\
\
Эта цена в 16:30 и будет ценой открытия (или в 23:00 ценой закрытия по МСК). **Far Indicative Clearing Price** - дальняя индикативная цена. то же, что и ближняя цена, но рыночные заявки не учитываются. В сухом остатке решается задача оптимизации: снизить имбаланс. Для этого заявки **On Open**/**On Close** и имбалансные заявки, поступившие от участников торгов, подстраиваются под лучшие цены.\
\
Для постмаркета процесс такой же, как для премаркета, только скрещивание идет по другому расписанию: расчет **NOII** начинается в 22:55. В 22:58 лимитные заявки по цене закрытия **LOC **прекращают поступать, в 23:00 - сессия заканчивается, появляется принт в ленте с флагом **X**.

{% hint style="info" %}
В терминале Webull есть пробный бесплатный период, позволяющий смотреть некоторые данные по перекрёстным сессиям.

[https://www.webull.com/activity/get-free-nasdaq-totalview](https://www.webull.com/activity/get-free-nasdaq-totalview)
{% endhint %}
