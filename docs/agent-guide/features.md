# LightCraft — funcionalidades e módulos

Última atualização: 2026-10-28.

## Fluxo principal

| Etapa | Entradas | Responsabilidade e dependências | Saídas |
|---|---|---|---|
| Abrir e editar | Fotos RAW/raster, metadados e ajustes | [raw](../../crates/raw/), [codecs](../../crates/codecs/), [meta](../../crates/meta/), [develop](../../crates/develop/), [catalog](../../crates/catalog/), [engine](../../crates/engine/) | Catálogo e parâmetros de revelação, preservando originais |
| Avaliar e produzir saída | Estado editado e parâmetros de saída | [pipeline](../../crates/pipeline/), [gpu](../../crates/gpu/), [preview](../../crates/preview/) | Previews e imagens exportadas |
| Acesso pelo usuário ou agente | Gestos, argumentos CLI ou chamadas estruturadas | [apps](../../apps/), [engine](../../crates/engine/) e interfaces descritas abaixo | Estado atualizado, resposta ou erro; comportamento detalhado nos contratos |

A tabela mostra o caminho conceitual, não uma garantia de suporte a todos os formatos. APIs, estruturas, unidades, efeitos colaterais e limites ficam na [arquitetura](../architecture.md), nos documentos de assunto e nos módulos indicados.

## Contratos e exemplos de uso

- [raw-parity.md](../raw-parity.md)
- [process-versions.md](../process-versions.md)
- [xmp-interop.md](../xmp-interop.md)
- [gpu-pipeline.md](../gpu-pipeline.md)
- [background-tasks.md](../background-tasks.md)
- [control-protocol.md](../control-protocol.md)
- [mcp.md](../mcp.md)

## Inventário de bibliotecas

Responsabilidades resumidas a partir dos manifestos e comentários de módulo do checkout. As dependências listadas são as internas diretas declaradas em `dependencies` (incluindo condicionais); não incluem dependências de teste/build nem expansão de features. O manifesto de cada linha é a fonte exata.

