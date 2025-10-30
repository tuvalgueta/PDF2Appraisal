# PDF2Appraisal

You are a senior full-stack developer specializing in browser-based document automation. Build a **single HTML file** (no backend, no Node.js) that runs entirely in the browser (Chrome) and does the following:


### INPUT:
1. **שומה.docx** – a Word document (complex layout: tables, images, headers, footers, Hebrew RTL, signatures, logos).

2. **נסח טאבו.pdf** – a scanned PDF (from Israeli Land Registry), possibly with tables, headers, and mixed text/image content.



---



### GOAL:

- Extract specific fields from the **נסח טאבו (PDF)** using **OCR when needed**.

- **Update the original שומה.docx** by replacing placeholder tags like `{{GUSH}}`, `{{HELKA}}`, etc., with real values.

- **Preserve 100% of formatting, images, tables, headers, footers, and RTL Hebrew layout**.

- **Download the updated DOCX** with a new name: `שומה_מעודכנת_YYYY-MM-DD.docx`.



---



### REQUIRED FIELDS TO EXTRACT (with exact regex patterns that work on OCR output):



| Field | Placeholder | Regex (for OCR text) | Example |

|------|-------------|----------------------|--------|

| גוש | `{{GUSH}}` | `/גוש\D*(\d{4,6})/i` → fallback: `/\b6146\b/` | 6146 |

| חלקה | `{{HELKA}}` | `/חלקה\D*(\d{1,4})/i` → fallback: `/\b321\b/` | 321 |

| תת חלקה | `{{TAT_HELKA}}` | `/ת\D*חלקה\D*(\d{1,4})/i` → fallback: `/\b106\b/` | 106 |

| שטח דירה | `{{AREA_APT}}` | `/\b112\b/` | 112 |

| מרפסת גג | `{{ROOF_TERRACE}}` | `/57[.,]?2/` | 57.2 |

| חניה | `{{PARKING_AREA}}` | `/9[.,]?89/` | 9.89 |

| סימון חניה | `{{PARKING_MARK}}` | `/\bסא\b/` | סא |

| חלק ברכוש משותף | `{{COMMON_SHARE}}` | `/9\s*\/\s*935/` | 9/935 |

| שטח חלקה | `{{LOT_AREA}}` | `/5[,]?153/` | 5153 |

| תיק בית משותף | `{{HOUSE_FILE}}` | `/327\s*\/\s*05/` | 327/05 |



---



### TECHNICAL REQUIREMENTS (MUST USE):



1. **`docxtemplater` + `pizzip`** – ONLY way to generate **real .docx** with full formatting preserved.

   - Load DOCX → apply template tags → export as blob → download.

2. **`pdfjs-dist`** – extract text from searchable PDFs.

3. **`Tesseract.js` (heb traineddata)** – OCR for scanned pages (scale: 2.0, logger for progress).

4. **`FileSaver.js`** – trigger download.

5. **No mammoth.js** – it breaks formatting.

6. **Hebrew RTL support** – `dir="rtl"`, proper font rendering.

7. **Progress bar** with clear Hebrew messages:

   - "קורא PDF..."

   - "דף 1 סרוק – מפעיל OCR... 67%"

   - "מעדכן תגיות..."

   - "יוצר קובץ..."



---



### UI (Hebrew, clean, professional):



```html

<h1>עדכון שומה מנסח טאבו</h1>

1. קובץ שומה (DOCX) עם תגיות {{GUSH}} וכו'

<input type="file" id="docx" accept=".docx">



2. נסח טאבו (PDF) – גם סרוק!

<input type="file" id="pdf" accept=".pdf">



<button onclick="process()">עדכן שומה</button>



<div id="progress"></div>

<div id="status"></div>
