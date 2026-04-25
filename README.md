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
- `layouts/` — HTML-шаблоны Hugo.
- `.github/workflows/hugo.yml` — автоматический деплой на GitHub Pages.

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
