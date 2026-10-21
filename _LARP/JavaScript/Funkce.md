# JavaScript – funkce

Navazuje na [[JavaScript/Řízení toku|Řízení toku]], dál pokračuje
[[JavaScript/Moduly, soubory a async|Moduly, soubory a async]].

---

## 1. Funkce

Kus kódu s názvem, který jde spustit kdykoliv znovu. Dostane vstup (**parametry**)
a přes `return` vrátí výsledek.

```js
function pozdrav(jmeno) {
  return `Ahoj, ${jmeno}!`;
}

pozdrav("Jondo");    // 'Ahoj, Jondo!'
pozdrav("světe");    // 'Ahoj, světe!'
```

- Bez `return` funkce vrací `undefined`.
- `return` funkci okamžitě ukončí.
- Typy parametrů se nepíšou (na rozdíl od C#).

### Výchozí hodnota parametru

```js
function nacti(soubor, vychozi = {}) { … }
nacti("a.json");        // vychozi je {}
```

---

## 2. Arrow funkce (šipka)

Kratší zápis téže věci:

```js
const pozdrav = (jmeno) => `Ahoj, ${jmeno}!`;      // jeden výraz → return je automaticky

const secti = (a, b) => {
  const vysledek = a + b;
  return vysledek;                                 // se složenými závorkami return psát musíš
};
```

Nejčastěji se používají jako **vstup do jiných funkcí** – viz filter a map.

---

## 3. Funkce pole: `filter`, `map` a spol.

Pole mají vestavěné funkce, které jako parametr berou **jinou funkci**.
Originální pole nemění – vrací **nové**.

```js
// filter: nech jen prvky, pro které funkce vrátí true
const dlouhe = playlist.filter((p) => p.delka > 240);

// map: z každého prvku udělej něco nového
const nazvy = playlist.map((p) => p.nazev);
// [ 'Bohemian Rhapsody', 'Thunderstruck', ... ]

// převod na jiný tvar objektu – kulaté závorky kolem { } jsou nutné!
const jednodussi = playlist.map((p) => ({ nazev: p.nazev, id: p.id }));

// řetězení
const nazvyDlouhych = playlist
  .filter((p) => p.delka > 240)
  .map((p) => p.nazev);
```

Další užitečné:

```js
playlist.find((p) => p.id === "a1")       // první vyhovující prvek (nebo undefined)
playlist.some((p) => p.delka > 600)       // je aspoň jeden? → true/false
playlist.every((p) => p.delka > 0)        // jsou všechny? → true/false
[3, 1, 2].sort((a, b) => a - b)           // seřazení (POZOR: mění původní pole)
```

---

## 4. Regulární výrazy (jen základ)

Vzor znaků mezi lomítky. Hodí se na hledání a nahrazování.

```js
// nahradí všechny znaky, které nesmí být v názvu souboru, podtržítkem
"AC/DC - Thunderstruck".replace(/[\\/:*?"<>|]/g, "_");   // 'AC_DC - Thunderstruck'

// ~ na začátku textu → domovská složka
"~/Hudba".replace(/^~/, "/home/jonas");                   // '/home/jonas/Hudba'
```

- `[…]` = kterýkoliv z těchto znaků
- `^` = začátek textu
- `g` za lomítkem = všechny výskyty, ne jen první

Na začátek stačí je umět použít, rozumět jim do detailu není potřeba.

---

## 5. Async funkce

Funkce, ve které se čeká (`await`), musí mít před sebou `async`.
Vysvětlení v [[JavaScript/Moduly, soubory a async|Moduly, soubory a async]].

```js
async function nactiText(soubor) {
  return await readFile(soubor, "utf8");
}

const nactiText2 = async (soubor) => await readFile(soubor, "utf8");
```
