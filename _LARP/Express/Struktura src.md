# Co patří do složky `src/`

Kam co ukládat v Node.js + Express projektu, proč zrovna tam a co do `src/` naopak
nepatří. Psáno na projektu *Miluj svůj hydrant*. Navazuje na [[Routy]].

---

## 1. Základní pravidlo

**`src/` = zdrojový kód aplikace, tedy to, co jsi napsal ty a co aplikace potřebuje k běhu.**

Všechno ostatní je mimo:

| mimo `src/` | proč |
|---|---|
| `node_modules/` | cizí kód, instaluje se z `package.json`, do gitu nepatří |
| `package.json` | popis projektu, ne kód |
| `.env` | tajné hodnoty (heslo k DB), do gitu nikdy |
| `db/schema.sql` | pouští se ručně přes `psql`, aplikace ho nečte |
| `todo/` | poznámky, ne kód |
| `README.md` | dokumentace |

Nepovinné to je – `src/` je zvyk, ne požadavek Node.js. Ale jakmile projekt povyroste,
je dobré mít jednu složku, kde je *jen* kód aplikace, a kořen projektu čistý.

---

## 2. Cílový strom

```
src/
├── server.js        vstupní bod – nastaví Express a nastartuje ho
├── db.js            připojení k PostgreSQL (Pool), sdílené všemi routami
├── routes/          Express routy rozdělené podle tématu
│   ├── hydrants.js
│   └── auth.js
├── middleware/      vlastní middleware (requireLogin, upload)
│   └── auth.js
├── views/           EJS šablony = HTML, které server generuje
│   ├── partials/
│   │   ├── header.ejs
│   │   └── footer.ejs
│   ├── index.ejs
│   └── hydrants/
│       ├── index.ejs
│       └── detail.ejs
├── public/          statické soubory, které si stáhne prohlížeč
│   ├── css/style.css
│   └── js/map.js
└── uploads/         nahrané fotky od uživatelů (mimo git)
```

V projektu teď existuje `server.js`, `db.js`, `views/index.ejs` a prázdné
`routes/`, `public/`, `uploads/` (držené `.gitkeep`). Zbytek přibude podle
`todo/main.md`.

---

## 3. `server.js` – vstupní bod

Jediný soubor, který se spouští (`npm run dev` → `nodemon src/server.js`).
Má **jednu úlohu: složit aplikaci dohromady a nastartovat ji.** Žádnou byznys logiku.

Pořadí uvnitř je důležité, protože Express jde shora dolů:

```js
// 1. importy
const express = require('express');
const path = require('path');
require('dotenv').config();

const app = express();

// 2. nastavení
app.set('view engine', 'ejs');
app.set('views', path.join(__dirname, 'views'));

// 3. middleware (musí být NAD routami)
app.use(express.static(path.join(__dirname, 'public')));
app.use('/uploads', express.static(path.join(__dirname, 'uploads')));
app.use(express.urlencoded({ extended: true }));

// 4. routy
app.use('/', require('./routes/hydrants'));
app.use('/', require('./routes/auth'));

// 5. 404 a chyby (úplně naposled)
app.use((req, res) => res.status(404).render('404'));
app.use((err, req, res, next) => { console.error(err); res.status(500).render('500'); });

// 6. start
app.listen(PORT, () => console.log(`Server bezi na http://localhost:${PORT}`));
```

Když ti `server.js` přeroste ~100 řádků, něco z něj patří do `routes/`.

---

## 4. `db.js` – připojení k databázi

Jeden soubor, jeden `Pool`, exportovaný ven:

```js
require('dotenv').config();
const { Pool } = require('pg');

const pool = new Pool({ connectionString: process.env.DATABASE_URL });

