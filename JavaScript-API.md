
# Корзина товаров - JS-класс "ShoppingCart"

## Добавление товаров в корзину

Добавление товаров в корзину работает без JavaScript. Но для добавления без перезагрузки страницы (Ajax) нужно использовать JavaScript.

```html
<script>
    var shoppingCart;

    document.addEventListener('DOMContentLoaded', function() {//Событие загрузки HTML элементов страницы
        
        // Корзина товаров
        shoppingCart = new ShoppingCart({
            baseUrl: '{{ app.request.baseUrl }}',// Базовый URL
            connectorUrl: '{{ pathLocalized('shop_cart_action') }}',// URL для запросов
            selector: '#shoppingCartSmallContainer',// Селектор контейнера корзины
            productFormSelector: '.js-shopping-cart-product form',// Селектор формы товаров
            templateName: 'shop_cart'// Имя шаблона
        });
        shoppingCart
            .addEventListener('load', function(e) {// Событие загрузки корзины товаров
                if (e.detail
                    && e.detail.response
                    && !e.detail.response.success
                    && e.detail.response.message) {
                        alert(e.detail.response.message);// Показываем сообщение об ошибке
                }
            });
        
    });

</script>
```

### Параметр класса "ShoppingCart" с настройками (передается в конструктор класса)

- **baseUrl** - Базовый URL. По умолчанию: '/'.
- **connectorUrl** - URL для запросов. По умолчанию: '/shop_cart/action'.
- **selector** - Селектор контейнера корзины. По умолчанию: ''.
- **useNumberFormat** - Использовать авто-форматирование чисел. По умолчанию: true.
- **selectorPriceTotal** - Селектор элемента с общей ценой (нужно для авто-обновления, см. "autoUpdateElements"). По умолчанию: '.shopping-cart-price-total'.
- **selectorCountTotal** - Селектор элемента с общим количеством товара. По умолчанию: '.shopping-cart-count-total'.
- **selectorCountUniqueTotal** - Селектор элемента с количеством уникальных товаров. По умолчанию: '.shopping-cart-count-unique-total'.
- **selectorDeclension** - Селектор элемента с текстом, склонение которого нужно обновить в зависимости от количества тоара. По умолчанию: '.shopping-cart-declension'.
- **productFormSelector** - Селектор формы товаров. По умолчанию: ''.
- **templateName** - Название шаблона корзины. По умолчанию: ''.
- **autoUpdateElements** - Автоматически обновлять содержимое элементов после действий корзины. По умолчанию: false.
- **data** - Первоначальные данные корзины (до какого-то действия).

### Данные корзины

Состояние корзины можно узнать из свойства "data".
Пример:
```html
<script>
    alert(shoppingCart.data.shop.price_total);
</script>
```

Содержимое объекта "data":

- **delivery_name** - Название способа доставки.
- **delivery_price** - Цена доставки.
- **ids** - Массив с ID товаров.
- **items_total** - Общее количество товара.
- **items_unique_total** - Количество уникальных товаров.
- **price_total** - Общая цена товаров в корзине.

Данные содержатся в отдельных объектах, имена которых заданы в поле с именем "type" формы товара. По умолчанию имя объекта - "shop".

### Форма с данными товара

Пример формы с кнопкой добавления товара в коризну:

```html
<form action="{{ pathLocalized('shop_cart_action') }}" method="post">
    {{ renderOutputTypeArray(item, fields, '', {"currency": currency}) }}
    <input type="hidden" name="item_id" value="{{ item._id }}">
    <input type="hidden" name="category_id" value="{{ item.parentId }}">
    <input type="hidden" name="type" value="shop">
    <div class="row no-gutters">
        <div class="col-4">
            <input type="number" class="form-control d-inline-block text-center" value="1" min="1" step="1" name="count">
        </div>
        <div class="col-8 pl-2">
            <button type="submit" class="btn btn-primary btn-block" name="submit_button">
                {{ 'Add to cart' | trans }}
            </button>
        </div>
    </div>
</form>
```

Поля формы:

- **item_id** - Идентификатор товара
- **count** - Количество товара.
- **category_id** - Системное имя категории.
- **type** - Тип корзины. Это могут быть разные корзины или "избранные" товары. По умолчанию: shop.

### События

- **load** - Обновление корзины после действия. Данные: **response** (ответ сервера), **container** (конетейнер корзины), **element** (элемент формы товара).
- **requestBefore** - До отправки запроса. Данные: **request** (обект XMLHttpRequest).
- **requestAfter** - После отправки запроса. Данные: **request** (обект XMLHttpRequest).
- **formSubmitBefore** - До отправки данных формы товара. Данные: **element** (элемент формы товара), **formData** (объект FormData).

Пример использования событий:

```html
<script>
    shoppingCart
        .addEventListener('formSubmitBefore', function(e) {// До отправки данных формы
            var buttonEl = e.detail.element.querySelector('button[type="submit"]');
            if (buttonEl) {
                buttonEl.setAttribute('disabled', '');// Блокируем кнопку
            }
        })
        .addEventListener('load', function(e) {// После получения ответа корзины покупок
            if (e.detail.element) {
                var buttonEl = e.detail.element.querySelector('button[type="submit"]');
                if (buttonEl) {
                    buttonEl.removeAttribute('disabled');// Убираем блокировку кнопки
                }
            }
        });
</script>
```

