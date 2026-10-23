# Отчёт по лабораторной работе №3. Bubble

**Студент:** Дусов Алексей
**Группа:** СПД ИИ-241

## 1. Краткое описание выполненного

На бесплатном плане Bubble создано приложение «Книжная полка» — публичный сайт со списком книг. Каркас приложения сгенерирован AI-агентом Bubble по промпту из раздела 2. Затем по Build Guide «Search functionality» добавлен поиск книг по названию, и вручную, без AI, доработан UI: заголовок, Global variable для цвета, новый стиль, готовый компонент. В Data создан новый тип данных «Отзыв» (Review) с полями разных типов. На отдельной странице `reviews` для него реализован полный CRUD: каждое действие — отдельный workflow.

## 2. Промпт для генерации каркаса (дословно)

```text
Создай приложение на тему «Список книг» — «Книжная полка».
Пусть этот сайт будет публичный.
Страницу обязательно создай по структуре header + body + footer.
Обязательно укажи в заготовке следующую строку: «ТВП: Дусов»
Добавь несколько интерактивных элементов (реализующих CRUD): форму добавления книги (название, автор, жанр, год), список книг, кнопки редактирования и удаления книги.
```

План, который AI-агент составил по промпту:

![План приложения](../screenshots/01-prompt-plan.png)
![Список функций](../screenshots/01-prompt-features.png)

Результат генерации и строка «ТВП: Дусов» в подвале:

![Результат генерации](../screenshots/01-prompt.png)
![Строка ТВП в footer](../screenshots/01-prompt-footer.png)

## 3. Ссылка на dev-версию приложения

