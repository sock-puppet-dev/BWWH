# GUIDE to Hugo Templates

Тема: Hugo Go Template Expressions.

Эта шпаргалка объясняет шаблоны Hugo простыми словами: что означает `{{ ... }}`, что такое точка `.`, как работают `.Title`, `.Content`, `.Site.Title`, условия, циклы, pipes и комментарии.

## 1. Что использует Hugo

Hugo использует шаблонизатор Go:

- `html/template`;
- `text/template`.

В документации Hugo это называется templating.

Шаблоны нужны, чтобы превратить:

- настройки сайта;
- Markdown-контент;
- front matter;
- данные Hugo;
- функции шаблона;

в готовую HTML-страницу.

## 2. Что такое `{{ ... }}`

Конструкция:

```go-html-template
{{ .Site.Title }}
```

называется template action.

Проще:

> Все, что находится внутри `{{ ... }}`, Hugo не печатает как обычный текст, а выполняет как выражение шаблона.

Пример:

```go-html-template
<h1>{{ .Site.Title }}</h1>
```

Если в `hugo.yaml`:

```yaml
title: My New Hugo Project
```

то в HTML получится:

```html
<h1>My New Hugo Project</h1>
```

## 3. Что такое точка `.`

Точка называется context.

В Hugo точка `.` означает "текущий объект".

На обычной странице текущий объект чаще всего — это текущая страница.

Пример:

```go-html-template
{{ .Title }}
```

означает:

> Возьми `Title` у текущей страницы.

Если в `content/about.md`:

```yaml
---
title: "About"
---
```

то:

```go-html-template
<h1>{{ .Title }}</h1>
```

станет:

```html
<h1>About</h1>
```

## 4. `.Site`

`.Site` — это объект всего сайта.

Пример:

```go-html-template
{{ .Site.Title }}
```

Читаем слева направо:

1. `.` — текущий контекст;
2. `.Site` — сайт;
3. `.Title` — название сайта.

То есть:

> Из текущего контекста возьми сайт, а у сайта возьми title.

`Site.Title` возвращает `title` из конфигурации сайта, например из `hugo.yaml`.

## 5. `.Content`

```go-html-template
{{ .Content }}
```

`.Content` выводит основной Markdown-контент страницы, уже преобразованный в HTML.

Пример `content/about.md`:

```markdown
---
title: "About"
---

This is my About page.
```

Пример шаблона:

```go-html-template
<h1>{{ .Title }}</h1>

{{ .Content }}
```

Результат:

```html
<h1>About</h1>

<p>This is my About page.</p>
```

## 6. Комментарии в Hugo-шаблонах

Комментарий Hugo:

```go-html-template
{{/* Это комментарий Hugo. Он не попадет в итоговый HTML. */}}
```

HTML-комментарий:

```html
<!-- Это HTML-комментарий. Он может попасть в итоговый HTML. -->
```

Для учебных заметок внутри шаблона лучше использовать комментарии Hugo:

```go-html-template
{{/* Выводим заголовок текущей страницы */}}
<h1>{{ .Title }}</h1>
```

## 7. Page methods / Page variables

Это значения, которые относятся к текущей странице.

```go-html-template
{{ .Title }}
{{ .Content }}
{{ .Summary }}
{{ .Description }}
{{ .Date }}
{{ .Permalink }}
{{ .RelPermalink }}
{{ .Params }}
{{ .Section }}
{{ .Kind }}
{{ .Type }}
{{ .IsHome }}
{{ .IsPage }}
{{ .IsSection }}
```

Самые важные для начала:

```go-html-template
{{ .Title }}        {{/* заголовок текущей страницы */}}
{{ .Content }}      {{/* основной контент страницы */}}
{{ .Description }}  {{/* описание страницы */}}
{{ .RelPermalink }} {{/* относительная ссылка, например /about/ */}}
```

Пример:

```go-html-template
<article>
    <h1>{{ .Title }}</h1>
    {{ with .Description }}
        <p>{{ . }}</p>
    {{ end }}
    {{ .Content }}
</article>
```

## 8. Site methods / Site variables

Это значения, которые относятся ко всему сайту.

