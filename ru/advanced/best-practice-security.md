---
layout: page
title: Лучшие практические методы защиты для Express в рабочей среде
menu: advanced
lang: ru
description: Discover crucial security best practices for Express apps in production,
  including using TLS, input validation, secure cookies, and preventing vulnerabilities.
---

# Лучшие практические методы для рабочей среды: Защита

## Обзор

Термин *"рабочий режим"* означает тот этап жизненного цикла программного обеспечения, на котором приложение или API является в целом доступным для конечных пользователей или потребителей. Напротив, на этапе *"разработки"* происходит активное создание и тестирование кода, и приложение не является открытым для внешнего доступа. Соответствующие системные среды называются, соответственно, *рабочей* средой и средой *разработки*.

Настройки среды разработки и рабочей среды при установке, как правило, являются различными, и к этим средам предъявляются абсолютно разные требования. То, что идеально для разработки, не всегда приемлемо в рабочем режиме. Например, в среде разработки можно задать подробное протоколирование ошибок для отладки, тогда как в рабочей среде такая особенность настройки может привести к уязвимости защиты. Во время разработки можно не беспокоиться о масштабируемости, надежности и производительности, тогда как в рабочем режиме все эти вопросы играют решающую роль.

В настоящей статье речь пойдет о лучших практических методах в области защиты приложений Express, развернутых в рабочей среде.

## Не используйте устаревшие или уязвимые версии Express

Версии Express 2.x и 3.x больше не поддерживаются. Проблемы, связанные с защитой и производительностью в этих версиях, не будут подлежать решению. Не используйте эти версии!  Если вы еще не перешли к работе с версией 4, выполните инструкции, приведенные в [руководстве по миграции](/{{ page.lang }}/guide/migrating-4.html).

Кроме того, убедитесь в том, что уязвимые версии Express, перечисленные на странице [Обновления системы защиты](/{{ page.lang }}/advanced/security-updates.html), вами не используются. В противном случае, выполните обновление до одного из стабильных выпусков, предпочтительно, до последнего.

## Использование TLS

