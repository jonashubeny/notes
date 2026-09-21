# Časté chyby v `CREATE TABLE`

Chyby, na které jsem sám narazil při psaní `schema.sql`, i s hláškou, kterou vyhodí.
Navazuje na [[Základy]] a [[Spouštění a testování]].

## Rychlá kontrola před spuštěním

- [ ] Čárka za **každým** sloupcem kromě posledního
- [ ] Za posledním sloupcem čárka **není**
- [ ] Každý sloupec má datový typ
- [ ] Každý sloupec je na vlastním řádku, dva naráz nejdou
- [ ] Každý cizí klíč má `REFERENCES ... ON DELETE CASCADE`
- [ ] `UNIQUE` přes víc sloupců je na samostatném řádku na konci
- [ ] Soubor jsem po úpravě **spustil** (uložení v editoru nestačí)

---

## 1. Editace souboru nic nedělá

`schema.sql` je jen text na disku. `\d` se dívá **do databáze**, ne do souboru.
Dokud soubor nespustíš, databáze o nových tabulkách neví.

```
psql -d miluj_svuj_hydrant -c '\d hydrants'
Nelze nalézt relaci se jménem "hydrants".
```

Uložení v editoru = nic. Po každé úpravě:

```bash
psql -v ON_ERROR_STOP=1 -1 -d miluj_svuj_hydrant -f db/schema.sql
```

---

## 2. Chybějící čárky mezi sloupci

```
ERROR:  syntax error at or near "username"
ŘÁDKA 3:     username TEXT NOT NULL UNIQUE
             ^
```

```sql
-- ŠPATNĚ
id SERIAL PRIMARY KEY
username TEXT NOT NULL UNIQUE

-- SPRÁVNĚ
id SERIAL PRIMARY KEY,
username TEXT NOT NULL UNIQUE,
```

**Pravidlo pro čtení hlášek:** u `syntax error at or near X` je problém skoro vždycky
těsně **před** tím X, ne v něm. Parser přečetl `id SERIAL PRIMARY KEY`, čekal čárku
nebo `)` – a našel `username`.

Taky pozor na ta dvě čísla: `schema.sql:16` je řádek, kde příkaz **končí** (středník),
`ŘÁDKA 3` je řádek uvnitř příkazu. Koukej na stříšku, ne na první číslo.

---

## 3. Čárka navíc před závorkou

```
ERROR:  syntax error at or near ")"
```

```sql
-- ŠPATNĚ
created_at TIMESTAMPTZ NOT NULL DEFAULT now(),
);

-- SPRÁVNĚ
created_at TIMESTAMPTZ NOT NULL DEFAULT now()
);
```

Opak chyby č. 2 a stejně častý.

---

## 4. Dva sloupce na jednom řádku

```
ERROR:  syntax error at or near ","
ŘÁDKA 7:     lat, lng DOUBLE PRECISION,
                ^
```

```sql
-- ŠPATNĚ
lat, lng DOUBLE PRECISION,

-- SPRÁVNĚ
lat DOUBLE PRECISION,
lng DOUBLE PRECISION,
```

Každý sloupec se deklaruje samostatně, společný typ pro víc sloupců SQL nezná.

---

## 5. Sloupec bez datového typu

```sql
-- ŠPATNĚ
email NOT NULL

-- SPRÁVNĚ
email TEXT NOT NULL UNIQUE
```

Snadno přehlédnutelné, protože `NOT NULL` na tom místě vypadá jako typ.

---

## 6. Cizí klíč bez `REFERENCES`

Tohle **nespadne** – proto je to nejnebezpečnější chyba v seznamu. Vznikne obyčejný
číselný sloupec bez jakékoli vazby: půjde uložit lajk na neexistující hydrant a smazání
hydrantu po sobě lajky neuklidí.

```sql
-- ŠPATNĚ
hydrant_id INTEGER NOT NULL,

-- SPRÁVNĚ
hydrant_id INTEGER NOT NULL REFERENCES hydrants(id) ON DELETE CASCADE,
```

