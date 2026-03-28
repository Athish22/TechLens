TechLens — Offline Image Quality Analyzer

A fully offline, single-file image technical quality analyzer that runs entirely in your browser — no server, no API, no installation.


## What is TechLens?

TechLens analyzes the **technical quality** of images using 14 pixel-level algorithms computed entirely inside the browser via the Canvas API. It produces a scored report with expandable issue cards, actionable fix recommendations, an 8-axis quality radar chart, and tooltips explaining every metric — all without sending your image anywhere.

**Focus:** Technical photographic quality only — exposure, sharpness, noise, color accuracy, lens artifacts, and more. Not semantics, not aesthetics, not content.

---

## Features

- ** 100% offline** — single `.html` file, no internet required after download
- ** 14 analysis algorithms** — see full list below
- ** Quality radar chart** — 8-axis visual overview of all dimensions
- ** Rich tooltips** — hover any metric, radar axis, severity pill, or algorithm tag to learn what it means and how it is computed
- ** Scored report** — 0–100 composite technical score with severity-sorted issue cards
- ** Fix recommendations** — specific, actionable corrections for each detected issue
- ** Algorithm attribution** — each issue shows which algorithm detected it
- ** RGB histogram** — live channel frequency distribution
- ** Image metadata** — dimensions, file size, megapixels, aspect ratio, format

---

## Demo

Open `TechLens_Offline.html` directly in any browser. No build step, no server.

```
git clone https://github.com/your-username/techlens.git
cd TechLens
open TechLens_Offline.html  
```

---

## Algorithms

| # | Algorithm | Detects |
|---|-----------|---------|
| 1 | **Luminance Histogram** | Blown highlights, crushed shadows, clipping percentage |
| 2 | **Mean Luminance** | Overall exposure level (over/underexposure) |
| 3 | **Laplacian Variance** | Sharpness, blur, camera shake, over-sharpening halos |
| 4 | **Local Residual Analysis** | Luminance & chrominance noise, ISO artifacts |
| 5 | **Grey World Algorithm** | Color cast, white balance deviation per channel |
| 6 | **HSV Saturation** | Undersaturation, oversaturation, color clipping risk |
| 7 | **Histogram Entropy** | Dynamic range, tonal flatness, narrow tonal range |
| 8 | **Block Boundary Analysis** | JPEG compression artifacts, 8×8 DCT blocking |
| 9 | **Edge Channel Analysis** | Chromatic aberration, red/cyan & green/magenta fringing |
| 10 | **Corner Brightness Falloff** | Vignetting — corner vs centre luminance, symmetry |
| 11 | **Gradient Banding Detection** | Banding, posterization, 8-bit depth limitations |
| 12 | **Isolated Pixel Detection** | Dead pixels (stuck black), hot pixels (stuck bright) |
| 13 | **Edge Straightness Analysis** | Lens distortion — barrel and pincushion warping |
| 14 | **Tile Variance Analysis** | Local contrast, micro-detail, haze, flatness |

Additionally: **Per-Channel Clipping** checks R, G, and B channels independently to catch single-channel saturation that appears normal in overall luminance.

---

## Metrics Explained

| Metric | Method | Good Range |
|--------|--------|-----------|
| Mean Luminance | Perceptual weights: R×0.2126 + G×0.7152 + B×0.0722 | 60–200 |
| Laplacian Variance | 3×3 Laplacian kernel edge energy, 512×512 centre crop | > 12 |
| Noise Residual | Pixel vs 4-neighbor mean absolute difference | < 4 |
| Blown % | Pixels with luminance ≥ 245/255 | < 1% |
| DR Entropy | Shannon entropy of 256-bin luminance histogram | > 6 bits |
| Color Cast | Grey World neutral deviation, dominant channel | < 5% |
| Vignette Score | (1 − corner_falloff) × 100, 12% corner zones | > 70 |
| Local Contrast | Median 32×32 tile brightness variance | > 50 |
| Artifact Score | 100 − (block_ratio × 20) − (CA_score × 2) | > 70 |

---

## Tooltip System

Every interactive element has a hover tooltip explaining:

- **Metric cards** — algorithm used, what the number means, color-coded threshold ranges
- **Radar axes** — exact formula used to compute each radar score
- **Severity pills** — what High / Medium / Low means practically
- **Algorithm tags** — full technical description of the detection method
- **Histogram** — how to read the RGB channel distribution
- **Score bar** — how the 0–100 composite score is calculated

---

## Score Calculation

The composite score starts at 100 and deducts weighted penalties per detected issue:

| Severity | Penalty |
|----------|---------|
| High | −16 points |
| Medium | −7 points |
| Low | −2.5 points |

| Score | Rating |
|-------|--------|
| 80–100 | Excellent |
| 65–79 | Good |
| 45–64 | Fair |
| 0–44 | Needs Work |

---

## Browser Compatibility

| Browser | Support |
|---------|---------|
| Chrome / Edge 90+ | ✅ Full |
| Firefox 88+ | ✅ Full |
| Safari 14+ | ✅ Full |
| Opera 76+ | ✅ Full |

Requires Canvas 2D API (`getImageData`) — available in all modern browsers.

---

## Project Structure

```
techlens/
├── TechLens_Offline.html   # The entire application — HTML + CSS + JS in one file
└── README.md
```

There are no dependencies, no `package.json`, no build tools, no frameworks.

---

## How It Works

1. User drops or selects an image file
2. The image is drawn to a hidden `<canvas>` element
3. `ctx.getImageData()` extracts raw RGBA pixel data into a `Uint8ClampedArray`
4. All 14 algorithms run directly on this pixel array in the browser
5. Results are scored, sorted by severity, and rendered as expandable issue cards
6. A radar chart visualizes the 8-dimensional quality profile
7. Tooltips are attached via a delegated `mouseover` listener using `data-tip` attributes

No image data leaves the browser. No network requests are made after the page loads.

---

## Supported Formats

Any image format the browser's native `<img>` element can decode:

- **PNG** (including edited/composited images)
- **JPEG / JPG**
- **WEBP**
- **GIF** (first frame)
- **AVIF** (Chrome/Firefox)
- **BMP**

---

## Limitations

- **Canvas pixel access** is limited by browser security. Cross-origin images will be blocked by CORS — use local files.
- **Large images** (> 30 MP) may be slow on lower-end hardware. Analysis is performed on sampled regions where noted.
- **RAW formats** (CR2, NEF, ARW, DNG) are not supported — convert to TIFF or PNG first.
- Lens distortion detection is heuristic and works best on images with straight lines near frame edges.
- Dead/hot pixel detection uses a 1024×1024 sample region for performance.

---

## License

MIT License — free to use, modify, and distribute.

---

*Built with vanilla JavaScript, Canvas 2D API, and zero dependencies.*
