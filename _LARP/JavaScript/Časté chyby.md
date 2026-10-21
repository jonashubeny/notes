# Časté chyby v JavaScriptu (pro začátečníka)

Chyby, na které se narazí hned na začátku, i s hláškou, kterou Node.js vypíše.
Navazuje na [[JavaScript/Základy|Základy]], [[JavaScript/Řízení toku|Řízení toku]],
[[JavaScript/Funkce|Funkce]] a [[JavaScript/Moduly, soubory a async|Moduly, soubory a async]].

---

## Rychlá kontrola, než začneš hledat

- [ ] Uložil jsem soubor, než jsem pustil `node`?
- [ ] Jsem ve správné složce (tam, kde je `package.json` a `node_modules`)?
- [ ] Má každá `(`, `{`, `[` a uvozovka svůj pár?
- [ ] Porovnávám přes `===`, ne `=`?
- [ ] Je před voláním async funkce `await`?
- [ ] Dočetl jsem chybovou hlášku **i s číslem řádku**?

> Nejlepší ladicí nástroj je `console.log`: vypiš si hodnotu těsně před místem, kde to padá.

---

## Chybové hlášky

| Hláška | Co to znamená |
|---|---|
| `SyntaxError: Unexpected token` | Překlep v zápisu – chybí závorka, uvozovka nebo čárka. Koukni na ukázaný řádek **i ten před ním**. |
| `ReferenceError: x is not defined` | Proměnná nebo funkce neexistuje. Překlep v názvu? Zapomenutý `import`? |
| `TypeError: Cannot read properties of undefined (reading 'nazev')` | Čteš vlastnost z něčeho, co je `undefined` – např. `playlist[5].nazev`, když má pole 3 prvky. Vypiš si to. |
| `TypeError: x is not a function` | Voláš `x()`, ale `x` není funkce – překlep, nebo špatný import. |
| `TypeError: Assignment to constant variable.` | Měníš `const`. Použij `let`. |
| `Cannot use import statement outside a module` / varování o typu modulu | V `package.json` chybí `"type": "module"`. |
| `await is only valid in async functions` | `await` ve funkci bez `async`. |
| `Cannot find package 'xyz'` | Spouštíš v jiné složce, než kde jsi dělal `npm install`, nebo jsi install zapomněl. |
| `ENOENT: no such file or directory` | Soubor nebo program nebyl nalezen. Špatná cesta, nebo program není v `PATH` (`which nazev`). |
| `Unexpected token … in JSON` | `JSON.parse` dostal text, který není platný JSON (čárka navíc, jednoduché uvozovky…). |

---

## Zrádné věci bez chybové hlášky

### 1. Zapomenutý `await`

```js
const text = readFile("a.json", "utf8");
console.log(text);           // Promise { <pending> }
```

Program nespadne, jen pracuje s Promise místo s výsledkem. Když ve výpisu uvidíš
`Promise { <pending> }`, chybí `await`.

### 2. `=` místo `===`

```js
if (x = 5) { … }     // nepřirovnává – PŘIŘADÍ 5 do x, a podmínka je vždycky pravda
if (x === 5) { … }   // ✅
```

### 3. Obyčejné uvozovky místo zpětných

```js
console.log("Ahoj ${jmeno}");    // vypíše doslova: Ahoj ${jmeno}
console.log(`Ahoj ${jmeno}`);    // ✅
```

Obdoba zapomenutého `$` v C#.

### 4. Index od nuly

```js
const pole = ["a", "b", "c"];
pole[1]               // 'b', ne 'a'
pole[pole.length]     // undefined – poslední je pole[pole.length - 1]
```

### 5. `+` u textu a čísla

```js
"5" + 3     // '53'  ← spojí jako text
5 + 3       // 8
Number("5") + 3   // 8
```

### 6. map s objektem bez závorek

```js
pole.map((p) => { nazev: p.nazev });     // vrátí pole undefined! { } se bere jako tělo funkce
pole.map((p) => ({ nazev: p.nazev }));   // ✅
```

### 7. `~` v cestě

```js
await readFile("~/Hudba/a.json");   // ENOENT – Node nezná ~, to umí jen terminál
```

Použij `homedir()` z `node:os` (viz [[JavaScript/Moduly, soubory a async|Moduly, soubory a async]]).
