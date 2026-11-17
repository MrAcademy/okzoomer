# OK Zoomer

A single-page viewer for the Kitten Academy live stream. The cameras are laid out
in four quadrants; the buttons zoom the embedded YouTube player so one quadrant
fills the window.

Hosted on GitHub Pages from this repo (`okzoomer.html`).

## How the stream is chosen

1. **Saved link** – if the viewer has saved a link via the ⚙ settings button, that
   video is played. It is stored in the viewer's browser (`localStorage`, key
   `okzoomer.customVideoId`) and nowhere else. "Use automatic" clears it.
2. **Automatic (default)** – embeds `youtube.com/embed/live_stream?channel=<CHANNEL_ID>`,
   which YouTube resolves to whatever the channel is currently live-streaming. So when
   YouTube closes the stream and a new one starts, **no code change is needed**.
3. If nothing is live (or the player errors / the stream ends), a friendly
   "stream appears to be missing" message is shown and the page re-checks every
   60 seconds. If a *saved* link stops working, the message offers to switch back
   to automatic instead.

The channel ID and timings are constants at the top of the `<script>` block.

## Fullscreen

The ⛶ button fullscreens the whole app, so the quadrant buttons and zoom keep
working. (YouTube's own fullscreen button only enlarges the raw video and loses the
zoom.) It hides itself where fullscreen isn't available, e.g. iPhone Safari or when
framed without `allow="fullscreen"`.

## Embedding (kitten.academy)

`okzoomer.html?embed=1` switches to a light, compact theme that matches the
Squarespace site. Paste this into a Squarespace **Embed block → Code Snippet**:

```html
<div style="position:relative;width:100%;padding-top:calc(56.25% + 40px);">
  <iframe
    src="https://mracademy.github.io/okzoomer/okzoomer.html?embed=1"
    title="Kitten Academy Live Stream"
    style="position:absolute;inset:0;width:100%;height:100%;border:0;"
    allow="autoplay; encrypted-media; fullscreen; picture-in-picture"
    allowfullscreen></iframe>
</div>
```

The `+ 40px` is the button bar; on phones the buttons wrap to two rows, leaving thin
black bars beside the video. The `allow` attribute is required for autoplay and the
fullscreen button. A saved stream link is stored separately when embedded (browsers
partition storage for framed sites).

## Run locally

The YouTube IFrame API wants a real http origin, so don't open the file directly:

    python3 -m http.server 8000
    # then open http://localhost:8000/okzoomer.html

## Deploy

    git push

GitHub Pages republishes automatically within a minute or two.

## Gotchas

- When nothing is live, the channel embed reports the placeholder video id
  `live_stream`; the page treats that as "missing".
- If the YouTube IFrame API script is blocked (some privacy extensions), the page
  falls back to a plain embed without the missing-stream detection.
- Zoom offsets are pixel-based and are re-applied on window resize.
- If the channel runs a second ("special") stream at the same time, YouTube's
  channel embed picks one of them; we don't control which. Viewers can paste a
  specific link into ⚙ settings if needed. (Deliberately kept simple; doing this
  properly would need a server-side job listing live streams via the YouTube API.)
