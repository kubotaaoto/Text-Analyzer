# Text Analyzer — Learn English with Immersion

A web app that finds vocabulary worth learning in the things you already read and watch. Upload a subtitle file, e-book, or document (or paste text), and Text Analyzer pulls out advanced words and ranks every word by how rare it is, so you can study the vocabulary that actually shows up in your media.

Signed-in users can mark words they already know; those words are saved to their account and greyed out in future analyses.

## Features

- **Multi-format input** — SRT subtitles, EPUB, PDF, DOCX, RTF, and TXT files, or plain pasted text
- **Lemmatization** — words are reduced to their dictionary form with spaCy (`running` → `run`), so variants count as one word
- **Advanced-word detection** — matches the text's vocabulary against a GRE word list
- **Frequency ranking** — sorts words from least to most common using an English word-frequency list
- **Text stats** — total words, unique words, and number of advanced words
- **Known-word tracking** — log in with email/password or Google, select the words you know, and they're stored in Firestore
- **Demo mode** — try it instantly with a bundled subtitle file from *Suits* 

## How It Works

1. The frontend sends a file (`/api/analyzeFile`) or text (`/api/analyzeText`) to the Flask backend.
2. The backend extracts plain text with a format-specific extractor (e.g. strips timestamps from SRT files, reads the HTML chapters inside an EPUB).
3. spaCy lemmatizes the text and collects the unique words.
4. Each word is checked against `advanced_words.csv` (GRE list) and looked up in `Frequency_FLT.json` (frequency rank).
5. The frontend displays the stats, the advanced words, and the frequency-sorted word list.

## Project Structure

```
Text-Analyzer/
├── app.py                  # Flask API: text extraction + vocabulary analysis
├── advanced_words.csv      # GRE word list used for "advanced" detection
├── Frequency_FLT.json      # English word-frequency ranks
└── frontend/
    ├── public/             # Static assets + demo .srt file
    └── src/
        ├── App.js          # Routes: /, /login, /signup
        ├── AuthContext.js  # Firebase auth state provider
        ├── firebase.js     # Firebase config (fill in your own)
        ├── useTextAnalyzer.js  # Hook that calls the backend API
        ├── Components/     # Header, Footer, Input, Results
        └── Pages/          # Home, Login, Signup
```

