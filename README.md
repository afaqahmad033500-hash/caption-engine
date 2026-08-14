# Pronunciation Caption Engine

A self-contained, single-file caption generator for TikTok/Shorts/Reels-style
pronunciation-teaching captions (IPA + word, cinematic stacked-line layout).

**Live demo (after enabling Pages):** `https://<your-username>.github.io/<repo-name>/`

## Usage

1. Open `index.html` (locally or via GitHub Pages).
2. Tap the **＋** button (or press `T`) and paste your transcript as
   `word /ipa/ word /ipa/ ...` — line breaks don't matter.
3. Click **Generate Captions**. The engine will:
   - auto-chunk into 5–9 word captions at punctuation boundaries
   - classify each word small / medium / large (stopwords → small,
     stress marks / syllable count / flapping / hard consonants → large)
   - assign a per-chunk autoplay duration
   - render it through the fixed "master style" renderer
4. Preview with Play / Next / Prev, adjust the speed and font-size sliders,
   toggle IPA visibility.
5. Click **Export JSON** to download the generated `captions` array if you
   want to hand-tune it — reopen the pencil (✎) icon / press `E` to paste
   edited JSON back in.
6. Press `F` (or the ⛶ button) for distraction-free fullscreen recording mode.

## Deploying to GitHub Pages

1. Push this repo to GitHub.
2. Settings → Pages → Source: **Deploy from a branch**, branch `main`, folder `/ (root)`.
3. Your engine will be live at `https://<your-username>.github.io/<repo-name>/`.

## Architecture

Everything lives in `index.html`:

- **Renderer** (`buildLinesStructural`, `buildCompositionDOM`, `fitComposition`,
  `goTo`) — the fixed visual "master style." Do not edit this to add features;
  it should stay identical across projects.
- **Transcript parser** (`parseTranscript`, `chunkWords`, `classifyChunk`,
  `buildCaptionsFromWords`) — the only part that changes per project. Takes
  `word /ipa/` pairs and produces the `captions` data array the renderer consumes.
- **Data format**:
  ```json
  [
    { "duration": 2400, "words": [
      { "text": "Tonight", "ipa": "/təˈnaɪt/", "size": "large" }
    ]}
  ]
  ```

Because the renderer only ever reads the `captions` array, you can plug in
other sources later (SRT import, automatic transcription, audio sync) without
touching the visual code — just produce the same array shape.

## How caption length is decided (dynamic, not fixed)

There is no fixed word-count target. Each word is first classified on its
own merits (stopword → small; syllable count, stress, length, flapping,
hard consonants → medium or large) — independent of whatever other words
end up nearby, so the same word always renders the same size no matter
which caption it lands in.

Compositions are then built one word at a time: each candidate addition is
actually rendered off-screen through the real fitting pipeline, and only
kept if the composition still fits at (essentially) full, consistent scale.
The moment the next word would require real shrinking — or would push a
composition past three "large" pronunciation-focus words — that word
starts the next caption instead. Word count is a *result* of what
comfortably fits, never an input: a caption might end up with 3 words or
9, whichever keeps the typography consistent across the whole speech. A
small (0.85) safety floor still exists purely as a last resort for
otherwise-unavoidable edge cases (e.g. one extremely long word), not as
the primary sizing mechanism.

## Recording a caption sequence

1. Paste your transcript and click **Generate Captions** (or load a JSON set).
2. Click **⏺ Start Recording** in the bottom control panel.
3. Your browser will show its native screen/tab-share picker — choose **This Tab**
   (Chrome shows this as a one-click option; the app also passes a hint to
   preselect it). This is the only manual step; everything else is automatic.
4. The app switches to presentation mode (no controls visible, just the caption)
   and auto-plays the full sequence from the start.
5. When the sequence finishes (or you click **⏹ Stop Recording** early), a
   `.webm` file downloads automatically.

**Why tab capture instead of a canvas recorder:** capturing the literal
rendered tab guarantees pixel-accurate fonts, letter-spacing, and CSS
transition timing — a canvas/`html2canvas`-style per-frame redraw cannot
reliably reproduce those. The trade-off is a one-time native permission
prompt each time you record.

**Format:** WebM only. MP4 cannot be encoded reliably in-browser without a
server-side transcoder, so this intentionally does not claim to produce MP4.
WebM plays natively in Chrome/Firefox/most editors, and converts to MP4
easily via `ffmpeg -i in.webm out.mp4` or any online converter if needed.

## Keyboard shortcuts

| Key | Action |
|---|---|
| Space / ↓ | Next caption |
| ↑ | Previous caption |
| Home / End | First / last caption |
| F | Presentation (fullscreen recording) mode |
| Esc | Exit presentation mode |
| E | Open JSON edit mode |
| T | Open transcript import |

## License

Use freely for your own projects.
