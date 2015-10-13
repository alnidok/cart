# Twig шаблоны
В качестве шаблонизатора используется twig,  [русская документация по нему](http://x-twig.ru/).

Все шаблоны располагаются по пути `twig_templates`, указанному в `dirs.yaml`. В нашем случае /catalog/templates/...

Есть главный шаблон - `layout.twig`, в который включены следующие блоки: `head`, `column_left`, `column_right`, `content_top`, `content`, `bottom_scripts`.
Соответственно для новых страниц мы можем использовать этот шаблон, меняя блоки (обычно только content и bottom_scripts).

Рассмотрим для примера шаблон `home.twig` для главной страницы. Здесь переопределяются блоки content_top и content.

#Рендер шаблона в контроллере
Если нужно вывести шаблон, делаем так:
```...
$this->data['var'] = 'val';
$this->renderTwig('home.twig');```
Пример: /catalog/common/home/hobbygames/home.php (метод index)

Если же надо вернуть html код в переменную:
```$this->data['var'] = 'val';
$rendered = $this->store->getTwig()->render('home.twig', $this->data);```
Пример: /catalog/common/home/hobbygames/blocks.php


Внутри шаблона помимо передаваемых в data значений можно использовать глобальные переменные, функции и фильтры (это определяется в CartTwigExtension)

#Глобальные переменные


- `STORE` - [CART: src/Cart/Store.php]
- `DP` - [HG: system/library/dataprovider.php]
- `LANG` - языковые переменные
- `ORDER` - [CART: src/Cart/Cart/Order.php]
- `CART` - [CART: src/Cart/Cart/Cart.php]
- `IMG` - [CART: config2.example/0/image.yaml] конфиг размера изображений (например для передачи ширины и высоты картинки для аватара пишем просто IMG.avatar.0 и IMG.avatar.1 соответственно)
- `DOCUMENT` - [CART: src/Cart/System/Document.php]

