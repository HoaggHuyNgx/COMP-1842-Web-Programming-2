# Vocab Builder

A full-stack vocabulary learning application for studying English &harr; German word pairs. Built as a Vue.js single-page application backed by an Express REST API and MongoDB.

---

## Tech Stack

| Layer        | Technology                          |
|--------------|-------------------------------------|
| Front end    | Vue 2, Vue Router 3, Axios          |
| Styling      | Semantic UI CSS                     |
| Notifications | vue-flash-message                  |
| Back end     | Node.js, Express 5                  |
| ORM          | Mongoose                            |
| Database     | MongoDB (local instance)             |

---

## Project Structure

```
vocab-builder/
├── .gitignore
├── README.md
├── front-end/               # Vue.js SPA
│   ├── jsconfig.json
│   ├── package.json
│   ├── public/
│   │   ├── favicon.ico
│   │   └── index.html       # HTML shell
│   └── src/
│       ├── App.vue          # Root component + navigation layout
│       ├── main.js          # Vue bootstrap
│       ├── router.js        # Route definitions
│       ├── helpers.js       # API service layer (Axios)
│       ├── components/
│       │   ├── VocabTest.vue   # Quiz engine
│       │   └── WordForm.vue    # Shared add/edit form
│       └── views/
│           ├── Words.vue    # Word list table
│           ├── New.vue      # Add new word pair
│           ├── Show.vue     # View single word pair
│           ├── Edit.vue     # Edit existing word pair
│           └── Test.vue     # Quiz entry page
└── server/                  # Express API
    ├── package.json
    ├── server.js            # App bootstrap (port 3000)
    └── api/
        ├── controllers/
        │   └── vocabController.js   # CRUD request handlers
        ├── models/
        │   └── vocabModel.js        # Mongoose schema + model
        └── routes/
            └── vocabRoutes.js       # Route definitions
```

---

## Database Schema

**Collection:** `vocab-builder.vocab`

| Field    | Type   | Validation               |
|----------|--------|--------------------------|
| `_id`    | ObjectId | Auto-generated          |
| `english` | String | Required, non-empty      |
| `german`  | String | Required, non-empty      |

---

## API Endpoints

| Method | Path              | Handler                   | Description                |
|--------|-------------------|---------------------------|----------------------------|
| GET    | `/words`          | `list_all_words`          | Return all word documents  |
| POST   | `/words`          | `create_a_word`           | Create a new word pair     |
| GET    | `/words/:wordId`  | `read_a_word`             | Return one word by `_id`   |
| PUT    | `/words/:wordId`  | `update_a_word`           | Update a word by `_id`     |
| DELETE | `/words/:wordId`  | `delete_a_word`           | Delete a word by `_id`     |

---

## Application Screens

| Route               | View         | Description                                     |
|---------------------|--------------|-------------------------------------------------|
| `/words`            | Words.vue    | Table of all word pairs; delete action          |
| `/words/new`        | New.vue      | Form to add a new English/German pair          |
| `/words/:id`        | Show.vue     | Read-only view of a single word pair           |
| `/words/:id/edit`   | Edit.vue     | Pre-filled form to update an existing pair     |
| `/test`             | Test.vue     | Entry gate — requires at least 5 words         |
| `/test` (inside)    | VocabTest.vue| Quiz: German prompt &rarr; English input       |

---

## Data Flow

```
Browser (Vue SPA)
│
├── App.vue ──── navigation + flash messages
│
├── Words.vue ──── GET all / DELETE
│     └── helpers.js (api.getWords / api.deleteWord)
│
├── New.vue ──── POST
│     └── WordForm.vue → emit createOrUpdate
│           └── helpers.js (api.createWord)
│
├── Show.vue ──── GET one
│     └── helpers.js (api.getWord)
│
├── Edit.vue ──── GET one + PUT
│     └── WordForm.vue → emit createOrUpdate
│           └── helpers.js (api.updateWord)
│
└── Test.vue ──── GET all (gate: ≥ 5 words)
      └── VocabTest.vue (quiz engine, no API)

Express API (port 3000)
  └── vocabRoutes → vocabController → Mongoose → MongoDB
```

---

## Getting Started

### Prerequisites

- Node.js
- MongoDB running locally (`mongod`)

### Install dependencies

```bash
# Back end
cd server
npm install

# Front end
cd front-end
npm install
```

### Run the application

```bash
# Terminal 1 — API server (port 3000)
cd server
npm start

# Terminal 2 — Front end dev server (default: port 8080)
cd front-end
npm run serve
```

Open [http://localhost:8080](http://localhost:8080) in your browser.

### Build for production

```bash
cd front-end
npm run build
```

The compiled output will be in `front-end/dist/`.

---

## Known Limitations

- No loading indicators — pages are blank while waiting for API responses
- No reverse quiz mode — test always prompts German and expects English
- No authentication — all operations are open to any user
- CORS is open to all origins — not suitable for production as-is
- `vue-flash-message` registered via standalone `Vue` instance in `helpers.js` instead of the standard `Vue.use()` pattern
