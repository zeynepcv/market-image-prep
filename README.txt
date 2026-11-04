Market Image Prep — standalone website
======================================

What it does
------------
Drop in a set of campaign images, one per market. The page:

  1. reads the headline in each image and works out the language
  2. compresses each file under a size cap WITHOUT changing pixel dimensions
  3. names them CODE_Campaign.jpg (EN_Macro26.jpg, DE_Macro26.jpg, …)
  4. hands them back as a ZIP

Everything runs inside the visitor's browser. No account, no sign-in,
no API key, no server. No image or text is ever uploaded anywhere.


How to put it online
--------------------
Upload the CONTENTS of this folder (index.html, vendor/, tessdata/)
to any static web host, keeping the folder structure intact.

Free options that work as-is:
  - Netlify Drop  — drag the folder onto app.netlify.com/drop
  - GitHub Pages  — push the folder, enable Pages in the repo settings
  - Any internal web server, IIS, SharePoint site or network web folder

It must be served over http:// or https://. Opening index.html directly
from disk (file://) will NOT work: browsers block the OCR engine's
worker on file:// URLs. If you need an offline copy on one machine,
run a tiny local server in the folder instead, e.g.:

  python -m http.server 8080      (then open http://localhost:8080)


First load
----------
The OCR engine and its English language data are about 17 MB, served
from this folder. The browser downloads them the first time someone
presses "Detect languages" and caches them afterwards. Nothing is
fetched from the internet at any point.


How the language detection works
--------------------------------
The page crops the top third of each image, raises the contrast so
white headline text becomes black on white, and runs OCR on it. It
then scores the words against the market languages — distinctive words
first ("ontdek", "upptäck", "zachyťte"), then accented characters
(ñ, ł, ř, ã, ß, å). A confident match fills the code in; anything
uncertain is flagged so you can fix it with "Assign by hand".

Accuracy depends on the headline being large and legible, which it is
in these templates. Always glance at the reading it shows next to each
file before downloading.


Changing the market list
------------------------
Edit the "Market codes" box on the page — it is remembered per browser.
The page knows these languages for detection:

  EN DE FR IT ES PT NL PL CZ FI SE HU

To teach it another language, open index.html and add an entry to the
LANG object near the top of the <script> block: a few distinctive words
("strong") and a few common ones ("weak"). Add any unique accented
characters to the CHARS list below it.


Folder contents
---------------
  index.html                  the whole app — layout, logic, styling
  vendor/tesseract.min.js     OCR library
  vendor/worker.min.js        its background worker
  vendor/core/                the OCR engine (WebAssembly)
  vendor/jszip.min.js         ZIP packing
  tessdata/eng.traineddata.gz the OCR language model

Licences: Tesseract.js and JSZip are open source (Apache-2.0 and
MIT/GPL respectively); their licence files ship inside the minified
builds.
