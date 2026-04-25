# GUIDE to Hugo

Эта шпаргалка написана для новичка. Ее цель — помочь понять, из каких частей состоит Hugo-проект, какие команды запускать и где искать причину ошибки.

Опубликованная версия проекта BWWH:

```text
https://sock-puppet-dev.github.io/BWWH/
```

## 1. Что такое Hugo

Hugo — это генератор статических сайтов.

Это значит:

1. Ты пишешь контент в Markdown-файлах внутри папки `content`.
2. Ты пишешь HTML-шаблоны внутри папки `layouts`.
3. Hugo соединяет контент и шаблоны.
4. На выходе получается готовый HTML-сайт в папке `public`.

Важно: папка `public` — это результат сборки. Обычно ее не редактируют руками, потому что Hugo пересоздает ее при новой сборке.

## 2. Создать новый проект Hugo

```bash
hugo new site aerocool-site
```

Эта команда создает новую папку `aerocool-site` со структурой Hugo-проекта.

Если ты уже находишься внутри нужной пустой папки, можно создать проект прямо в текущей папке:

```bash
hugo new site . --format yaml
```

Комментарий:

- `.` означает "текущая папка";
- `--format yaml` говорит Hugo создать конфигурацию в формате YAML, например `hugo.yaml`.

## 3. Запустить сайт локально

```bash
hugo server -D
```

После запуска Hugo обычно показывает адрес:

```text
http://localhost:1313/
```

Открой этот адрес в браузере.

Комментарий:

- `hugo server` запускает локальный сервер для разработки;
- `-D` показывает страницы со статусом `draft: true`;
- если файл сохранен, Hugo обычно сам обновляет сайт в браузере.

## 4. Собрать сайт в папку public

```bash
hugo
```

Эта команда создает или обновляет папку `public`.

Комментарий:

- `hugo server` нужен для разработки;
- `hugo` нужен для финальной сборки;
- файлы в `public` — готовый результат, а не исходники.

## 5. Главные папки Hugo-проекта

В твоем проекте BWWH сейчас есть такая базовая структура:

```text
BWWH/
├── archetypes/
│   └── default.md
├── content/
│   ├── _index.md
│   └── about.md
├── layouts/
│   ├── index.html
│   └── single.html
├── public/
├── hugo.yaml
├── GUIDE to Hugo.md
└── GUIDE to Hugo Templates.md
```

Что это значит:

- `hugo.yaml` — настройки всего сайта;
- `content` — страницы сайта в Markdown;
- `layouts` — HTML-шаблоны;
- `archetypes` — шаблоны для новых Markdown-страниц;
- `public` — готовый сайт после сборки;
- `GUIDE...md` — твои учебные материалы.

## 6. Конфигурация сайта: hugo.yaml

Пример из текущего проекта:

```yaml
baseURL: https://example.org/
languageCode: en-us
title: My New Hugo Project
```

Что означает каждая строка:

- `baseURL` — основной адрес сайта;
- `languageCode` — язык сайта;
- `title` — название сайта.

В шаблоне значение `title` можно получить так:

```go-html-template
{{ .Site.Title }}
```

Пример:

```html
<h1>{{ .Site.Title }}</h1>
```

Если в `hugo.yaml` написано:

```yaml
title: My New Hugo Project
```

то Hugo выведет:

```html
<h1>My New Hugo Project</h1>
```

## 7. Контент: папка content

Контент обычно пишется в Markdown.

Пример страницы:

```markdown
---
title: "About"
date: 2026-04-24T05:57:40+03:00
draft: false
---

This is my About page.
```

Верхний блок между `---` называется front matter.

Front matter — это настройки конкретной страницы.

Частые поля:

- `title` — заголовок страницы;
- `date` — дата страницы;
- `draft` — черновик или опубликованная страница;
- `description` — описание страницы;
- `tags` — теги;
- `categories` — категории.

Пример с дополнительными полями:

```markdown
---
title: "My First Project"
description: "Short description for search engines and previews."
date: 2026-04-25
draft: false
tags:
  - hugo
  - templates
---

This is my first project page.
```

## 8. Главная страница: content/_index.md

Файл `content/_index.md` обычно отвечает за контент главной страницы или раздела.

Пример:

```markdown
---
title: "Home"
description: "My personal portfolio site."
---

This is my portfolio.

On this site, you'll find:

- My biography
- My projects
- My resume
```

Комментарий:

- `_index.md` часто используется для главной страницы или страницы раздела;
- обычный файл вроде `about.md` становится отдельной страницей;
- для главной страницы Hugo обычно использует шаблон `layouts/index.html`.

## 9. Создать новую страницу

```bash
hugo new about.md
```

Hugo создаст файл:

```text
content/about.md
```

Создать страницу внутри раздела:

```bash
hugo new posts/my-first-post.md
```

Hugo создаст:

```text
content/posts/my-first-post.md
```

## 10. Шаблоны: папка layouts

Шаблоны говорят Hugo, как превратить Markdown в HTML.

В твоем проекте есть:

```text
layouts/index.html
layouts/single.html
```

Обычно:

- `layouts/index.html` используется для главной страницы;
- `layouts/single.html` используется для отдельной страницы, например `/about/`.

Простой пример `layouts/single.html`:

```go-html-template
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="utf-8">
    <title>{{ .Title }} | {{ .Site.Title }}</title>
</head>
<body>
    <h1>{{ .Title }}</h1>

    {{ .Content }}
</body>
</html>
```

Комментарий:

- `{{ .Title }}` — заголовок текущей страницы из front matter;
- `{{ .Site.Title }}` — заголовок всего сайта из `hugo.yaml`;
- `{{ .Content }}` — содержимое Markdown после front matter.

