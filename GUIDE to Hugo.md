# GUIDE to Hugo

Этот файл — основной учебный гайд по текущему проекту BWWH.

Гайд написан для новичка. Он объясняет не абстрактный Hugo-проект, а именно текущую структуру BWWH на момент проверки.

## 1. Что такое Hugo

Hugo — генератор статических сайтов.

Простая схема:

```text
Markdown-контент + конфигурация + шаблоны = готовый HTML-сайт
```

В BWWH это выглядит так:

```text
content/                  -> основные страницы сайта
themes/basic/content/     -> учебные посты темы
hugo.yaml                 -> настройки сайта
themes/basic/layouts/     -> HTML-шаблоны
themes/basic/assets/      -> CSS и JavaScript
public/                   -> готовый сайт после сборки
```

Папку `public/` не нужно редактировать вручную. Hugo пересоздает ее при сборке.

## 2. Опубликованная версия

```text
https://sock-puppet-dev.github.io/BWWH/
```

## 3. Текущая структура проекта

```text
BWWH/
├── .github/
│   └── workflows/
│       └── hugo.yml
├── archetypes/
│   └── default.md
├── content/
│   ├── _index.md
│   ├── about/
│   │   └── index.md
│   ├── contact/
│   │   └── index.md
│   ├── resume/
│   │   └── index.md
│   └── tags/
│       └── _index.md
├── themes/
│   └── basic/
│       ├── assets/
│       ├── content/
│       │   └── posts/
│       └── layouts/
├── hugo.yaml
├── mise.toml
├── README.md
├── GUIDE to Hugo.md
├── GUIDE to Hugo Templates.md
└── GUIDE to Hugo commands.md
```

## 4. Главная конфигурация: hugo.yaml

Текущий файл:

```yaml
baseURL: https://sock-puppet-dev.github.io/BWWH/
locale: ru-UA
title: BWWH
theme: basic

params:
  description: Учебный проект по Hugo, Go Templates, темам, шаблонам и деплою на GitHub Pages.
```

Пояснение:

- `baseURL` — адрес опубликованного сайта.
- `locale` — язык и регион сайта. В BWWH используется `ru-UA`, чтобы HTML и локализованные даты соответствовали русскоязычному учебному проекту.
- `title` — название сайта.
- `theme` — подключенная тема.
- `params.description` — общее описание сайта, доступное в шаблонах через `site.Params.description`.

Важно: в новых версиях Hugo используется `locale`. Старое поле `languageCode` считается устаревшим.

## 5. Меню сайта

Меню задано в `hugo.yaml`:

```yaml
menus:
  main:
    - name: Главная
      pageRef: /
      weight: 10
```

Пояснение:

- `menus.main` — главное меню.
- `name` — текст ссылки.
- `pageRef` — ссылка на страницу Hugo.
- `weight` — порядок пункта меню. Чем меньше число, тем раньше пункт.

Меню выводится partial-шаблоном:

```text
themes/basic/layouts/_partials/menu.html
```

## 6. Инструменты: mise.toml

Файл `mise.toml` управляет версиями инструментов:

```toml
[tools]
hugo = "latest"
node = "24"
python = "3.14"
```

Пояснение:

- `hugo = "latest"` — использовать последнюю доступную версию Hugo.
- `node = "24"` — использовать Node.js 24.
- `python = "3.14"` — использовать Python 3.14.

GitHub Actions тоже читает этот файл, поэтому локальная среда и деплой используют одну настройку инструментов.

## 7. Контент проекта

Основные страницы находятся в `content/`:

```text
content/_index.md
content/about/index.md
content/resume/index.md
content/contact/index.md
content/tags/_index.md
```

`content/_index.md` — главная страница.

`content/tags/_index.md` — страница списка тегов. Она нужна, чтобы заголовок `/tags/` был русским: `Теги`.

Файлы вида:

```text
content/about/index.md
```

называются leaf bundle. Это обычная страница, но в виде папки. Такой формат удобен, если рядом со страницей позже понадобятся картинки или другие файлы.

## 8. Front matter

Верхний блок Markdown-файла между `---` называется front matter.

Пример из текущего проекта:

```markdown
---
date: '2026-04-24T05:57:40+03:00'
title: 'О проекте'
description: 'Для чего нужен учебный Hugo-проект BWWH.'
draft: false
---
```

Пояснение:

- `date` — дата страницы.
- `title` — заголовок страницы.
- `description` — описание страницы для шаблонов и meta-тега.
- `draft: false` — страница опубликована.

Если поставить:

```yaml
draft: true
```

страница станет черновиком и может не попасть в обычную сборку.

## 9. Почему есть контент внутри темы

В проекте подключена локальная тема:

```yaml
theme: basic
```

У темы есть свои учебные посты:

