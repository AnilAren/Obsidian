

| Library                     | Best for                                                    | Pros                                                                                                                | Cons                                                                                 |
| --------------------------- | ----------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------ |
| **pypdf** (formerly PyPDF2) | Text extraction where layout isn’t critical                 | ✅ Light, fast, minimal dependencies. ✅ Works fine when PDF is mostly linear text (like tutorial pdf).               | ❌ No positional data (x/y coords), so layout like tables or columns may merge oddly. |
| **pdfplumber**              | When PDF has complex layout (tables, multiple columns, OCR) | ✅ Extracts positional info (`x0, y0`), can handle columns & detect tables. ✅ Can remove noise using bounding boxes. | ❌ Slower, heavier dependency chain. ❌ Slightly more memory usage.                    |