Если ваше приложение предназначено для работы с чувствительными данными или для их передачи, для защиты соединения и данных необходимо использовать криптографический протокол [Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security) (TLS). Данная технология позволяет шифровать данные до передачи с клиента на сервер, тем самым обеспечивая защиту от многих  распространенных (и простых) способов несанкционированного доступа. Хотя запросы Ajax и POST могут казаться неочевидными и "скрытыми" в браузерах, инициируемая ими передача данных в сети является уязвимой для [незаконного сбора и анализа пакетов](https://en.wikipedia.org/wiki/Packet_analyzer) и[атак посредника (атак "человек посередине")](https://en.wikipedia.org/wiki/Man-in-the-middle_attack).

Возможно, вам знаком криптографический протокол Secure Socket Layer (SSL). [SSL является предшественником TLS](https://msdn.microsoft.com/en-us/library/windows/desktop/aa380515(v=vs.85).aspx). Другими словами, если раньше вы пользовались SSL, пора переходить к TLS.  В целом, для работы с TLS мы рекомендуем использовать сервер Nginx.  Подробные инструкции по настройке TLS на Nginx (и на других серверах) можно найти в разделе    [Рекомендуемые конфигурации серверов (Mozilla Wiki)](https://wiki.mozilla.org/Security/Server_Side_TLS#Recommended_Server_Configurations).

Кроме того, удобным инструментом для получения бесплатного сертификата TLS является [Let's Encrypt](https://letsencrypt.org/about/) - бесплатная, автоматическая и открытая сертификатная  компания (CA), предоставленная корпорацией [Internet Security Research Group (ISRG)](https://letsencrypt.org/isrg/).

## Использование Helmet

[Helmet](https://www.npmjs.com/package/helmet) помогает защитить приложение от некоторых широко известных веб-уязвимостей путем соответствующей настройки заголовков HTTP.

Helmet, по сути, представляет собой набор из девяти более мелких функций промежуточной обработки, обеспечивающих настройку заголовков HTTP, связанную с защитой:

* [csp](https://github.com/helmetjs/csp) задает заголовок `Content-Security-Policy` для предотвращения атак межсайтового скриптинга и прочих межсайтовых вмешательств.
* [hidePoweredBy](https://github.com/helmetjs/hide-powered-by) удаляет заголовок `X-Powered-By`.
* [hsts](https://github.com/helmetjs/hsts) задает заголовок `Strict-Transport-Security`, принудительно активирующий защиту соединений с сервером (по протоколу HTTP с использованием SSL/TLS).
* [ieNoOpen](https://github.com/helmetjs/ienoopen) задает заголовок `X-Download-Options` для IE8+.
* [noCache](https://github.com/helmetjs/nocache) задает заголовок `Cache-Control` и заголовки Pragma для отключения кеширования на стороне клиента.
* [noSniff](https://github.com/helmetjs/dont-sniff-mimetype) задает заголовок `X-Content-Type-Options` для защиты браузеров от прослушивания (сниффинга) MIME ответов с отличным от объявленного типом содержимого (content-type).
* [frameguard](https://github.com/helmetjs/frameguard) задает заголовок `X-Frame-Options` для защиты от  [кликджекинга](https://www.owasp.org/index.php/Clickjacking).
* [xssFilter](https://github.com/helmetjs/x-xss-protection) задает заголовок `X-XSS-Protection` для активации фильтра XSS (фильтра межсайтового скриптинга) в большинстве современных веб-браузеров.

Установите Helmet, как обычный модуль:

```bash
$ npm install --save helmet
```

Затем используйте его в своем коде:

```js
/// ...

const helmet = require('helmet')
app.use(helmet())

/// ...
```

### Как минимум, отключите заголовок X-Powered-By

Если использовать Helmet не нужно, как минимум, отключите заголовок `X-Powered-By`.  Злоумышленники могут использовать этот заголовок (включенный по умолчанию) для выявления приложений на базе Express и активации целенаправленных атак.

Поэтому рекомендуется отключить данный заголовок с помощью метода `app.disable()`.

```js
app.disable('x-powered-by')
```

Если вы используете `helmet.js`, это будет сделано автоматически.

## Безопасное использование cookie

Для того чтобы файлы cookie не подвергали опасности ваши приложения, не используйте стандартные имена сеансовых cookie и соответствующим образом настройте опции защиты файлов cookie.

Существует два основных сеансовых модуля cookie для промежуточной обработки:

* Модуль [express-session](https://www.npmjs.com/package/express-session), заменяющий собой промежуточный обработчик `express.session`, встроенный в Express 3.x.
* Модуль [cookie-session](https://www.npmjs.com/package/cookie-session), заменяющий собой промежуточный обработчик `express.cookieSession`, встроенный в Express 3.x.

Основное различие между этими двумя модулями состоит в способе сохранения сеансовых данных cookie.  Промежуточный обработчик [express-session](https://www.npmjs.com/package/express-session) сохраняет данные о сеансе на сервере; в самом файле cookie сохраняется только ИД сеанса, но не данные сеанса.  По умолчанию, используется хранилище в оперативной памяти, но данный способ не предназначен для рабочей среды.  В рабочей среде необходимо настроить масштабируемое хранилище сеансов; см. список [совместимых хранилищ сеансов](https://github.com/expressjs/session#compatible-session-stores).

Промежуточный обработчик [cookie-session](https://www.npmjs.com/package/cookie-session), в отличие от описанного выше, реализует хранение на основе файлов cookie: выполняется полная сериализация сеанса в файл cookie, вместо того, чтобы сохранять только ключ сеанса.  Этот способ следует использовать только при условии, что данные сеанса имеют относительно небольшой объем и легко могут быть преобразованы в элементарные значения (а не объекты).  Хотя браузеры должны поддерживать не менее 4096 байт на каждый файл cookie, позаботьтесь о том, чтобы не допустить превышения данного ограничения. Размер не должен превышать 4093 байт на каждый домен.  Кроме того, помните о том, что данные cookie являются видимыми для клиента, поэтому, если по какой-либо причине их следует защитить или скрыть, остановите свой выбор на модуле express-session как на более подходящем.

### Не используйте стандартные имена сеансовых cookie

Использование имен сеансовых cookie, предлагаемых по умолчанию, может сделать ваше приложение уязвимым для разного рода атак.  В данном случае возникает та же проблема с безопасностью, что и при использовании заголовка `X-Powered-By`: потенциальный злоумышленник может воспользоваться им для идентификации на сервере и организации целенаправленных атак.

Для того чтобы избежать такой проблемы, используйте обобщенные имена cookie; например, при использовании промежуточного обработчика [express-session](https://www.npmjs.com/package/express-session):

```js
const session = require('express-session')
app.set('trust proxy', 1) // trust first proxy
app.use(session({
  secret: 's3Cur3',
  name: 'sessionId'
})
)
```

### Настройка опций защиты cookie

Для обеспечения защиты необходимо настроить следующие опции защиты файлов cookie:

* `secure` - обеспечивает отправку файлов cookie браузером только с использованием протокола HTTPS.
* `httpOnly` - обеспечивает отправку cookie только с использованием протокола HTTP(S), а не клиентского JavaScript, что способствует защите от атак межсайтового скриптинга.
* `domain` - указывает домен cookie; используется для сравнения с доменом сервера, на котором запрашивается данный URL. В случае совпадения выполняется проверка следующего атрибута - пути.
* `path` - указывает путь cookie; используется для сравнения с путем запроса. Если путь и домен совпадают, выполняется отправка cookie в запросе.
* `expires` - используется для настройки даты окончания срока хранения для постоянных cookie.

Ниже приведен пример с использованием промежуточного обработчика [cookie-session](https://www.npmjs.com/package/cookie-session):

```js
const session = require('cookie-session')
const express = require('express')
const app = express()

const expiryDate = new Date(Date.now() + 60 * 60 * 1000) // 1 hour
app.use(session({
  name: 'session',
  keys: ['key1', 'key2'],
  cookie: {
    secure: true,
    httpOnly: true,
    domain: 'example.com',
    path: 'foo/bar',
    expires: expiryDate
  }
})
)
```

## Дополнительные замечания

Ниже приводится несколько дополнительных рекомендаций, взятых из исчерпывающего [Контрольного списка требований к защите Node.js](https://blog.risingstack.com/node-js-security-checklist/).  В этой публикации можно найти дополнительную информацию по всем приведенным ниже рекомендациям:

* Введите ограничение скорости передачи данных во избежание атак методом грубого подбора сочетаний символов для идентификации.  Для реализации стратегии ограничения скорости передачи данных можно воспользоваться [Шлюзом API StrongLoop](https://web.archive.org/web/20240000000000/https://strongloop.com/node-js/api-gateway/).  В качестве альтернативы, можно использовать промежуточный обработчик, например, [express-limiter](https://www.npmjs.com/package/express-limiter), но для этого придется внести некоторые изменения в код.
* Всегда применяйте фильтрацию и очистку пользовательского ввода в целях защиты от атак межсайтового скриптинга (XSS) и ввода ложных команд.
* Обеспечьте защиту от атак внедрения SQL-кода с помощью параметризованных запросов или подготовленных операторов.
* Используйте инструмент [sqlmap](http://sqlmap.org/) с открытым исходным кодом для выявления уязвимостей к внедрению SQL-кода в приложение.
* Используйте инструменты [nmap](https://nmap.org/) и [sslyze](https://github.com/nabla-c0d3/sslyze) для проверки конфигурации шифров, ключей и повторных согласований SSL, а также действительности сертификата.
* Используйте [safe-regex](https://www.npmjs.com/package/safe-regex), чтобы убедиться в невосприимчивости регулярных выражений к атакам [отказа в обслуживании регулярных выражений](https://www.owasp.org/index.php/Regular_expression_Denial_of_Service_-_ReDoS).

## Избегайте прочих известных уязвимостей

Следите за рекомендациями [Node Security Project](https://npmjs.com/advisories), касающимися Express или других модулей, используемых вашим приложением.  В целом, Node Security Project - это непревзойденный ресурс, предоставляющий ценные знания и инструменты, связанные с безопасностью Node.

И наконец, приложения Express - как и любые другие приложения - могут быть уязвимы к разнообразным веб-атакам. Ознакомьтесь с описаниями известных [веб-уязвимостей](https://www.owasp.org/www-project-top-ten/) и примите соответствующие меры предосторожности, чтобы их избежать.
