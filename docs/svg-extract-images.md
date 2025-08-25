# svg-extract-images

Extract all images from an SVG file, including embedded data URLs and external references.

## Features

- **Embedded data URLs**: Extracts `data:image/*;base64,<payload>` images
- **External references**: Fetches images from `<image>`/`<feImage>` elements with `href`/`xlink:href` attributes and CSS `url(...)` references
  - HTTP(S) URLs are fetched with redirect following using httpx
  - File paths are resolved relative to the SVG file location
- **Format support**: Common raster formats (PNG, JPG, GIF, WebP, BMP, TIFF, ICO, AVIF, HEIC) and vector formats (SVG+XML)
- **Gzip support**: Automatically handles `.svgz` (gzipped SVG) files
- **Smart naming**: 
  - Single image: `<svg_stem>.<ext>`
  - Multiple images: `<svg_stem>_01.<ext>`, `<svg_stem>_02.<ext>`, etc.
- **Content deduplication**: Option to remove duplicate images based on content hash
- **URL encoding handling**: Properly handles URL-encoded base64 and whitespace/newlines in payloads

## Usage

### Basic Usage

```bash
# Using uvx (recommended)
uvx hgc-scripts svg-extract-images input.svg

# Local development
uv run scripts/extract_svg_images.py input.svg
```

### Command Line Options

```
positional arguments:
  svg_file              Path to the SVG file to process

options:
  -h, --help            Show help message and exit
  -o OUTDIR, --output-dir OUTDIR
                        Output directory (default: current directory)
  --skip-external       Skip fetching external HTTP(S) references
  --dedupe             Remove duplicate images based on content hash
  --verbose            Show detailed processing information
```

## Examples

### Extract to Current Directory
```bash
uvx hgc-scripts svg-extract-images diagram.svg
```

### Extract to Specific Directory with Verbose Output
```bash
uvx hgc-scripts svg-extract-images complex.svg -o extracted_images --verbose
```

### Skip External URLs and Remove Duplicates
```bash
uvx hgc-scripts svg-extract-images chart.svg --skip-external --dedupe
```

### Local Development Examples
```bash
# Direct script execution
uv run scripts/extract_svg_images.py input.svg -o output --verbose

# Via local package installation
uv pip install -e .
svg-extract-images input.svg --dedupe
```

## Output Behavior

- **Default output directory**: Current working directory (`.`)
- **Single image**: Saved as `<svg_filename>.<extension>`
- **Multiple images**: Saved as `<svg_filename>_01.<extension>`, `<svg_filename>_02.<extension>`, etc.
- **Verbose mode**: Shows detailed information about discovered and processed images
- **Deduplication**: When enabled, identical images (by content hash) are saved only once

## Dependencies

- Python 3.13+
- lxml >= 5.2.0
- httpx >= 0.27.0

Dependencies are automatically handled when using `uv run` or `uvx` through PEP 723 script metadata.