# 🗄️ Database Connection Guide

This document explains how to connect to the shared **Supabase PostgreSQL** database for the Innovation Hub project.

> [!CAUTION]
> **Never share or commit the database password publicly.** Get the password directly from the project owner (via a private message or password manager). Never paste it into GitHub, Discord chats, or any public channel.

---

## 📋 Connection Details

| Field | Value |
| :--- | :--- |
| **Host** | `db.oiuwldqrjvkijfrnyclc.supabase.co` |
| **Port** | `5432` |
| **Database** | `postgres` |
| **User** | `postgres` |
| **Password** | *(ask the project owner privately)* |
| **SSL** | Required |

**Full connection string:**
```
postgresql://postgres:[YOUR-PASSWORD]@db.oiuwldqrjvkijfrnyclc.supabase.co:5432/postgres
```

**Supabase Project URL (for frontend SDK):**
```
https://oiuwldqrjvkijfrnyclc.supabase.co
```

---

## ⚙️ Setup Instructions

### Step 1 — Copy the `.env` template
```bash
cp .env.example .env
```
Then open `.env` and replace `[YOUR-PASSWORD]` with the real password.

> [!IMPORTANT]
> Make sure `.env` is in your `.gitignore`. It already is if you use the provided template — but double-check before your first commit.

---

## 🐍 Python (FastAPI / SQLAlchemy)

**Install dependency:**
```bash
pip install psycopg2-binary sqlalchemy python-dotenv
```

**Connect:**
```python
import os
from sqlalchemy import create_engine
from dotenv import load_dotenv

load_dotenv()

engine = create_engine(
    os.getenv("DATABASE_URL"),
    connect_args={"sslmode": "require"}
)
```

**Test the connection:**
```python
with engine.connect() as conn:
    result = conn.execute("SELECT version()")
    print(result.fetchone())
```

---

## 🟩 Node.js / Next.js (Supabase Client SDK)

**Install dependency:**
```bash
npm install @supabase/supabase-js
```

**Initialize client (`lib/supabase.ts`):**
```typescript
import { createClient } from '@supabase/supabase-js'

const supabaseUrl = process.env.NEXT_PUBLIC_SUPABASE_URL!
const supabaseAnonKey = process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY!

export const supabase = createClient(supabaseUrl, supabaseAnonKey)
```

**Query example:**
```typescript
const { data, error } = await supabase.from('users').select('*')
```

---

## 🟩 Node.js (Direct PostgreSQL via `pg`)

**Install dependency:**
```bash
npm install pg dotenv
```

**Connect:**
```javascript
const { Pool } = require('pg')
require('dotenv').config()

const pool = new Pool({
  connectionString: process.env.DATABASE_URL,
  ssl: { rejectUnauthorized: false }
})

pool.query('SELECT NOW()', (err, res) => {
  console.log(err ? err : res.rows[0])
  pool.end()
})
```

---

## 🔧 Prisma ORM (Node.js / Next.js)

**Install:**
```bash
npm install prisma @prisma/client
npx prisma init
```

**`prisma/schema.prisma`:**
```prisma
datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}
```

**Pull existing schema from Supabase:**
```bash
npx prisma db pull
npx prisma generate
```

---

## 🖥️ GUI Tools (Recommended for Exploration)

| Tool | How to Connect |
| :--- | :--- |
| **TablePlus** | New connection → PostgreSQL → paste connection string |
| **DBeaver** | New Connection → PostgreSQL → fill in host/port/user/pass |
| **pgAdmin** | Servers → Register Server → Connection tab → fill in details |
| **Supabase Dashboard** | Log into [supabase.com](https://supabase.com) → ask owner to invite you |

---

## 🛡️ Security Rules

1. ✅ Use `.env` files for credentials — never hardcode them.
2. ✅ Always add `.env` to `.gitignore`.
3. ✅ Use **Anon Key** on the frontend — never the Service Role Key.
4. ✅ Enable Row Level Security (RLS) on all tables in Supabase.
5. ❌ Never commit or share the `DB_PASSWORD` or `SUPABASE_SERVICE_ROLE_KEY`.
