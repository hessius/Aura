# Regenerating Fonts with Full International Character Support

## Overview
To completely solve the international character rendering issue, custom LVGL font files need to be regenerated with a comprehensive character set that includes all commonly used international characters.

**Current Issue**: The existing custom Latin fonts only support a limited character set (`°¿ÉÊÍÓÜßáäçèéíñóöúûü‐→Îàâéèêëîïôöùûüÿç`), and LVGL's built-in Montserrat fonts are ASCII-only as documented in lv_conf.h.

## Required Tools
- LVGL Font Converter (https://lvgl.io/tools/fontconverter)
- Montserrat-Light.ttf font file

## Complete Character Set for Full UTF-8 Support

### All Required Characters
```
 !"#$%&'()*+,-./0123456789:;<=>?@ABCDEFGHIJKLMNOPQRSTUVWXYZ[\]^_`abcdefghijklmnopqrstuvwxyz{|}~°¿ÀÁÂÄÅÆÇÈÉÊËÌÍÎÏÑÒÓÔÖØÙÚÛÜÝßàáâäåæçèéêëìíîïñòóôöøùúûüýÿĄąĆćČčĎďĘęĚěŁłŃńŇňŘřŚśŠšŤťŮůŸŹźŻżŽž‐→
```

### Current Support (Limited)
`°¿ÉÊÍÓÜßáäçèéíñóöúûü‐→Îàâéèêëîïôöùûüÿç`

### Missing Characters (To Add)
`ÀÁÂÄÅÆÇÈËÌÎÏÑÒÔÖØÙÚÛÝåæøÿĄąĆćČčĎďĘęĚěŁłŃńŇňŘřŚśŠšŤťŮůŸŹźŻżŽž`

## Font Generation Steps

### Step 1: Generate Font Files
Use LVGL Font Converter with these exact settings:

**Font Settings:**
- Font: Montserrat-Light.ttf
- Sizes: 12, 14, 16, 20, 42
- BPP: 4
- Format: LVGL

**Character Set (paste into Range field):**
```
 !"#$%&'()*+,-./0123456789:;<=>?@ABCDEFGHIJKLMNOPQRSTUVWXYZ[\]^_`abcdefghijklmnopqrstuvwxyz{|}~°¿ÀÁÂÄÅÆÇÈÉÊËÌÍÎÏÑÒÓÔÖØÙÚÛÜÝßàáâäåæçèéêëìíîïñòóôöøùúûüýÿĄąĆćČčĎďĘęĚěŁłŃńŇňŘřŚśŠšŤťŮůŸŹźŻżŽž‐→
```

### Step 2: File Replacement
Replace the existing custom font files:
```
aura/lv_font_montserrat_latin_12.c  →  lv_font_montserrat_international_12.c
aura/lv_font_montserrat_latin_14.c  →  lv_font_montserrat_international_14.c
aura/lv_font_montserrat_latin_16.c  →  lv_font_montserrat_international_16.c
aura/lv_font_montserrat_latin_20.c  →  lv_font_montserrat_international_20.c
aura/lv_font_montserrat_latin_42.c  →  lv_font_montserrat_international_42.c
```

### Step 3: Update Code
Update font declarations in `weather.ino`:

```cpp
// International Montserrat fonts with comprehensive UTF-8 support
LV_FONT_DECLARE(lv_font_montserrat_international_12);
LV_FONT_DECLARE(lv_font_montserrat_international_14);
LV_FONT_DECLARE(lv_font_montserrat_international_16);
LV_FONT_DECLARE(lv_font_montserrat_international_20);
LV_FONT_DECLARE(lv_font_montserrat_international_42);

const lv_font_t* get_font_12() {
  return &lv_font_montserrat_international_12;
}

const lv_font_t* get_font_14() {
  return &lv_font_montserrat_international_14;
}

const lv_font_t* get_font_16() {
  return &lv_font_montserrat_international_16;
}

const lv_font_t* get_font_20() {
  return &lv_font_montserrat_international_20;
}

const lv_font_t* get_font_42() {
  return &lv_font_montserrat_international_42;
}
```

### Step 4: Remove Transliteration
Simplify the transliteration function:

```cpp
String transliterate_unsupported_chars(const String &input) {
  // All characters supported by international fonts - no transliteration needed
  return input;
}
```

## Command Line Generation (Alternative)
If using lv_font_conv command line tool:

```bash
# Generate fonts with comprehensive character set
lv_font_conv --font Montserrat-Light.ttf --size 12 --bpp 4 \
  --symbols " !\"#$%&'()*+,-./0123456789:;<=>?@ABCDEFGHIJKLMNOPQRSTUVWXYZ[\\]^_\`abcdefghijklmnopqrstuvwxyz{|}~°¿ÀÁÂÄÅÆÇÈÉÊËÌÍÎÏÑÒÓÔÖØÙÚÛÜÝßàáâäåæçèéêëìíîïñòóôöøùúûüýÿĄąĆćČčĎďĘęĚěŁłŃńŇňŘřŚśŠšŤťŮůŸŹźŻżŽž‐→" \
  --format lvgl -o lv_font_montserrat_international_12.c

# Repeat for sizes 14, 16, 20, 42
```

## Testing Coverage
After implementation, test with these international location names:
- **Scandinavian**: Spånga, Malmö, Århus, København
- **German**: München, Düsseldorf, Köln
- **French**: Montréal, Château-Thierry
- **Polish**: Kraków, Gdańsk, Łódź
- **Czech**: České Budějovice, Ústí nad Labem
- **Turkish**: İstanbul, Ankara

## Memory Considerations
- Extended character sets increase font file size (~2-3x larger)
- Monitor ESP32 memory usage during compilation
- Current partition scheme allows up to 3MB for app
- Remove unused font sizes if memory becomes critical

## Benefits
- True UTF-8 support without transliteration
- Proper display of international location names
- Support for major European languages
- No character degradation or approximation

This solution provides comprehensive international character support while maintaining the visual quality and readability of the original Montserrat font.