Kontrola: `\d likes` musí dole vypsat sekci `Foreign-key constraints`.
Když tam není, vazba nevznikla.

Cizí klíč má vždycky tuhle trojici:

```
INTEGER  NOT NULL  REFERENCES <tabulka>(id)  ON DELETE CASCADE
   │         │              │                        │
  typ     povinnost      vazba              co při mazání rodiče
```

---

## 7. Zapomenuté `UNIQUE` přes víc sloupců

Taky nespadne. Bez něj může jeden člověk lajknout stejný hydrant tisíckrát.

```sql
CREATE TABLE likes (
    ...
    created_at TIMESTAMPTZ NOT NULL DEFAULT now(),
    UNIQUE (user_id, hydrant_id)     -- samostatný řádek na konci
);
```

Pozor na rozdíl – tohle je něco úplně jiného:

```sql
user_id    INTEGER ... UNIQUE,   -- uživatel by mohl lajknout jen jeden hydrant
hydrant_id INTEGER ... UNIQUE,   -- hydrant by měl max jeden lajk
```

---

## Správné znění tabulek

```sql
DROP TABLE IF EXISTS comments, likes, hydrants, users CASCADE;

CREATE TABLE users (
    id            SERIAL PRIMARY KEY,
    username      TEXT NOT NULL UNIQUE,
    email         TEXT NOT NULL UNIQUE,
    password_hash TEXT NOT NULL,
    is_admin      BOOLEAN NOT NULL DEFAULT FALSE,
    created_at    TIMESTAMPTZ NOT NULL DEFAULT now()
);

CREATE TABLE hydrants (
    id         SERIAL PRIMARY KEY,
    user_id    INTEGER NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    title      TEXT,
    photo_path TEXT NOT NULL,
    city       TEXT,
    lat        DOUBLE PRECISION,
    lng        DOUBLE PRECISION,
    created_at TIMESTAMPTZ NOT NULL DEFAULT now(),
    CHECK ((lat IS NULL) = (lng IS NULL))   -- souřadnice buď obě, nebo žádná
);

CREATE TABLE likes (
    id         SERIAL PRIMARY KEY,
    user_id    INTEGER NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    hydrant_id INTEGER NOT NULL REFERENCES hydrants(id) ON DELETE CASCADE,
    created_at TIMESTAMPTZ NOT NULL DEFAULT now(),
    UNIQUE (user_id, hydrant_id)
);

CREATE TABLE comments (
    id         SERIAL PRIMARY KEY,
    user_id    INTEGER NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    hydrant_id INTEGER NOT NULL REFERENCES hydrants(id) ON DELETE CASCADE,
    text       TEXT NOT NULL,
    created_at TIMESTAMPTZ NOT NULL DEFAULT now()
);

CREATE INDEX ON hydrants (user_id);
CREATE INDEX ON likes (hydrant_id);
CREATE INDEX ON comments (hydrant_id);
```

Pořadí tabulek není libovolné: cizí klíč nemůže odkazovat na tabulku, která ještě
neexistuje. `users` → `hydrants` → `likes` → `comments`.

---

## Které chyby databáze nechytí

| chyba | spadne? |
|---|---|
| chybějící čárka | ✅ ano |
| čárka navíc | ✅ ano |
| chybějící typ | ✅ ano |
| dva sloupce na řádku | ✅ ano |
| **chybějící `REFERENCES`** | ❌ **ne** |
| **chybějící `UNIQUE`** | ❌ **ne** |
| **chybějící `NOT NULL`** | ❌ **ne** |

První čtyři najdeš tím, že soubor pustíš. Poslední tři musíš zkontrolovat sám
přes `\d <tabulka>` – a nejlíp otestovat tím, že se schválně pokusíš vložit
neplatná data. **U constraintů je úspěšný test ten, který selže.**
