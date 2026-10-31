These are two Tesseract command-line flags passed via pytesseract's config= argument. They control the OCR engine and how it interprets page layout.
Example: "--oem 3 --psm 6"

--oem 3 — OCR Engine Mode
oem = OCR Engine Mode. Tells Tesseract which recognition engine to use.

Value	Engine	Notes
0	Legacy engine only	Original pre-4.0 Tesseract; character-shape based
1	Neural nets LSTM only	Modern deep-learning engine (Tesseract 4+)
2	Legacy + LSTM	Runs both, combines results — slow
3	Default (LSTM + Legacy if available)	Uses LSTM when available, falls back to Legacy otherwise
Why 3: it's the recommended safe default — you get the modern LSTM engine on any Tesseract 4+ install without erroring out on older/partial installs that lack LSTM models for some languages.

--psm 6 — Page Segmentation Mode
psm = Page Segmentation Mode. Tells Tesseract how the text is laid out on the page before running character recognition. Wrong PSM → garbled or missed text.

The full list (there are 14):

Mode	Meaning
0	Orientation and script detection (OSD) only, no OCR
1	Automatic page segmentation with OSD
2	Automatic segmentation, no OSD, no OCR
3	Fully automatic page segmentation, no OSD (Tesseract default)
4	Single column of text of variable sizes
5	Single uniform block of vertically aligned text
6	Single uniform block of text ← used here
7	Single text line
8	Single word
9	Single word in a circle
10	Single character
11	Sparse text — find as much text as possible in no particular order ← the fallback used in this notebook
12	Sparse text with OSD
13	Raw line — bypass Tesseract-specific hacks
Why 6 (default in this notebook): sick-note certificates are structured forms where text flows as one uniform block (aligned rows, consistent font size). PSM 6 tells Tesseract "don't try to detect columns/regions, just OCR this as one block top-to-bottom" — this is the most reliable mode for form-like documents.

Why 11 (fallback): if PSM 6 fails to find a patient name, the layout probably isn't a clean block — it might be a stamped or scattered layout (e.g. rotated seal, text scattered around a form). PSM 11 abandons layout assumptions and just hunts for any legible text anywhere on the page.