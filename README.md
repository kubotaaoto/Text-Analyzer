# Text Analyzer — Learn English with Immersion

A web app that finds vocabulary worth learning in the things you already read and watch. Upload a subtitle file, e-book, or document (or paste text), and Text Analyzer pulls out advanced words that are not too easy, but also not too obscure. 

<img width="1199" height="799" alt="スクリーンショット 2026-11-02 午後2 18 05" src="https://github.com/user-attachments/assets/938278c6-3a46-4a20-83f6-b9645baeec5f" />

**👉 Try it live: [find-advanced-words.web.app](https://find-advanced-words.web.app/)**

## Using the App
1. Open the [live app](https://find-advanced-words.web.app/).
2. Upload a file (SRT, EPUB, PDF, DOCX, RTF, or TXT) or paste text, then click **Analyze**.
   No file handy? Click **Try with demo data** to load a *Suits* subtitle file.
3. Review the results: total and unique word counts, the advanced words found, and every word ranked from rarest to most common.
4. *(Optional)* Sign up or log in with email or Google, click the advanced words you already know, and hit **Submit**. They'll be greyed out in every future analysis.

## Features

- **Multi-format input** — SRT subtitles, EPUB, PDF, DOCX, RTF, and TXT files, or plain pasted text
- **Lemmatization** — words are reduced to their dictionary form with spaCy (`running` → `run`), so variants count as one word
- **Advanced-word detection** — matches the text's vocabulary against a GRE word list
- **Frequency ranking** — sorts words from least to most common using an English word-frequency list
- **Text stats** — total words, unique words, and number of advanced words
- **Known-word tracking** — log in with email/password or Google, select the words you know, and they're stored in Firestore
- **Demo mode** — try it instantly with a bundled subtitle file from *Suits*

## Tech Stack
 
| Layer | Tools |
|---|---|
| Frontend | React 19, React Router, Tailwind CSS, lucide-react |
| Backend | Python, Flask, Flask-CORS |
| NLP | spaCy (`en_core_web_sm`) |
| File parsing | pdfplumber, python-docx, striprtf, BeautifulSoup |
| Auth & data | Firebase Authentication, Cloud Firestore |
| Hosting | Firebase Hosting (frontend), Render (backend, gunicorn) |

## How It Works

1. The frontend sends a file (`/api/analyzeFile`) or text (`/api/analyzeText`) to the Flask backend.
2. The backend extracts plain text with a format-specific extractor (e.g. strips timestamps from SRT files, reads the HTML chapters inside an EPUB).
3. spaCy lemmatizes the text and collects the unique words.
4. Each word is checked against `advanced_words.csv` (GRE list) and looked up in `Frequency_FLT.json` (frequency rank).
5. The frontend displays the stats, the advanced words, and the frequency-sorted word list.

## API Reference
 
| Method | Endpoint | Body | Description |
|---|---|---|---|
| `POST` | `/api/analyzeFile` | `multipart/form-data` with a `file` field | Analyze an uploaded file |
| `POST` | `/api/analyzeText` | JSON `{ "text": "..." }` | Analyze raw text |
| `GET` | `/health` | — | Health check |
 
Example response:
 
```json
{
  "total_num": 5123,
  "unique_num": 1042,
  "advanced_words": ["abate", "candor", "..."],
  "sorted_words": [
    { "word": "subpoena", "frequency": 24871 },
    { "word": "the", "frequency": 1 }
  ]
}
```
 
`frequency` is a rank — a higher number means a rarer word.
 
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
        ├── firebase.js     # Firebase config
        ├── useTextAnalyzer.js  # Hook that calls the backend API
        ├── Components/     # Header, Footer, Input, Results
        └── Pages/          # Home, Login, Signup
```

