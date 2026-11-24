# Практична робота № 1

**Дисципліна:** Основи побудови інформаційних систем та мереж

**Тема:** Спостереження за процесом звернення до вебресурсу. Побудова власної моделі рівнів взаємодії

| | |
|---|---|
| **Прізвище, ім'я** | Шевченко Ірина |
| **Група** | ЗКІ-21 |
| **Номер варіанта** | 5 |
| **Домен варіанта** | afrinic.net |
| **Середовище виконання** | Windows |
| **Версія curl** | curl 8.4.0 (Windows) libcurl/8.4.0 Schannel WinIDN |
| **Дата виконання** | 24.11.2026 |

---

## Частина A. Збір експериментальних даних

### A.1. Запит із діагностичним виводом

**Команда:**

```
curl.exe -v https://afrinic.net
```

**Вивід:**

```
* Trying 196.216.3.4:443...
* Connected to afrinic.net (196.216.3.4) port 443
* schannel: disabled automatic use of client certificate
* ALPN: curl offers http/1.1
* ALPN: server accepted http/1.1
* using HTTP/1.1
> GET / HTTP/1.1
> Host: afrinic.net
> User-Agent: curl/8.4.0
> Accept: */*
>
* schannel: remote party requests renegotiation
* schannel: renegotiating SSL/TLS connection
* schannel: SSL/TLS connection renegotiated
* schannel: remote party requests renegotiation
* schannel: renegotiating SSL/TLS connection
* schannel: SSL/TLS connection renegotiated
* schannel: failed to decrypt data, need more data
< HTTP/1.1 200 OK
< server: nginx
< date: Mon, 23 Nov 2026 23:33:45 GMT
< content-type: text/html
< content-length: 55961
< last-modified: Mon, 23 Nov 2026 13:23:10 GMT
< etag: "6b043e3e-da99"
< accept-ranges: bytes
< strict-transport-security: max-age=31536000; includeSubDomains; preload
< content-security-policy: frame-ancestors 'self' https://translate.google.com https://translate.googleusercontent.com
< x-xss-protection: 1; mode=block
< x-content-type-options: nosniff
< set-cookie: SRV=; Expires=Thu, 01-Jan-1970 00:00:01 GMT; path=/
< cache-control: private
```

---

### A.2. Запит без захисту з'єднання

**Команда:**

```
curl.exe -v http://neverssl.com
```

**Вивід:**

```
* Trying 34.223.124.45:80...
* connect to 34.223.124.45 port 80 failed: Timed out
* Failed to connect to neverssl.com port 80 after 93095 ms: Couldn't connect to server
* Closing connection
curl: (28) Failed to connect to neverssl.com port 80 after 93095 ms: Couldn't connect to server
```

---

### A.3. Запит до служби доменних імен

**Команда (перше виконання):**

```
Resolve-DnsName afrinic.net
```

**Вивід:**

```
Name                                           Type   TTL   Section    IPAddress
----                                           ----   ---   -------    ---------
afrinic.net                                    AAAA   60    Answer     2001:42d0:0:250::4
afrinic.net                                    A      60    Answer     196.216.3.4
```

**Команда (повторне виконання через 5–7 хвилин):**

```
Resolve-DnsName afrinic.net
```

**Вивід:**

```
Name                                           Type   TTL   Section    IPAddress
----                                           ----   ---   -------    ---------
afrinic.net                                    AAAA   60    Answer     2001:42d0:0:250::4
afrinic.net                                    A      60    Answer     196.216.3.4
```

**Зафіксовані значення:**

| Параметр | Перше виконання | Повторне виконання |
|---|---|---|
| Час виконання (год:хв) | 01:22 | 01:31 |
| IP-адреса | 2001:42d0:0:250::4 | 2001:42d0:0:250::4 |
| Значення TTL | 60 | 60 |

> Якщо друге значення TTL виявилося більшим за перше — це нормально: кеш резолвера встиг оновитися. Зафіксуйте як є.

---

### A.4. Контрольний ресурс

**Команда:**

```
curl.exe -v https://google.com
```

**Вивід:**

