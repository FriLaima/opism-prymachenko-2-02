# Практична робота № 2

**Дисципліна:** Основи побудови інформаційних систем та мереж (ОК-13)

**Тема:** Структура повідомлень прикладного протоколу HTTP. Формування запиту вручну

| Поле | Значення |
|---|---|
| Студент (прізвище, ім'я, по батькові) | Примаченко Ксенія Анатоліївна |
| Група | КБЗІ-2.02 |
| Номер варіанта | 21 |
| Індивідуальний домен | openssl.org |
| «Чужий» домен для завдання A.3.1 (варіант ± 15) | kpi.ua |
| Середовище виконання | Linux (Kali) |
| Дата виконання | 06.10.2026 |

> Бланк заповнюють, не змінюючи структури розділів. Порожні заготовки блоків коду замінюють власними виводами. Позначки-підказки в кутових дужках вилучають.

---

## Частина A. Збір експериментальних даних

### Завдання A.1. Формування запиту вручну

**Команда:**

```
nc -C openssl.org 80
```

**Набраний запит:**

```
GET / HTTP/1.1    
Host: openssl.org
Connection: close
```

**Відповідь:**

```
HTTP/1.1 301 Moved Permanently
Cache-Control: private
Location: https://openssl.org:443/
Content-Length: 0
Date: Tue, 06 Oct 2026 09:53:25 GMT
Content-Type: text/html; charset=UTF-8
Connection: close
```

---

### Завдання A.2. Запит без поля `Host` у версії 1.1

**Команда:**

```
printf 'GET / HTTP/1.1\r\nConnection: close\r\n\r\n' | nc openssl.org 80
```

**Вивід:**

```
HTTP/1.1 301 Moved Permanently
Cache-Control: private
Location: https://34.49.79.89:443/
Content-Length: 0
Date: Tue, 06 Oct 2026 09:57:25 GMT
Content-Type: text/html; charset=UTF-8
Connection: close
```

### Завдання A.3. Вплив поля `Host` на відповідь сервера

#### A.3.1. Чуже доменне ім'я в полі `Host`

**Команда:**

```
printf 'GET / HTTP/1.1\r\nHost: kpi.ua\r\nConnection: close\r\n\r\n' | nc openssl.org 80
```

**Вивід:**

```
HTTP/1.1 301 Moved Permanently
Cache-Control: private
Location: https://kpi.ua:443/
Content-Length: 0
Date: Tue, 06 Oct 2026 10:00:33 GMT
Content-Type: text/html; charset=UTF-8
Connection: close
```

#### A.3.2. Неіснуюче ім'я в полі `Host`

**Команда:**

```
printf 'GET / HTTP/1.1\r\nHost: opism-pr02.invalid\r\nConnection: close\r\n\r\n' | nc openssl.org 80
```

**Вивід:**

```
HTTP/1.1 301 Moved Permanently
Cache-Control: private
Location: https://opism-pr02.invalid:443/
Content-Length: 0
Date: Tue, 06 Oct 2026 10:02:45 GMT
Content-Type: text/html; charset=UTF-8
Connection: close
```

#### A.3.3. Запит без поля `Host` у версії 1.0

**Команда:**

```
printf 'GET / HTTP/1.0\r\n\r\n' | nc openssl.org 80
```

**Вивід:**

```
HTTP/1.0 301 Moved Permanently
Cache-Control: private
Location: https://34.49.79.89:443/
Content-Length: 0
Date: Tue, 06 Oct 2026 10:03:40 GMT
Content-Type: text/html; charset=UTF-8
```

Зведення результатів наведено в **Додатку Д**.

---

### Завдання A.4. Два запити в одному з'єднанні

**Команда:**

```
printf 'GET /opism-pr02-12345 HTTP/1.1\r\nHost: openssl.org\r\n\r\nGET / HTTP/1.1\r\nHost: openssl.org\r\nConnection: close\r\n\r\n' | nc -C openssl.org 80
```

**Вивід:**

```
HTTP/1.1 301 Moved Permanently
Cache-Control: private
Location: https://openssl.org:443/opism-pr02-12345
Content-Length: 0
Date: Tue, 06 Oct 2026 10:08:07 GMT
Content-Type: text/html; charset=UTF-8
```

**Кількість отриманих відповідей:** 1.

**Коди стану отриманих відповідей:** 301 Moved Permanently.

---

### Завдання A.5. Запит за допомогою клієнтської програми

**Команда:**

```
curl -v --http1.1 http://openssl.org/ -o /dev/null 
```

**Вивід:**

```
*   Trying 34.49.79.89:80...
* Host openssl.org:80 was resolved.
* IPv6: 2600:1901:0:d50b::
* IPv4: 34.49.79.89
* Established connection to openssl.org (34.49.79.89 port 80) from 192.168.19.129 port 47992 
  % Total    % Received % Xferd  Average Speed  Time    Time    Time   Current
                                 Dload  Upload  Total   Spent   Left   Speed
  0      0   0      0   0      0      0      0                              0* using HTTP/1.x
> GET / HTTP/1.1
> Host: openssl.org
> User-Agent: curl/8.22.0
> Accept: */*
> 
* Request completely sent off
< HTTP/1.1 301 Moved Permanently
< Cache-Control: private
< Location: https://openssl.org:443/
< Content-Length: 0
< Date: Tue, 06 Oct 2026 10:13:40 GMT
< Content-Type: text/html; charset=UTF-8
< 
  0      0   0      0   0      0      0      0                              0
* Connection #0 to host openssl.org:80 left intact
```

---

### Завдання A.6. Запит через захищене з'єднання

**Ресурс, на якому виконано завдання:** openssl.org

**Підстава для використання резервного ресурсу (заповнюють за потреби):**

**Команда:**

```
openssl s_client -connect openssl.org:443 -servername openssl.org -crlf -quiet
```

**Набраний запит:**

```
GET / HTTP/1.1 
Host: openssl.org
Connection: close
```
<details>
<summary>**Вивід:**</summary>

```
HTTP/1.1 200 OK
x-guploader-uploadid: AP6rU83kWoQSr5oWB1K-CWYNfSZttTd1DWXszSF8r33pkyjdb1rVX-MBX1DxXouR4fCjOj5aes5OcMs
x-goog-generation: 1773234596883265
x-goog-metageneration: 29
x-goog-stored-content-encoding: identity
x-goog-stored-content-length: 7057
x-goog-meta-goog-reserved-file-mtime: 1790278422
x-goog-hash: crc32c=5zshHw==
x-goog-hash: md5=espRkH5CHPA5eiaXzQA8+Q==
x-goog-storage-class: STANDARD
accept-ranges: bytes
server: UploadServer
via: 1.1 google
date: Tue, 06 Oct 2026 10:18:17 GMT
Last-Modified: Wed, 11 Mar 2026 13:09:56 GMT
ETag: "7aca51907e421cf0397a2697cd003cf9"
Content-Type: text/html
Content-Length: 7057
Age: 1
Cache-Control: public,no-cache
X-Content-Type-Options: nosniff
X-Frame-Options: SAMEORIGIN
X-XSS-Protection: 1; mode=block
Referrer-Policy: strict-origin-when-cross-origin
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
Alt-Svc: h3=":443"; ma=2592000
Connection: close

<!DOCTYPE html>
<html lang="en">
  <head>
        <meta name="generator" content="Hugo 0.145.0">
    <title>
      
        OpenSSL
      
    </title>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    
  
    
    
      
        
          <link rel="stylesheet" href="/css/main.abe249895d3af7f37653d265973a575e7fbd4ad64b517d07ffdc2b164fe3f42d.css" integrity="sha256-q&#43;JJiV069/N2U9JllzpXXn&#43;9StZLUX0H/9wrFk/j9C0=" crossorigin="anonymous" />
        
      
    
  





  
    
      <link rel="stylesheet" href="/css/fontawesome.min.80a5cf80e4ba6d1ff172b4995ad5ce996071022227860700308ed797fbd150ad.css" integrity="sha256-gKXPgOS6bR/xcrSZWtXOmWBxAiInhgcAMI7Xl/vRUK0=" crossorigin="anonymous" />
    
  



  
    
      <link rel="stylesheet" href="/css/brands.9c662012d93fb448c96f67697bbee60ae06d6552398ca9487dcc79df4a032eb3.css" integrity="sha256-nGYgEtk/tEjJb2dpe77mCuBtZVI5jKlIfcx530oDLrM=" crossorigin="anonymous" />
    
  



  
    
      <link rel="stylesheet" href="/css/solid.40e9c76835b2f8346bf5a2dda24f8a0ec0624a4a2232ad15dad086f44a672772.css" integrity="sha256-QOnHaDWy&#43;DRr9aLdok&#43;KDsBiSkoiMq0V2tCG9EpnJ3I=" crossorigin="anonymous" />
    
  

<link rel="alternate" type="application/rss+xml" href="https://openssl.org/index.xml" title="OpenSSL">
  </head>
  <body class="flex flex-col min-h-screen">
    <nav class="bg-white top-0 z-50">
  <div class="container mx-auto px-4">
    
    <div class="flex px-8 h-24 items-center justify-end">

      
      <ul class="flex items-center space-x-4">
        
        
          
            
            
              <li>
                <a href="/faq/" class="inline-block py-2 px-3 text-lg hover:underline hover:decoration-dotted hover:underline-offset-4">
                  FAQ
                </a>
              </li>
            
          
        
          
            
            
              <li>
                <a href="/about/" class="inline-block py-2 px-3 text-lg hover:underline hover:decoration-dotted hover:underline-offset-4">
                  About
                </a>
              </li>
            
          
        
      </ul>
    </div>
  </div>
</nav>


    <main class="flex-1 flex max-md:flex-col py-10 justify-center container px-4 mx-auto">
      <div class="px-8 ">
        
  <section>
  <div class="container mx-auto px-4">
    <div class="w-3/5 mx-auto rounded-full flex flex-col items-center justify-center text-center relative mb-24">
      <p class="text-4xl font-light text-[#003d48] mb-8">
        MISSION
      </p>
      <p class="text-3xl font-bold italic mb-4">
        &ldquo;We believe everyone should have access to security and privacy tools,
whoever they are, wherever they are or whatever their personal beliefs
are, as a fundamental human right.&rdquo;
      </p>
        <a href="https://openssl-mission.org/" class="text-sm uppercase underline ml-auto">
          Discover Our Mission
        </a>
    </div>

    <div class="mt-10 grid grid-cols-1 sm:grid-cols-3 gap-12">
        <section>
          <div class="bg-white drop-shadow-[0_0px_10px_rgba(0,0,0,0.25)] rounded-lg p-6 text-center transition-all duration-300 hover:bg-[linear-gradient(180deg,_#fff_0%,_#fff_35%,_rgba(215,224,227,0.5)_100%)]">
              <a href="https://openssl-library.org/" class="block mb-4">
                <img class="w-full h-auto object-cover" src="/images/openssl_logo_library_393_265.png" alt="OpenSSL Library" />
              </a>
              <p class="text-3xl font-bold mb-2">OpenSSL Library</p>
              <p>
                <a href="https://openssl-library.org/" class="text-sm uppercase relative z-10">
                  Learn more
                </a>
              </p>
          </div>
        </section>
        <section>
          <div class="bg-white drop-shadow-[0_0px_10px_rgba(0,0,0,0.25)] rounded-lg p-6 text-center transition-all duration-300 hover:bg-[linear-gradient(180deg,_#fff_0%,_#fff_35%,_rgba(215,224,227,0.5)_100%)]">
              <a href="https://www.bouncycastle.org/" class="block mb-4">
                <img class="w-full h-auto object-cover" src="/images/bouncy_castle.png" alt="Bouncy Castle" />
              </a>
              <p class="text-3xl font-bold mb-2">Bouncy Castle</p>
              <p>
                <a href="https://www.bouncycastle.org/" class="text-sm uppercase relative z-10">
                  Learn more
                </a>
              </p>
          </div>
        </section>
        <section>
          <div class="bg-white drop-shadow-[0_0px_10px_rgba(0,0,0,0.25)] rounded-lg p-6 text-center transition-all duration-300 hover:bg-[linear-gradient(180deg,_#fff_0%,_#fff_35%,_rgba(215,224,227,0.5)_100%)]">
              <a href="https://cryptlib.com/" class="block mb-4">
                <img class="w-full h-auto object-cover" src="/images/cryptlib_logo_393_265.png" alt="Cryptlib" />
              </a>
              <p class="text-3xl font-bold mb-2">Cryptlib</p>
              <p>
                <a href="https://cryptlib.com/" class="text-sm uppercase relative z-10">
                  Learn more
                </a>
              </p>
          </div>
        </section>
    </div>
  </div>
</section>

  


      </div>
    </main>

    <footer class="bg-neutral-100 text-lg text-neutral-400 pt-2 pb-8">
  <div class="container px-4 mx-auto flex flex-col gap-4 items-center">
    <ul class="icons">
    </ul>

    <ul class="text-center font-light flex flex-wrap max-sm:gap-2 max-sm:flex-col *:px-4.5 sm:divide-x-1 divide-neutral-300">
          <li><a href="https://openssl.org">OpenSSL.org</a></li>
          <li><a href="https://openssl-library.org">Library</a></li>
          <li><a href="https://openssl-mission.org">Mission</a></li>
          <li><a href="https://openssl-communities.org">Communities</a></li>
          <li><a href="https://openssl-corporation.org">Corporation</a></li>
          <li><a href="https://openssl.foundation">Foundation</a></li>
          <li><a href="https://openssl-projects.org">Projects</a></li>
          <li><a href="https://openssl-conference.org">Conference</a></li>
    </ul>

    <div class="py-2 font-light">
      © 2026 OpenSSL. All rights reserved
    </div>
  </div>
</footer>


     <script defer async type="text/javascript" id="mp-loader" src="https://api.transpond.io/tracker?am=MzgyOTE%253D"></script>
    <script>
      document.addEventListener('click', function (event) {
        const target = event.target.closest('a');
        if (target && target.href) {
          const url = target.href;
          const url_path = target.href.replace(window.location.origin, '');
          const fileExtensions = ['pdf', 'zip', 'jpg', 'png', 'docx', 'xlsx'];
          const isDownload = target.hasAttribute('download') ||
                  fileExtensions.some(ext => url.endsWith('.' + ext));

          if (isDownload) {
            manualTracking(url_path, '', '', '', '', '', '')
          }
        }
      });
    </script>





  </body>
</html>
40D72ABDBC7F0000:error:0A000126:SSL routines::unexpected eof while reading:../ssl/record/rec_layer_s3.c:698:
40D72ABDBC7F0000:error:0A000197:SSL routines:SSL_shutdown:shutdown while in init:../ssl/ssl_lib.c:2804:
```
</details>

---

## Частина B. Розбір полів заголовка

Розбирається відповідь, отримана в завданні A.1.

**Загальна кількість полів заголовка у відповіді:** 6

| № | Поле заголовка | Значення | Призначення (власне формулювання) | Походження: сервер / проміжний вузол / не визначено | Обґрунтування |
|---|---|---|---|---|---|
| 1 | Cache-Control | private | Кешування дозволяється лише для конкретного клієнта | сервер | Сервер встановив `private`, що означає, що відповідь не повинна зберігатися у спільному кеші. Немає ознак зміни проміжним вузлом |
| 2 | Location | https://openssl.org:443/ | Указує адресу для перенаправлення | сервер | Значення містить конкретну цільову адресу `https://openssl.org:443/`. Немає ознак зміни проміжним вузлом |
| 3 | Content-Length | 0 | Указує розмір тіла відповіді | сервер | Значення `0` означає, що відповідь не містить тіла. Немає ознак зміни проміжним вузлом |
| 4 | Date | Tue, 06 Oct 2026 09:53:25 GMT | Указує час формування відповіді | сервер | Тут вказані дата й точний час, коли була сформована відповідь. Немає ознак зміни проміжним вузлом |
| 5 | Content-Type | text/html; charset=UTF-8 | Указує тип (HTML) і кодування вмісту (UTF-8) | сервер | У заголовку вказано, що відповідь має тип HTML і кодування UTF-8, але самої HTML-сторінки у відповіді немає |
| 6 | Connection | close | Указує на закриття з'єднання після відповіді | сервер | Значення `close` означає, що після передачі відповіді з'єднання буде закрито |

> Рядок наводять на кожне поле, яке реально надійшло. Зайві рядки вилучають, за потреби додають нові. Поле, походження якого встановити не вдалося, зазначають із позначкою «не визначено» та поясненням утруднення.

---

## Частина D. Висновки

Обсяг — 150–300 слів. Висновки спираються на власні спостереження.

**D.1.** Що з поведінки сервера виявилося неочевидним або несподіваним. Конкретно, з посиланням на рядок виводу.

Для неіснуючого ім'я в полі `Host` сервер сформував перенаправлення  `Location: https://opism-pr02.invalid:443/` (А.3.2).

**D.2.** Яке з полів заголовка викликало найбільше утруднення при визначенні походження (частина B) та з якої причини.

`Cache-Control: private` так як його між змінити проміжний вузол, але зазвичай його встановлює сервер і тому я обрала саме таке походження.

**D.3.** Яке питання залишилося без відповіді після виконання роботи.

Чому у мене в частині А ні разу не виводилась HTML-сторінка.

---

## Контрольні питання

**1.** У завданні A.1 сервер не надсилав відповіді, доки не було введено порожній рядок. Чим це зумовлено?

Порожній рядок потрібен для завершення HTTP-запиту. Без йього сервер очікує продовження введення запиту.

**2.** Порівняйте результати завдань A.1, A.2 та A.3.1–A.3.3 (таблиця Додатка Д). За яких значень поля `Host` і за якої версії протоколу сервер обслуговує запит, а за яких — ні? Яку задачу розв'язує поле `Host`? Відповідь має посилатися на конкретні рядки ваших виводів.

У A.1 при `Host: openssl.org` сервер повернув `301 Moved Permanently`. У A.2 без `Host` у `HTTP/1.1` сервер також відповів `301`, але в `Location` використав **IP-адресу**. У A.3.1 із `Host: kpi.ua` та в A.3.2 із неіснуючим `Host: opism-pr02.invalid` сервер теж відповів `301`. У A.3.3 для `HTTP/1.0` без `Host` також отримано `301`.

**3.** Скільки відповідей надійшло у завданні A.4 і з якими кодами стану? Чи залежить відповідь сервера на порту 80 від запитаного шляху — і що це говорить про роль цього сервера? Якщо надійшла одна відповідь, знайдіть у ній поле заголовка, яке це пояснює, або зазначте, що такого поля немає. Якщо надійшло дві — що це означає для клієнтської програми, яка завантажує сторінку з великою кількістю вкладених ресурсів?

Надійшла 1 відповідь `301 Moved Permanently`. Відповідь сервера на порті `80` не залежить від запитаного шляху в тому що сервер повертає `301`. Поля, яке б пояснювало відсутність другої відповіді немає.

**4.** Які поля заголовка програма `curl` додала самостійно (завдання A.5)? Ці поля не є обов'язковими — сервер відповів і без них у завданні A.1. З якою метою їх додано?

Програма `curl` самостійно додала поля `User-Agent: curl/8.22.0` та `Accept: */*`. `User-Agent` повідомляє серверу, яка програма виконує запит, а `Accept` вказує, які типи вмісту клієнт готовий прийняти.

**5.** За якими ознаками у вашому виводі виявляється присутність проміжного вузла? Якщо таких ознак не виявлено, поясніть, що з цього випливає.

У виводі А.6 видно заголовки `via: 1.1 google` та `server: UploadServer`. Поле `via` прямо показує участь іншого вузла під час передавання відповіді. Також у відповіді є багато заголовків `x-goog-*`, що вказує на використання інфраструктури **Google**.

**6.** Три рядки, про які не йшлося на лекції, наведено в **Додатку В**.

---

## Додаток В. Відповіді на питання 6

| № | Рядок виводу | Джерело (номер завдання) |
|---|---|---|
| 1 | Cache-Control: private | A.1 |
| 2 | Location: https://openssl.org:443/ | A.1 |
| 3 | User-Agent: curl/8.22.0 | A.5 |

---

## Додаток Д. Зведення результатів завдання A.3

**Вузол, з яким установлювалося з'єднання (у всіх пробах однаковий):** openssl.org:80

| Проба | Значення поля `Host` | Версія | Код стану | Обсяг тіла відповіді | Збігається з A.1 (так / ні) |
|---|---|---|---|---|---|
| A.1 (вихідна) | `openssl.org` | 1.1 | 301 | 0 | — |
| A.2 | поле відсутнє | 1.1 | 301 | 0 | так |
| A.3.1 | `kpi.ua` | 1.1 | 301 | 0 | так, але тільки `Location` інший |
| A.3.2 | `opism-pr02.invalid` | 1.1 | 301 | 0 | так, але тільки `Location` інший |
| A.3.3 | поле відсутнє | 1.0 | 301 | 0 | так |

**Висновок за таблицею (2–4 речення):** що саме змінювалося у запиті від проби до проби і як на це реагував сервер.

У пробах змінювалися значення поля `Host` та версія `HTTP`, а в `A.2` і `A.3.3` поле `Host` взагалі було відсутнє. У всіх випадках сервер повертав код `301` і порожнє тіло відповіді. Значення `Host` впливало переважно на заголовок `Location` для інших значень `Host` сервер формував відповідне перенаправлення.

---

## Декларування використання технологій штучного інтелекту

Для цієї роботи встановлено **рівень Р3 — ШІ як співвиконавець**.

Виводи команд частини A не можуть бути згенеровані та мають бути отримані внаслідок фактичного виконання команд.

**Чи використовувалися технології ШІ під час виконання роботи:** так.

| № | Інструмент (назва, версія) | Етап роботи | Дослівний текст запиту (промпту) | Як використано результат |
|---|---|---|---|---|
| 1 | GPT-5.6 Luna | Редагування | Виправ де потрібно мої формулювання в файлі README.md  | Редагування звіту |

**Підтвердження:** усі виводи команд, наведені в частині A, отримано внаслідок фактичного виконання команд на зазначеному індивідуальному домені.
