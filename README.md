# BWWH

BWWH — учебный проект по Hugo и Hugo Go Template Expressions.

Цель проекта — на практике разобрать, как Hugo собирает статический сайт из конфигурации, Markdown-контента, темы, шаблонов, partials, assets и GitHub Actions.

## 1. Опубликованный сайт

```text
https://sock-puppet-dev.github.io/BWWH/
```

## 2. Основные учебные материалы

- `GUIDE to Hugo.md` — общий гайд по Hugo и текущей структуре проекта.
- `GUIDE to Hugo Templates.md` — гайд по Hugo Go Template Expressions.
- `GUIDE to Hugo commands.md` — краткий справочник команд.

## 3. Текущая структура проекта

```text
BWWH/
├── .github/workflows/hugo.yml
├── archetypes/default.md
├── content/
│   ├── _index.md
│   ├── about/index.md
│   ├── contact/index.md
│   ├── resume/index.md
│   └── tags/_index.md
├── themes/basic/
│   ├── content/posts/
│   ├── layouts/
│   └── assets/
├── hugo.yaml
├── mise.toml
├── README.md
├── GUIDE to Hugo.md
├── GUIDE to Hugo Templates.md
└── GUIDE to Hugo commands.md
```

## 4. Важные файлы

- `hugo.yaml` — главная конфигурация сайта.
- `mise.toml` — версии инструментов для локальной разработки и GitHub Actions.
- `content/` — основные страницы проекта.
- `themes/basic/layouts/` — активные HTML-шаблоны темы.
- `themes/basic/content/posts/` — учебные посты темы.
- `.github/workflows/hugo.yml` — автоматическая сборка и деплой на GitHub Pages.
- `public/` — результат сборки. Эту папку не нужно редактировать вручную.

## 5. Локальный запуск

```bash
hugo server -D
```

После запуска сайт обычно доступен по адресу:

```text
http://localhost:1313/
```

## 6. Сборка

```bash
hugo --minify --baseURL "https://sock-puppet-dev.github.io/BWWH/"
```

Результат сборки появляется в папке `public`.

## 7. Деплой

Деплой выполняется автоматически через GitHub Actions.

Цепочка такая:

1. Изменения отправляются в ветку `main`.
2. GitHub запускает `.github/workflows/hugo.yml`.
3. `mise` устанавливает Hugo, Node.js и Python из `mise.toml`.
4. GitHub Actions запускает `hugo --minify`.
5. Папка `public` публикуется на GitHub Pages.

## 8. Учебная особенность проекта

В проекте специально используется локальная тема `basic`.

Это сделано, чтобы изучить:

- как Hugo подключает тему через `theme: basic`;
- как работают `baseof.html`, `home.html`, `page.html`, `section.html`, `taxonomy.html`;
- как работают partials;
- как Hugo собирает контент не только из `content/`, но и из `themes/basic/content/`;
- как меню задается в `hugo.yaml`;
- как GitHub Pages получает готовую папку `public`.
