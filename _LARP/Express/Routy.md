# Routy v Expressu od nuly

Co to route je, jak se píše, v jakém pořadí se vyhodnocují a jak je rozdělit do
souborů, až jich bude moc. Psáno na projektu *Miluj svůj hydrant* (Node.js + Express 4
+ EJS + PostgreSQL). Navazuje na [[Struktura src]].

---

## 1. Co je route

**Route = pravidlo „když přijde tenhle požadavek, udělej tohle a pošli tohle zpátky".**

Prohlížeč pošle na server požadavek. Ten má dvě části, které tě zajímají:

- **metodu** – `GET` (dej mi stránku), `POST` (tady máš data, ulož je)
- **cestu (path)** – `/`, `/hydranty`, `/hydranty/42`

Server má seznam rout a hledá první, která na dvojici *metoda + cesta* sedí.
Když žádná nesedí, vrátí 404.

```js
app.get('/', (req, res) => {
    res.render('index', { title: 'Domovská stránka' });
});
```

Přečteno slovy: *„Když někdo zadá GET na `/`, vyrenderuj šablonu `index.ejs`
a předej jí proměnnou `title`."*

Tohle už v projektu je, v `src/server.js`.

---

## 2. Anatomie jednoho řádku

```js
app.get('/hydranty/:id', (req, res) => { ... });
 │   │        │             │     │
 │   │        │             │     └── res = odpověď, kterou skládáš ty
 │   │        │             └──────── req = požadavek, který přišel
 │   │        └────────────────────── cesta, `:id` je proměnná část
 │   └─────────────────────────────── HTTP metoda (malým písmem)
 └─────────────────────────────────── aplikace (nebo router, viz sekce 8)
```

Funkce uvnitř se říká **handler**. Spustí se až ve chvíli, kdy požadavek přijde –
ne při startu serveru. To je nejčastější zdroj zmatku u začátečníka: při `node server.js`
se ta funkce jen *zaregistruje*, nespustí.

---

## 3. Metody – které potřebuješ

| metoda | k čemu                    | příklad v tomhle projektu        |
| ------ | ------------------------- | -------------------------------- |
| `GET`  | zobraz stránku, nic neměň | `GET /hydranty` – výpis fotek    |
| `POST` | pošli data, něco se změní | `POST /hydranty` – nahrání fotky |

`PUT`, `PATCH` a `DELETE` existují taky, ale **HTML formulář je neumí poslat** –
`<form method="...">` zvládne jen `GET` a `POST`. Mazání se proto v klasické
serverové aplikaci dělá jako `POST /hydranty/:id/delete`. Na `PUT`/`DELETE` dojde,
až budeš volat server javascriptem přes `fetch()`.

**Pravidlo:** `GET` nikdy nesmí nic měnit v databázi. Prohlížeč si `GET` cachuje,
předvytáhne odkazy dopředu a vyhledávače je proklikávají. Odkaz
`<a href="/hydranty/5/delete">Smazat</a>` ti dřív nebo později smaže data sám od sebe.

---

## 4. `req` – co v požadavku přišlo

Čtyři místa, odkud se berou data. Pletou se, proto je tabulka:

| kde                | jak vypadá URL / vstup | v kódu               | musí být               |
| ------------------ | ---------------------- | -------------------- | ---------------------- |
| **route parametr** | `/hydranty/42`         | `req.params.id`      | `:id` v cestě          |
| **query string**   | `/hydranty?mesto=Brno` | `req.query.mesto`    | nic                    |
| **tělo formuláře** | `<form method="post">` | `req.body.title`     | `express.urlencoded()` |
| **session**        | (cookie)               | `req.session.userId` | `express-session`      |

```js
app.get('/hydranty/:id', (req, res) => {
    req.params.id      // "42"  – vždycky STRING, i když to vypadá jako číslo
});
```

Že je to string, je důležité u SQL i u porovnávání (`"42" === 42` je `false`).
Do SQL to ale posílej jak to je, přes parametr – `pg` si typ ošetří:

```js
const { rows } = await pool.query('SELECT * FROM hydrants WHERE id = $1', [req.params.id]);
```

**Nikdy neskládej SQL spojováním stringů** (`'... WHERE id = ' + req.params.id`) –
to je SQL injection, návštěvník ti přes URL smaže tabulku.

### Tělo formuláře nefunguje samo

Bez tohohle řádku je `req.body` `undefined` a nic ti nepřijde:

```js
app.use(express.urlencoded({ extended: true }));   // data z HTML formulářů
app.use(express.json());                           // data z fetch() jako JSON
```

Patří do `server.js` **nad** routy. V projektu zatím chybí – přidej, až budeš dělat
registraci (krok 3 v `todo/main.md`).

---

## 5. `res` – čím odpověď ukončíš

Každá route musí skončit **právě jednou** odpovědí:

