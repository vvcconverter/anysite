# API встраивания VVC Player (вариант 4)

Плеер: **https://vvec-player.pages.dev/** — онлайн воспроизведение VVC и VVC-in-MP4 в браузере (canvas + WASM vvdec, не HTML5).

## Встраивание на любой сайт

### 1. Iframe

```html
<iframe id="vvcFrame" src="https://vvec-player.pages.dev/" allow="fullscreen"></iframe>
```

### 2. Управление через postMessage (разные домены)

Отправляй команды во фрейм с **вторым аргументом** `"https://vvec-player.pages.dev"` (origin плеера):

**Воспроизвести по ссылке:**
```javascript
document.getElementById("vvcFrame").contentWindow.postMessage(
  { type: "VVC_PLAYER_COMMAND", cmd: "play", url: "https://example.com/video.vvc" },
  "https://vvec-player.pages.dev"
);
```

**Пауза:**
```javascript
frame.contentWindow.postMessage(
  { type: "VVC_PLAYER_COMMAND", cmd: "pause" },
  "https://vvec-player.pages.dev"
);
```

**Стоп:**
```javascript
frame.contentWindow.postMessage(
  { type: "VVC_PLAYER_COMMAND", cmd: "stop" },
  "https://vvec-player.pages.dev"
);
```

**Повтор (repeat):** в команде `play` можно передать `repeat`: число повторений, `0` = бесконечно.

### 3. Прямой доступ (тот же origin)

Если страница открыта с того же домена, что и плеер, можно использовать `window.VVCPlayerAPI` внутри iframe:

- `VVCPlayerAPI.isReady` — готов ли плеер
- `VVCPlayerAPI.play(url, repeat)` — воспроизвести
- `VVCPlayerAPI.pause()` / `continue()` / `stop()`
- `VVCPlayerAPI.setBitstreams([url1, [url2, "Label"]])` — подставить список ссылок
- Событие `vvcplayerready` — плеер готов к командам

Формат видео: VVC (сырой bitstream) или VVC в контейнере MP4. Сервер должен отдавать CORS-заголовки, если домен другой.