module.exports = pool;
```

**Proč jeden na celou aplikaci:** `Pool` drží sadu otevřených spojení a půjčuje je
dotazům. Kdyby si každý soubor vytvořil vlastní `Pool`, otevřeš desítky spojení,
PostgreSQL má jejich počet omezený a dojdou.

Node.js modul se vyhodnotí **jen jednou** a další `require` dostanou tentýž objekt.
Takže stačí v každé routě:

```js
const pool = require('../db');
```

a všechny sdílejí jeden pool. Nic víc řešit nemusíš.

---

## 5. `routes/` – routy

Detailně v [[Routy]]. Pro strukturu stačí:

- jeden soubor = jedno téma (`hydrants.js`, `auth.js`), ne jedna route
- vždy `const router = express.Router()` … `module.exports = router`
- soubor se sám od sebe nespustí, musí být zapojený přes `app.use()` v `server.js`

Route má být **krátká**: vezmi vstup z `req`, zavolej SQL, předej výsledek do `res.render`.
Když jeden handler přeroste ~30 řádků, výpočty patří jinam (viz sekce 10).

---

## 6. `views/` – šablony (běží na serveru)

EJS šablona je HTML s dírami, které server vyplní daty **než odpověď odešle**.
Prohlížeč dostane hotové HTML a o EJS nikdy neví.

```html
<!-- src/views/hydrants/detail.ejs -->
<h1><%= hydrant.title %></h1>
<img src="/uploads/<%= hydrant.photo_path %>" alt="">
```

Dvě značky, které si musíš zapamatovat:

| značka | co dělá |
|---|---|
| `<%= promenna %>` | vypíše hodnotu **bezpečně** (escapuje HTML) – tohle používej |
| `<%- promenna %>` | vypíše syrově, včetně HTML tagů |
| `<% kod %>` | jen spustí JS (cykly, podmínky), nic nevypíše |

`<%- %>` na uživatelském vstupu je díra – někdo pošle komentář
`<script>...</script>` a on se návštěvníkům spustí (XSS). Na text od uživatele
**vždycky `<%= %>`**. Výjimka jsou partials (viz níž).

Do šablony se dostane jen to, co jí předáš druhým argumentem `res.render`:

```js
res.render('hydrants/detail', { hydrant: rows[0] });
```

Cokoliv jiného hodí `xy is not defined`. Cesta je relativní k `src/views/`
a **bez přípony** – `'hydrants/detail'`, ne `'views/hydrants/detail.ejs'`.

### `views/partials/` – ať se `<html>` neopisuje

```html
<%- include('partials/header', { title: title }) %>
  <h1>Hydranty</h1>
