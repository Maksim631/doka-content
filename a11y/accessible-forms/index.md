---
title: "Доступные формы"
description: "Рекомендации по созданию доступных форм: валидация, элементы управления и их атрибуты."
authors:
  - oskarstud
related:
  - a11y/chto-takoe-a11y
  - a11y/screenreaders
  - a11y/a11y-html
tags:
  - article
---

## Кратко

Форма — важный элемент интерфейса. Важно сделать её доступной, так как формы могут быть визуально и когнитивно сложными. Они ставят пользователя в тупик, поэтому он может уйти с сайта. Доступными формами легче пользоваться всем, включая людей с инвалидностью.

Сделать форму доступной помогут её логичность и простота использования, осведомлённость пользователя об ошибках и форматах данных, а также правильная разметка. Например, в форме должны быть связанные с полями [`<label>`](/html/label/).

## Общие принципы доступности форм

### Пользователь понимает назначение полей

Каждое поле должно иметь связанную с ним подпись в элементе `<label>`, который описывает его назначение. Благодаря этому текст визуально связан с элементом управления формой (контролом) и считывается [скринридерами](/a11y/screenreaders/).

Кроме этого, нажатие на `<label>` переносит фокус на связанное с ним поле и делает его более крупным элементом. Поэтому для подписей полей формы рекомендуется использовать `<label>`, вместо `title` и `placeholder`.

### Используются семантические HTML-теги

У нативных HTML-тегов много полезных встроенных функций. Давайте разберём это на примере.

```html
<form>
  <label for="firstname">
    Имя
  </label>
  <input type="text" id="firstname" name="firstname" required>
  <div for="surname">
    Фамилия
  </div>
  <div id="surname" contenteditable></div>
  <button type="submit">
    Сохранить
  </button>
</form>
```

Если нажмёте на подпись «Фамилия», поле не окажется в фокусе. Подпись `<label>Имя</label>` и поле  [`<input>`](/html/input/) связаны друг с другом с помощью атрибутов `id` и `for`, поэтому трюк с фокусом сработает.

Также скринридеры не распознают [`<div>`](/html/div/) (и [`<span>`](/html/span/) тоже) как элемент ввода по умолчанию. Пользователь не сможет заполнить такое поле без дополнительных усилий — [ARIA](/a11y/aria-intro/), JavaScript и атрибута `contenteditable`. В примере пользователь услышит только текст «Фамилия» и не узнает, что элемент является полем для фамилии.

Следующий минус `<div>` — введённые в него данные не попадут в запрос после отправки данных из формы. Да-да, технически с помощью JavaScript можно сделать отправку данных, но `<input>` это делает по умолчанию.

