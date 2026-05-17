# BWWH

Учебный проект по Hugo и Hugo Go Template Expressions.

Опубликованный сайт:

```text
https://sock-puppet-dev.github.io/BWWH/
```

## Что внутри

- `GUIDE to Hugo.md` — шпаргалка по Hugo, структуре проекта, командам и деплою.
- `GUIDE to Hugo Templates.md` — шпаргалка по Hugo templates, context `.`, `if`, `with`, `range`, pipes и функциям.
- `content/` — Markdown-страницы сайта.
- `themes/basic/layouts/` — активные HTML-шаблоны темы Hugo.
- `themes/basic/content/posts/` — учебные demo posts из темы, которые Hugo тоже публикует.
- `.github/workflows/hugo.yml` — автоматический деплой на GitHub Pages.

## Учебная цель

Проект специально оставляет тему `basic` внутри репозитория, чтобы можно было изучать:

- как Hugo подключает тему через `theme: basic`;
- как работают `layouts`, `partials`, assets и меню;
- почему контент из `themes/basic/content/` тоже попадает в итоговый сайт;
- как GitHub Actions собирает Hugo и публикует `public` на GitHub Pages.

## Локальный запуск

```bash
hugo server -D
```

После запуска сайт обычно доступен по адресу:

```text
http://localhost:1313/
```

## Сборка

```bash
hugo
```

Результат сборки появляется в папке `public`.
