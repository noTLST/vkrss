# Generating RSS Feed for opened wall of user or community (group, public page or event page) on vk.com
# Генерация RSS-ленты открытой стены пользователя или сообщества (группы, публичной страницы или мероприятия) во Вконтакте.

## Возможности:
* Получение RSS-ленты открытых стен (не требующих авторизации): извлечение описания из разных частей (включая вложенные) и построение заголовков на основе описания.
* Фильтрация постов по соответствию и/или несоответствию регулярному выражению в стиле PCRE.
* Извлечение хеш-тегов в качестве RSS-категорий.
* Использование HTTP, HTTPS, SOCKS4, SOCKS4A или SOCKS5 прокси-сервера для запросов.

## Требования
* PHP>=5.2.2 (включая версии 5.3.X, 5.4.X, 5.5.X, 5.6.X, 7.0.X) с установленными по умолчанию поставляемыми расширениями `mbstring`, `json`, `pcre`.
* Для отправки запросов через HTTPS должно быть установлено расширение `openssl` у PHP. 
* Скрипт предпочитает использовать встроенные в PHP возможности по отправке запросов. Если у PHP отключена встроенная возможность загрузки файлов по URL (отключен параметр `allow_url_fopen` в конфигурации или параметрах интерпретатора), но при этом у PHP установлено расширение `cURL`, то именно оно будет использоваться для загрузки файлов. 
* Если необходимо использовать прокси-сервер, то в случае
   * HTTP-прокси — в конфигурации или параметрах интерпретатора PHP должен быть включён параметр `allow_url_fopen` либо установлено расширение `cURL`>=7.10,
   * HTTPS-прокси — необходимо расширение `openssl`, а также в конфигурации или параметрах интерпретатора PHP должен быть включён параметр `allow_url_fopen` либо установлено расширение `cURL`>=7.10,
   * SOCKS5-прокси — необходимо расширение `cURL`>=7.10,
   * SOCKS4-прокси — необходим PHP>=5.2.10 с расширением `cURL`>=7.10,
   * SOCKS4A-прокси — необходим PHP>=5.5.23 или PHP>=5.6.7 (включая 7.0.X) с расширением `cURL`>=7.18.

В случае каких-либо проблем вместо RSS-ленты выдается страница с HTTP-статусом, отличным от 200, и с описанием проблемы.

## Параметры:
Параметр `id` обязателен, остальные необязательны.

* [обязательный] `id` — короткое название, ID-номер (в случае сообщества ID начинается со знака `-`) или полный идентификатор человека/сообщества (в виде idXXXX, clubXXXX, publicXXXX, eventXXXX).  Примеры допустимых значений параметра `id`:
  * `123456`, `id123456` — оба значения указывают на одну и ту же страницу пользователя с ID 123456,
  * `-123456`, `club123456` — оба значения указывают на одну и ту же группу с ID 123456,
  * `-123456`, `public123456` — оба значения указывают на одну и ту же публичную страницу с ID 123456,
  * `-123456`, `event123456` — оба значения указывают на одну и ту же страницу мероприятия с ID 123456,
  * `apiclub` — значение указывает на пользователя или сообщество с данным коротким названием.
  
  Ради обратной совместимости допускается вместо `id` использовать `domain` или `owner_id`.
* `count` — количество выводимых записей, начиная с последней опубликованной (не более 100, по умолчанию 20).
* `include` — регистронезависимое регулярное выражение в стиле PCRE, которое должно соответствовать тексту записи. В начале и в конце выражения символ `/` **не** нужен.
* `exclude` — регистронезависимое регулярное выражение в стиле PCRE, которое **не** должно соответствовать тексту записи. В начале и в конце выражения символ `/` **не** нужен.
* `proxy` — адрес прокси-сервера. Допустимые форматы:
    * `address`,
    * `address:port`,
    * `type://address`,
    * `type://address:port`,
    * `login:password@address`,
    * `login:password@address:port`,
    * `type://login:password@address`,
    * `type://login:password@address:port`,
    
    где `address` — домен или IP-адрес прокси, `port` — порт, `type` — тип прокси (HTTP, HTTPS, SOCKS4, SOCKS4A, SOCKS5), `login` и `password` — логин и пароль для доступа к прокси, если необходимы. 
  
  Тип прокси и параметры авторизации можно передавать в виде отдельных параметров:
  * `proxy_type` — тип прокси (по умолчанию считается HTTP, если не указано в `proxy` и `proxy_type`),
  * `proxy_login` — логин для доступа к прокси-серверу,
  * `proxy_password` — пароль для доступа к прокси-серверу.

## Примеры использования:
```
index.php?id=apiclub
index.php?id=-1
index.php?id=id1
index.php?id=club1
index.php?id=apiclub&count=100&include=рекомендуем
index.php?id=apiclub&proxy=localhost:8080
index.php?id=apiclub&proxy=localhost:8080&proxy_type=https
index.php?id=apiclub&proxy=https5%3A%2F%2Flocalhost:8080
index.php?id=-1&count=100&include=(рекомендуем|приглашаем|\d{3,})
```
Примечание: в последнем примере при таком вызове напрямую через GET-параметры может потребоваться кодирование символов: ```index.php?id=-1&count=100&include=(%D1%80%D0%B5%D0%BA%D0%BE%D0%BC%D0%B5%D0%BD%D0%B4%D1%83%D0%B5%D0%BC%7C%D0%BF%D1%80%D0%B8%D0%B3%D0%BB%D0%B0%D1%88%D0%B0%D0%B5%D0%BC%7C%5Cd%7B3%2C%7D)```