Ещё одна неочевидная проблема `<div>` — с ним не работает [атрибут `inputmode`](/html/inputmode/). При этом, если у `<input>` указаны [`type`](/html/input/#type) или `inputmode`, на устройствах с экранной клавиатурой будет отображаться нужный набор символов при вводе данных в поле.

### Пользователь ориентируется в форме

Во-первых, надо показать пользователю какой именно элемент в форме активен. Это легко сделать с помощью псевдоклассов [`:focus`](/css/focus/) или [`:focus-visible`](/css/focus-visible/).

Во-вторых, визуальный порядок элементов должен совпадать с их порядком в [DOM](/js/dom/). То есть, порядок фокуса должен совпадать с порядком чтения и последовательно перемещаться слева направо, справа налево или сверху вниз в зависимости от языка интерфейса. Так что старайтесь не использовать [`order`](/css/order/).

В-третьих, если полей в форме много. Лучше всего разбить форму на несколько последовательных шагов. Благодаря этому работа с формой станет проще и пользователь не будет перегружен.

В-четверых, при заполнение одного из полей формы, фокус не должен переключаться на следующее поле. Пользователь должен сам контролировать данный процесс. Так как данные у пользователей могут быть разными.

### Пользователь знает о формате данных

Хорошо давать пользователям понятные инструкции по заполнению формы, особенно если она сложная. Рассмотрим на примере. Допустим, в поле должно быть минимум 8 символов и у нас есть проверка на это.

```html
<form>
  <label for="name">Имя (обязательно)</label>
  <input
    required
    minlength="8"
    type="text"
    id="name"
    name="name"
    autocomplete="given-name"
    aria-describedby="hint"
  >
  <span id="hint">Имя должно содержать как минимум 8 символов.</span>
  <button type="submit">Сохранить</button>
</form>
```

Чтобы рассказать пользователю о минимальном количестве символов, можно добавить подсказку об ожидаемом формате перед или под полем. Чтобы связать подсказку к полю для скринридеров, используйте атрибут [`aria-describedby`](/a11y/aria-describedby/). Атрибут с нужным значением в виде текста добавьте к полю `<input>`, а `id` с таким же значением — к обёртке `<span>` для текста подсказки.
Также можно добавить общую инструкцию по заполнению формы перед всеми полями формы, а подробную инструкцию по заполнению добавить по ссылке обязательно завернув её в `<a>`.

### Пользователь знает об ошибках и успехе

Важно сообщать об ошибках и успешности заполнения формы всем пользователям. К примеру, когда пользователь ввёл неверные данные в обязательное для заполнения поле с атрибутом `required`, то при отправке формы появится сообщение об ошибке. Текст ошибки важно не только расположить визуально рядом с полем, к которому она относится, но и сообщить о ней пользователям скринридеров.

После правильного заполнения формы и успешной отправки хорошо выводить сообщение об успехе.

Парочка рекомендаций по валидации ошибок:

1. Пишите простые и чёткие тексты ошибок, в которых написано, что и как нужно исправить.
1. Упростите задачу по исправлению ошибки. Для этого можно использовать фокусировку на поле с ошибкой при нажатии на сообщение о ней.
1. Используйте атрибут [`aria-invalid="true"`](/a11y/aria-invalid/) на полях с ошибками. Он сообщает скринридерам о том, что поле содержит ошибку.
1. Связывайте тексты ошибок с полями с помощью `aria-describedby` или [`aria-errormessage`](/a11y/aria-errormessage/). У `aria-errormessage` пока плохая поддержка.
1. Убедитесь, что сообщения об ошибках автоматически читаются скринридером. Для этого можно использовать `role="alert"` или `aria-live="assertive"` для сообщения об ошибке, чтобы оно мгновенно зачитывалось.
1. Дайте пользователю вернуться к полю, в котором допущена ошибка. Можно использовать ссылку или кнопку для возврата.
1. Ошибка появляется при отправке данных формы, а не когда пользователь начал вводить данные или при фокусе.
1. Подсказки и незначительные сообщения не должны выглядеть как ошибка или важное предупреждение.
1. Ошибки не верно заполненных полей формы обозначаются не только цветом, но и текстом или иконкой.
1. Учитывать региональные особенности, связанные с именами и фамилиями (например, где-то у людей три имени, где-то короткие фамилии и прочее).

Больше советов найдёте в [статье про валидацию форм на Smashing Magazine](https://www.smashingmagazine.com/2022/09/inline-validation-web-forms-ux/).

Основной элемент управления формой — [`<input>`](/html/input/).

У полей важно указывать верный тип в [атрибуте `type`](/html/input/#type). Пробежимся по основным типам, которые встречаются в формах.

#### `text`

Используйте `type="text"` для однострочных полей для ввода текста. Этот тип, на самом деле, есть у всех полей по умолчанию без явно заданного `type`.

```html
<label for="name">Введите имя:</label>
<input id="name" type="text" autocomplete="given-name">
```

Совпадающие значения в `for` и `id` связывают подпись с элементом управления формой. Поскольку значения `id` должны быть уникальными на каждой странице, у поля может быть только один связанный с ним `<label>`.

Также можно создать ассоциацию, поместив текст метки и входные данные внутри `<label>` элемента ( не используя `for` и `id`).

#### `checkbox`

Для чекбоксов используйте `type="checkbox"`.

```html
<fieldset>
  <legend>Выберите топпинг:</legend>
  <input id="bacon" type="checkbox" name="toppings" value="bacon">
  <label for="bacon">Бекон</label>
  <input id="pepperoni" type="checkbox" name="toppings" value="pepperoni">
  <label for="pepperoni">Пеперони</label>
  <input id="mushrooms" type="checkbox" name="toppings" value="mushrooms">
  <label for="mushrooms">Грибы</label>
  <input id="olives" type="checkbox" name="toppings" value="olives">
  <label for="olives">Оливки</label>
</fieldset>
```

В [`<fieldset>`](/html/fieldset/) вложена группа чекбоксов, а [`<legend>`](/html/legend/) добавляет к ней подпись. У группы интерактивных элементов всегда должна быть подпись. Скринридеры объявляют подпись группы при взаимодействии с её элементами, поэтому текст должен быть кратким и описательным.

Одиночные чекбоксы, у которых есть связанные с ними подписи в `<label>`, не нужно оборачивать в `<fieldset>` с `<legend>`.

Вложенные в друг друга `<fieldset>` могут вызывать странное поведение у скринридеров, так что избегайте этого.

#### `email`

Используйте для полей с почтой `type="email"`, а не `text`. Так браузеры правильно отобразят почту при включённом автозаполнении поля, провалидируют его по умолчанию, а на мобильных устройствах пользователям будет проще ввести адрес с виртуальной клавиатуры.

Для поля с почтой также используйте `autocomplete` со значением `email`. Не заставляйте пользователей вводить почту каждый раз.
```html
<label for="email">Введите почту:</label>
<input type="email" name="email" id="email" autocomplete="email">
```

#### `password`

Для полей с паролями используйте `type="password"`. Также для этого поля хорошо использовать атрибут `autocomplete` со значением `new-password`, если это новый пароль, или `current-password`, если текущий.

```html
<label for="userPassword">Введите свой пароль:</label>
<input id="userPassword" type="password" autocomplete="current-password">
```

#### `radio`

Для радиокнопок используйте `type="radio"`.

```html
<fieldset>
  <legend>Выберите время доставки:</legend>
  <input id="night" type="radio" name="shipping" value="night">
  <label for="night">Ночь</label>
  <input id="day" type="radio" name="shipping" value="day">
  <label for="day">День</label>
  <input id="morning" type="radio" name="shipping" value="morning">
  <label for="morning">Утро</label>
</fieldset>
```

### `<select>`

Выпадающие списки — сложный паттерн. Для простых случаев используйте [`<select>`](/html/select/).
```html
<label for="favCity">Выберите любимый город:</label>
<select id="favCity" name="select">
  <option value="amsterdam">Амстердам</option>
  <option value="paris">Париж</option>
  <option value="rome">Рим</option>
</select>
```

Длинные списки параметров можно сгруппировать с помощью [`<optgroup>`](/html/optgroup/) внутри `<select>`. Имейте в виду, что иногда `<optgroup>` игнорируют скринридеры, не полагайтесь на этот метод для передачи важной информации.

### Кнопки

У формы может быть одна и несколько кнопок. Их можно реализовать несколькими способами.

```html
<!-- Простая кнопка <button>, в неё встроена роль button -->
<button type="submit">Отправить</button>

<!-- Кнопки для отправки и сброса данных с типами submit и reset,
в них тоже встроена роль button -->
<input type="submit" name="submit" value="Отправить">
<input type="reset" name="reset" value="Сбросить">
```

Скринридеры читают вложенный в [`<button>`](/html/button/) текст и значения атрибута `value` у `<input>`. Кнопки формы никогда не должны быть пустыми.

`<button>` обязательно быть вложен внутрь `<form>`. Только в этом случае данные формы отправляться при нажатии на <kbd>Enter<kbd>.

Не используйте кнопки c типом `reset` без особой необходимости, потому что на них легко нажать по ошибке.

## Атрибуты элементов управления формой

У полей могут быть различные атрибуты, которые определяют их поведение и внешний вид. Самые важные атрибуты:

- `required` – поле обязательно для заполнения.
- `disabled` – поле недоступно для ввода.
- `readonly` – поле доступно только для чтения.
- `maxlength` – максимальное количество символов, которое может быть в поле.
- `pattern` – регулярное выражение, которому должны соответствовать введённые данные.
- `autocomplete` – должен ли браузер автоматически заполнять поле формы на основе предыдущих вводов пользователя. [Список всех значений `autocomplete`](https://www.w3.org/TR/WCAG21/#input-purposes).

## Важные «не»

- Не создавайте кастомные элементы, если есть нативные теги и атрибуты в HTML.
- Не делайте формы большими и запутанными. Это усложнит жизнь всех пользователей.
- Не создавайте формы, которые никак не заполнить с клавиатуры. Некоторые пользователи не могут пользоваться мышкой и другими устройствами ввода.
- Не используйте слишком маленькие поля ввода или кнопки. С ними может быть трудно взаимодействовать пользователя с инвалидностью.
- Не используйте только цвет для обязательных полей. Некоторые пользователи не различают цвета.
- Не используйте только звуковые оповещения для сообщения об ошибках или успешной отправке формы. Есть пользователи, которые слышат или не понимают звуки.
- Не очищайте быстро введённые данные из полей, если только это не финансовая операция. Дайте пользователям хотя бы 24 часа.
- Не запрещайте автозаполнение с помощью `autocomplete="off"`.
- Не давайте пользователям случайно отправить данные из формы несколько раз.

Подробнее про работу с формами и валидацию данных узнаете из статьи «[Работа с формами](/js/deal-with-forms/)».

## `<label>` и альтернативы

У `<label>` есть ограничение. К одному полю нельзя добавить несколько подписей или связать одну подпись с несколькими полями.

Это ограничение можно преодолеть с помощью скрытых `<label>` и ARIA-атрибутов:

- [`aria-labelledby`](/a11y/aria-labelledby/);
- [`aria-describedby`](/a11y/aria-describedby/);
- [`aria-label`](/a11y/aria-label/).

Также стоит добавить, что `<label>` бывает:

- Явным или прямым, когда они вложен.
- Неявным, когда теги расположены рядом и связаны через `for` и `id`. В целом они равносильны, но с явными могут быть проблемы с объявлением некоторыми скринридерами. [Пруф.](https://css-tricks.com/html-inputs-and-labels-a-love-story/#aa-how-to-pair-a-label-and-an-input)


### `aria-labelledby`

Если у поля есть связанный с ним `<label>` и `aria-labelledby`, скринридер будет читать текст из `aria-labelledby` вместо `<label>`.

### `aria-describedby`

```html
<label for="resetPass">Введите новый пароль:</label>
<input
  type="password"
  id="resetpass"
  autocomplete="new-password"
  aria-describedby="passwordHint"
>
<span id="passwordHint">
  Новый пароль должен содержать от 8 015 символов,
  включая буквы и цифры.
</span>
```

В примере `aria-describedby` связан с `id` элемента с требованиями к паролю. Скринридер обычно произносит текст из `<label>`, затем тип `<input>` (`text`, `checkbox` и т. д.), другие необходимые свойства полня (`required`, `readonly` и др.), а затем дополнительное описание.

С помощью `aria-describedby` можно ссылаться на несколько элементов. Для этого добавьте в атрибут несколько значений и разделите их пробелами.

Скринридер читает содержимое `aria-describedby` в дополнение к `<label>`, поэтому текст внутри `<label>` тоже нужен.

`aria-describedby` также можно использовать для дополнительной информации, которая отображаются в виде всплывающих подсказок — тултипов.

### Невидимый `<label>`

Иногда для полей не нужна видимая подпись в `<label>`. Например, одиночные поля вроде поля для поиска по сайту и странице обычно располагается рядом с кнопкой поиска, и их цель очевидна для зрячих пользователей. Так что видимая подпись может быть излишней. В таких случаях используйте скрытую подпись. Есть три способа её добавить.

Используйте только один из этих способов. Одновременное использование двух или более атрибутов приведёт к повторению информации скринридером. Например, скрытого `<label>` и атрибута `title`.

Скройте `<label>` с помощью CSS. Чаще всего элемент просто убирают за пределы экрана с помощью [приёма с классом `.visually-hidden`](/a11y/content-hidden/#klassy-.visually-hidden-.sr-only-.off-screen). Так подпись не будет отображаться визуально, но скринридер всё равно прочтёт её.

```html
<label class="visually-hidden" for="search">Поиск</label>
<input type="text" id="search">
```

Если у `<input>` есть атрибут `title`, но нет `<label>`, скринридер зачитает `title` как подпись. Это также приведёт к появлению всплывающей подсказки при наведении курсора на поле.

Хотя подсказка может быть полезной, у `title` больше недостатков. Её увидят только пользователи мыши и она может кого-то отвлекающей. Так что лучше использовать другие способы для добавления скрытой подписи к полю.

```html
<input id="search" type="text" title="Поиск">
```

`aria-label` тоже перезаписывает значение `<label>`.

```html
<input id="search" type="text" aria-label="Поиск">
```

## Тестируйте формы

Обязательно проверяйте доступность своих форм.

- Тестируйте со скринридерами NVDA, JAWS, VoiceOver и другими.
- Пользуйтесь специальными инструментами разработчика для проверки доступности в [Chrome](https://developer.chrome.com/docs/devtools/accessibility/reference/#explore-tree) и [Firefox](https://firefox-source-docs.mozilla.org/devtools-user/accessibility_inspector/#accessing-the-accessibility-inspector).
- Используйте автоматические инструменты для проверки доступности. Например, [axe](https://www.deque.com/axe/) или [Lighthouse](https://developer.chrome.com/docs/lighthouse/overview/).
- Можно использовать [HTML-валидатор](https://validator.w3.org).