```go-html-template
{{ .Site.Title }}
{{ .Site.BaseURL }}
{{ .Site.Language }}
{{ .Site.Params }}
{{ .Site.Menus }}
{{ .Site.Pages }}
{{ .Site.RegularPages }}
{{ .Site.Taxonomies }}
```

Самые важные для начала:

```go-html-template
{{ .Site.Title }}        {{/* название сайта */}}
{{ .Site.BaseURL }}      {{/* основной URL сайта */}}
{{ .Site.RegularPages }} {{/* обычные страницы сайта */}}
```

Пример:

```go-html-template
<header>
    <a href="/">{{ .Site.Title }}</a>
</header>
```

## 9. `.Params`

`.Params` — это дополнительные поля из front matter.

Пример Markdown:

```markdown
---
title: "About"
description: "Short page description."
image: "/images/about.jpg"
featured: true
---
```

В шаблоне:

```go-html-template
{{ .Params.image }}
{{ .Params.featured }}
```

Пример с проверкой:

```go-html-template
{{ if .Params.image }}
    <img src="{{ .Params.image }}" alt="{{ .Title }}">
{{ end }}
```

Комментарий:

- если `image` есть, картинка появится;
- если `image` нет, Hugo просто пропустит этот блок.

## 10. Условия: `if`

`if` проверяет условие.

```go-html-template
{{ if .IsHome }}
    <h1>Home page</h1>
{{ end }}
```

Обязательно закрывай `if` через:

```go-html-template
{{ end }}
```

Пример:

```go-html-template
{{ if .Description }}
    <p>{{ .Description }}</p>
{{ else }}
    <p>No description yet.</p>
{{ end }}
```

Комментарий:

- `if` выполняет первый блок, если значение существует или равно `true`;
- `else` выполняется, если условие не сработало.

## 11. `with`

`with` похож на `if`, но внутри него меняется точка `.`.

Пример:

```go-html-template
{{ with .Description }}
    <p>{{ . }}</p>
{{ end }}
```

Что происходит:

1. Hugo проверяет, есть ли `.Description`.
2. Если есть, заходит внутрь блока.
3. Внутри блока точка `.` теперь означает само значение `.Description`.

То есть это:

```go-html-template
{{ with .Description }}
    <p>{{ . }}</p>
{{ end }}
```

похоже на:

```go-html-template
{{ if .Description }}
    <p>{{ .Description }}</p>
{{ end }}
```

Но `with` удобнее, когда значение длинное.

## 12. Циклы: `range`

`range` перебирает список.

Пример:

```go-html-template
{{ range .Site.RegularPages }}
    <h2>{{ .Title }}</h2>
{{ end }}
```

Комментарий:

- `.Site.RegularPages` — список обычных страниц сайта;
- `range` проходит по каждой странице;
- внутри `range` точка `.` временно означает текущую страницу из списка.

Пример списка страниц:

```go-html-template
<ul>
    {{ range .Site.RegularPages }}
        <li>
            <a href="{{ .RelPermalink }}">{{ .Title }}</a>
        </li>
    {{ end }}
</ul>
```

## 13. Важная идея: точка меняется

Снаружи:

```go-html-template
{{ .Title }}
```

может означать заголовок текущей страницы.

Но внутри `range`:

```go-html-template
{{ range .Site.RegularPages }}
    {{ .Title }}
{{ end }}
```

точка `.` означает уже не страницу, на которой находится шаблон, а текущую страницу из списка.

Чтобы сохранить старый контекст, используют переменную.

```go-html-template
{{ $currentPage := . }}

{{ range .Site.RegularPages }}
    <p>
        Current item: {{ .Title }}
        Site title: {{ $currentPage.Site.Title }}
    </p>
{{ end }}
```

Комментарий:

- `$currentPage := .` сохраняет текущий контекст в переменную;
- переменные в шаблонах Hugo начинаются с `$`.

## 14. Pipes: конвейеры

Pipe — это символ:

```go-html-template
|
```

Он передает результат дальше.

Пример без pipe:

```go-html-template
{{ upper .Title }}
```

Пример с pipe:

```go-html-template
{{ .Title | upper }}
```

