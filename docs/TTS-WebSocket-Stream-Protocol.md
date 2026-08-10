# TTS WebSocket Streaming Protocol

**tf-assistant** exposes a WebSocket streaming endpoint that lets external
clients synthesize speech from text in real time with the local **Audio8-TTS**
model. Text is submitted incrementally (per chunk / paragraph), and the audio
segments are streamed back over the same connection so the client can start
playing before synthesis finishes. The client owns playback.

- Third-party demo using this protocol: <https://github.com/jason-ni/mdclipper>

---

## Endpoint

| | |
|---|---|
| Path | `GET /ws/tts` |
| Default URL | `ws://127.0.0.1:8080/ws/tts` |
| Enable | Settings → HTTP → **TTS streaming WebSocket** (`http.server.tts_ws_enabled`) |
| Auth | Requires the machine to be registered (same guard as `/translate`). No token header — the server checks the local app's registration state. |

## Audio format

- **Mono**, sample rate **44 100 Hz** (advertised by the server in `hello.sampleRate`)
- **Raw little-endian IEEE f32 PCM** (`pcm_f32`), 4 bytes per sample
- Each `segment` frame carries one base64-encoded PCM chunk in `data_b64`

## Connection lifecycle

1. Client opens the WebSocket.
2. Server replies with `hello` (voices + audio format) or `error` (disabled / no model / unregistered).
3. Client sends `SpeechTextChunk` messages. The server synthesizes them one at a time (queuing any that arrive while one is in flight) and streams `chunkBegin` → `segment`… → `chunkEnd` back.
4. Client may send `CancelChunk` (interrupt one chunk), `ListVoices`, or `Stop` at any time.
5. `Stop` or a disconnect ends the session and closes the connection.

One connection = one long-lived streaming session that occupies the TTS worker
until the connection closes.

---

## Server → client frames

### `hello` — sent immediately after connect

```json
{
  "type": "hello",
  "voices": ["en_man", "en_woman", "zh_man"],
  "sampleRate": 44100,
  "format": "pcm_f32"
}
```

- `voices`: voice ids available for this install.
- `sampleRate` / `format`: audio contract for every `segment` frame.

### `chunkBegin` — a chunk started synthesizing

```json
{
  "type": "chunkBegin",
  "chunk_id": "c1",
  "clock": 1,
  "segments": 3
}
```

- `segments`: how many `segment` frames this chunk will produce.

### `segment` — one synthesized audio piece (playable immediately)

```json
{
  "type": "segment",
  "chunk_id": "c1",
  "clock": 1,
  "index": 0,
  "total": 3,
  "text": "The synthesized text of this segment.",
  "sample_rate": 44100,
  "format": "pcm_f32",
  "data_b64": "<base64-encoded f32 PCM>"
}
```

- `index` / `total`: ordinal of this segment within the chunk (0-based).
- `text`: the exact text this segment speaks — useful for captions/highlighting.
- `data_b64`: the audio payload. Decode as little-endian `f32` at `sample_rate`
  (fall back to `hello.sampleRate` when absent).
- `sample_rate` / `format`: per-segment contract (currently always 44.1 kHz
  mono `pcm_f32`).

### `chunkEnd` — a chunk finished

```json
{
  "type": "chunkEnd",
  "chunk_id": "c1",
  "clock": 1
}
```

### `voices` — reply to `ListVoices`

```json
{
  "type": "voices",
  "voices": ["en_man", "en_woman", "zh_man"]
}
```

### `error` — something went wrong

```json
{
  "type": "error",
  "message": "engine channel closed",
  "chunk_id": "c1",
  "clock": 1
}
```

`chunk_id` / `clock` are present when the error relates to a submitted chunk.

### `stream_ended` — the streaming session terminated

```json
{ "type": "stream_ended" }
```

Emitted when the underlying worker session ends (e.g. worker shutdown). It is
**not** guaranteed after a clean `Stop` (the connection is closed first).

---

## Client → server frames

### `SpeechTextChunk` — synthesize a paragraph

```json
{
  "type": "SpeechTextChunk",
  "chunk_id": "c1",
  "clock": 1,
  "voice": "en_man",
  "text": "Hello, world.",
  "temperature": 0.7,
  "maxTokens": 256,
  "seed": 42
}
```

| Field | Type | Required | Notes |
|---|---|---|---|
| `chunk_id` | string | yes | Client-chosen identifier, echoed in every stream frame of this chunk. Must be non-empty. |
| `clock` | integer | yes | Per-connection **logical clock**; must be **strictly increasing** across `SpeechTextChunk` messages. |
| `voice` | string | yes | A voice id from `hello.voices`. |
| `text` | string | yes | Text to synthesize. Must be non-empty. |
| `temperature` | float | no | Sampling temperature. |
| `maxTokens` | integer | no | Max tokens per synthesized segment. |
| `seed` | integer | no | Reproducibility seed. |

### `CancelChunk` — interrupt one chunk

```json
{
  "type": "CancelChunk",
  "chunk_id": "c1",
  "clock": 1
}
```

- `clock` references the chunk's **originating clock** (the clock of the
  `SpeechTextChunk` that submitted it), not a new message clock. It must be
  `<=` the last submitted chunk clock.
- Stops that chunk's remaining synthesis and removes it from the queue.
  Chunks submitted before/after it are unaffected.

### `ListVoices` — re-query available voices

```json
{ "type": "ListVoices" }
```

### `Stop` — end the session gracefully

```json
{ "type": "Stop" }
```

---

## Clock & staleness semantics

- `clock` is a per-connection monotonic logical clock, advanced only by
  `SpeechTextChunk` (strictly increasing). Duplicate or lower clocks are
  rejected with an `error`.
- A chunk is identified by its **originating clock**; every `segment` /
  `chunkEnd` frame echoes that clock.
- After a `CancelChunk`, any segment still carrying the cancelled clock is
  **stale** and must be dropped by the client before playback. Cancelling chunk
  A never invalidates queued chunk B — this is what makes cancellation
  selective.

## Concurrency & ordering

- Chunks are synthesized in submission order, one at a time; anything submitted
  while a chunk is in flight is queued. Play `segment` frames in arrival order.
- Interleaving is not possible on one connection: frames for chunk N+1 never
  appear before `chunkEnd` for chunk N.

## Reference implementations

- Python reference client and mock server live in the **tf-assistant** repo:
  - `tools/tts_ws_client.py` — interactive client (live playback via `sounddevice`)
  - `tools/mock_tts_ws_server.py` — mock server for offline testing
- Third-party demo project using this protocol:
  <https://github.com/jason-ni/mdclipper>
