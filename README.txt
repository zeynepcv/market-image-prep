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
The OCR engine and its English language data are about 20 MB, served
from this folder. The browser downloads them the first time someone
presses "Detect languages" and caches them afterwards. Nothing is
fetched from the internet at any point.


How the language detection works
--------------------------------
The page crops the top third of each image, upscales it, and runs OCR on
it. It then identifies the language with two statistical n-gram models
(franc and tinyld) restricted to your market list. Two batch-level tricks
make this reliable on headlines only a few words long:

  - Words appearing in most images of the batch are the campaign name
    ("Singles' Day"), not a language signal, so they are removed first.
  - A campaign set carries one image per market, so when there are no
    more images than market codes, each code is assigned once. This
    settles near-ties: if one image is confidently Spanish, another
    ambiguous one is no longer competing for it.

Anything the page is not confident about is flagged, and the text it read
is shown beside each file. Always glance at those readings before
downloading.

Note on translation: the page identifies the language but does not
translate. Offline translation would need a model far larger than the
whole site, so it is not practical here.


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
  vendor/langid.js            language identification models
  tessdata/eng.traineddata    the OCR language model

Licences: Tesseract.js and JSZip are open source (Apache-2.0 and
MIT/GPL respectively); their licence files ship inside the minified
builds.
