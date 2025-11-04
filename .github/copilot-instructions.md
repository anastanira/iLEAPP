# iLEAPP AI Assistant Instructions

## Project Overview
iLEAPP (iOS Logs, Events, And Plists Parser) is a forensics tool for analyzing iOS/iPadOS device data. The tool processes data from filesystem extractions, compressed archives (tar/zip/gz), and iTunes/Finder backups.

## Key Architecture Components

### Plugin System
- Plugins are defined in individual Python files in `scripts/artifacts/`
- Each plugin must define metadata using `__artifacts_v2__` dictionary:
```python
__artifacts_v2__ = {
    "artifact_function_name": {
        "name": "Display Name",
        "description": "What the artifact analyzes",
        "author": "@username",
        "category": "Category",
        "paths": ('*/path/pattern/*.db',),  # File patterns to search
    }
}
```

### Core Processing Flow
1. `ileapp.py`/`ileappGUI.py` - Entry points
2. `plugin_loader.py` - Dynamically loads artifact plugins
3. `ilapfuncs.py` - Core utilities and `@artifact_processor` decorator
4. Individual artifact processors in `scripts/artifacts/`

### Data Processing Pattern
1. Use `@artifact_processor` decorator on main artifact function
2. Function should return tuple: `(data_headers, data_list, source_path)`
3. Data automatically formatted for HTML, TSV, timeline, and LAVA outputs

## Project Conventions

### File Patterns
- Use glob patterns in plugin `paths` to locate target files
- Patterns should be specific to minimize false positives
- Common paths: `*/Media/`, `*/Library/`, `*/mobile/Library/`

### Data Output
- LAVA format is primary output (SQLite-based)
- Additional outputs: HTML, TSV, timeline, KML
- Media files handled via `check_in_media()`/`check_in_embedded_media()`

## Development Workflow

### Adding New Artifacts
1. Create new file in `scripts/artifacts/`
2. Define `__artifacts_v2__` metadata
3. Implement main processing function with `@artifact_processor`
4. Follow existing artifacts like `accountConfig.py` as examples

### Testing
- Test with sample data in `zCaseDataExample.lcasedata`
- Verify output formats: LAVA DB, HTML report
- Check media handling if processing images/videos

## Integration Points
- Python version support: 3.10 - 3.12
- Key dependencies in `requirements.txt`
- Uses `simplekml` for geolocation data
- `pyliblzfse` for iOS compression support

## File Organization
- `scripts/artifacts/` - Individual artifact parsers
- `scripts/_elements/` - UI/report assets
- `admin/docs/` - Architecture documentation
- `scripts/ccl/` - Core libraries

Remember to:
- Always use the `@artifact_processor` decorator
- Follow the established plugin metadata structure
- Use appropriate glob patterns for file searching
- Handle media files through helper functions
- Document artifact sources and data formats