Главная страница: [https://my-app-261023-90430.bubbleapps.io/version-test](https://my-app-261023-90430.bubbleapps.io/version-test)
Страница отзывов (CRUD): [https://my-app-261023-90430.bubbleapps.io/version-test/reviews](https://my-app-261023-90430.bubbleapps.io/version-test/reviews)

## 4. Исследование идеи (Build Guides)

Build Guides сгенерированы по описанию идеи приложения (промпт для Build Guides):

```text
Bookshelf app: a public catalog of books with title, author, genre and year. Users can add, edit and delete books, filter by genre and search books by title. Also users can leave reviews for books with rating, date and "recommend" checkbox.
```

| Что сделал | Описание |
| --- | --- |
| Что посмотрел | список из 6 гайдов под мою идею (User authentication, Search functionality, Genre filtering, Public catalog display, Book catalog management, Book reviews and ratings) и подробно гайд **Search functionality** |
| Что показалось полезным | гайд разбивает функцию на шаги Data → Design → Logic: какие поля нужны в типе Book, какой элемент добавить и как связать его с поиском |
| Что взял в своё приложение | **поиск книги по названию**: поле `Input Search` и ограничение `Название contains Input Search's value` в Data source списка `Books List` (с Ignore empty constraints). Отличие от гайда: поиск срабатывает сразу при вводе, без отдельной кнопки и workflow |

![Список Build Guides](../screenshots/02-buildguide-1.png)
![Гайд Search functionality](../screenshots/02-buildguide-2.png)

Настройка поиска (ограничение в Data source списка книг):

![Настройка поиска](../screenshots/02-buildguide-search-setup.png)

**До и после применения идеи:**

| До | После (поиск «Атлас») |
| :---: | :---: |
| ![до](../screenshots/02-buildguide-before.png) | ![после](../screenshots/02-buildguide-after.png) |

## 5. Ручная доработка UI (без AI)

| Шаг | Что сделано | Скриншот |
| --- | --- | --- |
| 3.1 Заголовок | в reusable element **Header** текст изменён на «ТВП: Алексей Дусов» | ![03-header](../screenshots/03-header-name.png) |
| 3.2 Global variable | в Styles → Variables → Colors создана переменная `primary-color` (Color, `#2E7D32`). Применена к цвету текста надписи «ОБЩАЯ БИБЛИОТЕКА» (Eyebrow Text) | ![03](../screenshots/03-global-variable.png) ![03-applied](../screenshots/03-global-variable-applied.png) |
| 3.3 Стиль | стиль `Button Primary Dusov` (Button): Background = `primary-color`, текст Primary contrast (белый), Radius 8. Применён к кнопке «Добавить» (Add Button) | ![04](../screenshots/04-style.png) ![04-applied](../screenshots/04-style-applied.png) |
| 3.4 Компонент | из COMPONENTS (Drag & drop UI components → Marketing) добавлен hero-баннер **Group Hero**; тексты заменены на блок «Книга месяца — Мастер и Маргарита» с кнопкой «Читать отзывы» | ![05](../screenshots/05-component.png) |

## 6. Data и Workflows

### 6.1. Новая сущность «Отзыв» (Review)

| Поле | Тип | Назначение |
| --- | --- | --- |
| `text` | text | текст отзыва |
| `rating` | number | оценка от 1 до 5 |
| `read_date` | date | дата прочтения |
| `recommend` | yes / no | рекомендую ли книгу |
| `book` | Book (ссылка на тип) | к какой книге отзыв |

Добавлено 3 записи вручную через App Data. Поле `book` связано с типом Книга; чтобы в списках показывались названия книг, а не ID, в **Edit primary fields** для типа Книга выбрано поле «Название».

**Приватность:** при создании типа была включена галочка «private by default», и отзывы видел бы только их автор. Поскольку сайт публичный, в Data → Privacy для **Everyone else** включены `Find this in searches`, `View` (все поля) и `Auto-bind`.

**Проблема с лимитом:** при добавлении первой записи Bubble выдал ошибку «This application is at its usage limit». Причина — лимит бесплатного плана: **200 записей в базе (Database things)**, а AI-агент при генерации создал 230 тестовых книг. Лишние тестовые книги удалены через App data (осталось 185), после чего записи сохраняются. Вывод: данные, сгенерированные AI, нужно проверять.

![Типы данных](../screenshots/06-data-type.png)
![Приватность Review](../screenshots/06-data-privacy.png)
![Записи](../screenshots/07-data-records.png)

### 6.2. CRUD на странице `reviews`

UI страницы (форма «Добавить отзыв» и список «Reviews List» с полями редактирования в каждой карточке) создан AI-агентом Bubble по промпту из раздела 7. Workflows проверены вручную: каждое действие — отдельный workflow.

| Операция | Элемент | Workflow |
| --- | --- | --- |
| **Create** | кнопка «Добавить отзыв» (Add Button) | workflow «Добавить отзыв»: Add Button is clicked → **Create a new Review** (book = Book Dropdown's value, text = Text Input's value, rating = Rating Dropdown's value, read_date = Date picker's value, recommend = Recommend Checkbox is checked) → **Reset relevant inputs** |
| **Read** | Repeating Group «Reviews List» | Data source: `Do a search for Reviews` — список всех отзывов, по карточке на каждый |
| **Update** | кнопка «Сохранить» в карточке | workflow «Сохранить отзыв»: Save Button is clicked → **Make changes to Review**, Thing to change = `Parent group's Review`, поля берутся из полей этой карточки |
| **Delete** | кнопка «Удалить» в карточке | workflow «Удалить отзыв»: Delete Button is clicked → **Delete Review**, To delete = `Parent group's Review` |

`Parent group's Review` — это отзыв той карточки, внутри которой нажата кнопка: карточка-группа получает данные из текущей ячейки Repeating Group, поэтому изменяется или удаляется именно этот отзыв.

**Страница, созданная AI:**

![AI создал страницу reviews](../screenshots/08-ai-reviews-page.png)

**Проверка в Preview (Read и Create):**

![Read — список отзывов](../screenshots/08-workflow-read.png)
![Create — новый отзыв в списке](../screenshots/08-workflow-create.png)

**Workflows:**

![Workflow Create](../screenshots/09-workflow-create.png)
![Поля Create a new Review](../screenshots/09-workflow-create-fields.png)
![Workflow Delete](../screenshots/10-workflow-delete.png)
![Шаг Delete Review](../screenshots/10-workflow-delete-step.png)
![Workflow Update](../screenshots/10-workflow-update.png)

## 7. Использованные AI-промпты

* *Промпт генерации каркаса* — см. раздел 2.
* *Промпт для новой сущности (внешний AI, Claude):*
  «Придумай для Bubble-приложения "Книжная полка" новую сущность, связанную с книгами. Дай название типа данных и 4–5 полей разных типов (text, number, date, yes/no, ссылка на тип Book) с кратким назначением каждого поля».
  Результат: тип **Review (Отзыв)** с полями `text`, `rating`, `read_date`, `recommend`, `book` (см. раздел 6.1).
* *Промпт для страницы CRUD (AI-агент Bubble):*
  «Создай новую страницу reviews для типа данных Review. На странице: форма добавления отзыва (выбор книги из списка Book, текст отзыва, оценка 1–5, дата прочтения, чекбокс "Рекомендую") и кнопка "Добавить отзыв"; ниже Repeating Group со всеми отзывами, в каждой ячейке поля для редактирования и кнопки "Сохранить" и "Удалить". Сохрани структуру header + body + footer и строку «ТВП: Дусов». Не создавай тестовые данные и не меняй существующие типы данных»».

## 8. Освоенные элементы и workflows

* **Элементы:** Text, Button, Input, Multiline Input, Dropdown, Date/Time Picker, Checkbox, Repeating Group, Group, готовый компонент из COMPONENTS.
* **Дизайн:** Global variables (Color), Styles.
* **Data:** Data Types, поля разных типов, связь между типами (Review → Book), App Data.
* **Workflows:** события `Button is clicked`; действия `Create a new thing`, `Make changes to a thing`, `Delete thing`, `Reset relevant inputs`; источник данных `Do a search for` с ограничениями (constraints) и `Ignore empty constraints`; ссылки `Parent group's ...`.
* **Data и настройки:** Privacy rules, Edit primary fields, лимиты бесплатного плана (200 записей), App Metrics.

## 9. Вывод

[ сделать приложение было прикольно, просто берешь тыкаешь собераешь и както что то работает , уперся в лемит  по книгам  часть стер ну кароче было весело]
