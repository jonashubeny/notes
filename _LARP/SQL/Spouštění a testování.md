# PostgreSQL – spouštění a testování schématu

Praktická část k [[Základy]] – jak schéma pustit, jak poznat, že funguje,
a jak ho pouštět opakovaně. Poznámky vznikly na Fedoře s PostgreSQL 18.

---

## 0. Jednorázové zprovoznění

```bash
sudo dnf install postgresql-server postgresql
sudo postgresql-setup --initdb
sudo systemctl enable --now postgresql
pg_isready                              # "server přijímá spojení"
```

`enable --now` = nastartuj hned a startuj i po každém restartu počítače. Od té chvíle
Postgres běží jako služba na pozadí a **už ho nespouštíš**. Není to jako `npm run dev`.

Opakované `systemctl start` na běžící službě neudělá nic – je to idempotentní.
Dvě instance na stejném portu ani nejdou (`Address already in use`).

### Role a databáze

```bash
sudo -u postgres createuser --interactive --pwprompt jonas
sudo -u postgres createdb -O jonas miluj_svuj_hydrant
```

Po částech:

- **`sudo -u postgres`** – spusť to jako systémový uživatel `postgres`, kterého založí
  instalace. Hned po instalaci je to jediný účet se superuživatelskými právy v databázi;
  ty jako `jonas` uvnitř Postgresu zatím neexistuješ, takže si databázi sám založit nemůžeš.
- **`createuser`** – obal nad SQL `CREATE ROLE`. `--pwprompt` se zeptá na heslo,
  `--interactive` se ptá na práva → **na superuživatele odpověz `n`**.
- **`createdb`** – obal nad `CREATE DATABASE`. Totéž by udělalo:
  `sudo -u postgres psql -c 'CREATE DATABASE miluj_svuj_hydrant OWNER jonas;'`
- **`-O jonas`** – owner. Role databázi vlastní, tedy v ní smí všechno.
  Bez toho by ji vlastnil `postgres` a aplikace by narážela na „permission denied".

Na pořadí záleží: `-O jonas` vyžaduje, aby role už existovala.

Oboje se pouští **jednou za život projektu**. Podruhé `createdb` řekne
`database already exists` – nic se nerozbije. Úplný reset: `sudo -u postgres dropdb miluj_svuj_hydrant`.

Kdybys na superuživatele omylem odpověděl `a`:

```bash
sudo -u postgres psql -c 'ALTER ROLE jonas NOSUPERUSER;'
```

### Past na Fedoře: `Ident authentication failed`

Fedora má ve výchozím `pg_hba.conf` u TCP spojení metodu `ident` – „zeptej se ident služby
operačního systému, kdo volá". Ta neběží, takže spojení z aplikace skončí na:

```
FATAL: Ident authentication failed for user "jonas"
```

Na hesle ani na `.env` to nezávisí, projevit se to musí vždycky. Oprava:

```bash
sudo cp /var/lib/pgsql/data/pg_hba.conf /var/lib/pgsql/data/pg_hba.conf.bak
sudo sed -i -E 's/^(host[[:space:]]+all[[:space:]]+all[[:space:]]+(127\.0\.0\.1\/32|::1\/128)[[:space:]]+)ident/\1scram-sha-256/' /var/lib/pgsql/data/pg_hba.conf
sudo systemctl reload postgresql
```

Kontrola – u řádků s `127.0.0.1/32` a `::1/128` musí být `scram-sha-256`:

```bash
sudo grep -E '^(host|local)' /var/lib/pgsql/data/pg_hba.conf
```

`reload` stačí, `restart` není potřeba. `scram-sha-256` je ověření heslem a od PostgreSQL 14
výchozí způsob, jak se hesla ukládají – nastavení v `pg_hba.conf` tomu jen musí odpovídat.
`md5` je starší varianta, není potřeba.

---

## 1. `DATABASE_URL` – co to je

Je to **adresa, kam se aplikace připojí**. Nic nevytváří, nic nenastavuje – jako URL webu.
Když na té adrese nic neběží, dostaneš chybu.

Cesta hodnoty v Node.js projektu:

1. `require('dotenv').config()` přečte `.env` do `process.env`
2. `new Pool({ connectionString: process.env.DATABASE_URL })` si string rozparsuje
3. `pg` z něj vytáhne hostitele, port, uživatele, heslo a databázi a otevře spojení

```
postgresql://jonas:heslo@localhost:5432/miluj_svuj_hydrant
└────┬───┘   └─┬─┘ └─┬─┘ └───┬───┘ └┬─┘ └────────┬───────┘
  protokol  uživatel heslo  hostitel port    databáze
```

