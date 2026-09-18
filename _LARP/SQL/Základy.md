# SQL – základy schématu (PostgreSQL)

Poznámky vznikly nad schématem projektu „Miluj svůj hydrant"
(`~/_PROJEKTY/_MSH/msh/db/schema.sql`).

---

## 1. Kostra příkazu

```sql
CREATE TABLE jmeno_tabulky (
    jmeno_sloupce  DATOVÝ_TYP  [omezení...],
    jmeno_sloupce  DATOVÝ_TYP  [omezení...],
    [omezení přes víc sloupců]
);
```

Tři věci u každého sloupce: **jak se jmenuje**, **co v něm smí být** (typ), **jaká pravidla platí** (constraints). Nic víc v tom není.

Konvence: tabulky v množném čísle a malými písmeny (`users`, ne `Users`) — Postgres nezauvozovkované názvy stejně převede na malá písmena, takže `Users` a `users` je totéž. Když to jednou dáš do uvozovek (`"Users"`), musíš uvozovky psát pořád. Nedělej to.

---

## 2. Datové typy

| typ | co to je |
|---|---|
| `SERIAL` | celé číslo, které si samo doplňuje 1, 2, 3, … Není to skutečný typ — je to zkratka (viz níže). |
| `INTEGER` | celé číslo, cca ±2 miliardy |
| `TEXT` | řetězec libovolné délky |
| `BOOLEAN` | `TRUE` / `FALSE` / `NULL` |
| `DOUBLE PRECISION` | desetinné číslo (float, 8 bajtů) |
| `TIMESTAMPTZ` | okamžik v čase včetně časové zóny |

**K `TEXT`:** v Postgresu nemá `VARCHAR(50)` oproti `TEXT` žádnou výkonnostní výhodu. `VARCHAR(n)` použij jen tehdy, když ten limit je opravdu byznysové pravidlo. Pro `password_hash` nikdy nedávej limit — bcrypt hash má 60 znaků, ale kdybys přešel na jiný algoritmus, je delší.

**K `DOUBLE PRECISION` u `lat`/`lng`:** float je pro GPS souřadnice v pohodě (přesnost ~cm). `NUMERIC` bys chtěl u peněz, kde nesmí vznikat zaokrouhlovací chyby.

**K `TIMESTAMPTZ` vs `TIMESTAMP`:** vždycky `TIMESTAMPTZ`. Ukládá okamžik v UTC a při čtení ho převede do zóny klienta. `TIMESTAMP` (bez TZ) je jen „číslo na ciferníku" bez informace, v jaké zóně — s letním časem a s uživateli mimo tvou zónu si tím naděláš problém.

---

## 3. Omezení (constraints)

### `PRIMARY KEY`

Jednoznačný identifikátor řádku. Znamená to dohromady `UNIQUE` + `NOT NULL` + automaticky se vytvoří index. V jedné tabulce může být jen jeden.

### `NOT NULL`

Do sloupce se nesmí uložit `NULL`. Pozor, `NULL` **není** prázdný řetězec — `''` projde i přes `NOT NULL`. To si musíš hlídat v aplikaci nebo `CHECK`em.

Všímej si i toho, kde `NOT NULL` **chybí**: u hydrantu `title`, `city`, `lat`, `lng`. To je záměr — popisek a poloha jsou nepovinné.

### `UNIQUE`

Hodnota se nesmí v tabulce opakovat. Vytvoří si vlastní index.

Zrada č. 1: `UNIQUE` **nebrání víc `NULL` hodnotám**. V SQL platí `NULL <> NULL`, takže deset řádků s `NULL` v unikátním sloupci projde. Řeší se tím, že sloupec má zároveň `NOT NULL`.

Zrada č. 2: `UNIQUE` je case-sensitive. `Jonas@x.cz` a `jonas@x.cz` jsou pro databázi dva různé e-maily. Standardní řešení je ukládat e-mail vždy malými písmeny (`.toLowerCase()` v aplikaci) nebo udělat:

```sql
CREATE UNIQUE INDEX ON users (lower(email));
```

### `DEFAULT hodnota`

Co se dosadí, když sloupec při `INSERT`u vynecháš. `DEFAULT now()` znamená, že `created_at` nikdy nemusíš posílat z aplikace — a je to tak lepší, protože čas pak bere databáze, ne hodiny aplikačního serveru.

`now()` je funkce vracející čas **začátku transakce**. Kdyby to vadilo, existuje `clock_timestamp()`.

### `REFERENCES users(id)` — cizí klíč

