# JavaScript – moduly, soubory a async/await

Jak v Node.js číst a zapisovat soubory, spouštět jiné programy a hlavně **proč se všude píše `await`**.
Navazuje na [[JavaScript/Funkce|Funkce]] a [[JavaScript/Spouštění a nastavení|Spouštění a nastavení]].

---

## 1. Moduly a `import`

Node.js má vestavěné nástroje rozdělené do **modulů**. Co potřebuješ, si importuješ
na začátku souboru:

```js
import { readFile, writeFile, mkdir, unlink, access } from "node:fs/promises"; // soubory
import { execFile } from "node:child_process";   // spouštění programů
import { promisify } from "node:util";
import { homedir } from "node:os";               // domovská složka
import path from "node:path";                    // skládání cest
```

- `{ … }` = vezmi z modulu jen tyhle věci (jmenný import)
- bez závorek = vezmi „hlavní“ export modulu (default import)
- prefix `node:` = vestavěný modul Node.js; bez prefixu = knihovna z npm

```js
import spotifyUrlInfo from "spotify-url-info";   // knihovna z node_modules
```

Vyžaduje `"type": "module"` v `package.json` (viz [[JavaScript/Spouštění a nastavení|Spouštění a nastavení]]).

### Vlastní moduly

```js
// pomocne.js
export function secti(a, b) { return a + b; }

// index.js
import { secti } from "./pomocne.js";      // u vlastních souborů ./ a přípona .js
```

---

## 2. async / await – proč se čeká

Čtení souboru, stahování z internetu nebo spuštění programu chvíli trvá.
Node.js na to **nečeká automaticky** – místo výsledku ti hned vrátí **Promise**
(slib: „výsledek bude“). Slovo `await` znamená: **počkej, až se slib splní, a dej mi výsledek.**

```js
const bez = readFile("package.json", "utf8");
console.log(bez);        // Promise { <pending> }   ← zapomenutý await

const s = await readFile("package.json", "utf8");
console.log(s);          // obsah souboru
```

Pravidla:
- `await` jde použít jen ve funkci označené `async` …
- … nebo přímo v hlavním těle souboru (*top-level await*, jen v modulech).
- Async funkce **vždycky** vrací Promise → když ji voláš, zase `await`.

```js
async function nactiText(soubor) {
  return await readFile(soubor, "utf8");
}
const obsah = await nactiText("package.json");
```

### Jedno po druhém vs. najednou

```js
for (const p of playlist) {
  await stahni(p);                  // postupně – další začne, až předchozí skončí
}

await Promise.all(playlist.map((p) => stahni(p)));   // všechno najednou
```

---

## 3. try / catch – chyby

Když se něco pokazí (soubor neexistuje, program spadne), vyhodí se **chyba** a program
skončí. Chybu jde „chytit“:

```js
try {
  const t = await readFile("neexistuje.json", "utf8");
  console.log(t);
} catch (chyba) {
  console.log(`Nepovedlo se: ${chyba.message}`);
}
```

Typický vzor – načti JSON, a když soubor není, vrať výchozí hodnotu:

```js
async function nactiJson(soubor, vychozi) {
  try {
    return JSON.parse(await readFile(soubor, "utf8"));
  } catch {
    return vychozi;
  }
}
```

(`catch` bez `(chyba)` jde, když chybu nepotřebuješ.)

---

## 4. Soubory a složky

```js
await writeFile("data.json", JSON.stringify(data, null, 2), "utf8");  // zápis (přepíše)
const text = await readFile("data.json", "utf8");                     // čtení
await mkdir("~/Hudba/x", { recursive: true });   // ❌ ~ Node nezná!
await mkdir(path.join(homedir(), "Hudba", "x"), { recursive: true }); // ✅
await unlink("soubor.mp3");                      // smazání souboru
await access("soubor.mp3");                      // vyhodí chybu, když neexistuje
```

### Cesty

```js
path.join("/home/jonas", "Hudba", "a.mp3")   // '/home/jonas/Hudba/a.mp3'
path.basename("/home/jonas/Hudba/a.mp3")     // 'a.mp3'
import.meta.dirname                          // složka, ve které leží tento .js soubor
```

`path.join` dává lomítka správně sám – neslepuj cesty ručně přes `+`.

---

## 5. Spouštění jiných programů

```js
import { execFile } from "node:child_process";
import { promisify } from "node:util";

const spust = promisify(execFile);   // verze pro await

const { stdout } = await spust("yt-dlp", ["--version"]);
console.log(stdout.trim());
```

- 1. parametr = název programu, 2. = **pole argumentů** (každý kousek příkazu jeden prvek).
  Uvozovky kolem argumentů nejsou potřeba.
- `{ stdout }` = destrukturace: vytáhne, co program vypsal (je tam i `stderr`).
- Když program skončí chybou → vyhodí chybu → `try/catch`.
- `ENOENT` = program nebyl nalezen (není nainstalovaný nebo není v `PATH`).
- Volitelně 3. parametr s nastavením, např. `{ timeout: 60000 }` (ms).

---

## 6. fetch – stahování z internetu

V Node.js je vestavěný, nic se neimportuje:

```js
const odpoved = await fetch("https://example.com/data.json");
const data = await odpoved.json();     // nebo .text() pro HTML/text
```