| část | co znamená |
|---|---|
| `postgresql://` | typ databáze (`postgres://` je alias, totéž) |
| `jonas` | **databázová role** – účet uvnitř Postgresu. Že se jmenuje stejně jako linuxový uživatel je jen shoda okolností, jsou to dvě různé věci. |
| `heslo` | heslo té role |
| `localhost` | kde server běží; v Dockeru název služby |
| `5432` | port, výchozí pro Postgres |
| `miluj_svuj_hydrant` | která databáze – jedna instance jich hostí víc vedle sebe |

**Past:** znaky `@ : / ? #` v hesle rozbijí parsování URL a musely by se procentově
enkódovat. Jednodušší je dát si heslo jen z písmen a číslic.

`.env` s heslem nepatří do gitu.

### V terminálu heslo nepotřebuješ

```bash
psql -d miluj_svuj_hydrant
```

Bez hostitele jde `psql` přes unixový socket, kde platí `peer` autentizace – linuxový
uživatel `jonas` se páruje se stejnojmennou rolí, heslo se neřeší. Tohle je příkaz pro
všechno ruční zkoušení; `DATABASE_URL` s heslem potřebuje jen aplikace (jde přes TCP).

Ověření, že sedí to, co dostane aplikace:

```bash
psql "postgresql://jonas:heslo@localhost:5432/miluj_svuj_hydrant" -c '\conninfo'
```

| hláška | příčina |
|---|---|
| `Connection refused` | server neběží |
| `Ident authentication failed` | `pg_hba.conf` má `ident` místo `scram-sha-256` |
| `password authentication failed` | špatné heslo v URL |
| `database "..." does not exist` | zapomenutý `createdb` |
| `role "..." does not exist` | zapomenutý `createuser` |

---

## 2. Smyčka, ve které budeš žít

```bash
psql -v ON_ERROR_STOP=1 -1 -d miluj_svuj_hydrant -f db/schema.sql
```

Napiš kus SQL → pusť → koukni na výstup → oprav → znova.

**`-v ON_ERROR_STOP=1`** je tady to podstatné. Bez něj `psql` po chybě **pokračuje dál**
a skončí s návratovým kódem 0, jako by bylo všechno v pořádku. Hláška proteče mezi ostatními
řádky a ty máš rozbité schéma a nevíš o tom.

**`-1`** zabalí soubor do jedné transakce: buď projde všechno, nebo se nezmění nic. Bez toho
ti při chybě u třetí tabulky zůstanou v databázi první dvě a příště `CREATE TABLE users`
spadne na „už existuje", i když jsi nic nedodělal.

Když to projde, vypíše se jen `CREATE TABLE` za každý příkaz. **Ticho je úspěch.**

---

## 3. Jak číst chyby

```
psql:db/schema.sql:12: ERROR:  syntax error at or near ")"
LINE 8:     );
            ^
```

Dvě čísla, dva významy:

- `db/schema.sql:12` – **řádek v souboru**, tam koukni první
- `LINE 8` – řádek v rámci toho jednoho SQL příkazu, stříška ukazuje na znak

| hláška | co se stalo |
|---|---|
| `syntax error at or near ")"` | čárka navíc za posledním sloupcem |
| `syntax error at or near "CREATE"` | chybí středník na konci **předchozího** příkazu – chyba se hlásí až o kus dál |
| `relation "users" does not exist` | cizí klíč odkazuje na tabulku, která je v souboru níž; přehoď pořadí |
| `foreign key constraint cannot be implemented` | nesedí typy – `user_id` musí být `INTEGER`, když `users.id` je `SERIAL` |
| `relation "users" already exists` | chybí `DROP TABLE IF EXISTS` na začátku souboru |
| `type "TIMESTAMPZ" does not exist` | překlep, správně `TIMESTAMPTZ` |

---

## 4. Co zkontrolovat po každém úspěšném běhu

```
\dt          -- vznikly tabulky, co jsem čekal?
\d hydrants  -- správné typy a dole sekce Foreign-key constraints?
\di          -- existují indexy?
\q
```

`\d hydrants` je důležitější než `\dt` – ukáže, co Postgres z tvého zápisu **doopravdy**
udělal. Když sekce `Foreign-key constraints` chybí, cizí klíč nevznikl a databáze nic
nehlídá, i když v souboru `REFERENCES` vidíš.

---

## 5. Experimenty, které nic nezanechají

V `psql` piš rovnou. Nezapomeň středník – bez něj `psql` čeká, že věta pokračuje
(prompt se změní z `=#` na `-#`).

U `INSERT`ů a `DELETE`ů obal experiment do transakce, kterou nakonec zahodíš:

```sql
BEGIN;
INSERT INTO users (username, email, password_hash) VALUES ('test', 't@x.cz', 'x');
SELECT * FROM users;   -- vidíš výsledek
ROLLBACK;              -- a je to pryč, jako by se nic nestalo
```

Uvnitř transakce vidíš všechny své změny, `ROLLBACK` je celé vrátí. Takhle si můžeš bez
obav zkusit i `DELETE FROM users`.

---

## 6. Postup po tabulkách