```js
res.render('detail', { hydrant });   // vyrenderuj EJS šablonu a pošli HTML
res.redirect('/hydranty');           // přesměruj prohlížeč jinam
res.json(rows);                      // pošli JSON (pro Leaflet, fetch)
res.send('Ahoj');                    // pošli holý text
res.status(404).render('404');       // stavový kód + odpověď
res.sendStatus(204);                 // jen kód, bez těla
```

Když neuděláš **nic**, prohlížeč se točí, dokud nevyprší timeout. Když uděláš **dvakrát**,
dostaneš `ERR_HTTP_HEADERS_SENT`. Typicky takhle:

```js
// ŠPATNĚ – po res.redirect se kód běží dál
if (!hydrant) {
    res.redirect('/');      // chybí return
}
res.render('detail', { hydrant });   // spustí se taky → pád

// SPRÁVNĚ
if (!hydrant) {
    return res.redirect('/');
}
res.render('detail', { hydrant });
```

**Pravidlo: před `res.` v `if`-u piš skoro vždycky `return`.**

### PRG: po POST vždycky redirect

Po úspěšném `POST` nikdy nerenderuj stránku rovnou – přesměruj:

```js
app.post('/hydranty', async (req, res) => {
    await pool.query('INSERT INTO hydrants ...');
    res.redirect('/hydranty');       // ne res.render()
});
```

Jinak zůstane v adresním řádku POST URL a uživatelovo F5 (nebo tlačítko zpět)
odešle formulář znovu → druhá kopie fotky v databázi. Vzoru se říká
**Post/Redirect/Get**.

---

## 6. Pořadí rout rozhoduje

Express jde seznamem **shora dolů** a použije **první** shodu. Ostatní se neprovedou.

```js
app.get('/hydranty/:id', ...);      // tahle chytne i "/hydranty/novy"
app.get('/hydranty/novy', ...);     // sem se nikdy nedostaneš
```

`:id` je zástupný znak za *cokoliv*, takže pohltí i `novy`. Konkrétní cesty patří
**nad** ty s parametrem:

```js
app.get('/hydranty/novy', ...);     // konkrétní první
app.get('/hydranty/:id', ...);      // parametr až potom
```

Ze stejného důvodu je **404 route úplně poslední** – kdyby byla první, chytala by všechno.

---

## 7. Middleware – funkce, která něco udělá a pustí dál

Middleware vypadá jako route, jen má třetí parametr `next`:

```js
function requireLogin(req, res, next) {
    if (!req.session.userId) {
        return res.redirect('/login');   // konec, dál to nepustíme
    }
    next();                              // pusť na další funkci v řadě
}
```

Použití – vloží se mezi cestu a handler:

```js
app.post('/hydranty', requireLogin, (req, res) => { ... });
```

Přečteno: *„Na POST /hydranty nejdřív zkontroluj přihlášení; když projde, teprve nahraj."*

**Bez `next()` nebo bez `res.*` se požadavek zasekne** – to je druhá klasická chyba.
Middleware musí vždycky buď odpovědět, nebo zavolat `next()`.

`app.use(...)` bez cesty znamená „tohle spusť u každého požadavku":

```js
app.use(express.static(path.join(__dirname, 'public')));   // už v projektu je
app.use(express.urlencoded({ extended: true }));
```

Middleware, které si napíšeš sám, patří do `src/middleware/` (viz [[Struktura src]]).

---

## 8. Rozdělení do souborů: `express.Router()`

Dokud jsou routy tři, můžou být v `server.js`. Jakmile jich je patnáct, je to nepřehledné.
`Router()` je „mini-aplikace", kterou napíšeš ve vlastním souboru a v `server.js` jen zapojíš.

**`src/routes/hydrants.js`:**

```js
const express = require('express');
const pool = require('../db');          // o složku výš

const router = express.Router();        // ne app, ale router

router.get('/hydranty', async (req, res) => {
    const { rows } = await pool.query('SELECT * FROM hydrants ORDER BY created_at DESC');
    res.render('hydrants/index', { hydrants: rows });
});

router.get('/hydranty/:id', async (req, res) => {
    const { rows } = await pool.query('SELECT * FROM hydrants WHERE id = $1', [req.params.id]);
    if (rows.length === 0) {
        return res.status(404).render('404');
    }
    res.render('hydrants/detail', { hydrant: rows[0] });
});

module.exports = router;                // bez tohohle je soubor k ničemu
```

**`src/server.js`:**

```js
const hydrantsRouter = require('./routes/hydrants');
app.use('/', hydrantsRouter);
```

Dvě věci, které se snadno zapomenou:
1. `module.exports = router` na konci souboru s routami
2. `app.use(...)` v `server.js` – bez toho soubor existuje, ale nikdy se nezavolá

### Prefix v `app.use()`

První argument `app.use()` se **předsadí** před všechny cesty uvnitř routeru:

```js
app.use('/hydranty', hydrantsRouter);   // a v routeru router.get('/', ...)
// výsledná adresa: /hydranty
```