```
*   Trying 142.250.130.101:443...
* Connected to google.com (142.250.130.101) port 443
* schannel: disabled automatic use of client certificate
* ALPN: curl offers http/1.1
* ALPN: server accepted http/1.1
* using HTTP/1.1
> GET / HTTP/1.1
> Host: google.com
> User-Agent: curl/8.4.0
> Accept: */*
>
* schannel: remote party requests renegotiation
* schannel: renegotiating SSL/TLS connection
* schannel: SSL/TLS connection renegotiated
* schannel: failed to decrypt data, need more data
< HTTP/1.1 301 Moved Permanently
< Location: https://www.google.com/
< Content-Type: text/html; charset=UTF-8
< Content-Security-Policy-Report-Only: object-src 'none';base-uri 'self';script-src 'nonce-UOGsoBBc81yyde-LwTdwhQ' 'strict-dynamic' 'report-sample' 'unsafe-eval' 'unsafe-inline' https: http:;report-uri https://csp.withgoogle.com/csp/gws/other-hp
< Date: Mon, 23 Nov 2026 23:59:25 GMT
< Expires: Wed, 23 Dec 2026 23:59:25 GMT
< Cache-Control: public, max-age=2592000
< Server: gws
< Content-Length: 220
< X-XSS-Protection: 0
< X-Frame-Options: SAMEORIGIN
< Alt-Svc: h3=":443"; ma=2592000,h3-29=":443"; ma=2592000
<
<HTML><HEAD><meta http-equiv="content-type" content="text/html;charset=utf-8">
<TITLE>301 Moved</TITLE></HEAD><BODY>
<H1>301 Moved</H1>
The document has moved
<A HREF="https://www.google.com/">here</A>.
</BODY></HTML>
* Connection #0 to host google.com left intact
```

---

### A.5. Ресурси з некоректною конфігурацією сертифіката

**Випадок 1**

```
curl.exe -v https://expired.badssl.com
```

```
*   Trying 104.154.89.105:443...
* Connected to expired.badssl.com (104.154.89.105) port 443
* schannel: disabled automatic use of client certificate
* ALPN: curl offers http/1.1
* schannel: next InitializeSecurityContext failed: SEC_E_CERT_EXPIRED (0x80090328) - Получен сертификат с истекшим сроком действия.
* Closing connection
* schannel: shutting down SSL/TLS connection with expired.badssl.com port 443
curl: (35) schannel: next InitializeSecurityContext failed: SEC_E_CERT_EXPIRED (0x80090328) - Получен сертификат с истекшим сроком действия.
```

**Випадок 2**

```
curl.exe -v https://wrong.host.badssl.com
```

```
*   Trying 104.154.89.105:443...
* Connected to wrong.host.badssl.com (104.154.89.105) port 443
* schannel: disabled automatic use of client certificate
* ALPN: curl offers http/1.1
* schannel: SNI or certificate check failed: SEC_E_WRONG_PRINCIPAL (0x80090322) - Главное конечное имя неверно.
* Closing connection
* schannel: shutting down SSL/TLS connection with wrong.host.badssl.com port 443
curl: (60) schannel: SNI or certificate check failed: SEC_E_WRONG_PRINCIPAL (0x80090322) - Главное конечное имя неверно.
More details here: https://curl.se/docs/sslcerts.html

curl failed to verify the legitimacy of the server and therefore could not
establish a secure connection to it. To learn more about this situation and
how to fix it, please visit the web page mentioned above.
```

**Випадок 3**

```
curl.exe -v https://self-signed.badssl.com
```

```
*   Trying 104.154.89.105:443...
* Connected to self-signed.badssl.com (104.154.89.105) port 443
* schannel: disabled automatic use of client certificate
* ALPN: curl offers http/1.1
* schannel: SEC_E_UNTRUSTED_ROOT (0x80090325) - Цепочка сертификатов выпущена центром сертификации, не имеющим доверия.
* Closing connection
* schannel: shutting down SSL/TLS connection with self-signed.badssl.com port 443
curl: (60) schannel: SEC_E_UNTRUSTED_ROOT (0x80090325) - Цепочка сертификатов выпущена центром сертификации, не имеющим доверия.
More details here: https://curl.se/docs/sslcerts.html

curl failed to verify the legitimacy of the server and therefore could not
establish a secure connection to it. To learn more about this situation and
how to fix it, please visit the web page mentioned above.
```

> Якщо використано альтернативний спосіб із параметром `--resolve` — зазначити це та навести фактичну команду.

---

## Частина B. Власна модель рівнів

**Кількість виділених груп:** _7_

