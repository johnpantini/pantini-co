# Решето Аткина

{% hint style="info" %}
Решето Аткина — алгоритм нахождения всех простых чисел до заданного целого числа `limit`.
{% endhint %}

{% embed url="https://en.wikipedia.org/wiki/Sieve_of_Atkin" %}

{% code title="atkin.js" %}
```javascript
module.exports = function (limit) {
  const limitSqrt = Math.sqrt(limit);
  const sieve     = Buffer.alloc(limit);
  let n;

  sieve[2] = 1;
  sieve[3] = 1;

  for (let x = 1; x <= limitSqrt; x++) {
    const xx = x * x;

    for (let y = 1; y <= limitSqrt; y++) {
      const yy = y * y;

      if (xx + yy >= limit) {
        break;
      }

      n = (4 * xx) + (yy);

      if (n <= limit && (n % 12 === 1 || n % 12 === 5)) {
        sieve[n] ^= 1;
      }

      n = (3 * xx) + (yy);

      if (n <= limit && (n % 12 === 7)) {
        sieve[n] ^= 1;
      }

      n = (3 * xx) - (yy);

      if (x > y && n <= limit && (n % 12 === 11)) {
        sieve[n] ^= 1;
      }
    }
  }

  for (n = 5; n <= limitSqrt; n++) {
    if (sieve[n]) {
      const x = n * n;

      for (let i = x; i <= limit; i += x) {
        sieve[i] = 0;
      }
    }
  }

  return sieve;
};
```
{% endcode %}

Буфер с результатом удобно сохранить в бинарный файл, чтобы пользоваться им в будущем без необходимости что-либо вычислять, например:

```javascript
const s9   = require('./atkin')(1e9 + 1);
const s500 = require('./atkin')((1e9 / 2) + 1);
const s8   = require('./atkin')(1e8 + 1);
const s7   = require('./atkin')(1e7 + 1);
const s6   = require('./atkin')(1e6 + 1);

require('fs').writeFileSync('./1-billion-sieve.dat', s9);
require('fs').writeFileSync('./500-million-sieve.dat', s500);
require('fs').writeFileSync('./100-million-sieve.dat', s8);
require('fs').writeFileSync('./10-million-sieve.dat', s7);
require('fs').writeFileSync('./1-million-sieve.dat', s6);
```