Nepiš všechny tabulky najednou. Každý běh trvá vteřinu a chybu máš vždycky v tom,
co jsi právě dopsal:

1. Napiš **jen `users`**, pusť, zkontroluj `\d users`
2. Přidej `hydrants`, pusť – teď poprvé uvidíš, jestli funguje cizí klíč
3. Přidej `likes` – tam je `UNIQUE (user_id, hydrant_id)`
4. Přidej `comments`
5. Nakonec indexy

Ve fázi 2 si ověř, že cizí klíč drží. Tohle **musí spadnout**:

```sql
INSERT INTO hydrants (user_id, photo_path) VALUES (999, 'a.jpg');
-- ERROR: insert or update on table "hydrants" violates foreign key constraint
```

Když to projde bez chyby, `REFERENCES` se nevytvořilo.
**U constraintů je úspěšný test ten, který selže.**

Celá sada testů schématu:

```sql
INSERT INTO users (username, email, password_hash) VALUES ('test', 't@x.cz', 'x') RETURNING id;
INSERT INTO hydrants (user_id, photo_path) VALUES (1, 'a.jpg') RETURNING id;

-- 1. duplicitní username → musí spadnout na users_username_key
INSERT INTO users (username, email, password_hash) VALUES ('test', 'jiny@x.cz', 'x');

-- 2. neexistující uživatel → musí spadnout na foreign key
INSERT INTO hydrants (user_id, photo_path) VALUES (999, 'b.jpg');

-- 3. dvojitý lajk → oba příkazy projdou, ale vloží se jen jeden řádek
INSERT INTO likes (user_id, hydrant_id) VALUES (1, 1) ON CONFLICT DO NOTHING;
INSERT INTO likes (user_id, hydrant_id) VALUES (1, 1) ON CONFLICT DO NOTHING;
SELECT COUNT(*) FROM likes;   -- musí být 1

-- 4. kaskáda
DELETE FROM users WHERE username = 'test';
SELECT COUNT(*) FROM hydrants;  -- 0
SELECT COUNT(*) FROM likes;     -- 0
```

---

## 7. Opakované spouštění schématu

`CREATE TABLE users` podruhé spadne na `relation "users" already exists`. Tři možnosti:

**a) `DROP` + `CREATE` – tohle chceš ve vývoji**

```sql
DROP TABLE IF EXISTS comments, likes, hydrants, users CASCADE;
```

Každé spuštění zahodí všechno a postaví to znovu. Schéma v souboru se **vždycky** rovná
schématu v databázi. Mažeš v opačném pořadí, než vytváříš. Data se ztratí – proto `seed.sql`.

**b) `CREATE TABLE IF NOT EXISTS` – past, nepoužívat**

Skript projde bez chyby, ale **existující tabulku neaktualizuje**. Přidáš sloupec,
pustíš to, Postgres řekne „už existuje, přeskakuju", sloupec nepřibude – a ty pak hledáš,
proč aplikace hlásí `column does not exist`, když ho v souboru jasně vidíš.
Varianta (a) selže hlasitě, tahle tiše.

**c) Migrace – až budou v databázi reálná data**

Číslované soubory, každý popisuje jen změnu a pustí se právě jednou:

```
db/migrations/001_init.sql
db/migrations/002_add_is_admin.sql   -- ALTER TABLE users ADD COLUMN is_admin BOOLEAN NOT NULL DEFAULT FALSE;
```

Po nasazení je `DROP TABLE` konec, tam se mění výhradně přes `ALTER TABLE`.

### npm skripty

```json
"scripts": {
  "db:reset": "psql -v ON_ERROR_STOP=1 -1 -d miluj_svuj_hydrant -f db/schema.sql",
  "db:seed":  "psql -v ON_ERROR_STOP=1 -1 -d miluj_svuj_hydrant -f db/seed.sql"
}
```

Běžný pohyb při vývoji pak je `npm run db:reset && npm run db:seed`.
V seedu musí být heslo jako **bcrypt hash**, ne plaintext – jinak se tím účtem nepřihlásíš.

---

## Tahák

| příkaz | co dělá |
|---|---|
| `pg_isready` | běží server? |
| `psql -d nazev_db` | připojení přes socket, bez hesla |
| `psql "$DATABASE_URL" -c '\conninfo'` | test spojení tak, jak ho vidí aplikace |
| `psql -v ON_ERROR_STOP=1 -1 -d db -f soubor.sql` | pustit soubor pořádně |
| `psql -d db -c 'SELECT ...'` | jeden dotaz a konec |
| `\dt` / `\d tabulka` / `\di` | tabulky / detail tabulky / indexy |
| `\l` | seznam databází |
| `\du` | seznam rolí |
| `\x` | přepne výpis na svislý (dlouhé řádky) |
| `\q` | konec |
| `BEGIN; ... ROLLBACK;` | experiment, který nic nezanechá |
| `sudo systemctl reload postgresql` | načíst změny v `pg_hba.conf` |
