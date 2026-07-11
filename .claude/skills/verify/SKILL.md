# Verify — Mound Metrics AI

Single-page static app: everything lives in `index.html` (CSS + two inline
scripts). No build step, no tests.

## Launch

```bash
python3 -m http.server 8901          # serve the repo root
# drive with Playwright (global install): NODE_PATH=/opt/node22/lib/node_modules node <script>
# chromium is preinstalled; PLAYWRIGHT_BROWSERS_PATH is already set
```

## Gotcha: MediaPipe CDN is blocked in remote sessions

The app loads `@mediapipe/pose@0.5` from cdn.jsdelivr.net, which the sandbox
network policy denies (403 on CONNECT). registry.npmjs.org IS allowed, so:

```bash
npm pack @mediapipe/pose@0.5 && mkdir -p mp-pose && tar xzf mediapipe-pose-*.tgz -C mp-pose --strip-components=1
```

then serve the files with a Playwright route:

```js
await page.route('**cdn.jsdelivr.net/npm/@mediapipe/pose@0.5/**', route => {
  const f = path.join(MP_DIR, path.basename(new URL(route.request().url()).pathname));
  fs.existsSync(f) ? route.fulfill({ body: fs.readFileSync(f),
    contentType: f.endsWith('.js') ? 'text/javascript' : f.endsWith('.wasm') ? 'application/wasm' : 'application/octet-stream' })
    : route.abort();
});
```

Pose detection runs on WASM/SwiftShader headlessly — expect ~1.5s per
detect() call, so full flows take tens of seconds; use generous timeouts.

## Feeding a video without fixture files

No ffmpeg with lavfi available. Generate a clip in-page instead:
canvas.captureStream(30) + MediaRecorder ('video/webm;codecs=vp8'), then
wrap the blob in a File, assign via DataTransfer to `#videoInput`, and
dispatch a bubbling `change` event. Draw a filled humanoid (head circle,
torso rect, limb strokes on a green background) if you need the pose model
to actually detect something; a plain rectangle reliably does NOT detect,
which is handy for exercising fallback paths.

## Flows worth driving

- Upload → auto-framing pill (`#frameStatus`) shows in the upload zone,
  then hides; canvas (`#canvas`) width/height reflect the chosen crop.
- `#beginBtn` → processing panel → workspace (`#workspacePanel` loses
  `.hidden`); dismiss `#introScrim` via `#introStartBtn`.
- `page.evaluate` strings are parsed as expressions — wrap multi-statement
  payloads in `(async () => { ... })()`.
- `.hidden` uses `display:none !important`; wait with waitForFunction on
  classList, not waitForSelector visibility.
