# JavaScript – spouštění a nastavení (Fedora)

Praktická část k [[JavaScript/Základy|Základy]] – jak dostat Node.js na disk, jak udělat projekt
a jak ho pustit.

---

## 0. Co je Node.js

**JavaScript** je jazyk. Původně běžel jen v prohlížeči (dělá weby „živé“).
**Node.js** je program, který umí JavaScript spustit i **mimo prohlížeč** – přímo na počítači.
Díky tomu jde v JS psát skripty, které čtou soubory, stahují věci z internetu nebo spouští jiné programy.

Analogie s C#: JavaScript ≈ C#, Node.js ≈ .NET.

---

## 1. Instalace

```bash
sudo dnf install nodejs
node --version      # v22.x → hotovo (stačí 20.11 a novější)
npm --version       # npm přijde s Node.js automaticky
```

---

## 2. Spuštění jednoho souboru

Soubor `ahoj.js`:

```js
console.log("Ahoj, světe!");
```

```bash
node ahoj.js        # → Ahoj, světe!
```

Žádný překlad jako u C# – Node soubor rovnou spustí. Chyby se proto ukážou
**až za běhu**, na řádku, kam program dojde.

---

## 3. Nový projekt

```bash
mkdir -p ~/muj-projekt && cd ~/muj-projekt
npm init -y          # vytvoří package.json
code .               # otevře složku ve VS Code
```

`package.json` je popis projektu (název, verze, knihovny). Hned do něj přidej:

```json
{
  "name": "muj-projekt",
  "type": "module",
  ...
}
```

`"type": "module"` = používám moderní zápis s `import` (viz [[JavaScript/Moduly, soubory a async|Moduly, soubory a async]]).
Bez něj Node buď píše varování, nebo hlásí `Cannot use import statement outside a module`.

---

## 4. Knihovny z npm

**npm** je „obchod“ s hotovým kódem od jiných lidí.

```bash
npm install nazev-knihovny        # přidá knihovnu do projektu
npm install                       # nainstaluje vše, co je v package.json
npm update                        # aktualizuje knihovny
```

Co se stane:
- vznikne složka `node_modules/` – stažený kód. **Nikdy ho needituj ručně.**
  Jde kdykoliv smazat a obnovit přes `npm install`. Do gitu nepatří (`.gitignore`).
- v `package.json` přibude knihovna v `"dependencies"`.
- vznikne `package-lock.json` – přesné verze, ty do gitu patří.

> **Zvyk:** když použiješ cizí knihovnu poprvé, vždycky si nejdřív `console.log` vypiš,
> co ti vrací. Dokumentace bývá neúplná, výpis nelže.

---

## 5. Skripty v package.json

```json
"scripts": {
  "start": "node index.js"
}
```

```bash
npm run start        # spustí "node index.js"
```

---

## 6. Argumenty z příkazové řádky

```bash
node skript.js --smazat
```

```js
console.log(process.argv);
// [ '/usr/bin/node', '/cesta/skript.js', '--smazat' ]

if (process.argv.includes("--smazat")) {
  // ...
}
```
