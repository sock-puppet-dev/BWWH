# GUIDE to Hugo Templates

Этот файл объясняет Hugo Go Template Expressions на примерах текущего проекта BWWH.

Главная цель: понять, что означает `{{ ... }}`, как работает точка `.`, как читать шаблоны и как Hugo превращает Markdown в HTML.

## 1. Что такое Hugo template

Template — это HTML-файл с выражениями Hugo.

Пример:

```go-html-template
<h1>{{ .Title }}</h1>
```

Hugo видит `{{ .Title }}` и подставляет туда заголовок текущей страницы.

## 2. Что означает `{{ ... }}`

Конструкция:

```go-html-template
{{ .Site.Title }}
```

называется template action.

Это значит:

> Выполни выражение внутри фигурных скобок и вставь результат в HTML.

Если в `hugo.yaml`:

```yaml
title: BWWH
```

то:

```go-html-template
<h1>{{ .Site.Title }}</h1>
```

даст:

```html
<h1>BWWH</h1>
```

## 3. Что означает точка `.`

Точка `.` — это текущий контекст.

На обычной странице точка обычно означает текущую страницу.

Пример:

```go-html-template
{{ .Title }}
```

Означает:

> Возьми `Title` текущей страницы.

Если страница `content/about/index.md` содержит:

```yaml
title: 'О проекте'
```

то `{{ .Title }}` вернет:

```text
О проекте
```

## 4. `.Site` и `site`

В Hugo можно встретить оба варианта:

```go-html-template
{{ .Site.Title }}
{{ site.Title }}
```

Оба получают данные сайта.

В текущей теме часто используется глобальная функция `site`:

```go-html-template
<h1>{{ site.Title }}</h1>
```

Она берет `title` из `hugo.yaml`.

## 5. `.Content`

```go-html-template
{{ .Content }}
```

`.Content` выводит Markdown-контент страницы, уже превращенный в HTML.

Пример Markdown:

```markdown
BWWH is a learning project.
```

В HTML это станет:

```html
<p>BWWH is a learning project.</p>
```

## 6. Общий каркас: baseof.html

Файл:

```text
themes/basic/layouts/baseof.html
```

Содержит общий HTML-каркас:

```go-html-template
<!DOCTYPE html>
<html lang="{{ site.Language.Locale }}" dir="{{ or site.Language.Direction `ltr` }}">
<head>
  {{ partial "head.html" . }}
</head>
<body>
  <header>
    {{ partial "header.html" . }}
  </header>
  <main>
    {{ block "main" . }}{{ end }}
  </main>
  <footer>
    {{ partial "footer.html" . }}
  </footer>
</body>
</html>
```

Пояснение:

- `partial "head.html" .` подключает partial и передает ему текущий контекст.
- `block "main" .` оставляет место для содержимого конкретной страницы.
- `{{ end }}` закрывает блок.

## 7. Partial

Partial — маленький шаблон, который подключается в другой шаблон.

Пример:

```go-html-template
{{ partial "header.html" . }}
```

Означает:

> Подключи файл `themes/basic/layouts/_partials/header.html` и передай туда текущую точку `.`.

Текущие partials:

```text
_partials/head.html
_partials/header.html
_partials/footer.html
_partials/menu.html
_partials/terms.html
```

## 8. Head и meta description

Файл:

```text
themes/basic/layouts/_partials/head.html
```

Текущий смысл:

```go-html-template
{{ $pageTitle := cond (eq .Title "Tags") "Теги" .Title }}
<title>{{ if .IsHome }}{{ site.Title }}{{ else }}{{ printf "%s | %s" $pageTitle site.Title }}{{ end }}</title>
{{ with or .Description site.Params.description }}
<meta name="description" content="{{ . }}">
{{ end }}
```

Пояснение:

- `$pageTitle` — переменная для заголовка страницы.
- `cond (eq .Title "Tags") "Теги" .Title` заменяет служебный заголовок `Tags` на русский `Теги`.
- `.IsHome` проверяет, главная ли это страница.
- `.Title` — заголовок текущей страницы.
- `site.Title` — название сайта.
- `printf "%s | %s"` собирает строку вида `О проекте | BWWH`.
- `or .Description site.Params.description` берет описание страницы, а если его нет — общее описание сайта.
- `with` выполняет блок только если описание найдено.

## 9. `if`

`if` — условие.

Пример из `page.html`:

```go-html-template
{{ if eq .Section "posts" }}
  <time datetime="{{ $dateMachine }}">{{ $dateHuman }}</time>
{{ end }}
```

Пояснение:

- `eq` означает "равно".
- `.Section` — раздел страницы.
- Если страница находится в разделе `posts`, Hugo выводит дату.
- Если это `about`, `resume` или `contact`, дата не выводится.

## 10. Переменные

Переменные начинаются с `$`.

Пример:

```go-html-template
{{ $dateMachine := .Date | time.Format "2006-01-02T15:04:05-07:00" }}
{{ $dateHuman := .Date | time.Format ":date_long" }}
```

Пояснение:

- `$dateMachine` — дата для HTML-атрибута `datetime`.
- `$dateHuman` — дата для человека.
- `:=` создает переменную.

## 11. Pipe `|`

Pipe передает результат дальше.

Пример:

```go-html-template
{{ .Date | time.Format ":date_long" }}
```

Читать так:

1. Возьми `.Date`.
2. Передай дату в `time.Format`.
3. Верни отформатированную дату.

## 12. `range`

`range` перебирает список.

Пример из `home.html`:

```go-html-template
{{ range $sitePages }}
  <section>
    <h3><a href="{{ .RelPermalink }}">{{ .LinkTitle }}</a></h3>
    {{ .Summary }}
  </section>
{{ end }}
```

Пояснение:

- `$sitePages` — список страниц.
- `range` проходит по каждой странице.
- Внутри `range` точка `.` означает текущую страницу из списка.
- `.RelPermalink` — относительная ссылка.
- `.LinkTitle` — заголовок для ссылки.
- `.Summary` — краткое содержимое страницы.

## 13. `where`

`where` фильтрует список.

Текущий пример:

```go-html-template
{{ $sitePages := where site.RegularPages "Section" "ne" "posts" }}
{{ $learningPosts := where site.RegularPages "Section" "posts" }}
```

Пояснение:

- `site.RegularPages` — все обычные страницы сайта.
- `"Section" "posts"` оставляет только страницы раздела `posts`.
- `"Section" "ne" "posts"` оставляет страницы, где section не равен `posts`.
- `ne` означает "не равно".

## 14. Почему точка меняется

Снаружи:

```go-html-template
{{ .Title }}
```

это заголовок текущей страницы.

Но внутри:

```go-html-template
{{ range $learningPosts }}
  {{ .Title }}
{{ end }}
```

точка означает текущий пост из списка.

Это ключевая идея Hugo templates:

> Всегда проверяй, что означает точка `.` в текущем месте шаблона.

## 15. `with`

`with` выполняет блок, если значение существует.

Пример:

```go-html-template
{{ with .Description }}
  <meta name="description" content="{{ . }}">
{{ end }}
```

Внутри `with` точка `.` становится значением `.Description`.

Если `.Description` пустой, блок не выводится.

## 16. Меню

Меню задано в `hugo.yaml`, а выводится в:

```text
themes/basic/layouts/_partials/menu.html
```

Главная идея:

```go-html-template
{{- with index site.Menus $menuID }}
  <nav>
    <ul>
      {{- partial "inline/menu/walk.html" (dict "page" $page "menuEntries" .) }}
    </ul>
  </nav>
{{- end }}
```

Пояснение:

- `index site.Menus $menuID` берет меню по имени.
- `with` проверяет, существует ли меню.
- `dict` создает словарь данных для partial.
- inline partial `walk.html` обходит пункты меню.

## 17. Terms и tags

Файл:

```text
themes/basic/layouts/_partials/terms.html
```

Используется в `page.html`:

```go-html-template
{{ partial "terms.html" (dict "taxonomy" "tags" "page" .) }}
```

Пояснение:

- `taxonomy` получает значение `tags`.
- `page` получает текущую страницу.
- partial выводит теги страницы, если они есть.

## 18. Hugo Pipes для CSS и JS

CSS подключается через:

```text
themes/basic/layouts/_partials/head/css.html
```

JS подключается через:

```text
themes/basic/layouts/_partials/head/js.html
```

В production Hugo:

- собирает файл;
- минифицирует его;
- добавляет fingerprint;
- вставляет `integrity`.

Это видно в итоговом HTML после сборки.

## 19. Частые выражения

```go-html-template
{{ .Title }}              {{/* заголовок страницы */}}
{{ .Content }}            {{/* HTML из Markdown */}}
{{ .Description }}        {{/* описание страницы */}}
{{ .RelPermalink }}       {{/* относительная ссылка */}}
{{ .Section }}            {{/* раздел страницы */}}
{{ site.Title }}          {{/* название сайта */}}
{{ site.RegularPages }}   {{/* обычные страницы */}}
{{ site.Params }}         {{/* параметры из hugo.yaml */}}
```

## 20. Частые ошибки

### Ошибка 1. Забыть `end`

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

### Ошибка 2. Не заметить смену точки

Внутри `range` точка меняется.

Если нужен внешний контекст, сохрани его:

```go-html-template
{{ $currentPage := . }}
```

### Ошибка 3. Путать `.Description` и `site.Params.description`

- `.Description` — описание текущей страницы.
- `site.Params.description` — общее описание сайта из `hugo.yaml`.

### Ошибка 4. Искать активные шаблоны в корневой папке layouts

В текущем проекте активные шаблоны находятся здесь:

```text
themes/basic/layouts/
```

## 21. Мини-упражнения

1. В `content/about/index.md` измени `description`.
2. Запусти `hugo`.
3. Найди новый meta description в `public/about/index.html`.
4. В `themes/basic/layouts/home.html` измени заголовок `Учебные посты`.
5. Запусти `hugo server -D`.
6. Проверь главную страницу в браузере.
7. В `themes/basic/layouts/page.html` временно убери проверку `if eq .Section "posts"`.
8. Посмотри, почему дата появится на страницах `О проекте`, `Навыки` и `Контакты`.
9. Верни проверку обратно.

## 22. Главный принцип

Hugo templates становятся понятнее, если каждый раз задавать два вопроса:

1. Что сейчас означает точка `.`?
2. Из какого файла Hugo взял этот шаблон?