Obě varianty jsou v pořádku, jen je nesmíš míchat – jinak ti vznikne
`/hydranty/hydranty`. Doporučení pro tenhle projekt: **prefix v `server.js`,
uvnitř routeru krátké cesty** (`'/'`, `'/:id'`, `'/:id/like'`).

---

## 9. Jak budou routy vypadat v tomhle projektu

Podle `todo/main.md`:

```
GET    /                       úvod
GET    /hydranty               výpis všech fotek
GET    /hydranty/novy          formulář na upload      (requireLogin)
POST   /hydranty               uložení uploadu         (requireLogin)
GET    /hydranty/:id           detail + komentáře
POST   /hydranty/:id/like      lajk                    (requireLogin)
POST   /hydranty/:id/comments  komentář                (requireLogin)
GET    /mapa                   Leaflet
GET    /api/hydrants           JSON pro mapu
GET    /register  POST /register
GET    /login     POST /login
POST   /logout
```

Všimni si dvojic `GET` + `POST` na stejné cestě u formulářů: **`GET` ukáže prázdný
formulář, `POST` přijme odeslaná data.** To je normální vzor, ne chyba.

Rozdělení do souborů:

```
src/routes/hydrants.js   # /hydranty*, /api/hydrants
src/routes/auth.js       # /register, /login, /logout
src/routes/pages.js      # /, /mapa   (nebo nech v server.js)
```

Dělíš **podle tématu, ne podle metody** – `GET /login` i `POST /login` patří
do stejného souboru vedle sebe.

---

## 10. Databáze v route: `async` / `await`

Dotaz do databáze trvá, takže handler musí být `async` a dotaz `await`:

```js
router.get('/hydranty', async (req, res) => {        // async
    const { rows } = await pool.query('SELECT ...');  // await
    res.render('hydrants/index', { hydrants: rows });
});
```

Bez `await` dostaneš do šablony `Promise`, ne data – v šabloně se pak vypíše
`[object Promise]`. To je spolehlivý příznak zapomenutého `await`.

`pool.query()` vrací objekt, data jsou až v `.rows` (pole). Proto ten rozklad
`const { rows } = ...`. Jeden řádek se bere jako `rows[0]` a **může chybět** –
vždycky ošetři prázdné pole, jinak spadneš na `undefined`.

### Chyby: `try/catch` nebo spadne server

V Expressu 4 **odmítnutý `await` v `async` handleru nikam nedojde** – Express o něm neví.
Buď obal každý dotaz:

```js
router.get('/hydranty', async (req, res, next) => {
    try {
        const { rows } = await pool.query('SELECT ...');
        res.render('hydrants/index', { hydrants: rows });
    } catch (err) {
        next(err);          // předej Expressu
    }
});
```

…nebo (jednodušší) nainstaluj `express-async-errors` a jedním `require` na začátku
`server.js` to vyřeš pro všechny routy naráz. V Expressu 5 už to funguje samo.

---

## 11. 404 a chybový handler

Úplně **na konec** `server.js`, pod všechny `app.use` s routami:

```js
// nic výš nesedělo → 404
app.use((req, res) => {
    res.status(404).render('404', { title: 'Nenalezeno' });
});

// cokoliv spadlo → 500 (čtyři parametry, podle toho ho Express pozná)
app.use((err, req, res, next) => {
    console.error(err);
    res.status(500).render('500', { title: 'Chyba serveru' });
});
```

Chybový handler musí mít **čtyři** parametry včetně nepoužitého `next`, jinak ho
Express považuje za obyčejné middleware a nikdy ho nezavolá.

---

## 12. Časté chyby – rychlý seznam

| příznak | příčina |
|---|---|
| `Cannot GET /hydranty` | route neexistuje, překlep v cestě, nebo chybí `app.use(router)` |
| stránka se točí donekonečna | handler neodpověděl – chybí `res.*` nebo `next()` |
| `ERR_HTTP_HEADERS_SENT` | dvě odpovědi v jednom handleru – chybí `return` |
| `req.body is undefined` | chybí `app.use(express.urlencoded({ extended: true }))` |
| konkrétní cesta se nikdy nespustí | je pod `/:id`, prohodit pořadí |
| v šabloně `[object Promise]` | chybí `await` u `pool.query` |
| `TypeError: Cannot read properties of undefined` | `rows[0]` u prázdného výsledku |
| server spadne při dotazu | chybí `try/catch` kolem `await` |
| změna v routě se neprojeví | server běží se starým kódem → `npm run dev` (nodemon) |
| `router is not a function` | chybí `module.exports = router` |

---

## 13. Jak route otestovat bez prohlížeče

```bash
curl -i http://localhost:3000/hydranty
curl -i -X POST -d 'title=Test&city=Brno' http://localhost:3000/hydranty
```

`-i` vypíše i stavový kód a hlavičky. Hledáš `200 OK`, `302 Found` (redirect po POSTu),
`404`, `500`. Rychlejší než klikat a vidíš přesně, co server poslal.

---

Související: [[Struktura src]] · [[Základy]] (SQL)
