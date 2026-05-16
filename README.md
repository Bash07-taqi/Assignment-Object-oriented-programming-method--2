# Assignment-Object-oriented-programming-method--2
Limkokwing Library Digital System (LLDS)
# Limkokwing Library Digital System (LLDS)

> A small, async-first FastAPI service that gives Limkokwing University Library a basic digital backbone — book search, borrowing, returning, and overdue tracking.

Built for **PROG315 — Object-Oriented Programming 2** Individual Assignment, Faculty of Information and Communication Technology, Limkokwing University Sierra Leone.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Python 3.11+](https://img.shields.io/badge/python-3.11+-blue.svg)](https://www.python.org/)
[![FastAPI](https://img.shields.io/badge/FastAPI-async-009688.svg)](https://fastapi.tiangolo.com/)

---

## Why this exists

Paper logbooks and ad-hoc spreadsheets do not scale. This API replaces them with one consistent interface that any client — a web portal, a circulation desk PC, or a future mobile app — can talk to. Search, borrow, return, and overdue tracking become real-time and automatic instead of manual.

This project is aligned with **UN Sustainable Development Goal 4 (Quality Education)**: a digital library reduces friction between students and the materials that support their courses, and lays the groundwork for extending equal access to remote learners.

## Features

- **Search** the catalogue by title, author, category, or availability
- **Borrow** with concurrency-safe inventory (no oversell, even under simultaneous requests)
- **Return** with automatic overdue-fine calculation in Sierra Leonean Leones (SLL)
- **Overdue tracking** in a single GET call
- **Async throughout** — multiple students can be served at the same time
- **Type-annotated** end-to-end and validated by Pydantic
- **Self-documenting** via Swagger UI at `/docs`

## Endpoints

| Method | Path | Purpose |
|---|---|---|
| `GET`   | `/api/v1/books`                       | Search the catalogue |
| `POST`  | `/api/v1/loans`                       | Borrow a book |
| `PATCH` | `/api/v1/loans/{loan_id}/return`      | Return a borrowed book |
| `GET`   | `/api/v1/loans/overdue`               | List overdue loans and accrued fines |

Full request/response schemas are auto-generated at <http://127.0.0.1:8000/docs> once the server is running.

## Quick start

```bash
# 1. Clone
git clone https://github.com/Big-Ram7z/limkokwing-library-api.git
cd limkokwing-library-api

# 2. Create a virtual environment
python -m venv .venv
# Windows:
.venv\Scripts\activate
# macOS / Linux:
source .venv/bin/activate

# 3. Install dependencies
pip install -r requirements.txt

# 4. Run the API
uvicorn main:app --reload
```

Then open <http://127.0.0.1:8000/docs> to try the endpoints interactively.

## Demonstrating concurrency

`demo.py` fires five simultaneous borrow requests at the only copy of *Designing Data-Intensive Applications*. Exactly one succeeds; the rest receive `409 Conflict`. This proves the `asyncio.Lock` around inventory works.

```bash
# Terminal 1
uvicorn main:app --reload

# Terminal 2
python demo.py
```

Expected output:

```
Five students racing for book id 5...

  LU2026-0101  [201]  OK   loan b1f0...
  LU2026-0102  [409]  DENY 'Designing Data-Intensive Applications' is currently unavailable.
  LU2026-0103  [409]  DENY 'Designing Data-Intensive Applications' is currently unavailable.
  LU2026-0104  [409]  DENY 'Designing Data-Intensive Applications' is currently unavailable.
  LU2026-0105  [409]  DENY 'Designing Data-Intensive Applications' is currently unavailable.

Final availability:
  Designing Data-Intensive Applications — 0/1 available
```

## Example requests

**Search for a software book that is available:**
```bash
curl "http://127.0.0.1:8000/api/v1/books?category=Software%20Engineering&available_only=true"
```

**Borrow:**
```bash
curl -X POST http://127.0.0.1:8000/api/v1/loans \
     -H "Content-Type: application/json" \
     -d '{"user_id":"LU2026-0421","book_id":3}'
```

**Return** (use the `loan_id` returned by the borrow call):
```bash
curl -X PATCH http://127.0.0.1:8000/api/v1/loans/<loan_id>/return
```

**List overdue loans:**
```bash
curl http://127.0.0.1:8000/api/v1/loans/overdue
```

## Project layout

```
limkokwing-library-api/
├── main.py            # FastAPI app — all four endpoints
├── demo.py            # Concurrency demonstration script
├── requirements.txt   # Python dependencies
├── README.md          # You are here
├── LICENSE            # MIT
└── .gitignore
```

## Roadmap

- E-book lending module so off-campus students keep equal access (extends SDG 4 reach)
- Persistent storage (SQLite via SQLModel)
- Authentication for staff vs. student roles
- Reservation queue when all copies are out

## License

Released under the [MIT License](LICENSE) — free to use, modify, and redistribute. Designed to qualify as a Digital Public Good.

## Author

**Bash07-taqi!** — PROG315 student, Limkokwing University Sierra Leone
Lecturer: Amandus Benjamin Coker · [@Alusp](https://github.com/Alusp)
