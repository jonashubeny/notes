# JavaScript – řízení toku

Podmínky a cykly. Navazuje na [[JavaScript/Základy|Základy]], dál pokračuje [[JavaScript/Funkce|Funkce]].

---

## 1. Podmínka `if`

```js
if (p.delka > 300) {
  console.log("dlouhá");
} else if (p.delka > 180) {
  console.log("normální");
} else {
  console.log("krátká");
}
```

### Porovnání

| Zápis | Význam |
|---|---|
| `===` | rovná se (**vždy tři rovnítka**) |
| `!==` | nerovná se |
| `>` `<` `>=` `<=` | větší, menší… |

> **Pozor:** `==` (dvě rovnítka) taky existuje, ale převádí typy: `"5" == 5` je `true`.
> To skoro nikdy nechceš. Piš `===`.

### Spojování podmínek

```js
a && b     // a zároveň b
a || b     // nebo
!a         // negace
!(p.id in stazeno)    // „id NENÍ ve stazeno“
```

### Co se počítá jako nepravda

`false`, `0`, `""` (prázdný text), `null`, `undefined`, `NaN`. Všechno ostatní je pravda.

```js
if (soubor) { … }     // proběhne, když soubor není null/undefined/""
```

---

## 2. Cyklus `for…of` – pro každý prvek

```js
for (const p of playlist) {
  console.log(p.nazev);
}
```

Čti: „pro každé `p` z playlistu udělej, co je v závorkách“. Při každém kole je v `p` další prvek.

### S pořadím

```js
for (const [i, p] of playlist.entries()) {
  console.log(`${i + 1}. ${p.nazev}`);      // i začíná od 0
}
```

### Počítání přes `let` mimo cyklus

```js
let dlouhych = 0;
for (const p of playlist) {
  if (p.delka > 240) {
    dlouhych++;
  }
}
console.log(`Delších než 4 minuty: ${dlouhych}`);
```

---

## 3. Klasický `for` a `while`

```js
for (let i = 0; i < 5; i++) {
  console.log(i);         // 0 1 2 3 4
}

let n = 3;
while (n > 0) {
  console.log(n);
  n--;
}
```

`for…of` je na procházení polí čitelnější – klasický `for` je potřeba jen když opravdu chceš
pracovat s čísly.

---

## 4. Procházení objektu

```js
for (const klic of Object.keys(stazeno)) {
  console.log(klic, stazeno[klic]);
}

for (const [klic, hodnota] of Object.entries(stazeno)) {
  console.log(klic, hodnota);
}
```

---

## 5. `break` a `continue`

```js
for (const p of playlist) {
  if (!p.id) continue;   // přeskoč tenhle prvek, pokračuj dalším
  if (p.nazev === "Stop") break;   // ukonči celý cyklus
}
```
