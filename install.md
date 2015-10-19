# Установка
## Composer
Composer - это менеджер пакетов для PHP. В файле настроек (composer.json) указываются необходимые для проекта пакеты, а сами они хранятся в директории vendor.

Можно выполнить глобальную или локальную установку. Здесь рассмотрим локальную в корень проекта. Команды удобнее всего выполнять в phpStorm (вкладка Terminal):

1. Качаем архив архив `composer.phar`:
`php -r "readfile('https://getcomposer.org/installer');" | php`
2. Качаем все зависимости
`php composer.phar install`
3. Для дальнейших обновлений
`php composer.phar update`

Итак, когда мы выполняем git pull внутри HG, надо выполнить обновление composer-зависимостей и для сайта, и для либы:
`cd PATH_TO_LIB && php composer.phar update`
`cd PATH_TO_HG && php composer.phar update`

Чтобы каждый раз не париться с обновлениями, создадим git-хук post-merge и повесим на него 3-й пункт. Соответсвенно обновление будет выполняться каждый раз после мержа (в т.ч. после git pull)
[Подробнее о git hooks] (https://git-scm.com/book/ru/v1/%D0%9D%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0-Git-%D0%9F%D0%B5%D1%80%D0%B5%D1%85%D0%B2%D0%B0%D1%82%D1%87%D0%B8%D0%BA%D0%B8-%D0%B2-Git)

## Redis
Redis (редиска) - хранилище данных в оперативной памяти.

Устанавливаем redis server.
https://github.com/rgl/redis/downloads

И запускаем redis_server.exe

## Solr (не обязательно)
Solr - система полнотекстового поиска, написанная на Java. У нас используется при поиске товаров.
Устанавливам Java, если нет. Просим архив с уже закешированными данными, дам.
Дальше идем в examples/start.sh и выполняем в консоли команду, что там указана (проще создать себе .bat аналог).

## Отдельная ветка HG: origin/lib
1. Переключаемся в отдельную ветку.
2. Делаем локальную установку composer
3. Копируем директорию vendor/bytex/cart/config2.example в любое место, например в корень
4. В корневом файле `config2.php` ставим путь `CONFIG_DIR` именно в это место
5. Настраиваем конфиги под себя:
	`db.yaml`
	`logging.yaml`
	`pechkin.yaml`
	`selectel.yaml`
	`0/dirs.yaml`
	`0/server.yaml`
6. Первоначально заполняем redis:
`php vendor/bytex/cart/examples/fill_redis.php`
7. Выполняем sql `vendor/bytex/cart/btx_regional_cache.sql`
8. Запускаем `php vendor/bytex/cart/examples/db_cached_data.php`

После этого ветка готова к работе.
*Для удобства можно создать себе .bat файл, в котором запускать все что нужно: apache, mysql, redis, solr.*