| № | Назва групи (власне формулювання) | Рядки виводу, віднесені до групи | Обґрунтування |
|---|---|---|---|
| 1 | Користувацький запит | curl.exe -v https://afrinic.net | Відображає команду, яку користувач вводить для звернення до вебресурсу. |
| 2 | Обмін HTTP-повідомленнями |тGET / HTTP/1.1, Host: afrinic.net, User-Agent: curl/8.4.0, HTTP/1.1 200 OK, HTTP/1.1 301 Moved Permanently | Ці рядки описують формування HTTP-запиту та отримання відповіді від сервера. |
| 3 | Перетворення доменного імені | Resolve-DnsName afrinic.net, A, AAAA, TTL | Показують отримання IP-адрес за доменним іменем та час зберігання DNS-записів у кеші. |
| 4 | Захист з'єднання | SSL/TLS, ALPN, schannel, SEC_E_CERT_EXPIRED, SEC_E_WRONG_PRINCIPAL, SEC_E_UNTRUSTED_ROOT | Ці рядки стосуються встановлення захищеного з'єднання та перевірки сертифікатів. |
| 5 | Встановлення транспортного з'єднання | Trying, Connected, port 443, port 80, Timed out |  Відображають спроби встановлення з'єднання із сервером через відповідний порт та результат цих спроб. |
| 6 | IP-адресація |  196.216.3.4, 2001:42d0:0:250::4, 142.250.130.101 | Вказують на IP-адреси, які використовуються для ідентифікації серверів у мережі. |
| 7 | Передавання через мережеве обладнання | У виводі curl безпосередньо не відображено | Фізичне передавання даних відбувається через мережеві пристрої та середовище зв'язку, але ці процеси не спостерігаються безпосередньо в отриманих результатах. |

*Групи впорядковано від найближчої до користувача (№ 1) до найближчої до апаратного забезпечення. Зайві рядки вилучити, за потреби — додати.*

**Рядки, які не вдалося віднести до жодної групи:**

| Рядок виводу | Причина утруднення |
|---|---|
| * schannel: disabled automatic use of client certificate | Повідомлення стосується налаштувань TLS у Windows, тому його складно віднести лише до одного рівня взаємодії. |
| * schannel: failed to decrypt data, need more data | Відображає внутрішній процес обробки захищених даних бібліотекою Schannel, а не безпосередньо обмін HTTP-повідомленнями. |
| * Connection #0 to host afrinic.net left intact | Повідомляє про стан з'єднання після завершення запиту, але не описує окремий мережевий протокол або процес передавання даних. |

---

## Контрольні питання

**1. Скільки рядків діагностичного виводу передує отриманню даних сторінки (завдання A.1)?**

> До початку HTML-вмісту було отримано 30 рядків діагностичного виводу, які містять інформацію про встановлення з'єднання, узгодження протоколу, HTTP-запит та відповідь сервера.

**2. Які рядки наявні у виводі A.1 і відсутні у виводі A.2? Чим це зумовлено?**

> У результаті A.1 присутні рядки, пов'язані з SSL/TLS, Schannel та ALPN. У результаті A.2 вони відсутні, оскільки використовується HTTP, який не передбачає встановлення захищеного TLS-з'єднання.

**3. Звідки у виводі з'явилося значення `443`, якщо його не було вказано в адресі?**

>  Порт 443 є стандартним портом для HTTPS-з'єднань. Програма curl автоматично використовує його, якщо в адресі ресурсу зазначено протокол HTTPS і порт не задано окремо.

**4. Як змінилося значення TTL між двома запитами (A.3)? Що означає це число?**

> Під час обох DNS-перевірок значення TTL становило 60 секунд для записів A та AAAA. Отримані IP-адреси також залишилися незмінними: 196.216.3.4 та 2001:42d0:0:250::4.

**5. Чим відрізняються між собою три причини помилок із завдання A.5? Сформулювати кожну однією фразою.**

| Випадок | Причина недовіри |
|---|---|
| `expired` | Прострочений сертифікат |
| `wrong.host` | Невідповідність доменного імені сертифікату |
| `self-signed` | Недовірений самопідписаний сертифікат |

**6. Три рядки з власних виводів, про які не йшлося на лекції 1:**

| № | Рядок виводу | Джерело (номер завдання) |
|---|---|---|
| 1 | * schannel: remote party requests renegotiation | А.1 |
| 2 | < strict-transport-security: max-age=31536000; includeSubDomains; preload | А.1 |
| 3 | < alt-svc: h3=":443"; ma=2592000,h3-29=":443"; ma=2592000 | А.4 |

*Пояснення до цих рядків не потрібне.*

---

