# Rug-Panel Translation System Documentation

> 🌍 **English Documentation** | **[Русская документация](TRANSLATION_GUIDE_RU.md)**

## Overview

Rug-Panel has a fully integrated multilingual system (i18n - internationalization) supporting Russian and English interface languages.

## Architecture

### File Structure

```
app/
├── languages/          # Language packages
│   ├── ru.json        # Russian language
│   └── en.json        # English language
├── i18n.py            # Internationalization module
├── config.py          # Updated: added LANGUAGE
└── dependencies.py    # Updated: translation integration in templates
```

### i18n.py Module

**Key Features:**
- **Singleton pattern** with thread-safe initialization
- **LRU caching** of loaded languages (maxsize=10)
- **Lazy loading** of language files
- **Fallback to Russian** if language not found
- **Nested keys** support (e.g., `nav.clients`)

**Core Functions:**
```python
from app.i18n import get_i18n, t, get_translations

# Get singleton instance
i18n = get_i18n()

# Quick access to translations
translated_text = t("nav.clients")  # "Clients" or "Клиенты"

# Get all translations for templates
translations = get_translations()
```

### Language File Structure (ru.json, en.json)

Language files are logically organized by sections:

```json
{
  "app_name": "Rug-Panel",
  "app_description": "...",
  
  "nav": { ... },           // Navigation
  "login": { ... },         // Login page
  "dashboard": { ... },     // Dashboard
  "peer": { ... },          // Client cards
  "peer_form": { ... },     // Client forms
  "peer_delete": { ... },   // Client deletion
  "server": { ... },        // Server settings
  "profile": { ... },       // User profile
  "users": { ... },         // User management
  "common": { ... },        // Common elements
  "errors": { ... },        // Error messages
  "time": { ... }           // Timestamps
}
```

## Configuration

### LANGUAGE Environment Variable

Added to `app/config.py`:
```python
# ==================== Interface Language ====================
# Supported languages: ru (Russian), en (English)
LANGUAGE = os.getenv("LANGUAGE", "ru")
```

### .env File

```env
# Interface language (ru - Russian, en - English)
LANGUAGE=en
```

### .env.example

Updated with documentation for the new variable.

## Usage in Templates

### Jinja2 Global Variables

In `app/dependencies.py` automatically available:
```python
templates.env.globals["t"] = get_translations()  # Translation dictionary
templates.env.globals["lang"] = LANGUAGE         # Language code
```

### Template Examples

**base.html:**
```html
<!DOCTYPE html>
<html lang="{{ lang }}">
<head>
    <title>{{ t.login.title }} | {{ app_name }}</title>
</head>
```

**Navigation:**
```html
<a href="/">{{ t.nav.clients }}</a>
<a href="/server/settings">{{ t.nav.server }}</a>
<a href="/users">{{ t.nav.users }}</a>
```

**Forms:**
```html
<label for="name">{{ t.peer_form.name }} *</label>
<input type="text" id="name" name="name" 
       placeholder="{{ t.peer_form.name_placeholder }}" required>
<small>{{ t.peer_form.name_hint }}</small>
```

**Buttons:**
```html
<button class="btn btn-primary">{{ t.common.save }}</button>
<button class="btn btn-outline">{{ t.common.cancel }}</button>
```

## Updated Templates

### Main Templates
- ✅ `base.html` - Base layout, navigation
- ✅ `login.html` - Login page
- ✅ `dashboard.html` - Main page

### Components
- ✅ `peers_list.html` - Client list
- ✅ `peer_form.html` - Client creation form

### Partially Updated
- ⚠️ `server_settings.html` - Uses old Russian texts
- ⚠️ `users.html` - Uses old Russian texts
- ⚠️ `profile.html` - Uses old Russian texts

## Testing

### Playwright Testing

Both languages tested:

**Russian Interface (LANGUAGE=ru):**
- ✅ Login page
- ✅ Navigation
- ✅ Dashboard with statistics
- ✅ Client creation modal window
- ✅ Client cards

**English Interface (LANGUAGE=en):**
- ✅ Login page: "Username", "Password", "Sign In"
- ✅ Navigation: "Clients", "Server", "Users"
- ✅ Dashboard: "VPN Clients", "Total", "Online", "Offline"
- ✅ Modal form: "New Client", "Client Name", "Create", "Cancel"
- ✅ Peer cards: "IP Address", "QR Code", "Edit", "Disable"

### Screenshots

Saved in `.playwright-mcp/`:
- `english-interface-dashboard.png` - Dashboard in English
- `english-interface-modal.png` - Modal window in English

## Optimizations

### Performance

1. **LRU language cache** - Loaded languages are cached
2. **Singleton pattern** - One I18n instance for entire application
3. **Thread-safe** - Safe operation in multithreaded environment
4. **Lazy loading** - Languages loaded on demand

### Memory

- Cache limited to 10 languages (maxsize=10)
- JSON files are compact (~15 KB per language)
- Translations loaded once at startup

## Language Switching

### For Development

```bash
# Set English
export LANGUAGE=en
python run.py

# Set Russian
export LANGUAGE=ru
python run.py
```

### For Production

Change in `.env`:
```env
LANGUAGE=en
```

And restart server:
```bash
docker compose restart
```

## Adding New Language

1. Create file `app/languages/fr.json` (e.g., for French)
2. Copy structure from `en.json` or `ru.json`
3. Translate all strings
4. Set `LANGUAGE=fr` in `.env`
5. Restart application

The i18n module will automatically detect the new language.

## Adding New Translations

1. Add key to `ru.json`:
```json
{
  "settings": {
    "theme": "Тема оформления"
  }
}
```

2. Add corresponding translation to `en.json`:
```json
{
  "settings": {
    "theme": "UI Theme"
  }
}
```

3. Use in template:
```html
<label>{{ t.settings.theme }}</label>
```

## Known Limitations

1. **Restart required** - Language change requires server restart
2. **Static texts** - Some old templates still contain hardcoded Russian texts
3. **Server messages** - Error messages in Python code are currently in Russian

## Roadmap

### Short-term Improvements
- [ ] Update remaining templates (server_settings, users, profile)
- [ ] Translate server error messages
- [ ] Add language switcher in UI

### Long-term Improvements
- [ ] Dynamic language switching without restart
- [ ] Add more languages (German, French, Spanish)
- [ ] Date/number formatting with locale support
- [ ] Pluralization support (1 client / 2 clients / 5 clients)

## Conclusion

The translation system is fully functional and tested. The panel supports two languages (Russian and English) with easy extensibility. Main interface components are translated and display correctly in both languages.

**Status:** ✅ Ready for use  
**Date:** January 3, 2026  
**Version:** 1.4.3

---

**For Russian documentation:** [TRANSLATION_GUIDE_RU.md](TRANSLATION_GUIDE_RU.md)