| Módulo / manifesto | Responsabilidade declarada | Dependências internas diretas |
|---|---|---|
| [catalog](../../crates/catalog/Cargo.toml) | LightCraft library: photos, albums, ratings/flags, search/filter/sort, op-log persistence (L3) | `lightcraft-develop`, `lightcraft-meta`, `lightcraft-raw` |
| [codecs](../../crates/codecs/Cargo.toml) | LightCraft standard image codecs: sniffing, decode to linear light, ICC, encoders (L1) | `lightcraft-color`, `lightcraft-geom`, `lightcraft-heif`, `lightcraft-raster` |
| [color](../../crates/color/Cargo.toml) | LightCraft color (L0) | `lightcraft-geom` |
| [denoise](../../crates/denoise/Cargo.toml) | LightCraft AI denoise (L1): denoise-model manifests, Bayer packing, tiling and blending, the cache file, and pure-Rust CPU inference | `lightcraft-denoise-core`, `lightcraft-raster` |
| [denoise-core](../../crates/denoise-core/Cargo.toml) | LightCraft denoise model metadata, Bayer tiling and cache formats (L1; no inference backend) | `lightcraft-raster` |
| [develop](../../crates/develop/Cargo.toml) | LightCraft develop settings: the non-destructive edit model, control specs, presets (L1) | `lightcraft-color`, `lightcraft-geom` |
| [engine](../../crates/engine/Cargo.toml) | LightCraft engine: session, command registry, history, render jobs, view-models (L4) | `lightcraft-catalog`, `lightcraft-codecs`, `lightcraft-color`, `lightcraft-denoise`, `lightcraft-denoise-core`, `lightcraft-develop`, `lightcraft-faces`, `lightcraft-fetch`, `lightcraft-geocode`, `lightcraft-geom`, `lightcraft-gpu`, `lightcraft-lensdb`, `lightcraft-merge`, `lightcraft-meta`, `lightcraft-pipeline`, `lightcraft-preview`, `lightcraft-raster`, `lightcraft-raw`, `lightcraft-scenes`, `lightcraft-segment`, `lightcraft-slideshow` |
| [faces](../../crates/faces/Cargo.toml) | LightCraft faces (L1): bounded ONNX CPU inference, face-model manifests, validation and detection | Nenhuma declarada nesta categoria |
| [fetch](../../crates/fetch/Cargo.toml) | LightCraft model downloads: mirrors, resume, pinned SHA-256, over HTTPS in pure Rust (L0) | Nenhuma declarada nesta categoria |
| [geocode](../../crates/geocode/Cargo.toml) | LightCraft offline reverse geocoding: GPS position to sublocation / city / state / country from a GeoNames dump (L1) | Nenhuma declarada nesta categoria |
| [geom](../../crates/geom/Cargo.toml) | LightCraft geom (L0) | Nenhuma declarada nesta categoria |
| [gpu](../../crates/gpu/Cargo.toml) | LightCraft GPU develop pipeline: wgpu compute (WGSL) twin of the CPU pipeline, which stays the reference (L3) | `lightcraft-color`, `lightcraft-denoise`, `lightcraft-develop`, `lightcraft-faces`, `lightcraft-geom`, `lightcraft-pipeline`, `lightcraft-raster` |
| [heif](../../crates/heif/Cargo.toml) | Optional HEIF/HEIC still-image decoder for LightCraft (heic-rs for the container and HEVC, libheif-matching colour conversion, panic-guarded) | Nenhuma declarada nesta categoria |
| [inpaint](../../crates/inpaint/Cargo.toml) | Content-aware hole filling: PatchMatch-based patch completion and membrane fill (L1). Ported from PhotoCraft | Nenhuma declarada nesta categoria |
| [lensdb](../../crates/lensdb/Cargo.toml) | Lens correction profiles: distortion, TCA and vignetting models, lensfun-format database loader (L2) | `lightcraft-develop`, `lightcraft-geom` |
| [mcp](../../crates/mcp/Cargo.toml) | LightCraft MCP server (L5): JSON-RPC 2.0 over stdio, driving a running app or a headless session | `lightcraft-codecs`, `lightcraft-engine`, `lightcraft-raster` |
| [merge](../../crates/merge/Cargo.toml) | LightCraft Photo Merge: HDR merge (align, deghost) and panorama stitching (L3) | `lightcraft-codecs`, `lightcraft-color`, `lightcraft-geom`, `lightcraft-meta`, `lightcraft-raster`, `lightcraft-raw`, `lightcraft-scenes` |
| [meta](../../crates/meta/Cargo.toml) | LightCraft metadata: EXIF, XMP, IPTC (L1) | `lightcraft-geom`, `lightcraft-tiff` |
| [pipeline](../../crates/pipeline/Cargo.toml) | LightCraft develop pipeline: scene-referred, float, resolution independent (CPU reference) (L2) | `lightcraft-color`, `lightcraft-develop`, `lightcraft-geom`, `lightcraft-inpaint`, `lightcraft-raster`, `lightcraft-raw` |
| [preview](../../crates/preview/Cargo.toml) | LightCraft preview cache: content hashing, memory LRU, disk thumbnail cache, prioritized job pool (L3) | `lightcraft-raster` |
| [raster](../../crates/raster/Cargo.toml) | LightCraft raster (L0) | `lightcraft-color`, `lightcraft-geom` |
| [raw](../../crates/raw/Cargo.toml) | LightCraft RAW decoding, demosaicing and DNG colour model (L1) | `lightcraft-color`, `lightcraft-geom`, `lightcraft-meta`, `lightcraft-raster`, `lightcraft-tiff` |
| [scenes](../../crates/scenes/Cargo.toml) | Procedurally generated photographic demo scenes (HDR, linear Rec.2020) for LightCraft demos and tests (L1) | `lightcraft-color`, `lightcraft-raster` |
| [segment](../../crates/segment/Cargo.toml) | LightCraft segmentation: SAM 3 (Segment Anything with Concepts) inference in pure Rust (L3) | `lightcraft-fetch` |
| [slideshow](../../crates/slideshow/Cargo.toml) | LightCraft slideshow slides: layout, options and backdrop settings and the CPU slide composer (L1) | `lightcraft-raster` |
| [sysmem](../../crates/sysmem/Cargo.toml) | LightCraft sysmem (L0): returns freed allocator memory to the OS. The workspace's only crate allowed `unsafe` (one FFI call). | Nenhuma declarada nesta categoria |
| [testkit](../../crates/testkit/Cargo.toml) | LightCraft test tooling (dev-dependency only): a test that can't run here records why, so `cargo xtask ci` reports it | Nenhuma declarada nesta categoria |
| [tiff](../../crates/tiff/Cargo.toml) | LightCraft TIFF/IFD reader and writer (L0) | Nenhuma declarada nesta categoria |
| [ui-egui](../../crates/ui-egui/Cargo.toml) | LightCraft egui frontend (Lightroom-style UI); swappable — nothing below depends on it (L5) | `lightcraft-catalog`, `lightcraft-color`, `lightcraft-develop`, `lightcraft-engine`, `lightcraft-geom`, `lightcraft-meta`, `lightcraft-pipeline`, `lightcraft-preview`, `lightcraft-raster`, `lightcraft-slideshow` |

Ao mudar uma entrada, saída ou dependência, atualize primeiro o contrato do módulo e depois esta navegação. [Voltar ao índice](../INDEX.md).
