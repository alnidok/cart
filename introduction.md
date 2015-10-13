#Введение
## Что используется
[Redis](http://redis.io)
Хранилище данных в оперативной памяти.

[Pimple](http://pimple.sensiolabs.org/) ([статья на хабре](http://habrahabr.ru/post/199296/))
Контейнер для работы с классами сущностей


[Twig](http://twig.sensiolabs.org/) ([русская документация](http://x-twig.ru/))
Шаблонизатор

[Yaml](http://symfony.com/doc/current/components/yaml/introduction.html)
Используется в конфигах библиотеки

##Основные изменения
1. Настройки по-максимуму вынесены в конфиги (config2.example/*.yaml)
2. Почти все системные файлы и либы опенкарта перенесены в общую библиотеку. 
3. Вместо stocks теперь у нас регионы, которые хранятся в config2.example/regions.yaml. Соответственно при смене города меняется регион, а *store_id* остается прежним. Если надо получить старый *store_id*: 
`$this->store->getRegion()->getOldStoreId()`
4. Для всевозможных сущностей есть соответствующие классы в библиотеке, списки называются репозиториями, например `productsRepository`, `categoriesRepository`. Из них можно вытащить отдельные экземпляры, например:
`$this->store->getProductsRepository()->getById(12345)` вернет экземпляр товара с *id = 12345*.
То же самое внутри twig-шаблона: 
`STORE.productsRepositories.byId(12345)`, т.е. get можно опускать.