```text
themes/basic/content/posts/post-1.md
themes/basic/content/posts/post-2.md
themes/basic/content/posts/post-3/index.md
```

Hugo публикует их вместе с основным контентом.

Это сделано специально, чтобы показать:

- как тема может содержать demo content;
- как работает `site.RegularPages`;
- как работают sections;
- как работают tags и taxonomy pages;
- как работает page bundle на примере `post-3/index.md`.

В production-проекте demo content обычно удаляют. В BWWH он оставлен как учебный материал.

## 10. Шаблоны проекта

Активные шаблоны находятся в теме:

```text
themes/basic/layouts/
```

Основные файлы:

```text
baseof.html      -> общий HTML-каркас
home.html        -> главная страница
page.html        -> обычная страница
section.html     -> страница раздела
taxonomy.html    -> список терминов таксономии
term.html        -> конкретный термин таксономии
```

Partials:

```text
_partials/head.html
_partials/header.html
_partials/footer.html
_partials/menu.html
_partials/terms.html
```

Partial — это маленький шаблон, который подключается внутрь другого шаблона.

## 11. Как Hugo выбирает шаблоны

Упрощенно для текущего проекта:

```text
/                         -> themes/basic/layouts/home.html
/about/                   -> themes/basic/layouts/page.html
/resume/                  -> themes/basic/layouts/page.html
/contact/                 -> themes/basic/layouts/page.html
/posts/                   -> themes/basic/layouts/section.html
/tags/                    -> themes/basic/layouts/taxonomy.html
/tags/red/                -> themes/basic/layouts/term.html
```

Общий каркас для всех страниц:

```text
themes/basic/layouts/baseof.html
```

## 12. Assets: CSS и JavaScript

Исходники assets:

```text
themes/basic/assets/css/main.css
themes/basic/assets/js/main.js
```

Подключение происходит через partials:

```text
themes/basic/layouts/_partials/head/css.html
themes/basic/layouts/_partials/head/js.html
```

Hugo обрабатывает эти файлы через Hugo Pipes:

- собирает CSS;
- собирает JS;
- в production добавляет fingerprint;
- подключает итоговые файлы в HTML.

## 13. Локальный запуск

```bash
hugo server -D
```

Обычно сайт открывается здесь:

```text
http://localhost:1313/
```

`-D` показывает draft-страницы.

## 14. Сборка

Обычная сборка:

```bash
hugo
```

Сборка как для GitHub Pages:

```bash
hugo --minify --baseURL "https://sock-puppet-dev.github.io/BWWH/"
```

Результат появляется в папке:

```text
public/
```

## 15. Деплой на GitHub Pages

Файл деплоя:

```text
.github/workflows/hugo.yml
```

Что делает workflow:

1. Запускается после `git push` в ветку `main`.
2. Забирает код из репозитория.
3. Устанавливает Node.js 24.
4. Устанавливает инструменты из `mise.toml`.
5. Показывает версии инструментов.
6. Настраивает GitHub Pages.
7. Запускает Hugo.
8. Загружает папку `public`.
9. Публикует сайт.

Ключевая команда сборки:

```bash
hugo --minify --baseURL "${{ steps.pages.outputs.base_url }}/"
```

GitHub сам подставляет правильный адрес Pages.

## 16. Что не нужно делать

Не редактируй вручную:

```text
public/
```

Не храни реальные секреты в:

```text
hugo.yaml
README.md
content/
```

Не запускай повторно без причины:

```bash
hugo new theme basic
```

Эта команда может перезаписать учебную тему.

## 17. Частые ошибки новичка

### Ошибка 1. Редактировать public

Правильно редактировать исходники:

```text
content/
themes/basic/layouts/
themes/basic/assets/
hugo.yaml
```

Потом запускать:

```bash
hugo
```

### Ошибка 2. Не понимать, откуда взялись посты

Посты берутся из:

```text
themes/basic/content/posts/
```

Это учебная особенность проекта.

### Ошибка 3. Ожидать, что меню строится само

В текущем проекте меню задано вручную в:

```text
hugo.yaml
```

### Ошибка 4. Путать локальный адрес и адрес GitHub Pages

Локальный адрес:

```text
http://localhost:1313/
```

Опубликованный адрес:

```text
https://sock-puppet-dev.github.io/BWWH/
```

## 18. Мини-план обучения

1. Измени текст в `content/_index.md`.
2. Запусти `hugo server -D`.
3. Посмотри, как меняется главная страница.
4. Открой `themes/basic/layouts/home.html`.
5. Найди `where site.RegularPages`.
6. Измени заголовок `Учебные посты`.
7. Собери сайт командой `hugo`.
8. Отправь изменения через Git.
9. Проверь GitHub Actions.
10. Открой опубликованный сайт.
