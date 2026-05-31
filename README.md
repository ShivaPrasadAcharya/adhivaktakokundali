# ⚖ अधिवक्ता सूची — Smart Search

A fast, fully client-side advocate (lawyer) directory viewer for **Nepal Bar Council** records. Built as a single HTML file — no server, no database, no build step required.

> **Developer:** शिवप्रसाद आचार्य  
> **Last updated:** २०८२ (2082 BS)

---

## Table of Contents

- [Overview](#overview)
- [Getting Started](#getting-started)
- [Data File Format](#data-file-format)
- [Loading Multiple Data Files](#loading-multiple-data-files)
- [Search Features](#search-features)
- [Filters & Sort](#filters--sort)
- [Navigation](#navigation)
- [Column Visibility](#column-visibility)
- [Keyboard Shortcuts](#keyboard-shortcuts)
- [Theme](#theme)
- [Copying Data](#copying-data)
- [URL Parameters](#url-parameters)
- [Browser Support](#browser-support)
- [External Dependencies](#external-dependencies)
- [Project Structure](#project-structure)

---

## Overview

This is a zero-dependency, single-file HTML application that loads advocate records from one or more `.js` data chunk files and presents them in a searchable, filterable, sortable table. Everything runs in the browser — no internet connection is required once the page and its data files are downloaded (except for loading fonts and icons on first visit).

Key characteristics:

- **No backend** — pure HTML + CSS + vanilla JavaScript
- **Nepali-aware search** — normalizes Devanagari script variations for fuzzy matching
- **Paginated table** — handles tens of thousands of records smoothly
- **Responsive** — works on desktop, tablet, and mobile
- **Light & dark mode** — preference saved in `localStorage`
- **Print-friendly** — ribbon and pagination are hidden when printing

---

## Getting Started

1. Place `index.html` (this file) in a folder on your computer or web server.
2. Add at least one data file (see [Data File Format](#data-file-format)) to the **same folder**.
3. Open `index.html` in any modern browser.

> **Local file note:** Some browsers block script loading from `file://` URLs. If the table shows "Data file भेटिएन", serve the folder through a simple local server:
> ```bash
> # Python 3
> python -m http.server 8080
> # Then open http://localhost:8080
> ```

---

## Data File Format

Each data file must call `window.registerAdvocateDataChunk(partNumber, rows)` with an array of record objects.

```js
// advocatedata1.js
window.registerAdvocateDataChunk(1, [
  {
    licenceNo:   "12345",
    name:        "राम बहादुर श्रेष्ठ",
    englishName: "Ram Bahadur Shrestha",
    address:     "काठमाडौं",
    sex:         "पुरुष",
    type:        "Advocate",
    issueDate:   "2065-04-15"
  },
  // … more records
]);
```

### Field reference

| Field | Type | Description |
|---|---|---|
| `licenceNo` | string | Bar Council licence number |
| `name` | string | Full name in Nepali (Devanagari) |
| `englishName` | string | Full name in English |
| `address` | string | District / province address |
| `sex` | string | e.g. `पुरुष`, `महिला` |
| `type` | string | e.g. `Advocate`, `Senior Advocate` |
| `issueDate` | string | Licence issue date (any format) |

All fields are optional — missing fields default to an empty string.

---

## Loading Multiple Data Files

The app supports several patterns for loading data, tried in this order:

### 1. URL query parameter (highest priority)
Pass a comma-separated list of filenames via `?data=`:
```
index.html?data=advocatedata1.js,advocatedata2.js,senioradvocate.js
```

### 2. Manifest file
Create `advocatedatafiles.js` in the same folder:
```js
window.ADVOCATE_DATA_FILES = [
  "advocatedata1.js",
  "advocatedata2.js",
  "senioradvocate.js"
];
```

### 3. Automatic sequential loading
The app automatically tries `advocatedata1.js`, `advocatedata2.js`, … up to `advocatedata300.js`, stopping after the first missing file.

### 4. Senior advocates file
`senioradvocate.js` is always attempted automatically alongside the sequential files.

All successfully loaded chunks are merged and sorted by chunk number before display.

---

## Search Features

The search box in the sticky ribbon searches across all columns by default. Press **Enter** or click the orange search button to apply.

### Search modes (Settings → Search Options → Mode)

| Mode | Behaviour |
|---|---|
| **Contains** *(default)* | Matches any record where the column contains the query string |
| **All words** | All space-separated words must appear (in any order) |
| **Any word** | At least one of the space-separated words must appear |
| **Starts with** | Column value must begin with the query string |
| **Regex** | Full JavaScript regular expression support |

### Column scope (Settings → Search Options → Column)

Restrict the search to a single column: Licence No, Nepali Name, English Name, Address, Sex, Type, or Issuedate. Defaults to **All columns**.

### Nepali normalization (`सिव=शीब` toggle)

When enabled (default), the search engine normalizes both the query and the data before matching, making the following variants equivalent:

- Long/short vowels: `ी` ↔ `ि`, `ू` ↔ `ु`
- Sibilants: `श`, `ष` → `स`
- Nasals: `ङ`, `ण`, `ञ`, `ं` → `न`
- `व` → `ब`
- Nepali digits → ASCII digits

This means searching `सिव` will find `शीव`, `शीब`, `सीब`, etc.

Disable with the **सिव=शीब** toggle pill or **Exact Nepali** checkbox for strict matching.

### Real-time search

Enable **Real-time** in Settings to trigger search on every keystroke (useful for small datasets; may be slow with very large ones).

### Case sensitivity

Off by default. Enable **Case sensitive** in Settings for exact-case English matching.

---

## Filters & Sort

Open the **Settings** dropdown (sliders icon) for advanced controls.

### Dropdown filters

- **Sex** — filter to a specific sex value (populated dynamically from data)
- **Type** — filter to a specific advocate type (populated dynamically from data)
- **Licence from / to** — filter by numeric licence number range (supports both ASCII and Nepali digits)

### Filter rows toggle

When **Filter rows** is checked (default), only matching records are shown. Uncheck to highlight matches in a full-data view.

### Sort

Choose any column and direction (A–Z, Z–A, Ascending, Descending), then click the funnel button. Licence numbers and dates use smart numeric/date sorting. Click the reset button to restore original data order.

---

## Navigation

### Ribbon — Compass dropdown

| Control | Action |
|---|---|
| ↑ / ↓ arrows | Jump between highlighted search matches on the current page |
| Match counter | Shows current match position, e.g. `3/25` |
| ⏮ ◀ ▶ ⏭ | First / previous / next / last page |
| Go to page | Type a page number and press Enter or click → |
| Records info | Shows filtered count / total and current page |

### Bottom pager

The same first / previous / next / last page buttons appear below the table for convenience.

### Back to top button

A floating button appears in the bottom-right corner after scrolling down 500 px.

---

## Column Visibility

Open **Settings → Show / Hide Columns** to toggle individual columns on or off. At least one column must remain visible (the last checked column cannot be unchecked).

Available columns: S.N., Licence No, Name, English Name, Address, Sex, Type, Issuedate.

---

## Keyboard Shortcuts

| Shortcut | Action |
|---|---|
| `Enter` (in search box) | Run search |
| `Enter` (in Go to page box) | Jump to that page |
| `Alt + →` | Next search match |
| `Alt + ←` | Previous search match |
| `Escape` | Close any open dropdown menu |

---

## Theme

Click the **moon/sun icon** in the top-right of the header to toggle between light and dark mode. The chosen theme is saved in `localStorage` under the key `advTheme` and restored automatically on next visit.

---

## Copying Data

### Copy a single name
Hover over any row — a copy icon appears next to the name. Click it to copy that advocate's name (with श्री prefix if enabled) to the clipboard.

### Copy entire table
Open **Settings** and click the teal **clipboard** button. The full filtered and sorted dataset (all pages, visible columns only) is copied as tab-separated text, ready to paste into Excel or Google Sheets.

### श्री prefix toggle
The **श्री** pill in the ribbon prepends `श्री ` to Nepali names in the display and in copied text. Toggle it off for plain names.

---

## URL Parameters

| Parameter | Example | Description |
|---|---|---|
| `data` | `?data=file1.js,file2.js` | Comma-separated list of data files to load |
| `files` | `?files=file1.js` | Alias for `data` |

Paths must be relative to the HTML file. Paths containing `..` or absolute URLs are rejected for security.

---

## Browser Support

Any modern browser released after 2020:

- Chrome / Edge 90+
- Firefox 88+
- Safari 14+

Internet Explorer is not supported.

---

## External Dependencies

All loaded from CDN — only required on first visit if cached by the browser.

| Library | Version | Purpose |
|---|---|---|
| [Google Fonts](https://fonts.google.com) | — | Playfair Display (headings), Instrument Sans (body) |
| [Phosphor Icons](https://phosphoricons.com) | 2.1.1 | All toolbar and UI icons |

No JavaScript framework, no npm, no bundler.

---

## Project Structure

```
your-folder/
├── index.html              ← This file (the entire application)
├── advocatedata1.js        ← Data chunk 1 (required)
├── advocatedata2.js        ← Data chunk 2 (optional)
├── advocatedata3.js        ← Data chunk 3 (optional)
├── …
├── senioradvocate.js       ← Senior advocate records (optional)
└── advocatedatafiles.js    ← Optional manifest listing all files
```

Data files must stay in the **same directory** as `index.html` unless you use the `?data=` query parameter with explicit filenames, or update the `COMMON_CUSTOM_DATA_FILES` / `OPTIONAL_DATA_MANIFEST` constants near the top of the script.

---

## Customisation Reference

The following constants at the top of the `<script>` block can be edited directly in the HTML file:

```js
const DATA_CHUNK_PREFIX = 'advocatedata';   // filename prefix for sequential chunks
const DATA_CHUNK_EXT    = '.js';            // file extension
const MAX_CHUNK_FILES   = 300;              // how many sequential files to attempt
const STOP_AFTER_MISSES = 1;               // stop after this many consecutive 404s
const COMMON_CUSTOM_DATA_FILES = ['senioradvocate.js']; // always-attempted files
const OPTIONAL_DATA_MANIFEST   = 'advocatedatafiles.js'; // manifest filename
```

CSS design tokens (colours, radii, shadows) are all defined as CSS custom properties in `:root` and `html[data-theme=dark]` at the top of the `<style>` block, making visual customisation straightforward.

---

## Licence Audit Report

Licence NoTypeदोहोरिएका नामहरू15अधिवक्ताश्री शुरेन्द्र बहादुर बस्न्यात क्षेत्री क; श्री अजिम कुमार नाग19अधिवक्ताश्री विरेन्द्र पुरुष ढकाल; श्री कुञ्ज विहारी प्रसाद सिंह (क)29अधिवक्ताश्री जगन्नाथ प्रसाद लामिछाने; श्री बलरामप्रसाद सिंह48अधिवक्ताश्री नगेन्द्रनाथ शर्मा (ख); श्री कुमार प्रसाद शर्मा (क)784अधिवक्ताश्री कन्हैया लाल वैद्य; श्री लक्ष्मी नारायण शर्मा971अधिवक्ताश्री दिनेश यादव (क); श्री माधव कुमार क्षेत्री (क)1240अधिवक्ताश्री राम पदारथ शाह (ख); श्री जय बहादुर तामाङ्ग (क)1397अधिवक्ताश्री बालकृष्ण अधिकारी (ख); श्री राजकिशोर सिंह (क)1410अधिवक्ताश्री विश्वनाथ श्रेष्ठ; श्री विजयमान श्रेष्ठ (क)1512अधिवक्ताश्री लक्ष्मण प्रसाद खरेल (ख); श्री शिवप्रसाद उपाध्याय (क)1591अधिवक्ताश्री कर्णबहादुर थापा; श्री अनन्तराज उपाध्याय (क)1657अधिवक्ताश्री नविन कुँवर; श्री पुष्पराज कडेल (क)1684अधिवक्ताश्री टंक प्रसाद सापकोटा; श्री चिरञ्जीवी प्रसाद शर्मा (ख)1864अधिवक्ताश्री प्रमोदकुमार वैद्य श्रेष्ठ (क); श्री हरिकुमार अधिकारी1957अधिवक्ताश्री दिननाथ प्रसाद; श्री हरिप्रसाद सुवेदी (क)1974अधिवक्ताश्री चौधरीनागेन्द्रराय यादव (क); श्री हेमराज पराजुली2197अधिवक्ताश्री वावुकाजी श्रेष्ठ; श्री धर्मराज सापकोटा (क)2297अधिवक्ताश्री वावुराजा कार्की; श्री देवी बहादुर घिमिरे2355अधिवक्ताश्री चुढामणि जोशी (ख); श्री मोहन बहादुर कार्की (क)2357अधिवक्ताश्री वसन्तराज अधिकारी (क); श्री अनुरजंन गोइत (ख)2940अधिवक्ताश्री नीलहरी शर्मा (क); श्री प्रेम प्रसाद न्यौपाने2989अधिवक्ताश्री चिरण प्रसाद पराजुली (क); श्री जनार्दन प्रसाद शुक्ल (क)3949अधिवक्ताश्री लक्ष्मीदेवी मानन्धर (ख); श्री काशीनाथ शर्मा सेढाई (क)3992अधिवक्ताश्री विजयलाल ठाकुर (ख); श्री भोजराज रेग्मी (क)4046अधिवक्ताश्री बलेन्द्र बहादुर सिंह; श्री राजेन्द्र प्रसाद रेग्मी (क)4047अधिवक्ताश्री सन्जु थापा (बस्नेत); श्री किरण पौडेल (क)4233अधिवक्ताश्री इश्वरी प्रसाद भट्टराई; श्री भीम बहादुर ढकाल (क)4930अधिवक्ताश्री मनोज कुमार पोखरेल; श्री रामबहादुर राई5228अधिवक्ताश्री महन्त झा; श्री हिरामणि लामिछाने (क)5371अधिवक्ताश्री परशुराम रिजाल; श्री जगदीश शर्मा पौडेल5673अधिवक्ताश्री गोविन्दप्रकाश सुवेदी; श्री रामकिशोर प्रसाद

**जम्मा repeated licence groups:** 31

## २. Missed licence number

१ देखि २१५५८ सम्मको उपलब्ध Licence No हेर्दा छुटेका नम्बरहरू:

**599, 783, 876, 878, 1082, 1313, 1314, 1315, 1316, 1317, 1318, 1319, 1320, 1321, 1322, 1325, 1326, 1327, 1328, 1329, 1330, 1331, 1332, 1333, 1334, 1651, 1778, 1858, 2187, 2299, 2576, 2697, 2837, 3555, 3948, 4553, 4673, 4926, 5138, 5379, 5674, 5847, 5848, 5849, 5850, 5851, 5852, 5853, 5854, 5855, 5856, 6004, 6317, 9071, 12010, 12629, 14209, 14930**

**जम्मा missed licence number:** 58

---

*Made with ❤ for the Nepal legal community.*