Nejdůležitější věc v celém schématu. Říká: *hodnota v `hydrants.user_id` musí existovat jako `users.id`*. Databáze to vynucuje — když zkusíš vložit hydrant s `user_id = 999` a takový uživatel neexistuje, `INSERT` skončí chybou. Nemůžou tak vzniknout osiřelé záznamy.

Plný zápis:

```sql
user_id INTEGER NOT NULL REFERENCES users(id)
-- což je zkratka za:
user_id INTEGER NOT NULL,
FOREIGN KEY (user_id) REFERENCES users(id)
```

**Typ cizího klíče musí sedět s typem primárního klíče.** `SERIAL` je pod kapotou `INTEGER`, proto je `user_id INTEGER`. Kdyby v `users` bylo `BIGSERIAL`, musel by být `user_id BIGINT`.

### `ON DELETE CASCADE`

Odpověď na otázku: *co se stane s hydranty, když smažu uživatele?*

| varianta | chování při smazání uživatele |
|---|---|
| `ON DELETE NO ACTION` (výchozí) | smazání selže, dokud má uživatel hydranty |
| `ON DELETE CASCADE` | smažou se i jeho hydranty |
| `ON DELETE SET NULL` | `user_id` se přepíše na `NULL` (vyžaduje, aby sloupec **neměl** `NOT NULL`) |
| `ON DELETE RESTRICT` | jako NO ACTION, jen se kontroluje dřív |

Kaskáda se řetězí: smažeš uživatele → smažou se jeho hydranty → a protože `likes.hydrant_id` má taky `CASCADE`, smažou se i lajky a komentáře pod těmi hydranty. Jedním `DELETE FROM users WHERE id = 5` uklidíš celou stopu. Přesně to u „smazat účet" chceš — ale ten příkaz je hodně destruktivní.

### `UNIQUE (user_id, hydrant_id)` — omezení přes víc sloupců

Když se pravidlo týká víc sloupců, nepatří k jednomu sloupci, ale na samostatný řádek na konci tabulky. Tenhle zápis znamená: **dvojice** (uživatel, hydrant) se nesmí opakovat. Uživatel může lajkovat spoustu hydrantů a hydrant může být lajknutý spoustou lidí — jen ne dvakrát tím samým.

Pozor na rozdíl, tohle je něco úplně jiného:

```sql
user_id    INTEGER ... UNIQUE,  -- ŠPATNĚ: uživatel by mohl lajknout jen jeden jediný hydrant
hydrant_id INTEGER ... UNIQUE,  -- ŠPATNĚ: hydrant by měl max jeden lajk
```

Díky tomuhle constraintu jde napsat:

```sql
INSERT INTO likes (user_id, hydrant_id) VALUES ($1, $2) ON CONFLICT DO NOTHING;
```

Druhý klik na lajk prostě tiše neudělá nic. Žádné `SELECT` + `if` v aplikaci, žádná race condition.

### `CHECK` — vlastní podmínka

```sql
lat  DOUBLE PRECISION CHECK (lat BETWEEN -90 AND 90),
lng  DOUBLE PRECISION CHECK (lng BETWEEN -180 AND 180),
text TEXT NOT NULL CHECK (length(trim(text)) > 0)
```

To poslední je řešení toho „prázdný řetězec projde přes `NOT NULL`".

---

## 4. `SERIAL` — co se za tím schovává

```sql
id SERIAL PRIMARY KEY
```

je zkratka, kterou Postgres rozbalí na tři věci: vytvoří sekvenci `users_id_seq`, nastaví typ `INTEGER` a `DEFAULT nextval('users_id_seq')`.

Moderní ekvivalent:

```sql
id INTEGER GENERATED ALWAYS AS IDENTITY PRIMARY KEY
```

To je SQL standard a je odolnější (nejde do něj omylem vložit vlastní hodnotu a rozbít sekvenci). `SERIAL` je pořád úplně běžný a funguje.

**Důsledek pro aplikaci:** `id` nikdy nevkládáš. Když ho potřebuješ zpátky (např. po uploadu, abys přesměroval na detail), použiješ `RETURNING`:

```sql
INSERT INTO hydrants (user_id, photo_path, city) VALUES ($1, $2, $3) RETURNING id;
```

---

## 5. Věci kolem, které schéma samo neřeší

### Na pořadí tabulek záleží

`hydrants` odkazuje na `users`, takže `users` musí existovat dřív. Při opačném pořadí to `psql` odmítne.

### Soubor musí jít pustit opakovaně

