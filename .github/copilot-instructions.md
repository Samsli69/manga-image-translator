# Copilot Instructions for Manga Image Translator

## Project Overview
Manga Image Translator is a Python-based tool for automated translation of manga/comics. It processes images through a pipeline: Upscaling -> Text Detection -> OCR -> Translation -> Inpainting -> Text Rendering.

## Architecture & Core Components
- **Pipeline Orchestration**: `manga_translator/manga_translator.py` is the core. The `MangaTranslator` class manages the async pipeline.
- **Data Flow**: A `Context` object (`ctx`) is passed through all pipeline stages, accumulating state (original image, masks, text regions, translated text).
- **Modules**: Each pipeline stage (ocr, detection, inpainting, etc.) has a `dispatch.py` to handle model selection and execution.
- **Server**: `server/main.py` provides a FastAPI backend for the web interface and API.

## Key Directories
- `manga_translator/`: Core library code.
- `manga_translator/translators/`: Translation logic (Google, DeepL, OpenAI, etc.).
- `manga_translator/detection/`, `ocr/`, `inpainting/`: Model implementations for specific tasks.
- `server/`: Web server and API handling.

## Development Workflows
- **CLI Usage**: Run via `python -m manga_translator local -i <path>`.
- **Web Server**: Run via `python server/main.py`.
- **Debugging**: Use `--verbose` flag. This saves intermediate images (masks, bounding boxes, inpainting results) to the `result/` directory, which is crucial for debugging pipeline issues.
- **Configuration**: Settings are managed via the `Config` class. See `manga_translator/config.py` or `examples/config-example.json`.

## Coding Conventions
- **Async/Await**: The main pipeline is asynchronous. Use `async def` and `await` for I/O bound or heavy model operations where appropriate.
- **Context Object**: Do not pass individual variables between pipeline stages; attach them to the `ctx` object.
- **Error Handling**: The pipeline uses `ignore_errors` config. Critical failures in one stage (e.g., OCR) should often return a fallback (e.g., empty list) rather than crashing, unless strictly necessary.
- **Logging**: Use the project's logger (`logger = logging.getLogger('manga_translator')`).

## Specific Patterns
- **Text Regions**: Text is stored in `TextRegion` objects containing bounding boxes, raw text, and translation.
- **Model Loading**: Models are loaded on demand or preloaded based on `models_ttl`. See `prepare_*` functions in dispatchers.
