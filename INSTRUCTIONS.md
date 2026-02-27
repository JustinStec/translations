# How to Translate a Hebrew Book to English

A step-by-step guide for using the translation notebook. No programming
experience required.

---

## What You Need

- A **Google account** (any Gmail address works)
- A **web browser** (Chrome works best)
- Your **Hebrew book** as a PDF file or a text (.txt) file
- A **Claude API key** from Anthropic (usage-based pricing, roughly $1-3 per
  average-length book)
- About **30-60 minutes** for a full book, depending on length

---

## Step 1: Get Your Claude API Key

The notebook uses Anthropic's Claude AI for translation. Claude produces
high-quality literary translations that preserve the author's voice, tone, and
style -- including italics and bold formatting from the original.

1. Go to [console.anthropic.com](https://console.anthropic.com/).
2. Create an account and add a payment method under **Billing**.
3. Go to [API Keys](https://console.anthropic.com/settings/keys) and click
   **Create Key**.
4. Copy the key (it starts with `sk-ant-...`). You will paste this into the
   notebook later.

**Important:** Treat your API key like a password. Do not share it with anyone
or post it online.

---

## Step 2: Open the Notebook in Google Colab

Google Colab is a free tool from Google that lets you run code in your browser.
You do not need to install anything.

1. Click this link to open the notebook:

   [Open in Google Colab](https://colab.research.google.com/github/JustinStec/translations/blob/main/hebrew_english_translation_colab.ipynb)

2. If prompted, sign in with your Google account.

3. You should see a page that looks like a document with gray code boxes. Each
   gray box is called a **cell**. You will run these cells one at a time, from
   top to bottom.

---

## Step 3: Set Up the GPU (Makes It Run Faster)

The notebook uses a GPU (a special processor) for reading the PDF. You need to
turn this on:

1. In the menu bar at the top, click **Runtime**.
2. Click **Change runtime type**.
3. Under **Hardware accelerator**, select **A100 GPU** from the dropdown. If
   A100 is not available, select **T4 GPU** instead.
4. Click **Save**.

You should see a small note in the top-right corner that says "A100" or "T4".
This is free to use (Google gives you limited GPU hours per day).

---

## Step 4: Run the Notebook (Cell by Cell)

Now you will run each cell in order. To run a cell, either:

- Click the **play button** on the left side of the cell, or
- Click inside the cell and press **Shift + Enter** on your keyboard.

After you run a cell, wait for it to finish before moving on. You will know it
is done when:

- The spinning circle next to the cell stops.
- A green checkmark appears.
- You see output text below the cell.

Here is what each cell does:

### Cell 1: Install dependencies

Click play and wait about 1-2 minutes. You will see installation messages
scroll by. This is normal. You may see some red "ERROR" text about version
conflicts at the end -- this is harmless, you can ignore it.

### Cell 2: Configuration

This is where you set up Claude. Before running it:

1. Find the line that says:

   ```
   TRANSLATION_BACKEND = "marianmt"
   ```

   Change `"marianmt"` to `"claude"` (keep the quotes):

   ```
   TRANSLATION_BACKEND = "claude"
   ```

2. Find the line that says:

   ```
   CLAUDE_API_KEY = ""
   ```

   Paste your API key between the quotes, like:

   ```
   CLAUDE_API_KEY = "sk-ant-your-key-here"
   ```

3. Click play to run the cell.

### Cell 3: Initialize Marker (PDF reader)

Click play. This loads Marker, a high-quality PDF text extraction tool. It
takes 1-2 minutes to download and load its models.

You should see "Marker models loaded and ready." when it finishes.

If you are uploading a `.txt` file instead of a PDF, this cell still needs to
run but its output will not be used.

### Cell 4: Upload your book

Click play. A file upload button will appear:

1. Click **Choose Files** (or **Browse**).
2. Find your Hebrew book file on your computer.
   - **PDF files** work -- text will be extracted using Marker with
     GPU-accelerated OCR, so even scanned PDFs should work well. The notebook
     preserves formatting (italics, bold) from the original.
   - **TXT files** also work (must be saved as UTF-8).
3. Select the file and click Open.
4. Wait for the upload to finish. For PDFs, Marker will run OCR on every page,
   which takes a few minutes depending on the book's length.

You will see a preview of the extracted text. **Check that the Hebrew looks
correct.** If it is garbled or mostly blank, the PDF may have unusual
formatting that Marker cannot handle (see Troubleshooting below).

### Cell 5: Load translation model

Click play. This frees the GPU memory used by Marker and connects to the
Claude API.

You should see "Claude client initialized."

### Cell 6: Translation functions + glossary

Click play. This sets up the translation logic. You will see a quick test
translation to confirm everything is working:

```
Test input:  שלום עולם. זה משפט פשוט לבדיקה.
Test output: Hello world. This is a simple sentence for testing.
```

**Optional -- custom glossary:** If there are specific Hebrew words you want
translated a certain way every time, scroll up in this cell and find the
`GLOSSARY` section. You can add entries like:

```python
GLOSSARY = {
    "שירה": "poetry",
    "סופר": "author",
}
```

You can come back and edit this later if you notice inconsistent translations.

### Cell 7: Detect chapters

Click play. The notebook will scan your book for chapter headings and split the
text automatically. You will see something like:

```
my_book.txt: 12 chapter(s) detected
  פרק א (3,412 words)
  פרק ב (2,876 words)
  ...

Total chapters to translate: 12
Total words: 35,420
```

If the chapter detection does not look right (for example, it detected 1
chapter when there should be 12), that is okay -- the translation will still
work, it will just translate the whole book as one piece.

### Cell 8: Translate

Click play. This is the main step and will take the longest.

The notebook translates **one paragraph at a time**, so that the Hebrew and
English stay aligned side by side in the output. Claude uses the preceding
paragraph as context to keep the translation consistent.

- Roughly 1 page per minute. A 200-page book takes about 2-3 hours.
- Cost is roughly $1-3 for an average-length book.

You will see a progress bar and status updates. The notebook saves progress
automatically, so **if Colab disconnects or you accidentally close the tab**,
just come back, re-run cells 1-7, and Cell 8 will pick up where it left off.

**Tip:** Keep the Colab tab in the foreground while it runs. Colab disconnects
idle sessions after about 30-90 minutes.

### Cell 9: Preview your translation

Click play to see the Hebrew and English side by side in a **paragraph-aligned
table** -- each row is one paragraph, Hebrew on the left (right-to-left),
English on the right. *Italics* and **bold** from the original are preserved.

You can change which chapter to preview by editing the number in the last line:

```python
preview_chapter(0)   # First chapter
preview_chapter(1)   # Second chapter
preview_chapter(5)   # Sixth chapter
```

### Cell 10: Export and download

Click play. This creates four files and automatically downloads them to your
computer:

| File | What It Is |
|------|------------|
| `translation_english.txt` | English-only plain text |
| `translation_bilingual.txt` | Paragraph-aligned Hebrew + English plain text |
| `translation_bilingual.docx` | Two-column Word document (Hebrew left, English right) with formatting |
| `translation_bilingual.html` | Paragraph-aligned HTML -- open in a browser, looks like a bilingual book |

The **HTML file** is the best way to read the translation. Open it in any web
browser and you will see a clean two-column layout with each Hebrew paragraph
next to its English translation. Italics and bold are rendered properly. You
can also print it directly from the browser.

The **Word document** has the same two-column table layout and can be edited
further if needed.

If the download does not start automatically, look for a popup blocker
notification in your browser and allow the downloads.

---

## Troubleshooting

### "WARNING: No GPU detected"

Go to **Runtime > Change runtime type > A100 GPU** (or T4 GPU) and click Save.
Then go to **Runtime > Restart and run all** to restart from the beginning.

### The upload button does not appear

Try a different browser (Chrome works best). Safari sometimes blocks the upload
widget.

### Marker OCR produced garbled or poor-quality text

Some PDFs have unusual encoding or are heavily image-based. If the preview in
Cell 4 looks wrong, try:

- A different PDF version of the same book, if available.
- Converting the PDF to images first, then back to PDF (this sometimes helps
  Marker process it better).
- Using an online Hebrew OCR service to produce a `.txt` file, then uploading
  that instead.

### Colab disconnected in the middle of translating

This happens if you leave the tab idle for too long. Just:

1. Re-open the notebook.
2. Go to **Runtime > Run all**.
3. When Cell 4 asks to upload, upload the same file again.
4. Cell 8 will automatically resume from where it left off (your progress was
   saved).

### "CUDA out of memory" error

Go to **Runtime > Restart runtime**, then re-run all cells. This clears the
GPU memory. If it keeps happening, try selecting an A100 GPU (which has more
memory).

---

## Tips for the Best Results

1. **Try one chapter first.** Before translating the whole book, run just one
   chapter to check the quality. You can re-run Cell 8 later for the full book
   and it will skip the chapter you already translated.

2. **Review and edit.** Machine translation is a starting point, not a finished
   product. Review the output and edit as needed, especially for literary
   works.

3. **Use the glossary.** If you notice a name or term being translated
   inconsistently, add it to the GLOSSARY dictionary in Cell 6 and re-run the
   translation.

4. **Save your work.** Download the exported files as soon as the translation
   finishes. Colab sessions are temporary and your files will be deleted when
   the session ends.
