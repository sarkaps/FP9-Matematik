# FP9 Matematik Test Generator
 
A Claude skill that automatically extracts FP9 Matematik practice tests from [studienet.dk](https://www.studienet.dk) and generates additional exam-ready PDF test packs — all in Danish, following the official FP9 (Folkeskolens Prøver, 9. klasse) exam format.
 
---
 
## What it does
 
1. **Scrapes** a FP9 Matematik test from studienet.dk using the Chrome browser
2. **Extracts** all 20 Opgaver (~50 sub-questions) with their contexts and answers
3. **Generates** N additional original practice tests (default: 10) based on the same structure and difficulty
4. **Produces PDFs** in three variants per test:
   - Questions only (exam-ready, with blank answer spaces)
   - Questions with embedded answers (self-study)
   - Standalone answer key / facitliste
5. **Packages** everything into a single ZIP file
 
---
 
## Exam structure
 
Every generated test mirrors the official FP9 format:
 
| Section | Opgave | Topics |
|---|---|---|
| Tal og algebra | 1 – 10 | Arithmetic, fractions, percentages, ratios, speed/distance, equations, coordinates, algebraic expressions |
| Geometri og måling | 11 – 17 | Unit conversion, similar figures, angles, area, perimeter, volume, surface area, transformations |
| Statistik og sandsynlighed | 18 – 20 | Bar charts, frequency tables, mean/median/mode, probability |
 
Each test has **20 Opgaver** with **2–4 sub-questions** each (~50 sub-questions total), all in Danish.
 
---
 
## Output files
 
For a run generating 10 tests, the ZIP contains **31 files**:
 
```
FP9_Matematik_Tests.zip
├── FP9_Matematik_Original_Test.pdf       ← extracted from studienet.dk
├── FP9_Test_01.pdf                       ← questions only
├── FP9_Test_01_med_svar.pdf              ← questions + answers
├── FP9_Test_01_svarnoegle.pdf            ← answer key
├── FP9_Test_02.pdf
│   ...
└── FP9_Test_10_svarnoegle.pdf
```
 
---
 
## How to trigger the skill
 
Just describe what you need — examples that will trigger this skill:
 
```
Go to studienet.dk/fp9.../opgavesaet-1, extract the test and make 10 practice tests. Zip everything.
 
Make 5 FP9 math practice tests with answer banks.
 
Extract the FP9 Matematik sample test from this URL into a PDF.
 
Generate Danish 9th grade math exam tests — I need 10 with facitlister.
```
 
---
 
## Skill files
 
```
fp9-matematik-tests/
├── SKILL.md                  ← skill instructions for Claude
├── README.md                 ← this file
└── scripts/
    └── pdf_generator.py      ← reusable PDF generation script
```
 
### `SKILL.md`
Contains the full step-by-step workflow: how to scrape the source page, structure test data, call the PDF generator, and package the output. Claude reads this automatically when the skill triggers.
 
### `scripts/pdf_generator.py`
A standalone ReportLab-based PDF renderer. Accepts JSON test data and produces all three PDF variants. Can also be run directly from the command line:
 
```bash
# Install dependency
pip install reportlab
 
# Generate PDFs
python scripts/pdf_generator.py \
  --original-data original_test.json \
  --new-tests-data new_tests.json \
  --output-dir ./outputs/
```
 
**Input JSON format** (`original_test.json` / entries in `new_tests.json`):
 
```json
[
  {
    "num": 1,
    "section": "Tal og algebra",
    "context": "Herunder ses et menukort:\nBuffet: 349 kr. | Drikkevarer: 199 kr.",
    "sub": [
      { "q": "1.1", "text": "Hvad koster buffet og drikkevarer tilsammen?", "answer": "548 kr." },
      { "q": "1.2", "text": "Hvad koster All inclusive om mandagen?",        "answer": "449 kr." }
    ]
  }
]
```
 
`new_tests.json` is a list of tests (each following the same structure above).
 
---
 
## PDF design
 
| Element | Style |
|---|---|
| Cover page | Dark blue title banner, info table, amber instruction box |
| Section banners | Mid-blue full-width banner |
| Context boxes | Light grey, italic text, thin border |
| Answer spaces (questions-only) | 18px gap + thin HR rule |
| Answer boxes (with-answers) | Green-tinted box with "Svar:" label |
| Answer key | Two-column table with alternating row shading |
| Font | Helvetica (built-in, no installation needed) |
| Page size | A4 |
 
---
 
## Requirements
 
- **Python 3.8+**
- **ReportLab** (`pip install reportlab`)
- **Chrome browser** with Claude in Chrome extension (for scraping studienet.dk)
 
---
 
## Source
 
Default test URL:
```
https://www.studienet.dk/fp9-matematik/opgaver-du-kan-oeve-dig-paa/uden-hjaelpemidler/opgavesaet-1
```
 
The skill works with any Opgavesæt URL from the studienet.dk FP9 Matematik section. Note that full answer content on studienet.dk requires a membership — this skill uses the question text that is publicly visible and independently generates correct answers for all new practice tests.
