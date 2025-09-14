# Regenerating Fonts with Full International Character Support

## Overview
To completely solve the international character rendering issue, the LVGL font files need to be regenerated with an extended character set that includes all commonly used international characters.

## Required Tools
- LVGL Font Converter (online tool or offline converter)
- Montserrat font file (TTF)

## Character Set for International Support
Based on the Unicode analysis, include these characters in the font generation:

### Current Support
Already supported by existing fonts: `°¿ÉÊÍÎÓÜßàáâäçèéêëíîïñóôöùúûüÿ‐→`

### Missing Characters to Add
Essential international characters that should be added:
```
ÀÁÂÄÅÆÇÈËÌÎÏÑÒÔÖØÙÚÛÝàâåæêëìîïòôøùýÿĄąĆćČčĎďĘęĚěŁłŃńŇňŘřŚśŠšŤťŮůŸŹźŻżŽž
```

### Complete Character Set for Font Generation
```
 !"#$%&'()*+,-./0123456789:;<=>?@ABCDEFGHIJKLMNOPQRSTUVWXYZ[\]^_`abcdefghijklmnopqrstuvwxyz{|}~°¿ÀÁÂÄÅÆÇÈÉÊËÌÍÎÏÑÒÓÔÖØÙÚÛÜÝßàáâäåæçèéêëìíîïñòóôöøùúûüýÿĄąĆćČčĎďĘęĚěŁłŃńŇňŘřŚśŠšŤťŮůŸŹźŻżŽž‐→
```

## Steps to Regenerate Fonts

### Option 1: Online LVGL Font Converter
1. Go to https://lvgl.io/tools/fontconverter
2. Upload Montserrat-Light.ttf font file
3. Set font sizes: 12, 14, 16, 20, 42
4. Set BPP: 4
5. Paste the complete character set above into the "Range" field
6. Set format: "LVGL"
7. Generate and download the font files

### Option 2: Command Line (if available)
```bash
# Generate fonts with extended character set
lv_font_conv --font Montserrat-Light.ttf --size 12 --bpp 4 --symbols " !\"#$%&'()*+,-./0123456789:;<=>?@ABCDEFGHIJKLMNOPQRSTUVWXYZ[\\]^_\`abcdefghijklmnopqrstuvwxyz{|}~°¿ÀÁÂÄÅÆÇÈÉÊËÌÍÎÏÑÒÓÔÖØÙÚÛÜÝßàáâäåæçèéêëìíîïñòóôöøùúûüýÿĄąĆćČčĎďĘęĚěŁłŃńŇňŘřŚśŠšŤťŮůŸŹźŻżŽž‐→" --format lvgl -o lv_font_montserrat_international_12.c

# Repeat for sizes 14, 16, 20, 42
```

## Implementation Steps

1. **Generate new font files** with the complete character set
2. **Replace the existing font files** in the `aura/` directory:
   - `lv_font_montserrat_latin_12.c` → `lv_font_montserrat_international_12.c`
   - `lv_font_montserrat_latin_14.c` → `lv_font_montserrat_international_14.c` 
   - `lv_font_montserrat_latin_16.c` → `lv_font_montserrat_international_16.c`
   - `lv_font_montserrat_latin_20.c` → `lv_font_montserrat_international_20.c`
   - `lv_font_montserrat_latin_42.c` → `lv_font_montserrat_international_42.c`

3. **Update font declarations** in `weather.ino`:
   ```cpp
   LV_FONT_DECLARE(lv_font_montserrat_international_12);
   LV_FONT_DECLARE(lv_font_montserrat_international_14);
   LV_FONT_DECLARE(lv_font_montserrat_international_16);
   LV_FONT_DECLARE(lv_font_montserrat_international_20);
   LV_FONT_DECLARE(lv_font_montserrat_international_42);
   ```

4. **Update font getter functions**:
   ```cpp
   const lv_font_t* get_font_12() {
     return &lv_font_montserrat_international_12;
   }
   // Update all sizes...
   ```

5. **Remove transliteration function** or simplify it to just return the input string:
   ```cpp
   String transliterate_unsupported_chars(const String &input) {
     return input;  // All characters should now be supported
   }
   ```

## Testing
After implementing the changes:
1. Test with location names containing special characters: "Spånga", "Malmö", "Kraków", "Zürich"
2. Verify all international characters display correctly in both location search and settings
3. Test multiple languages to ensure comprehensive support

## Memory Considerations
- Extended character sets increase font file size
- Monitor memory usage on ESP32
- Consider removing unused sizes if memory becomes an issue
- Current partition scheme allows up to 3MB for the app

## Alternative: Use Different Base Font
If Montserrat becomes too large, consider other fonts with better international support:
- DejaVu Sans (good international support)
- Noto Sans (comprehensive Unicode coverage)
- Liberation Sans (open source alternative)