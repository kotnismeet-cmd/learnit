# LearnIt

A Duolingo-style adaptive learning app. Give it a topic and/or your own study material, and it builds a course with bite-sized lessons, mastery quizzes, and a tutor chatbot grounded in your sources.

## Features
- **Knowledge gathering**: Claude web search + Wikipedia, plus your PDF/DOCX/TXT/MD uploads and pasted URLs. Add more sources any time.
- **Duolingo-style path**: units and lessons that unlock in order, teaching cards, then 8-10 exercises (multiple choice, true/false, fill-in, ordering, matching), hearts, XP and a daily streak. Missed questions come back at the end of the lesson.
- **Mastery quizzes**: freshly generated each time, weighted toward the concepts you keep missing.
- **Mastery map**: per-concept accuracy so you can see weak spots.
- **Tutor chatbot**: retrieves from your sources (BM25), cites them as [S1], can search the web on request, and quizzes you back.

## Run
```bash
cd learnit
python -m venv .venv && source .venv/bin/activate   # Windows: .venv\Scripts\activate
pip install -r requirements.txt
cp .env.example .env        # then put your ANTHROPIC_API_KEY in it
python app.py
```
Open http://127.0.0.1:5000

## Notes
- Data lives in `learnit.db` (SQLite) next to the app.
- Course creation takes about a minute (web research + outline). Lessons are generated on first open, then cached.
- If the web search tool isn't enabled on your API account, the app falls back to Wikipedia plus your own material.
- Answers are checked in the browser, which is fine for a personal app. Move grading server-side if you ever expose it to others.

## Running it publicly
- **Separate data per visitor:** each browser gets a private random id (cookie). Courses, XP and streaks belong to it, and other visitors cannot open or delete them. There are no passwords, so clearing cookies or switching device starts a fresh account. Add real logins if you need progress to follow people around.
- **Daily limits** protect your API budget. Change them with environment variables: `LIMIT_COURSES_PER_DAY` (3), `LIMIT_LESSONS_PER_DAY` (40), `LIMIT_QUIZZES_PER_DAY` (15), `LIMIT_CHAT_PER_DAY` (60), `LIMIT_SOURCES_PER_DAY` (10), `MAX_COURSES_PER_USER` (10). Each is also capped per network address.
- **Safe URLs:** pasted links to internal or private addresses are refused.
- Set a monthly spending limit in the Anthropic console as a second safety net.
- Deploy with `render.yaml` (Render) or the `Procfile`; set `ANTHROPIC_API_KEY` as a secret.
