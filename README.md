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

Перевод выполняется прямо из браузера. Сервис выбирается переключателем:
- **Google** — `translate.googleapis.com`;
- **Microsoft** — бесплатный доступ, как в браузере Edge: токен с `edge.microsoft.com/translate/auth`,
  перевод через `api-edge.cognitive.microsofttranslator.com`.

Если выбранный сервис недоступен, перевод идёт через другой, последний резерв — MyMemory API. Длинные тексты (до 5000 символов) режутся на части.

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
add_header Content-Security-Policy "default-src 'self'; script-src 'self' 'unsafe-inline'; style-src 'self' 'unsafe-inline'; img-src 'self' data:; connect-src 'self' https://translate.googleapis.com https://edge.microsoft.com https://api-edge.cognitive.microsofttranslator.com https://api.mymemory.translated.net; media-src https://translate.google.com data:" always;
```

### Прокси для Microsoft

Браузер может не пустить прямые запросы к Microsoft (CORS). Тогда страница
обращается к `/ms/auth` и `/ms/translate` на своём же домене — добавьте в блок
`server { listen 443 ... }`:

```nginx
location = /ms/auth {
    proxy_pass https://edge.microsoft.com/translate/auth;
    proxy_ssl_server_name on;
    proxy_set_header Host edge.microsoft.com;
    proxy_set_header Origin "";
    proxy_set_header Referer "";
    proxy_set_header Cookie "";
    add_header Cache-Control "no-store" always;
}
location = /ms/translate {
    proxy_pass https://api-edge.cognitive.microsofttranslator.com/translate;
    proxy_ssl_server_name on;
    proxy_set_header Host api-edge.cognitive.microsofttranslator.com;
    proxy_set_header Origin "";
    proxy_set_header Referer "";
    proxy_set_header Cookie "";
    client_max_body_size 64k;
}
```
