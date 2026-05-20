+++
title = 'Учебный пример: Пост 3'
date = 2023-03-15T11:00:00-07:00
draft = false
tags = ['красный','зеленый','синий']
+++

Это учебный пост для проверки page bundle.

Файл поста находится здесь:

```text
themes/basic/content/posts/post-3/index.md
```

Рядом с ним лежит изображение:

```text
themes/basic/content/posts/post-3/bryce-canyon.jpg
```

Такой формат удобен, когда странице нужны собственные файлы: картинки, документы или другие ресурсы.

![Национальный парк Bryce Canyon](bryce-canyon.jpg)

Markdown-ссылка на изображение использует относительный путь `bryce-canyon.jpg`, потому что картинка лежит рядом с `index.md` внутри одного page bundle.