## 11. Как Hugo выбирает шаблон

Упрощенная логика для новичка:

```text
Главная страница       -> layouts/index.html
Обычная страница       -> layouts/single.html
Страница раздела       -> layouts/list.html или другой list-шаблон
```

Пример:

```text
content/about.md
```

обычно будет собран через:

```text
layouts/single.html
```

## 12. Минимальный HTML-шаблон с комментариями

```go-html-template
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="utf-8">

    {{/* .Title — заголовок текущей страницы */}}
    {{/* .Site.Title — название всего сайта из hugo.yaml */}}
    <title>{{ .Title }} | {{ .Site.Title }}</title>
</head>
<body>
    {{/* Главный заголовок страницы */}}
    <h1>{{ .Title }}</h1>

    {{/* .Content выводит Markdown-контент как HTML */}}
    {{ .Content }}
</body>
</html>
```

Важно: комментарии Hugo выглядят так:

```go-html-template
{{/* Это комментарий внутри шаблона Hugo */}}
```

Они не попадут в итоговый HTML.

HTML-комментарии выглядят так:

```html
<!-- Это HTML-комментарий -->
```

Они могут попасть в итоговую HTML-страницу.

## 13. Проверить, что сайт собирается

```bash
hugo
```

Если ошибок нет, Hugo покажет статистику сборки.

Если есть ошибка, внимательно смотри:

- имя файла;
- номер строки;
- текст ошибки.

Частые причины:

- забыта закрывающая скобка `}}`;
- забыто `{{ end }}` после `if` или `range`;
- неправильное имя переменной;
- ошибка в YAML front matter;
- файл лежит не в той папке.

## 14. Типичные ошибки новичка

### Ошибка 1: нет пробела в шаблоне

Так можно, но читать сложнее:

```go-html-template
{{ .Site.Title}}
```

Лучше так:

```go-html-template
{{ .Site.Title }}
```

### Ошибка 2: редактировать public

Не стоит править:

```text
public/index.html
```

Лучше править:

```text
content/_index.md
layouts/index.html
hugo.yaml
```

Потом запустить:

```bash
hugo
```

### Ошибка 3: ждать страницу, которая draft

Если в front matter:

```yaml
draft: true
```

то обычная сборка `hugo` может не показать страницу.

Для разработки используй:

```bash
hugo server -D
```

Или поменяй:

```yaml
draft: false
```

## 15. Маленький учебный план

1. Измени `title` в `hugo.yaml`.
2. Посмотри, как изменится `{{ .Site.Title }}` на сайте.
3. Измени `title` в `content/about.md`.
4. Посмотри, как изменится `{{ .Title }}` на странице About.
5. Добавь в `content/about.md` поле `description`.
6. Выведи его в `layouts/single.html`:

```go-html-template
{{ with .Description }}
    <p>{{ . }}</p>
{{ end }}
```

Комментарий:

- `with .Description` сработает только если описание существует;
- внутри `with` точка `.` временно означает значение `.Description`.

## 16. Короткая памятка команд

```bash
# Создать новый сайт в новой папке
hugo new site my-site

# Создать сайт в текущей папке
hugo new site . --format yaml

# Запустить локальный сервер с draft-страницами
hugo server -D

# Создать новую страницу
hugo new about.md

# Собрать сайт в public
hugo
```

## 17. Разместить Hugo на GitHub Pages

GitHub Pages может сам собирать Hugo-сайт через GitHub Actions.

Идея такая:

1. Ты отправляешь исходники проекта на GitHub.
2. GitHub Actions запускает команду `hugo`.
3. Hugo собирает сайт в папку `public`.
4. GitHub Pages публикует результат в интернете.

В проекте для этого используется файл:

```text
.github/workflows/hugo.yml
```

Workflow устанавливает последнюю версию Hugo автоматически:

```bash
HUGO_VERSION="$(curl -fsSL https://api.github.com/repos/gohugoio/hugo/releases/latest | jq -r '.tag_name' | sed 's/^v//')"
```

Комментарий:

- GitHub Actions обращается к GitHub API;
- берет тег последнего релиза Hugo, например `v0.160.1`;
- убирает букву `v`;
- скачивает соответствующий `.deb`-файл Hugo Extended;
- устанавливает его перед сборкой сайта.

Плюс такого подхода:

- не нужно вручную менять `HUGO_VERSION`;
- каждый новый deploy будет использовать самый свежий релиз Hugo.

Минус:

- если в новой версии Hugo появится несовместимое изменение, сборка может сломаться;
- для учебного проекта это нормально, потому что так ты быстрее увидишь, как версии инструментов влияют на сайт.

Команда сборки в workflow:

```bash
hugo --minify --baseURL "${{ steps.pages.outputs.base_url }}/"
```

Комментарий:

- `hugo` собирает сайт;
- `--minify` немного уменьшает итоговый HTML/CSS/JS;
- `--baseURL` подставляет правильный адрес GitHub Pages.

Что нужно сделать на GitHub:

1. Открой репозиторий.
2. Перейди в `Settings`.
3. Открой раздел `Pages`.
4. В `Build and deployment` выбери `Source: GitHub Actions`.
5. Сделай `git push` в ветку `main`.
6. Открой вкладку `Actions` и дождись зеленой галочки.

После успешного деплоя сайт будет доступен примерно по адресу:

```text
https://USERNAME.github.io/REPOSITORY/
```

Для этого проекта адрес будет похож на:

```text
https://sock-puppet-dev.github.io/BWWH/
```

Это текущий опубликованный адрес BWWH на GitHub Pages.
