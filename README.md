# WebGPU Player passes

The pass projects of [WebGPU Player](https://github.com/alchemyyy/WebGPU-Player): optional WGSL passes a host may offer its users, who add them to their presentation preset.
Each folder but `licenses/` is one project, and each WGSL file at a project's top level is one of its pass files, in the engine's pass file format, under a file name no other project uses.
`licenses/` holds the license texts of the upstream shaders the Anime4K and NIS passes come from, and no WGSL file.

| Project | Pass files | What it does |
| --- | --- | --- |
| `sharpen` | `sharpen.wgsl` | A 3x3 kernel that moves each pixel away from the mean of its 8 neighbors |
| `saturation` | `saturation.wgsl` | Scales chroma around BT.709 luma |
| `temporal-blend` | `temporal-blend.wgsl` | Blends toward the previous frame while history is valid |
| `smooth-motion` | `smooth-motion.wgsl` | Draws at the display rate and blends the two frames a refresh spans by the share each covers |
| `catmull-rom` | `catmull-rom.wgsl` | A 4x4 Catmull-Rom scaler in place of the built-in bilinear one |
| `anime4k` | `anime4k-l.wgsl`, `anime4k-ul.wgsl` | Anime4K v3.2's Upscale+Denoise CNN x2 L and UL networks, generated from the upstream mpv shaders in `anime4k/glsl/` |
| `nis` | `NVScaler.wgsl`, `NVSharpen.wgsl` | NVIDIA Image Scaling v1.0.2, converted from agyild's mpv port in `nis/glsl/` |

The engine checks this repository out as its `wgpup/` submodule.
Its book describes the pass file format, these projects, and how their generated files are made, in `docs/src/new-pass.md`, `docs/src/bundled-passes.md`, and `docs/src/mpv-shaders.md` of the engine repository.
The generated pass files are committed, so offering them needs no Python; the generators run from the engine root, and the NIS generator imports the engine's `tools/convert_mpv_shader.py`.
The engine's tests check every pass file against its pass rules, and the upstream sources against the SHA-256 their `PROVENANCE.txt` lists.
From the engine root, `npm run passes` copies this checkout's pass files into `bin/wgpup/`, where a host serves them and the sandbox lists them.

## Releases

`.github/workflows/release.yml` publishes every pass file, flat, as a GitHub release.
Run it by hand on `main` (Actions > Release > Run workflow) with a version such as `0.2.0`, which becomes the tag `v0.2.0`.
Without a version it is a dry run, which keeps the release files as the run's artifact.

A release carries every pass file, the license texts in `licenses/`, and `SHA256SUMS`, the SHA-256 of every file.
The engine pins one release, file by file, in its `tools/pinned-releases.json`, and its `build.sh` and `sandbox.sh` install the pinned pass files into `bin/wgpup/`.
`node scripts/pinned-releases.mjs --pin passes <tag>` moves the pin to another release.

## License

MIT; see [LICENSE](LICENSE).
The Anime4K passes come from Anime4K's shaders, MIT, Copyright (c) 2019 bloc97 (`licenses/Anime4K-LICENSE`), and the NIS passes from agyild's mpv port of NVIDIA Image Scaling, MIT, Copyright (c) 2022 NVIDIA CORPORATION & AFFILIATES (`licenses/NIS-LICENSE`).
