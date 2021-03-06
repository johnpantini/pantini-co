---
description: Intermarket Sweep Order
---

# Правило SEC 611 и ISO-ордер

## Правило SEC 611

Если вы попробуете купить $AAPL за $1500 за одну акцию (по состоянию на 2020 год), биржа этого не позволит. Почему? Потому что существует правило защиты ордеров **SEC 611**, которое не позволяет производить покупки дороже самого лучшего продавца (самый низкий ask в стакане, он же называется **best ask** или **best offer**) и продажи дешевле самого лучшего покупателя (самый высокий bid в стакане, он же называется **best bid**). Вместо $1500 заявка будет исполнена "по рынку". Это правило защищает от недоразумений, если цена была введена неправильно, а потом вы отправили заявку на биржу.

![Биржевой стакан из терминала Аврора](<../../.gitbook/assets/image (19).png>)

Посмотрим на биржевой стакан США. Видно, что в разных рыночных центрах может находиться ликвидность по одинаковой цене, например, **9983** акции в **Bats EDGX** и **10000 **в **NYSE Arca **по цене 0,5135$. Когда все рыночные центры сортируются в стакане по лучшим ценам, то такой вид стакана называется "montage" - "сборка". Биржевой стакан в Америке называют Level 2 (второй уровень), DOM (Depth of Market - глубина рынка), Order Book (книга заявок). Самый лучший ask - 0,5179$ (**EDGX**), самый лучший bid - 0,516$ (**EXB**). Вместе их называют **NBBO** (National Best Bid and Offer) - термин, относящийся к требованию **SEC**, чтобы брокеры осуществляли сделки с клиентами по лучшим ценам (из всех рыночных центров). Часто это значит, что клиентская заявка пройдет некоторый путь маршрутизации, чтобы исполниться по лучшей цене.

**Правило 611** "на пальцах" говорит, что если инвестор хочет купить акции "по рынку", то ему должны продать бумаги по цене не хуже лучшего ask (на картинке это цена 0,5179$). То же самое и с продажей, только наоборот.

В Америке разбор заявок происходит последовательно от лучших цен к худшим. То есть, пока покупатели не разберут ask по 0,5179$, никто не купит по 0,518$ (следующий ask на картинке).

Этот принцип не работает на бирже СПб, где используется пропорциональное (объёму заявки) исполнение заявок.

## Исключения из правила SEC 611

Нарушение правила **611 **называется торговлей сквозь рынок (**trade through**). Торговля сквозь рынок - это покупка по цене выше, чем лучший ask, или продажа по цене ниже, чем лучший bid.

У любого правила есть исключения, их всего 9, я опишу три наиболее понятных. 

{% hint style="info" %}
Принт - это сделка (не заявка) в ленте всех сделок на бирже. Название историческое, потому что раньше (во времена, когда на бирже орали голосом) тикер (механическое устройство) пропечатывал котировку на бумаге. Print - печатать. А сейчас тикером называют краткий код акции из нескольких букв и цифр.

[Подробнее о ленте сделок](../aurora.md)
{% endhint %}

1. Действие правила распространяется только на основную торговую сессию. В системные часы (пре- и постмаркет) правило не форсируется, можно получить любую цену.
2. Допускается торговля сквозь рынок в течение периода в 1 секунду. Если принт прошел по цене, которая секунду назад не была хуже рынка, а сейчас стала хуже (пока ордер исполнялся) то такой принт не считается торговлей сквозь рынок и помечается в ленте как "освобожденный" от правила **SEC 611** (**trade through exempt**). Для бумаг, листингованных на **NYSE**, это будет единичный флаг в sale condition. Для бумаг с **NASDAQ **это будет буква **X**. Это исключение покрывает несовершенство распределённых систем (задержки, проблемы с сетью и прочие технические сбои), коими являются рыночные центры.
3. Самое часто используемое исключение. Оно было добавлено **SEC **в правило **611 **специально для того, чтобы позволить крупным (да любым, в принципе) инвесторам покупать и продавать бумаги большими блоками, по возможности при этом не влияя на рынок, сохраняя свою конфиденциальность.

## Межрыночный sweep-ордер (Intermarket Sweep Order, ISO)

{% hint style="info" %}
[Статья про ISO в английской Википедии](https://en.wikipedia.org/wiki/Intermarket_sweep_order)
{% endhint %}

Исключение представлено в виде особого типа заявки, которая называется **ISO **(intermarket sweep-order, межрыночный свип-ордер). **Sweep **в данном контексте означает "пылесосить".

Предположим, стоит задача купить **100000 **акций какой-то компании. По правилу **611 **котировка лучшего продавца (**NBO - national best offer**) защищена и должна быть откуплена в обязательном порядке. Но что, если там продаётся всего **1000 **акций? 

Заявка будет исполнена частично, а **99000** бумаг остатка перейдут на сторону покупателей (bid) и будут висеть. Когда на bid висит такой большой объем, то это может спровоцировать покупки алгоритмическими системами (стервятниками). Грубо говоря, они понимают, что пришел крупный реальный покупатель, и начинают покупать, чувствуя поддержку от выставленной "плиты" в **99000 **акций. Тем самым они получают ликвидность "вперёд" инвестора, и цена от него "уходит". В таком режиме практически невозможно торговать крупными блоками акций.

**ISO **решает проблему. Эта заявка забирает ликвидность сразу из нескольких рыночных центров (включая частные, **Dark Pools**), пока ордер не будет исполнен всем объемом.

{% hint style="warning" %}
Ордер - это заявка.
{% endhint %}

Возвращаясь к раннему примеру, при покупке **100000 **акций, **1000 **бумаг купится по NBO (national best offer), а оставшиеся **99000 **_НЕ_ будут висеть на bid, а пойдут "пылесоситься" по другим рыночным центрам, где самая лучшая цена (но ХУЖЕ, чем начальная, у первого центра), **до полного исполнения**.

Например, **1000 **бумаг купится по 100$ (начальная цена) в **NSDQ**, далее **5000 **бумаг купится по 100,2$ (хуже начальной, но все в порядке - ордер помечен системой как **ISO**, правило **611 **не распространяется) в **PACX**, потом **10000 **бумаг купится по 100,25$ (тоже хуже начальной цены) в **BATS **и т.д., до полной "наливки". Интересный момент состоит в том, что остаток в **99000 **может быть исполнен в Dark Pool (приватном рыночном центре) вообще мимо стакана и без влияния на рыночную цену, чем и пользуются инвесторы.

**ISO** помечается в ленте сделок буквой **F** в поле условий сделки (см. картинку ниже). Цифра **1** в ленте сделок означает, что на сделку правило **611 **не распространяется (**trade through exempt**). Цифра **1** ставится для бумаг, листингованных на **NYSE**. Для бумаг, листингованных на **NASDAQ**, ставится буква **X**.

![Лента принтов (репродукция) с ISO-сделками (буква F)](<../../.gitbook/assets/image (18).png>)

Биржа **SPB **применяет **ISO**-ордера, когда (по её усмотрению) необходимо маршрутизировать русскую ликвидность в пулы ликвидности США.