### Авто-обновление мини-корзины

На странице может быть несколько корзин. Например, основная и мини-корзина. В этом случае для обновления мини-корзины можно использовать CSS-классы,
селекторы которых указаны в параметрах "selectorPriceTotal", "selectorCountTotal" и т.д.

Пример:
```html
<div class="shopping-cart-mini">
    <span class="shopping-cart-count-total">0</span>
    <span class="shopping-cart-price-total">0</span> руб.
</div>
```

Данные внутри элементов будут автоматически обновлени при совершении действий с корзиной, если включен режим "autoUpdateElements".

### Действия с корзиной товаров

Данные корзины должны находиться внутри элемента формы (``<form></form>``). Каждая кнопка с типом "submit" будет работать без обновления страницы,
если инициализирован объект класса "ShoppingCart".

Пример кнопки "Пересчитать":
```html
<form action="{{ path('shop_cart_action') }}" method="post">
    ...

    <button type="submit" name="action" value="update">
        {{ 'Recalculate' | trans }}
    </button>
</form>
```

Пример кнопки удаления товара из корзины по индексу:
```html
<form action="{{ path('shop_cart_action') }}" method="post">
    ...

    {% for item in items %}
        ...
        <button type="submit" name="remove_by_index" value="{{ loop.index0 }}">
            {{ 'Delete item' | trans }}
        </button>
    {% endfor %}
</form>
```

Действия корзины:
- **add_to_cart** - Добавить в корзину.
- **remove_by_index** (или "remove") - Удалить товар по индексу. 
- **remove_by_id** - Удалить товар по ID.
- **update** - Пресчитать количество товара в корзине.
- **clean** - Очистить корзину.

# JS-класс "Shopkeeper"

Если используется скрипт "app_build/app.js", то объект класса "Shopkeeper" уже создан в переменной "shk".

```html
<script src="{{ asset('app_build/app.js') }}"></script>
```

При создании объекта класса "Shopkeeper" автоматически вызывается метод "init", внутри которого вызываются методы "buttonsInit", "currencySelectInit".

### Параметр класса "Shopkeeper" с настройками (передается в конструктор класса)

- **baseUrl** - Базовый URL. По умолчанию: '/'.
- **multiCurrency** - Режим мультивалютности. По умолчанию: false (но включается автоматически при изменении валюты).
- **priceElSelector** - Селектор элементов с ценой (цена может меняться при применении параметров товара или изменении валюты). По умолчанию: 'shk-price'.
- **priceFilterName** - Название фильтра цены (нужно для изменения цен слайдера с ценами в соответствии с курсом валюты). По умолчанию: '' (может устанавливаться автоматически, см. шаблон "catalog/filters.html.twig").
- **currencyTranslate** - Переводы названий валют. По умолчанию: {RUB: 'руб.', UAH: 'грн'}.

### Методы класса

- **onReady** - Вызвать функцию после загрузки HTML элементов страницы.
- **buttonsInit** - Инициализация действий вспомогательных кнопок. Кнопка с селектором ".shk-button-filters-hide" - скрыть фильтры, ".shk-button-filters-show" - показать фльтры.
- **currencySelectInit** - Инициализация переключения валюты. Должен быть создан элемент "select" с идентификатором "shk-currency" (см. шаблон "catalog/currency_list.html.twig").
- **filtersInit** - Инициализация фильтрации товаров (автоматически вызывает методы "slidersInit()" и "markSelected()"). После изменения одного фильтра автоматически показывается элемент с селектором ".shk-onfilter-change".
- **slidersInit** - Инициализация слайдеров фильтрации.
- **markSelected** - Выставить значения фильтров по URL страницы (используется в случае, если HTML фильтров закэширован).
- **getUrlParams** - Получить все данные фильтров и сортировки.
- **catalogListChange** - Изменение типа вывода списка товаров (список, плитка и т.п.). Сохраняется в куки.
- **productParametersInit** - Инициализация параметров товаров, которые могут влиять на цену.
- **getCurrentCurrency** - Получить текущее значение валюты.
- **getCurrentCurrencyRate** - Получить курс валюты по названию.
- **updateOptions** - Обновить значения настроек.
- **showPreloaderToggle** - Показать/скрыть анимацию загрузки.

Пример инициализации фильтров товаров:
```js
shk.onAfterInit(function() {
    shk.filtersInit(true);
});
```

Пример использования метода "catalogListChange()":
```js
document.querySelectorAll('#shkNavListType button').forEach(function(buttonEl) {
        buttonEl.addEventListener('click', function(){
            shk.catalogListChange(this.value);
        }, false);
    });
}
```

Пример использовани метода "productParametersInit()":
```js
shk.productParametersInit('#shk-form', '.shk-price');
```

Пример использования метода "updateOptions()":
```js
document.addEventListener('DOMContentLoaded', function() {
    shk.updateOptions({priceFilterName: 'price'});
});
```
