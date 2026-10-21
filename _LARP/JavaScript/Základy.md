# JavaScript – úplné základy

První poznámky k JavaScriptu: **výpis**, **proměnné**, **typy hodnot**, **objekty** a **pole**.

Navazuje na [[JavaScript/Spouštění a nastavení|Spouštění a nastavení]] (jak program vůbec rozjet),
dál pokračuje [[JavaScript/Řízení toku|Řízení toku]], [[JavaScript/Funkce|Funkce]]
a [[JavaScript/Moduly, soubory a async|Moduly, soubory a async]].

---

## 1. Výpis a pravidla zápisu

```js
console.log("Ahoj, světe!");
console.log(2 + 3);          // 5
console.log("a", 42, true);  // víc věcí najednou, oddělí mezerou
```

`console.log(…)` vypíše, co mu dáš do závorky. Je to **hlavní nástroj na ladění** –
když nevíš, co program dělá, vypiš si hodnotu.

- Příkaz končí středníkem `;` (JS je občas odpustí, ale piš je).
- Text v uvozovkách `"takhle"` (jdou i `'jednoduché'`), čísla bez uvozovek.
- `//` = komentář na řádek, `/* … */` = víc řádků.
- Velká a malá písmena se rozlišují: `Console.log` ❌, `console.log` ✅.

---

## 2. Proměnné: `const` a `let`

```js
const nazev = "Bohemian Rhapsody";  // const = už se nezmění
let pocet = 0;                      // let = můžeš měnit

pocet = pocet + 1;     // ✅
pocet += 1;            // totéž kratší
pocet++;               // +1

nazev = "jiný";        // ❌ TypeError: Assignment to constant variable.
```

**Pravidlo:** piš `const`. `let` jen když víš, že hodnotu budeš měnit.

> Ve starém kódu uvidíš `var`. Dělá zhruba totéž co `let`, ale má zrádná pravidla.
> **Nepoužívej ho.**

Na rozdíl od C# se **typ nepíše** – proměnná může obsahovat cokoliv.

---

## 3. Typy hodnot

| Typ | Příklad | Poznámka |
|---|---|---|
| string | `"Queen"` | text |
| number | `354`, `3.5` | čísla celá i desetinná – jeden typ |
| boolean | `true`, `false` | ano/ne |
| array | `[1, 2, 3]` | pole, seznam |
| object | `{ nazev: "…" }` | věc s vlastnostmi |
| undefined | `undefined` | „nic tu není“ (proměnná bez hodnoty) |
| null | `null` | „záměrně prázdné“ |

```js
typeof "ahoj"    // 'string'
typeof 42        // 'number'
typeof [1, 2]    // 'object'  ← pole je technicky objekt
```

### Čísla

```js
7 / 2                     // 3.5  (na rozdíl od C# se nic neuřízne)
7 % 2                     // 1    zbytek po dělení
Math.floor(3.9)           // 3    zaokrouhlení dolů
Math.round(3.5)           // 4
```

### Texty

```js
const s = "Bohemian Rhapsody";
s.length                       // 17
s.toUpperCase()                // 'BOHEMIAN RHAPSODY'
s.includes("Rhap")             // true
s.startsWith("Bo")             // true
s.split(" ")                   // [ 'Bohemian', 'Rhapsody' ]
"spotify:track:ABC".split(":")[2]   // 'ABC'
"  mezery  ".trim()            // 'mezery'
String(5).padStart(2, "0")     // '05'
s.replace("Rhapsody", "X")     // nahradí první výskyt
```

### Template literály (skládání textu)

Zpětné uvozovky `` ` `` (na CZ klávesnici pravý Alt + `;`) a `${…}`:

```js
const interpret = "Queen";
console.log(`${interpret} - ${nazev}`);       // Queen - Bohemian Rhapsody
console.log(`Součet: ${2 + 3}`);              // Součet: 5
```

Obdoba `$"…{x}…"` z C#.

---

## 4. Objekty

Proměnná s víc vlastnostmi najednou:

```js
const pisnicka = {
  nazev: "Bohemian Rhapsody",
  interpret: "Queen",
  delka: 354,
};

pisnicka.nazev            // 'Bohemian Rhapsody'
pisnicka["nazev"]         // totéž – hranaté závorky jdou použít s proměnnou
pisnicka.rok = 1975;      // přidání vlastnosti (i u const objektu!)
pisnicka.neexistuje       // undefined
```

`const` hlídá jen to, aby do proměnné nešel **jiný** objekt – obsah měnit jde.

### Objekt jako slovník

Klíče můžou být libovolné texty, takže objekt funguje jako slovník (`Dictionary` v C#):

```js
const stazeno = { a1: "Queen - Bohemian Rhapsody.mp3" };

"a1" in stazeno                 // true – je tam?
"x9" in stazeno                 // false
stazeno["b2"] = "AC_DC - Thunderstruck.mp3";   // přidání
delete stazeno["a1"];           // odebrání
Object.keys(stazeno)            // [ 'b2' ] – pole klíčů
Object.keys(stazeno).length     // počet položek
```

### Destrukturace

Vytažení vlastností rovnou do proměnných:

```js
const { nazev, interpret } = pisnicka;
console.log(nazev);   // 'Bohemian Rhapsody'
```

### `?.` a `??`

```js
pisnicka.album?.nazev     // když album neexistuje → undefined místo chyby
pisnicka.delka ?? 0       // když je delka undefined/null → 0
```

---

## 5. Pole

```js
const playlist = [
  { nazev: "Bohemian Rhapsody", interpret: "Queen", delka: 354 },
  { nazev: "Thunderstruck", interpret: "AC/DC", delka: 292 },
];

playlist.length          // 2
playlist[0].nazev        // první prvek má index 0, ne 1!
playlist[5]              // undefined (neexistuje)
playlist.push({ nazev: "Shape of You", interpret: "Ed Sheeran", delka: 233 });  // přidá na konec
playlist.includes(x)     // je tam?
```

Procházení pole je v [[JavaScript/Řízení toku|Řízení toku]], `filter` a `map` v [[JavaScript/Funkce|Funkce]].

### Set (množina)

Seznam **bez duplikátů**, rychlé hledání:

```js
const ids = new Set(["a1", "b2"]);
ids.has("a1")     // true
ids.add("c3");
ids.size          // 3
```

---

## 6. JSON

Textový formát, který vypadá skoro jako JS objekt. Na ukládání dat do souborů.

```js
const text = JSON.stringify(playlist, null, 2);  // objekt → text (2 = odsazení)
const zpatky = JSON.parse(text);                 // text → objekt
```

Rozdíly proti JS objektu: klíče **musí** být v `"dvojitých uvozovkách"`,
žádné komentáře, žádná čárka za posledním prvkem.
