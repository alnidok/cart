# Twig шаблоны
В качестве шаблонизатора используется twig,  [русская документация по нему](http://x-twig.ru/).

Все шаблоны располагаются по пути `twig_templates`, указанному в `dirs.yaml`. В нашем случае /catalog/template/...

Есть главный шаблон - `layout.twig`, в который включены следующие блоки: `head`, `column_left`, `column_right`, `content_top`, `content`, `bottom_scripts`.
Соответственно для новых страниц мы можем использовать этот шаблон, меняя блоки (обычно только content и bottom_scripts).

Рассмотрим для примера шаблон `home.twig` для главной страницы. Здесь переопределяются блоки content_top и content.

##Рендер шаблона в контроллере
Если нужно вывести шаблон, делаем так:
`...
$this->data['var'] = 'val';
$this->renderTwig('home.twig');`
Пример: /catalog/common/home/hobbygames/home.php (метод index)

Если же надо вернуть html код в переменную:
`$this->data['var'] = 'val';
$rendered = $this->store->getTwig()->render('home.twig', $this->data);`
Пример: /catalog/common/home/hobbygames/blocks.php


Внутри шаблона помимо передаваемых в data значений можно использовать глобальные переменные, функции и фильтры (это определяется в CartTwigExtension)

##Глобальные переменные


- `STORE` - [CART: src/Cart/Store.php]
- `DP` - [HG: system/library/dataprovider.php]
- `LANG` - языковые переменные
- `ORDER` - [CART: src/Cart/Cart/Order.php]
- `CART` - [CART: src/Cart/Cart/Cart.php]
- `IMG` - [CART: config2.example/0/image.yaml] конфиг размера изображений (например для передачи ширины и высоты картинки для аватара пишем просто IMG.avatar.0 и IMG.avatar.1 соответственно)
- `DOCUMENT` - [CART: src/Cart/System/Document.php]
- `ASSETS` - [HG: system/library/assets.php]

##Использование языковых строк (LANG)
Если раньше в контроллере надо было подключать нужный файл
`$this->load->language('path');`
потом передавать переменные для каждой строки
`$this->data['langstring'] = $this->load->get('langstring');`
то теперь всего этого делать не нужно.
В каждом twig-шаблоне есть глобальная переменная LANG, которая имеет доступ к языковым строкам. 
При этом ищутся совпадения в главном языковом файле */catalog/language/russian/common/dataprovider.php*
Если нужно подключить какой-то файл, прямо в шаблоне пишем `{{ LANG.load('path') }}` (совпадающие ключи будут заменены на новые значения)
и выводим нужную строку:
 `{{ LANG.langstring }}`
 
*Пока используем существующую структуру директории language, в дальнейшем планируем перейти к более струкрутированному виду, например один файл для всех кнопок, другой для всех табов, третий для сообщений об ошибках и т.д.*

## Подключение модулей, css, js (ASSETS)
Под модулем понимается набор css/js файлов, необходимых для определенной задачи. Для подключения внутри twig-шаблона используем следующую конструкцию:
`{{ ASSETS.load('fancybox') }}`
Можно указывать как один модуль, так и несколько:
`{{ ASSETS.load(['fancybox', 'comments']) }}`

Список этих модулей хранится в `[HG: system/library/assets.php]`

Если нужно просто подключить css или js, используем:
`{{ ASSETS.loadCSS('pathToFile.css') }}`
`{{ ASSETS.loadJS('pathToFile.js') }}`
Аналогично для нескольких файлов:
`{{ ASSETS.loadCSS(['pathToFile1.css', 'pathToFile2.css']) }}`
`{{ ASSETS.loadJS(['pathToFile1.js', 'pathToFile2.js']) }}`

*Использовать эти ASSETS полезно, т.к. при рендере страницы будут загружены оптимизированные версии*


##Использование репозиториев
Почти для всех сущностей в библиотеке есть соответствующие объекты (репозитории).
Полный список можно посмотреть в файле [CART: src/Cart/Store.php].
Доступ к ним идет через  `$this->store` следующим образом:
`$this->store->getProductsRepository()` или 
`$this->store->getCategoriesRepository()`

Из этих объектов можно вытащить отдельные экземпляры, например:
`$this->store->getProductsRepository()->getById(12345)` вернет экземпляр товара с *id = 12345*.

Эти отдельные экземпляры тоже являются объектами, и для доступа к их свойствам имеются геттеры, например 
`$this->store->getProductsRepository()->getById(12345)->getName()` или
`$this->store->getProductsRepository()->getById(12345)->getCurrentPrice()`
Список этих геттеров можно посмотреть в библиотеке, например для товара это [CART: src/Cart/Model/Entitles/Product.php]

Исходя из всего этого, зная нужные id, мы можем вытаскивать любые данные непосредственно в twig-шаблонах, без использования контроллера:

То же самое внутри twig-шаблона: 
`{% set product = STORE.productsRepositories.byId(12345) %}
Наименование: {{ product.name }}
Цена: {{ currency_format(product.currentPrice) }}
`

*Здесь можно опускать get, т.е. `->getName()` эквивалентно `.name`, `->getProductsRepository()` эквивалентно `.productsRepository` и т.п.*

