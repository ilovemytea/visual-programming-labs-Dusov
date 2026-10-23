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

[https://<имя-приложения>.bubbleapps.io/version-test](https://<имя-приложения>.bubbleapps.io/version-test)

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

Добавлено 3 записи вручную через App Data.

![Типы данных](../screenshots/06-data-type.png)
![Записи](../screenshots/07-data-records.png)

### 6.2. CRUD на странице `reviews`

UI страницы создан AI-агентом Bubble, workflows настроены вручную.

| Операция | Элемент | Workflow |
| --- | --- | --- |
| **Create** | кнопка «Добавить отзыв» | When Button is clicked → Create a new thing (Review) → поля из inputs → Reset relevant inputs |
| **Read** | Repeating Group | Data source: `Do a search for Reviews`, сортировка по `read_date` (descending) |
| **Update** | кнопка «Сохранить» в ячейке | When Button is clicked → Make changes to a thing → `Current cell's Review` |
| **Delete** | кнопка «Удалить» в ячейке | When Button is clicked → Delete thing → `Current cell's Review` |

![Create в Preview](../screenshots/08-workflow-create.png)
![Workflow Create](../screenshots/09-workflow-create.png)
![Workflow Delete](../screenshots/10-workflow-delete.png)

## 7. Использованные AI-промпты

* *Промпт генерации каркаса* — см. раздел 2.
* *Промпт для новой сущности (внешний AI, Claude):*
  «Придумай для Bubble-приложения "Книжная полка" новую сущность, связанную с книгами. Дай название типа данных и 4–5 полей разных типов (text, number, date, yes/no, ссылка на тип Book) с кратким назначением каждого поля».
  Результат: тип **Review (Отзыв)** с полями `text`, `rating`, `read_date`, `recommend`, `book` (см. раздел 6.1).
* *Промпт для страницы CRUD (AI-агент Bubble):*
  «Создай новую страницу reviews для типа данных Review. На странице: форма добавления отзыва (выбор книги из списка Book, текст отзыва, оценка 1–5, дата прочтения, чекбокс "Рекомендую") и кнопка "Добавить отзыв"; ниже Repeating Group со всеми отзывами, в каждой ячейке поля для редактирования и кнопки "Сохранить" и "Удалить". Сохрани структуру header + body + footer и строку «ТВП: Дусов»».

## 8. Освоенные элементы и workflows

* **Элементы:** Text, Button, Input, Multiline Input, Dropdown, Date/Time Picker, Checkbox, Repeating Group, Group, готовый компонент из COMPONENTS.
* **Дизайн:** Global variables (Color), Styles.
* **Data:** Data Types, поля разных типов, связь между типами (Review → Book), App Data.
* **Workflows:** `Create a new thing`, `Make changes to a thing`, `Delete thing`, `Reset relevant inputs`; источник данных `Do a search for`.

## 9. Вывод

[Напишите своими словами 3–5 предложений: что сделал AI-агент хорошо, что пришлось дорабатывать руками, чем Bubble удобнее Node-RED и где неудобнее.]
