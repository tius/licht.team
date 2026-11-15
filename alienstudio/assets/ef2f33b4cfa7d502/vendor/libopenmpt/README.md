# libopenmpt browser decoder

Unmodified WebAssembly distribution of libopenmpt 0.8.10+release.

- source: <https://lib.openmpt.org/libopenmpt/download/>
- archive: <https://lib.openmpt.org/files/libopenmpt/dev/libopenmpt-0.8.10%2Brelease.dev.js.tar.gz>
- archive SHA-256: `71f930d56141cef1b45d4ecee423617a5daaf9068bb5e2bddd1ce4c3044071a9`
- extracted runtime: `bin/wasm/libopenmpt.js` and `bin/wasm/libopenmpt.wasm`
- upstream license and bundled component licenses: `license.txt`, `licenses/`

No external requests occur during playback. Serve the WASM file with
`application/wasm`. The wrapper loads this classic-script build inside a
dedicated worker and uses its heap views and function-table access to invoke
the documented C interactive interface. The 32-bit interface structure has
16 function pointers; channel mute is pointer 10. Keep this ABI adapter and
the browser decoder checks together when upgrading the pinned distribution.

The worker renders a stereo mix and four isolated-channel passes at 22050 Hz.
It records a common-scale RMS/peak envelope every 220 samples (about 10 ms).
It retains no individual-channel PCM after analysis. Input is bounded to
4 MiB and rendered audio to 120 seconds. Each request has a 30-second worker
timeout; a newer request cancels the previous worker.

Timing metadata starts at the default 125 BPM / speed 6 with first-row Fxx
commands applied. To catch timing changes beyond the decoded window, the
adapter also scans Fxx commands in every pattern referenced by the active
order list. This deliberately conservative check includes rows skipped by
jumps. It does not change the module's playback timing.

`mod-fixture.js` synthesizes original test notes and samples. No music assets
from the upstream distribution are bundled.