## Висновки


**D.1. Що виявилося неочевидним або несподіваним**

Несподіваним виявилося те, що успішне встановлення з'єднання із сервером ще не гарантує успішного отримання вебсторінки. Це можна побачити на прикладі команди A.5 для домену expired.badssl.com: у виводі є рядок * Connected to expired.badssl.com (104.154.89.105) port 443, але далі з'являється повідомлення SEC_E_CERT_EXPIRED, яке свідчить про прострочений сертифікат. Отже, мережеве з'єднання було встановлено, але захищене HTTPS-з'єднання не завершилося успішно через помилку перевірки сертифіката. Також неочевидним стало те, що під час DNS-перевірки один домен може повертати одночасно IPv4- та IPv6-адреси, що підтверджують записи A та AAAA у завданні A.3.

> 

**D.2. Чому саме така кількість груп у частині B**

Для власної моделі було обрано сім груп, оскільки під час аналізу результатів команд вдалося виділити сім функціонально відмінних напрямів: користувацький запит, HTTP-взаємодію, DNS-перетворення, захист TLS, транспортне з'єднання, IP-адресацію та передавання через мережеве обладнання. Рішення ґрунтувалося на призначенні кожного процесу та характері рядків, отриманих під час виконання команд. Наприклад, рядки GET / HTTP/1.1 та HTTP/1.1 200 OK стосуються обміну HTTP-повідомленнями, а SEC_E_CERT_EXPIRED — перевірки захищеного з'єднання. Кількість груп можна було б змінити, якби під час детальнішого аналізу виявилося, що окремі групи мають спільне призначення або, навпаки, містять процеси, які потребують окремого розгляду. Також на рішення могла б вплинути поява додаткових даних, які дозволили б точніше розділити мережеві процеси.

> 

**D.3. Питання, яке залишилося без відповіді**

> 

---

## Використання штучного інтелекту

*Розділ обов'язковий. Заповнюється незалежно від того, чи використовувався ШІ. Детальні вимоги — у документі «Політика використання технологій штучного інтелекту».*

**Факт використання:** використано / не використано *(потрібне залишити)*

**Установлений рівень для цієї роботи:** Р3 — ШІ як співвиконавець

**Фактичний рівень використання:** Р3

### Використані системи

| Система | Версія або модель | Період використання |
|---|---|---|
| ChatGPT | GPT-6 | 23-24.11.2026|

### Промпти

*Наводити дослівно, у тому вигляді, у якому запит було надано системі. Переказ не приймається.*

| № | Розділ роботи | Текст промпта |
|---|---|---|
| 1 | Аналіз завдання та виконання команд | Проаналізуй умови практичної роботи та поясни, що саме потрібно виконати. Покроково детально розпиши команди у Windows і розбери отримані результати. |
| 2 | Аналіз результатів і заповнення таблиць | Допоможи проаналізувати отримані результати команд і заповнити таблиці відповідно до вимог завдання. Використовуй дійсні дані,які я відправила тобі, а не вигадуй відсутню інформацію. |
| 3 | Висновки та оформлення звіту | Допоможи сформулювати відповіді на контрольні питання та висновки до практичної роботи відповідно до методичних вимог, використовуючи отримані мною результати. |

### Дії з отриманим результатом

| № промпта | Що перевірено | Що змінено | Що відхилено і чому |
|---|---|---|---|
| 1 | Проаналізовано умови практичної роботи, вимоги бланка та безпечність команд | Обрано покроковий спосіб виконання  | Відхилено можливість отримати готові результати команд від ШІ, оскільки їх потрібно отримувати самостійно |
| 2 | Перевірено відповідність інструкцій операційній системі Windows 11 | Використано команди curl.exe та Resolve-DnsName | Відхилено команди, не призначені для використання у Windows |
| 3 | Перевірено отримані результати та відповідність оформлення вимогам завдання | Уточнено структуру таблиць, пояснення та висновки відповідно до зауважень | Відхилено неточні або непідтверджені формулювання |

### Підтвердження

Підтверджую, що всі наведені в цьому звіті виводи команд отримано мною особисто внаслідок фактичного виконання відповідних дій, а відомості цього розділу є повними та достовірними.

> Виводи `curl`, `dig` та інші артефакти не можуть бути згенеровані. Це стосується будь-якого рівня використання ШІ.

---

## Примітки виконавця

*(необов'язковий розділ: що не спрацювало, які команди довелося змінити, які виникли труднощі)
