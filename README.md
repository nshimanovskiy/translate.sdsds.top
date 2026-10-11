# translate.sdsds.top

Бесплатный онлайн-переводчик текста — статическая страница без сервера и сборки.

- 100+ языков, автоопределение исходного языка
- автоперевод при вводе (можно выключить), `Ctrl+Enter` — перевести, `Ctrl+Shift+S` — поменять языки
- озвучка голосом Google (`translate.google.com/translate_tts`) частями без пауз (следующая часть грузится заранее),
  запасной вариант — голоса браузера (Web Speech API)
- копирование, вставка из буфера
- ссылка на перевод (`#sl=en&tl=ru&q=...`)
- история последних 20 переводов в `localStorage`
- светлая/тёмная тема, оформление в стиле share.sdsds.top

Перевод выполняется прямо из браузера через `translate.googleapis.com`
(резервный сервис — MyMemory API). Длинные тексты (до 5000 символов) режутся на части.

Если JavaScript выключен или запросы к `translate.googleapis.com` блокируются
(NoScript, uBlock Origin и т. п.), поверх страницы показывается плашка с просьбой
разрешить `sdsds.top` и `translate.googleapis.com`.

## Деплой

Достаточно отдать `index.html` любым статическим хостингом.
Для GitHub Pages файл `CNAME` уже указывает на `translate.sdsds.top` —
включите Pages в настройках репозитория и добавьте DNS-запись
`CNAME translate → <user>.github.io`.

Если nginx отдаёт заголовок `Content-Security-Policy`, в нём нужно разрешить:

```nginx
add_header Content-Security-Policy "default-src 'self'; script-src 'self' 'unsafe-inline'; style-src 'self' 'unsafe-inline'; img-src 'self' data:; connect-src 'self' https://translate.googleapis.com https://api.mymemory.translated.net; media-src https://translate.google.com data:" always;
```