Оба варианта делают заголовок большими буквами.

Еще пример:

```go-html-template
{{ .Summary | truncate 160 }}
```

Читаем так:

> Возьми `.Summary`, затем обрежь до 160 символов.

## 15. Функции Hugo

Функции выполняют действия над значениями.

```go-html-template
{{ now }}
{{ len .Site.RegularPages }}
{{ upper .Title }}
{{ lower .Title }}
{{ truncate 100 .Summary }}
{{ printf "%s | %s" .Title .Site.Title }}
```

Примеры:

```go-html-template
<p>Total pages: {{ len .Site.RegularPages }}</p>
```

```go-html-template
<title>{{ printf "%s | %s" .Title .Site.Title }}</title>
```

Комментарий:

- `len` считает количество элементов;
- `upper` делает текст большими буквами;
- `lower` делает текст маленькими буквами;
- `truncate` обрезает текст;
- `printf` собирает строку по шаблону.

## 16. Сравнения

Частые функции для условий:

```go-html-template
eq  {{/* равно */}}
ne  {{/* не равно */}}
lt  {{/* меньше */}}
le  {{/* меньше или равно */}}
gt  {{/* больше */}}
ge  {{/* больше или равно */}}
```

Пример:

```go-html-template
{{ if eq .Section "posts" }}
    <p>This page is inside posts section.</p>
{{ end }}
```

Пример проверки главной страницы:

```go-html-template
{{ if .IsHome }}
    <h1>{{ .Site.Title }}</h1>
{{ else }}
    <h1>{{ .Title }}</h1>
{{ end }}
```

## 17. Фильтрация страниц: `where`

`where` выбирает элементы из списка по условию.

Пример:

```go-html-template
{{ $pages := where .Site.RegularPages "Section" "posts" }}

{{ range $pages }}
    <h2>{{ .Title }}</h2>
{{ end }}
```

Комментарий:

- `.Site.RegularPages` — все обычные страницы;
- `where ... "Section" "posts"` оставляет только страницы из раздела `posts`;
- `$pages` — переменная со списком найденных страниц.

## 18. Сортировка

Пример сортировки страниц по дате:

```go-html-template
{{ range sort .Site.RegularPages "Date" "desc" }}
    <article>
        <h2>{{ .Title }}</h2>
        <time>{{ .Date.Format "2006-01-02" }}</time>
    </article>
{{ end }}
```

Комментарий:

- `sort` сортирует список;
- `"Date"` — поле для сортировки;
- `"desc"` — от новых к старым;
- формат даты в Go выглядит необычно: `"2006-01-02"` — это специальный пример-эталон.

## 19. Пример полного `layouts/index.html`

```go-html-template
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="utf-8">
    <title>{{ .Site.Title }}</title>
</head>
<body>
    {{/* На главной странице выводим название сайта */}}
    <h1>{{ .Site.Title }}</h1>

    {{/* Контент из content/_index.md */}}
    {{ .Content }}

    {{/* Список всех обычных страниц сайта */}}
    <h2>Pages</h2>
    <ul>
        {{ range .Site.RegularPages }}
            <li>
                <a href="{{ .RelPermalink }}">{{ .Title }}</a>
            </li>
        {{ end }}
    </ul>
</body>
</html>
```

## 20. Пример полного `layouts/single.html`

```go-html-template
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="utf-8">
    <title>{{ .Title }} | {{ .Site.Title }}</title>
</head>
<body>
    <header>
        <a href="/">{{ .Site.Title }}</a>
    </header>

    <main>
        {{/* Заголовок текущей страницы */}}
        <h1>{{ .Title }}</h1>

        {{/* Описание выводим только если оно есть */}}
        {{ with .Description }}
            <p>{{ . }}</p>
        {{ end }}

        {{/* Основной Markdown-контент */}}
        {{ .Content }}
    </main>
</body>
</html>
```

## 21. SEO-минимум

Полезные выражения для SEO:

```go-html-template
{{ .Title }}
{{ .Description }}
{{ .Params.description }}
{{ .Permalink }}
{{ .Site.Title }}
{{ .Site.Params.description }}
```

Пример:

```go-html-template
<title>{{ .Title }} | {{ .Site.Title }}</title>

{{ with .Description }}
    <meta name="description" content="{{ . }}">
{{ end }}
```

Если описание хранится в `.Params.description`:

```go-html-template
{{ with .Params.description }}
    <meta name="description" content="{{ . }}">
{{ end }}
```

## 22. Самые важные выражения для новичка

База страницы:

```go-html-template
{{ .Title }}
{{ .Content }}
{{ .Summary }}
{{ .Description }}
{{ .Date }}
{{ .Permalink }}
{{ .RelPermalink }}
```

База сайта:

```go-html-template
{{ .Site.Title }}
{{ .Site.BaseURL }}
{{ .Site.Params }}
{{ .Site.Menus }}
{{ .Site.RegularPages }}
```

Проверки:

```go-html-template
{{ if .IsHome }}
{{ if .IsPage }}
{{ if .IsSection }}
{{ if .Params.image }}
```

Циклы:

```go-html-template
{{ range .Site.RegularPages }}
    {{ .Title }}
{{ end }}
```

Функции:

```go-html-template
{{ upper .Title }}
{{ lower .Title }}
{{ truncate 160 .Summary }}
{{ len .Site.RegularPages }}
```

## 23. Типичные ошибки

### Ошибка 1: забыть `end`

Неправильно:

```go-html-template
{{ if .Description }}
    <p>{{ .Description }}</p>
```

Правильно:

```go-html-template
{{ if .Description }}
    <p>{{ .Description }}</p>
{{ end }}
```

### Ошибка 2: потерять контекст внутри `range`

```go-html-template
{{ range .Site.RegularPages }}
    {{ .Site.Title }}
{{ end }}
```

Внутри `range` точка поменялась. Часто это работает, потому что у страницы тоже есть доступ к `.Site`, но для обучения лучше явно понимать смену контекста.

Безопасный учебный вариант:

```go-html-template
{{ $siteTitle := .Site.Title }}

{{ range .Site.RegularPages }}
    <p>{{ .Title }} | {{ $siteTitle }}</p>
{{ end }}
```

### Ошибка 3: путать `.Description` и `.Params.description`

Если во front matter:

```yaml
description: "About me page."
```

то часто можно использовать:

```go-html-template
{{ .Description }}
```

Но если значение лежит в кастомном параметре:

```yaml
myDescription: "Custom text."
```

то нужно:

```go-html-template
{{ .Params.myDescription }}
```

### Ошибка 4: писать шаблон как обычный HTML

Так Hugo ничего не подставит:

```html
<h1>.Title</h1>
```

Нужно:

```go-html-template
<h1>{{ .Title }}</h1>
```

## 24. Мини-упражнения

### Упражнение 1

В `layouts/single.html` выведи заголовок страницы:

```go-html-template
<h1>{{ .Title }}</h1>
```

Проверь страницу `/about/`.

### Упражнение 2

Добавь в `content/about.md`:

```yaml
description: "This is a short About page description."
```

Выведи описание:

```go-html-template
{{ with .Description }}
    <p>{{ . }}</p>
{{ end }}
```

### Упражнение 3

На главной странице выведи список всех страниц:

```go-html-template
<ul>
    {{ range .Site.RegularPages }}
        <li>
            <a href="{{ .RelPermalink }}">{{ .Title }}</a>
        </li>
    {{ end }}
</ul>
```

### Упражнение 4

Покажи количество страниц:

```go-html-template
<p>Total pages: {{ len .Site.RegularPages }}</p>
```

## 25. Карта Hugo Template Expressions

Hugo Template Expressions — это не просто "переменные", а смесь:

1. methods — методы объектов, например `.Title`, `.Content`, `.Site.Title`;
2. functions — функции, например `upper`, `lower`, `where`, `dict`;
3. statements — конструкции Go templates, например `if`, `range`, `with`;
4. pipes — передача результата дальше через `|`;
5. variables — переменные, например `$pages`, `$siteTitle`.

Главная мысль:

> Всегда спрашивай себя: что сейчас означает точка `.`?

Если ты понимаешь текущий context, Hugo-шаблоны становятся намного проще.
