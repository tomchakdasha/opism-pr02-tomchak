# Практична робота № 2

**Дисципліна:** Основи побудови інформаційних систем та мереж (ОК-13)

**Тема:** Структура повідомлень прикладного протоколу HTTP. Формування запиту вручну

| Поле | Значення |
|---|---|
| Студент (прізвище, ім'я, по батькові) | Томчак Дар'я Ігорівна|
| Група | ІПЗ-2.01 |
| Номер варіанта | 31 |
| Індивідуальний домен | icann.org |
| «Чужий» домен для завдання A.3.1 (варіант ± 20) | nbuv.gov.ua |
| Середовище виконання | https://killercoda.com/playgrounds/scenario/ubuntu |
| Дата виконання | 05.10.2026 |

> Бланк заповнюють, не змінюючи структури розділів. Порожні заготовки блоків коду замінюють власними виводами. Позначки-підказки в кутових дужках вилучають.

---

## Частина A. Збір експериментальних даних

### Завдання A.1. Формування запиту вручну

**Команда:**

```
nc -C icann.org 80
```

**Набраний запит:**

```
GET / HTTP/1.1
Host: icann.org
Connection: close

```

**Відповідь:**

```
root@ubuntu:~$ nc -C icann.org 80
GET / HTTP/1.1
Host: icann.org
Connection: close

HTTP/1.1 301 Moved Permanently
Date: Sun, 04 Oct 2026 12:53:12 GMT
Server: Apache
Location: https://www.icann.org/
Cache-Control: max-age=345600
Expires: Thu, 08 Oct 2026 12:53:12 GMT
Content-Length: 270
Connection: close
Content-Type: text/html; charset=iso-8859-1

<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">
<html><head>
<title>301 Moved Permanently</title>
</head><body>
<h1>Moved Permanently</h1>
<p>The document has moved <a href="https://www.icann.org/">here</a>.</p>
</body></html>
```

---

### Завдання A.2. Запит без поля `Host` у версії 1.1

**Команда:**

```
printf 'GET / HTTP/1.1\r\nConnection: close\r\n\r\n' | nc icann.org 80
```

**Вивід:**

```
root@ubuntu:~$ printf 'GET / HTTP/1.1\r\nConnection: close\r\n\r\n' | nc icann.org 80
HTTP/1.1 400 Bad Request
Date: Wed, 30 Sep 2026 07:44:13 GMT
Server: Apache
Content-Length: 387
Connection: close
Content-Type: text/html; charset=iso-8859-1

<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">
<html><head>
<title>400 Bad Request</title>
</head><body>
<h1>Bad Request</h1>
<p>Your browser sent a request that this server could not understand.<br />
</p>
<p>Additionally, a 400 Bad Request
error was encountered while trying to use an ErrorDocument to handle the request.</p>
</body></html>
```

---

### Завдання A.3. Вплив поля `Host` на відповідь сервера

#### A.3.1. Чуже доменне ім'я в полі `Host`

**Команда:**

```
printf 'GET / HTTP/1.1\r\nHost: nbuv.gov.ua\r\nConnection: close\r\n\r\n' | nc icann.org 80
```

**Вивід:**

```
root@ubuntu:~$ printf 'GET / HTTP/1.1\r\nHost: nbuv.gov.ua\r\nConnection: close\r\n\r\n' | nc icann.org 80
HTTP/1.1 503 Service Unavailable
Date: Sun, 04 Oct 2026 14:27:34 GMT
Server: Apache
Content-Length: 28
Connection: close
Content-Type: text/html; charset=iso-8859-1
```

#### A.3.2. Неіснуюче ім'я в полі `Host`

**Команда:**

```
printf 'GET / HTTP/1.1\r\nHost: opism-pr02.invalid\r\nConnection: close\r\n\r\n' | nc icann.org 80
```

**Вивід:**

```
root@ubuntu:~$ printf 'GET / HTTP/1.1\r\nHost: opism-pr02.invalid\r\nConnection: close\r\n\r\n' | nc icann.org 80
HTTP/1.1 503 Service Unavailable
Date: Wed, 30 Sep 2026 07:48:27 GMT
Server: Apache
Content-Length: 28
Connection: close
Content-Type: text/html; charset=iso-8859-1

Status 503: Invalid headers.
```

#### A.3.3. Запит без поля `Host` у версії 1.0

**Команда:**

```
printf 'GET / HTTP/1.0\r\n\r\n' | nc icann.org 80
```

**Вивід:**

```
root@ubuntu:~$ printf 'GET / HTTP/1.0\r\n\r\n' | nc icann.org 80
HTTP/1.1 503 Service Unavailable
Date: Wed, 30 Sep 2026 07:49:24 GMT
Server: Apache
Content-Length: 28
Connection: close
Content-Type: text/html; charset=iso-8859-1

Status 503: Invalid headers.
```

Зведення результатів наведено в **Додатку Д**.

---

### Завдання A.4. Два запити в одному з'єднанні

**Команда:**

```
printf 'GET /opism-pr02-12345 HTTP/1.1\r\nHost: icann.org\r\n\r\nGET / HTTP/1.1\r\nHost: icann.org\r\nConnection: close\r\n\r\n' | nc -C icann.org 80
```

**Вивід:**

```
root@ubuntu:~$ printf 'GET /opism-pr02-12345 HTTP/1.1\r\nHost: icann.org\r\n\r\nGET / HTTP/1.1\r\nHost: icann.org\r\nConnection: close\r\n\r\n' | nc -C icann.org 80
HTTP/1.1 301 Moved Permanently
Date: Wed, 30 Sep 2026 07:42:28 GMT
Server: Apache
Location: https://www.icann.org/opism-pr02-12345
Cache-Control: max-age=345600
Expires: Sun, 04 Oct 2026 07:42:28 GMT
Content-Length: 286
Content-Type: text/html; charset=iso-8859-1

<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">
<html><head>
<title>301 Moved Permanently</title>
</head><body>
<h1>Moved Permanently</h1>
<p>The document has moved <a href="https://www.icann.org/opism-pr02-12345">here</a>.</p>
</body></html>
HTTP/1.1 301 Moved Permanently
Date: Wed, 30 Sep 2026 07:42:28 GMT
Server: Apache
Location: https://www.icann.org/
Cache-Control: max-age=345600
Expires: Sun, 04 Oct 2026 07:42:28 GMT
Content-Length: 270
Connection: close
Content-Type: text/html; charset=iso-8859-1

<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">
<html><head>
<title>301 Moved Permanently</title>
</head><body>
<h1>Moved Permanently</h1>
<p>The document has moved <a href="https://www.icann.org/">here</a>.</p>
</body></html>
```

**Кількість отриманих відповідей:** 2

**Коди стану отриманих відповідей:** 301 Moved Permanently

---

### Завдання A.5. Запит за допомогою клієнтської програми

**Команда:**

```
curl -v http://icann.org/
```

**Вивід:**

```
root@ubuntu:~$ curl -v http://icann.org/
* Host icann.org:80 was resolved.
* IPv6: 2001:500:88:200::7
* IPv4: 192.0.43.7
*   Trying 192.0.43.7:80...
* Connected to icann.org (192.0.43.7) port 80
> GET / HTTP/1.1
> Host: icann.org
> User-Agent: curl/8.5.0
> Accept: */*
> 
< HTTP/1.1 301 Moved Permanently
< Date: Wed, 30 Sep 2026 07:19:25 GMT
< Server: Apache
< Location: https://www.icann.org/
< Cache-Control: max-age=345600
< Expires: Sun, 04 Oct 2026 07:19:25 GMT
< Content-Length: 270
< Content-Type: text/html; charset=iso-8859-1
< 
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">
<html><head>
<title>301 Moved Permanently</title>
</head><body>
<h1>Moved Permanently</h1>
<p>The document has moved <a href="https://www.icann.org/">here</a>.</p>
</body></html>
* Connection #0 to host icann.org left intact
```

---

### Завдання A.6. Запит через захищене з'єднання

**Ресурс, на якому виконано завдання:** <власний домен / `iana.org`>

**Підстава для використання резервного ресурсу (заповнюють за потреби):**

**Команда:**

```
openssl s_client -connect icann.org:443 -servername icann.org -crlf
```

**Набраний запит:**

```
GET / HTTP/1.1
Host: icann.org
Connection: close

```

**Вивід:**

```
root@ubuntu:~$ openssl s_client -connect icann.org:443 -servername icann.org -crlf
CONNECTED(00000003)
depth=2 C = GB, O = Sectigo Limited, CN = Sectigo Public Server Authentication Root R46
verify return:1
depth=1 C = GB, O = Sectigo Limited, CN = Sectigo Public Server Authentication CA OV R36
verify return:1
depth=0 C = US, ST = California, O = Internet Corporation For Assigned Names and Numbers, CN = *.icann.org
verify return:1
---
Certificate chain
 0 s:C = US, ST = California, O = Internet Corporation For Assigned Names and Numbers, CN = *.icann.org
   i:C = GB, O = Sectigo Limited, CN = Sectigo Public Server Authentication CA OV R36
   a:PKEY: rsaEncryption, 4096 (bit); sigalg: RSA-SHA256
   v:NotBefore: Dec  6 00:00:00 2025 GMT; NotAfter: Jan  5 23:59:59 2027 GMT
 1 s:C = GB, O = Sectigo Limited, CN = Sectigo Public Server Authentication CA OV R36
   i:C = GB, O = Sectigo Limited, CN = Sectigo Public Server Authentication Root R46
   a:PKEY: rsaEncryption, 3072 (bit); sigalg: RSA-SHA384
   v:NotBefore: Mar 22 00:00:00 2021 GMT; NotAfter: Mar 21 23:59:59 2036 GMT
 2 s:C = GB, O = Sectigo Limited, CN = Sectigo Public Server Authentication Root R46
   i:C = US, ST = New Jersey, L = Jersey City, O = The USERTRUST Network, CN = USERTrust RSA Certification Authority
   a:PKEY: rsaEncryption, 4096 (bit); sigalg: RSA-SHA384
   v:NotBefore: Mar 22 00:00:00 2021 GMT; NotAfter: Jan 18 23:59:59 2038 GMT
 3 s:C = US, ST = New Jersey, L = Jersey City, O = The USERTRUST Network, CN = USERTrust RSA Certification Authority
   i:C = GB, ST = Greater Manchester, L = Salford, O = Comodo CA Limited, CN = AAA Certificate Services
   a:PKEY: rsaEncryption, 4096 (bit); sigalg: RSA-SHA384
   v:NotBefore: Mar 12 00:00:00 2019 GMT; NotAfter: Dec 31 23:59:59 2028 GMT
---
Server certificate
-----BEGIN CERTIFICATE-----
MIIIQDCCBqigAwIBAgIRAON9AbjfZwRzT1stP+CuufAwDQYJKoZIhvcNAQELBQAw
YDELMAkGA1UEBhMCR0IxGDAWBgNVBAoTD1NlY3RpZ28gTGltaXRlZDE3MDUGA1UE
AxMuU2VjdGlnbyBQdWJsaWMgU2VydmVyIEF1dGhlbnRpY2F0aW9uIENBIE9WIFIz
NjAeFw0yNTEyMDYwMDAwMDBaFw0yNzAxMDUyMzU5NTlaMHYxCzAJBgNVBAYTAlVT
MRMwEQYDVQQIEwpDYWxpZm9ybmlhMTwwOgYDVQQKEzNJbnRlcm5ldCBDb3Jwb3Jh
dGlvbiBGb3IgQXNzaWduZWQgTmFtZXMgYW5kIE51bWJlcnMxFDASBgNVBAMMCyou
aWNhbm4ub3JnMIICIjANBgkqhkiG9w0BAQEFAAOCAg8AMIICCgKCAgEAx2t92RLQ
591En+oX60jMdoX6S/o4uav4PSa56Em7iRvVa88OLDgIrbga6TeXuv8NQADxDyh/
mCXSYFYU8fwSoULYInZGsmFeITVjfwGKYJEXEzeq2jbWlKuDcJgtrZgpUT3kVM8t
Woie7wisi2qApZ3lay0uYQ+O0GG3424bwwkq7buuIQxZEgdWYaZUgkKasS13LehL
l/8+wKxN3Ou+g7NTMW225j1IkNQrATJzLeVHz4Yk86hZt8whR7/fXPRoN2H8Y31l
EEiHunpnrwv2AW4t5sIZqQu3eUyY86JLpLIJ/XfY4XYxt8Gd6aknbako0Y4Lyz+V
qNxe1D1SwPibNiLdTfLSwm3FjOv/i3vNdBfnizJBBpweilRqpo9zV85DtPpggNiw
3m7IBYAwbDs5mNpOLR/c4tZu/y7AclbzE6Rsbr2evmKb7vL27vCb0LmeopqUdFi/
zU4GLJj9LlvrjlzvKsKINWSslsA23DdIK3lsMSHn8qudLeJvUswenZjvGrKdfobt
2lCxzrBS9W0bgj/OJxHGt28AlGntQvQIh8d89N16T3RiMpl8RR4+wfYLORbumwX2
BdHgX0xf+JjKyXjw5nywoTBo6faqakRTe3V/GzIAibrSix/obsty9dVUO4VgFfQh
TdlH5WRtbejX/9s93OtLsYWGQkOfqTA7DpkCAwEAAaOCA10wggNZMB8GA1UdIwQY
MBaAFONmdLtwaI0sXU4OpkqPmzcinIKSMB0GA1UdDgQWBBQfihD1bH3YSpIFW4kZ
aRws2+AsvTAOBgNVHQ8BAf8EBAMCBaAwDAYDVR0TAQH/BAIwADAdBgNVHSUEFjAU
BggrBgEFBQcDAQYIKwYBBQUHAwIwSgYDVR0gBEMwQTA1BgwrBgEEAbIxAQIBAwQw
JTAjBggrBgEFBQcCARYXaHR0cHM6Ly9zZWN0aWdvLmNvbS9DUFMwCAYGZ4EMAQIC
MFQGA1UdHwRNMEswSaBHoEWGQ2h0dHA6Ly9jcmwuc2VjdGlnby5jb20vU2VjdGln
b1B1YmxpY1NlcnZlckF1dGhlbnRpY2F0aW9uQ0FPVlIzNi5jcmwwgYQGCCsGAQUF
BwEBBHgwdjBPBggrBgEFBQcwAoZDaHR0cDovL2NydC5zZWN0aWdvLmNvbS9TZWN0
aWdvUHVibGljU2VydmVyQXV0aGVudGljYXRpb25DQU9WUjM2LmNydDAjBggrBgEF
BQcwAYYXaHR0cDovL29jc3Auc2VjdGlnby5jb20wIQYDVR0RBBowGIILKi5pY2Fu
bi5vcmeCCWljYW5uLm9yZzCCAYwGCisGAQQB1nkCBAIEggF8BIIBeAF2AHUAYEya
r3p/d18B1Ab8kg3ImesLHH34yVIb+voXdzuXi8kAAAGa8QAR5gAABAMARjBEAiAi
vl5MUbM/yseNTCRicPEXkvdUsSEPA+C6XofLDAfqPAIgPolsuLb3EcpxoINbdRmP
MVE7YydnDpGYYPg3B3UmSxUAfQCOykcLrN5q86IGsKR6hLdG/h/Gv5U+JeabTuQC
SPPG6AAAAZrxABKhAAgAAAUAAFde9wQDAEYwRAIgdzMSZMB7/eWhMRq4yW2oEpeT
QfaslGC5HRDNAzA5j6ICIBCROBAlwBEsrH93ekrWgEIq7zDGfP5aTNgBQwmR1AZU
AH4AWW5sM4aUsllyolbIoOjdkEp26Ag92oc7AQg4KBQ87lkAAAGa8QATqQAIAAAF
AAAAm3QEAwBHMEUCIETMG7SHinWy6NPNH6bgTvef02uRN3VFAGSSKozmPHNeAiEA
034c8mvRdlLA0oMeIfbM0lSNWKiYjj9QCoWOYqfCLm0wDQYJKoZIhvcNAQELBQAD
ggGBAHgEPALXhF78mDO/N38jWB5OERMQ6PF1LdalP1QA29EdnZlDIrZy6bfFnjVi
sxllLbJzyU5BVj8EzIoO++PGk+brs1TkKvObDJxv2FMStGuohLZbzXtrO2riei2x
RQmwsKrQAHM+Oa/LCLcgurX3bJO2ezO4jTZmBC1OP93WTt7UA4YffYrSrZlUNO5O
Sl47grj5IQWMdNTam9Z1wHr6t1Hvl4bz+RNrYR7jeRPJx8BmlLNj6jVK0CCy9odG
Hw0Zyj014fKIqUFkG0sXtf3fWQlRVwLIEunWuXsKHaQ4Zqhuyhd1vQknNfC07Tty
p0R00mnKPyEQlnBR8DlVmJqZGbSvhOzSlR352eks2ts7B32AQMHYZpAagG4CuERk
nCB6/65SBVd2V6RbjUmD8in3VotE/4x6AUthnn2KLBgXmvVj4f/10Q4j7C6VBTC9
S2KQhM3din6nh3OPBuG8dKPMlt8jiaQOS17tLiBYEU62CESZpog4GDNM+o0smDiw
j/tXKg==
-----END CERTIFICATE-----
subject=C = US, ST = California, O = Internet Corporation For Assigned Names and Numbers, CN = *.icann.org
issuer=C = GB, O = Sectigo Limited, CN = Sectigo Public Server Authentication CA OV R36
---
No client certificate CA names sent
Peer signing digest: SHA512
Peer signature type: RSA-PSS
Server Temp Key: X25519, 253 bits
---
SSL handshake has read 7661 bytes and written 391 bytes
Verification: OK
---
New, TLSv1.3, Cipher is TLS_AES_256_GCM_SHA384
Server public key is 4096 bit
Secure Renegotiation IS NOT supported
Compression: NONE
Expansion: NONE
No ALPN negotiated
Early data was not sent
Verify return code: 0 (ok)
---
GET / HTTP/1.1
Host: icann.org
Connection: close

HTTP/1.1 301 Moved Permanently
Date: Sun, 04 Oct 2026 13:02:45 GMT
Server: Apache
Location: https://www.icann.org/
Cache-Control: max-age=345600
Expires: Thu, 08 Oct 2026 13:02:45 GMT
Content-Length: 270
Connection: close
Content-Type: text/html; charset=iso-8859-1
Strict-Transport-Security: max-age=48211200; preload

<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">
<html><head>
<title>301 Moved Permanently</title>
</head><body>
<h1>Moved Permanently</h1>
<p>The document has moved <a href="https://www.icann.org/">here</a>.</p>
</body></html>
40D71A6117760000:error:0A000126:SSL routines:ssl3_read_n:unexpected eof while reading:../ssl/record/rec_layer_s3.c:316:
```

---

## Частина B. Розбір полів заголовка

Розбирається відповідь, отримана в завданні A.1.

**Загальна кількість полів заголовка у відповіді:**

| № | Поле заголовка | Значення | Призначення (власне формулювання) | Походження: сервер / проміжний вузол / не визначено | Обґрунтування |
|---|---|---|---|---|---|
| 1 | Date | Sun, 04 Oct 2026 12:53:12 GMT | Показується точна дата, коли була надіслана відповідь | сервер | Сервер автоматично вказує дату формування відповіді |
| 2 | Server | Apache | Визначення програмного забезпечення сервера  | сервер | Так як вузол не переслав запит далі, а самостійно згенерував відповідь із перенаправленням, і у виводі немає полів посередника по типу Age, Via і тд, для цього конкретного запиту він є сервером |
| 3 | Location | https://www.icann.org/ | Адреса, куди нас редиректить | сервер | Сервер нас перенаправляє та зазначає адресу перенаправлення |
| 4 | Cache-control | max-age=345600 | Визначається максимальний час зберігання даних | сервер | Сервер зазначає як кешувати дані |
| 5 | Expires | Thu, 08 Oct 2026 12:53:12 GMT | Точна дата, коли копія сторінки стане простроченою | сервер | Сервер визначає час коли копія сторінки прострочиться і потрібно буде надсилати новий запит |
| 6 | Content-Length | 270 | Розмір відповіді у байтах | сервер | Саме сервер формує відповідіть, тому тільки він може порахувати її розмір |
| 7 | Connection | close | З'єдання закрито після надання відповіді | сервер | Сервер підтверджує, що з'єднання було закрито, так як про це зазначалося у запиті |
| 8 | Content-Type | text/html; charset=iso-8859-1 | Тип контенту, який передав сервер та його кодування | сервер | Сервер вказує в якому вигляді було отримано дані |

> Рядок наводять на кожне поле, яке реально надійшло. Зайві рядки вилучають, за потреби додають нові. Поле, походження якого встановити не вдалося, зазначають із позначкою «не визначено» та поясненням утруднення.

---

## Частина D. Висновки

Обсяг — 150–300 слів. Висновки спираються на власні спостереження.

**D.1.** Що з поведінки сервера виявилося неочевидним або несподіваним. Конкретно, з посиланням на рядок виводу.

У завданні A.4 на запит до не існуючого шляху GET /opism-pr02-12345 він не видав помилку 404, а просто перенаправив мене на таку ж адресу через HTTPS код 301 Moved Permanently

**D.2.** Яке з полів заголовка викликало найбільше утруднення при визначенні походження (частина B) та з якої причини.

Найважче виявилося виявити походження Server: Apache, бо воно також може виступати як проксі сервер, але так як не було ніяких інших ознак проксі, було вирішено, що його походження сервер. Також вагалася з заголовком Cache-Control: max-age=345600, в лекції записів щодо нього не знайшла, тому інтуїтивно віднесла до сервера

**D.3.** Яке питання залишилося без відповіді після виконання роботи.

Не зрозуміло чому при введенні чужого або вигаданого домену в полі Host в завданнях A.3.1 та A.3.2 сервер видає помилку 503 Service Unavailable. Код 5xx означає відмову на боці сервера, але ж це ми надіслали неправильний запит, тому було б логічніше отримати помилку 400?

---

## Контрольні питання

**1.** У завданні A.1 сервер не надсилав відповіді, доки не було введено порожній рядок. Чим це зумовлено?

Порожній рядок відділяє запит від тіла відповіді, без нього сервер думає, що ми продовжуємо вписувати запити

**2.** Порівняйте результати завдань A.1, A.2 та A.3.1–A.3.3 (таблиця Додатка Д). За яких значень поля `Host` і за якої версії протоколу сервер обслуговує запит, а за яких — ні? Яку задачу розв'язує поле `Host`? Відповідь має посилатися на конкретні рядки ваших виводів.

Сервер обслуговує запит і відповідає кодом HTTP/1.1 301 Moved Permanently лише у версії протоколу 1.1, якщо правильно вказано поле Host: icann.org, як це було у завданні A.1. Якщо цього поля нема, сервер повертає помилку HTTP/1.1 400 Bad Request як у вивіді завдання A.2. Якщо вказати чужий домен nbuv.gov.ua, неіснуючий домен opism-pr02.invalid або використати версію протоколу 1.0, сервер не обробляє запит і видає HTTP/1.1 503 Service Unavailable у всіх трьох випадках завдання A.3. Поле Host є обов'язковим у версії 1.1, так як на одній ІР-адресі може бути багато сайтів, це поле допомагає серверу визначити, який саме сайт віддати клієнту

**3.** Скільки відповідей надійшло у завданні A.4 і з якими кодами стану? Чи залежить відповідь сервера на порту 80 від запитаного шляху — і що це говорить про роль цього сервера? Якщо надійшла одна відповідь, знайдіть у ній поле заголовка, яке це пояснює, або зазначте, що такого поля немає. Якщо надійшло дві — що це означає для клієнтської програми, яка завантажує сторінку з великою кількістю вкладених ресурсів?

<відповідь>

**4.** Які поля заголовка програма `curl` додала самостійно (завдання A.5)? Ці поля не є обов'язковими — сервер відповів і без них у завданні A.1. З якою метою їх додано?

curl самостійно додала поля User-Agent: curl/8.5.0 та Accept: */*. Поле User-Agent повідомляє серверу назву та версію програми клієнта, а поле Accept вказує на те, що клієнт готовий прийняти та обробити абсолютно будь-який тип даних. Ці поля не є обов'язковими для базової відповіді, але їх додають для ідентифікації та надання серверу додаткової інформації про можливості клієнта

**5.** За якими ознаками у вашому виводі виявляється присутність проміжного вузла? Якщо таких ознак не виявлено, поясніть, що з цього випливає.

У виводах не було явних ознак проміжного вузла по типу Via чи Age, з цього випливає, що проміжного вузла або немає, або він просто не додавав своїх рядків

**6.** Три рядки, про які не йшлося на лекції, наведено в **Додатку В**.

---

## Додаток В. Відповіді на питання 6

| № | Рядок виводу | Джерело (номер завдання) |
|---|---|---|
| 1 | Cache-Control: max-age=345600 | А.1 |
| 2 | Expires: Sun, 04 Oct 2026 07:42:28 GMT | А.4 |
| 3 | Strict-Transport-Security: max-age=48211200; preload | А.6 |

---

## Додаток Д. Зведення результатів завдання A.3

**Вузол, з яким установлювалося з'єднання (у всіх пробах однаковий):**

| Проба | Значення поля `Host` | Версія | Код стану | Обсяг тіла відповіді | Збігається з A.1 (так / ні) |
|---|---|---|---|---|---|
| A.1 (вихідна) | icann.org | 1.1 | 301 | 270 | — |
| A.2 | поле відсутнє | 1.1 | 400 | 387 | ні |
| A.3.1 | nbuv.gov.ua | 1.1 | 503 | 28 | ні |
| A.3.2 | `opism-pr02.invalid` | 1.1 | 503 | 28 | ні |
| A.3.3 | поле відсутнє | 1.0 | 503 | 28 | ні |

**Висновок за таблицею (2–4 речення):** що саме змінювалося у запиті від проби до проби і як на це реагував сервер.

За відсутності обов'язкового поля Host у версії HTTP/1.1 сервер видає помилку клієнта 400 Bad Request. Якщо ж у запиті вказати чужий чи неіснуючий домен або виконати запит у версії HTTP/1.0, сервер блокує його помилкою 503. Сервер здійснює сувору перевірку поля Host 

---

## Декларування використання технологій штучного інтелекту

Для цієї роботи встановлено **рівень Р3 — ШІ як співвиконавець**.

Виводи команд частини A не можуть бути згенеровані та мають бути отримані внаслідок фактичного виконання команд.

**Чи використовувалися технології ШІ під час виконання роботи:** <так / ні>

Якщо так, заповнюють таблицю. Якщо ні, таблицю вилучають.

| № | Інструмент (назва, версія) | Етап роботи | Дослівний текст запиту (промпту) | Як використано результат |
|---|---|---|---|---|
| 1 | | | | |
| 2 | | | | |

**Підтвердження:** усі виводи команд, наведені в частині A, отримано внаслідок фактичного виконання команд на зазначеному індивідуальному домені.

---

*ОПІСМ (ОК-13) · Практична робота № 2 · бланк звіту*