<%- include('partials/footer') %>
```

`include` je jediné legitimní místo pro `<%- %>` – vkládáš svoje vlastní HTML.

---

## 7. `public/` vs. `uploads/` – nejdůležitější rozdíl

Obě složky obsahují soubory, které prohlížeč stahuje. Rozdíl je v tom, **kdo je tam dal**:

|  | `public/` | `uploads/` |
|---|---|---|
| kdo tam soubory dává | ty, v editoru | uživatelé, přes formulář |
| v gitu | **ano** | **ne** (`.gitignore`) |
| obsah | CSS, klientský JS, ikony, loga | fotky hydrantů |
| po `git clone` | je tam všechno | prázdné |

Proto to nejsou dvě podsložky jedné složky. Uploady jsou **data**, ne kód –
při nasazení na server se kopírují jinak (nebo jdou do S3).

Obojí se zpřístupní přes `express.static`:

```js
app.use(express.static(path.join(__dirname, 'public')));                     // /css/style.css
app.use('/uploads', express.static(path.join(__dirname, 'uploads')));        // /uploads/abc.jpg
```

První řádek nemá prefix, takže `src/public/css/style.css` je na adrese `/css/style.css`.
Slovo `public` se v URL **neobjeví** – v šabloně proto:

```html
<link rel="stylesheet" href="/css/style.css">     <!-- ne /public/css/style.css -->
```

Lomítko na začátku je povinné. Bez něj (`href="css/style.css"`) je cesta relativní
ke *stránce*, takže z `/hydranty/42` se prohlížeč ptá na `/hydranty/css/style.css`.
To je nejčastější důvod, proč „CSS nefunguje na detailu, ale na homepage jo".

### `public/js/` je klientský JS

Soubory v `public/js/` běží **v prohlížeči**, ne v Node.js. Nemají přístup k databázi,
k `process.env` ani k `require()`. Sem přijde Leaflet mapa (`map.js`), která si data
vytáhne přes `fetch('/api/hydrants')`.

Tohle je rozdíl, který začátečníka pálí nejčastěji: `src/routes/*.js` běží na serveru,
`src/public/js/*.js` v prohlížeči. Vypadají stejně, jsou to dva různé světy.
Nikdy nedávej do `public/` nic tajného – kdokoliv si to stáhne.

---

## 8. `middleware/` – vlastní mezikroky

Funkce, které se pouští před routou a jsou potřeba na víc místech. Typicky:

```js
// src/middleware/auth.js
function requireLogin(req, res, next) {
    if (!req.session.userId) return res.redirect('/login');
    next();
}
module.exports = { requireLogin };
```

Sem pak přijde i konfigurace `multer` pro upload. Dokud máš jednu takovou funkci,
může bydlet v `routes/auth.js`; jakmile ji potřebuješ ve dvou souborech,
přestěhuj ji sem, ať nevznikne kruhový import.

---

## 9. `path.join(__dirname, ...)` – proč pořád dokola

`__dirname` je absolutní cesta ke složce, kde leží **ten soubor**, ve kterém to napíšeš.

Kdybys napsal jen `app.use(express.static('public'))`, Node hledá `public` relativně
k tomu, **odkud jsi spustil `node`**. Spustíš `node src/server.js` z kořene → hledá
`./public` v kořeni → nenajde. Spustíš to z `src/` → najde. Stejný kód, jiné chování.

`path.join(__dirname, 'public')` je vždycky `/home/jonas/_PROJEKTY/_MSH/msh/src/public`,
bez ohledu na to, odkud server pouštíš. Proto se to používá **vždycky** u cest k souborům.

(`path.join` navíc správně skládá oddělovače – na Windows `\`, na Linuxu `/`.)

---

## 10. Kdy přidat další složku

Nepřidávej dopředu. Signály, kdy má smysl:

| složka | přidej, až… |
|---|---|
| `middleware/` | stejnou kontrolu potřebuješ ve dvou souborech rout |
| `models/` nebo `queries/` | stejný SQL dotaz píšeš na třetím místě |
| `utils/` | máš pomocnou funkci (formát data), kterou volá víc míst |
| `config/` | `server.js` je zahlcený nastavením |

Pro tenhle projekt bohatě stačí `routes/`, `views/`, `public/`, `uploads/`, `middleware/`.
`models/` přidej, až tě omrzí opisovat `SELECT * FROM hydrants ...`.

---

## 11. Pojmenování

- soubory **malými písmeny**, víceslovné s pomlčkou: `hydrant-detail.ejs`
- routy v množném čísle podle tabulky: `hydrants.js` ↔ tabulka `hydrants`
- Linux rozlišuje velikost písmen – `require('./Db')` na tvé Fedoře **spadne**,
  i kdyby to na jiném systému prošlo

---

## 12. Kam co patří – rychlý rozcestník

| chci přidat… | kam |
|---|---|
| novou stránku | route do `src/routes/` + šablona do `src/views/` |
| nový SQL dotaz | do routy (později `src/models/`) |
| CSS | `src/public/css/style.css` |
| JS pro mapu | `src/public/js/map.js` |
| kontrolu přihlášení | `src/middleware/auth.js` |
| novou tabulku | `db/schema.sql` – **mimo `src/`** |
| heslo, klíč, URL databáze | `.env` – **mimo `src/`, mimo git** |
| knihovnu (`npm install`) | nikam, jen `require` tam, kde ji potřebuješ |

---

Související: [[Routy]] · [[Základy]] (SQL) · [[Spouštění a testování]]
