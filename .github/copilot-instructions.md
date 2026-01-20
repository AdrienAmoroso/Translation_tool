# AI Coding Instructions for Tennis Manager Localization Translation

## Project Overview
This codebase implements a machine translation pipeline for localizing the "Tennis Manager 25" video game from English to Portuguese_BR. It processes Excel-based localization files using AI APIs (OpenAI GPT or Google Gemini) while preserving technical placeholders and maintaining game-specific translation quality.

## Architecture & Data Flow
- **Core Script**: `translate_loc.py` - Single-file implementation containing all logic
- **Data Source**: `localization.xlsx` - Excel workbook with multiple sheets (UI, Match, Media, Tuto, etc.)
- **Output**: Translated text written back to the same Excel file
- **Logging**: `logs/` directory with timestamped run logs and CSV key processing status

## Key Components

### Segment Processing
```python
@dataclass
class Segment:
    sheet: str          # Sheet name (UI, Match, Media, etc.)
    row_idx: int        # Excel row number (1-based)
    key: str           # Localization key identifier
    source_text: str   # English source text
    existing_target: str  # Existing Portuguese translation (if any)
    comment: str       # Optional context comment
    donottranslate: bool  # Skip translation flag
```

### Placeholder Protection System
- **Variables**: `{[...]} ` patterns → `__VAR0__`, `__VAR1__`, etc.
- **Tags**: `<...>` patterns → `__TAG0__`, `__TAG1__`, etc.
- **Validation**: Ensures all placeholders are preserved and maintain original order
- **Restoration**: Converts tokens back to original placeholders after translation

### Translation Workflow
1. Load segments from Excel sheets specified in `SHEETS_TO_TRAD`
2. Filter segments that need translation (non-empty source, no existing target, not marked donottranslate)
3. Protect placeholders in source text
4. Batch segments (default: 20 per batch)
5. Call AI API with detailed game-specific prompt
6. Validate placeholder preservation in translations
7. Restore original placeholders
8. Write translations back to Excel
9. Log all operations to CSV

## Critical Developer Workflows

### Running Translation
```bash
python translate_loc.py
```
- Requires `.env` file with `OPENAI_API_KEY` or `GEMINI_API_KEY`
- Set `USE_GEMINI = True/False` to switch AI providers
- Modify `SHEETS_TO_TRAD` list to control which sheets to process
- Check `logs/` for detailed execution logs and key status CSVs

### API Configuration
- **OpenAI**: Uses `gpt-4o-mini` via new SDK (`openai_client.responses.create`)
- **Gemini**: Uses `gemini-2.5-flash-lite` via `genai.Client`
- Rate limiting handled with exponential backoff and batch cooldowns (22s default)
- API keys loaded from environment variables

### Debugging Translation Issues
- Check CSV logs in `logs/` for status codes: `OK`, `MISSING_TOKENS`, `TOKENS_OUT_OF_ORDER`, `NO_TRANSLATION`
- Review log files for API errors and batch processing details
- Validate placeholder preservation by checking token presence and ordering

## Project-Specific Patterns

### Sheet-Specific Translation Rules
- **UI/Default/Geo/Tennis/Manager/Equip**: Keep translations short and clear
- **Tuto**: Allow slightly longer didactic sentences
- **Match/Ld_radio**: Dynamic commentator-style speech
- **Media**: Professional interview Q&A tone
- **Ld_talk**: Motivational locker-room dialogue
- **Ld_advices**: Concise coaching tips

### Gender Handling
- Keys ending in `_M`: Male character references
- Keys ending in `_F`: Female character references
- Only adapt gender where text refers to specific characters, not generic parts

### Technical Constraints
- Preserve exact line breaks and bullet markers
- Never modify, translate, or reorder placeholder tokens
- Maintain formality level matching English source
- Avoid significantly lengthening UI text

### Error Handling
- JSON parsing validation for API responses
- Placeholder validation with detailed logging
- Rate limit handling with configurable retry logic
- Graceful degradation for missing columns/comments

## Integration Points
- **Excel I/O**: `openpyxl` for reading/writing localization files
- **AI APIs**: Dual support for OpenAI and Google Gemini
- **Environment**: `.env` file for API key management
- **Logging**: Structured logging with timestamps and run IDs

## Common Modification Patterns
- **Add new sheet**: Update `DOC_SHEETS` list and `SHEETS_TO_TRAD`
- **Change target language**: Modify `TARGET_LANG` and `TARGET_COL`
- **Adjust batching**: Change `BATCH_SIZE` and `BATCH_COOLDOWN_SECONDS`
- **Add placeholder patterns**: Extend `PLACEHOLDER_PATTERN_TAG/VAR` regexes
- **Customize AI prompt**: Modify `build_system_prompt()` function

## Dependencies
- pandas: Excel data processing
- openpyxl: Excel file manipulation
- python-dotenv: Environment variable loading
- openai: OpenAI API client
- google-genai: Google Gemini API client