Při vývoji budeš schéma měnit a pouštět znovu. `CREATE TABLE users` podruhé skončí chybou „relation already exists". Proto se na začátek souboru dává úklid:

```sql
DROP TABLE IF EXISTS comments, likes, hydrants, users CASCADE;
```

Mažeš v **opačném pořadí** než vytváříš (nejdřív ty, co odkazují). `CASCADE` tady znamená „zahoď i závislé objekty" — a hlavně: **tohle smaže všechna data**. Na produkci tenhle řádek nepatří, tam se změny dělají přes `ALTER TABLE`.

### `text` jako název sloupce

Sloupec pojmenovaný `text` je zároveň název datového typu. Postgres to povolí (`text` není rezervované slovo), ale při čtení dotazů je to matoucí. Lepší je `body` nebo `content`.

### Indexy si musíš doplnit sám

Automaticky vzniknou jen pro `PRIMARY KEY` a `UNIQUE`. **Cizí klíče index nedostanou.** Přitom přesně na nich budeš filtrovat — „všechny komentáře k hydrantu 42", „všechny hydranty uživatele 7". Bez indexu Postgres projde celou tabulku.

```sql
CREATE INDEX ON hydrants (user_id);
CREATE INDEX ON likes (hydrant_id);
CREATE INDEX ON comments (hydrant_id);
```

(`likes` má `hydrant_id` až na druhém místě v `UNIQUE (user_id, hydrant_id)`, a složený index se dá použít jen zleva — proto vlastní index.)

Do `schema.sql` patří až za všechna `CREATE TABLE`.

---

## 6. Jak si to odladíš

```bash
createdb miluj_svuj_hydrant
psql -d miluj_svuj_hydrant -f db/schema.sql
```

Pak interaktivně — `psql -d miluj_svuj_hydrant`:

| příkaz | co udělá |
|---|---|
| `\dt` | vypíše tabulky |
| `\d users` | sloupce, typy, indexy a cizí klíče tabulky |
| `\d+ users` | totéž podrobněji |
| `\q` | konec |

`\d hydrants` je hlavní nástroj — uvidíš přesně, co Postgres z tvého zápisu udělal, včetně rozbaleného `SERIAL` a vypsaných constraintů.

**Vyzkoušej si, že constrainty fungují** — nejrychlejší způsob, jak to pochopit:

```sql
INSERT INTO users (username, email, password_hash) VALUES ('jonas', 'a@b.cz', 'xxx');
INSERT INTO users (username, email, password_hash) VALUES ('jonas', 'c@d.cz', 'yyy');
-- chyba: duplicate key value violates unique constraint "users_username_key"

INSERT INTO hydrants (user_id, photo_path) VALUES (999, 'a.jpg');
-- chyba: violates foreign key constraint

INSERT INTO hydrants (user_id, photo_path) VALUES (1, 'a.jpg');
SELECT * FROM hydrants;   -- id i created_at se doplnily samy

DELETE FROM users WHERE id = 1;
SELECT * FROM hydrants;   -- prázdno, kaskáda zabrala
```

Chybové hlášky čti celé. Postgres v nich říká název porušeného constraintu — a to samé dostaneš v Node.js v `err.constraint`, což se hodí, až budeš u registrace rozlišovat „obsazený username" od „obsazený e-mail".

---

## Shrnutí

Sloupec = **název + typ + pravidla**.

Pravidla: `NOT NULL` (musí být vyplněno), `UNIQUE` (nesmí se opakovat), `PRIMARY KEY` (obojí + identifikátor), `DEFAULT` (co se dosadí samo), `REFERENCES` (musí existovat jinde), `CHECK` (vlastní podmínka).

Pravidlo přes víc sloupců se píše na samostatný řádek na konci tabulky.

---

## Referenční schéma projektu

```sql
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
    created_at TIMESTAMPTZ NOT NULL DEFAULT now()
);

CREATE TABLE likes (
    id         SERIAL PRIMARY KEY,
    user_id    INTEGER NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    hydrant_id INTEGER NOT NULL REFERENCES hydrants(id) ON DELETE CASCADE,
    created_at TIMESTAMPTZ NOT NULL DEFAULT now(),
    UNIQUE (user_id, hydrant_id)   -- jeden lajk na uživatele
);

CREATE TABLE comments (
    id         SERIAL PRIMARY KEY,
    user_id    INTEGER NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    hydrant_id INTEGER NOT NULL REFERENCES hydrants(id) ON DELETE CASCADE,
    text       TEXT NOT NULL,
    created_at TIMESTAMPTZ NOT NULL DEFAULT now()
);
```
