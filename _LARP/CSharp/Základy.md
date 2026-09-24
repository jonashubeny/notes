# C# – úplné základy

První poznámky k C#. Cílem je umět **vypsat text**, **uložit si hodnotu do proměnné**
a chápat, proč to C# chce zapsané právě takhle.

Navazuje na [[CSharp/Spouštění a nastavení|Spouštění a nastavení]] (jak program vůbec rozjet),
dál pokračuje [[CSharp/Řízení toku|Řízení toku]] a [[CSharp/Metody a třídy|Metody a třídy]].

---

## 0. Co je C# a jak se liší od JavaScriptu

| | JavaScript | C# |
|---|---|---|
| kdy se kontrolují chyby | až za běhu | **při překladu**, před spuštěním |
| typy | volitelné | **povinné** u každé proměnné |
| jak se spouští | `node soubor.js` | `dotnet run` (nejdřív se přeloží) |

Tohle je ten hlavní rozdíl, který na začátku bolí: **C# tě nepustí program spustit,
dokud nesedí typy.** Zní to otravně, ale znamená to, že polovinu chyb najdeš ještě
před spuštěním, ne až u zákazníka.

C# je jazyk, **.NET** je platforma (runtime + knihovny + nástroj `dotnet`).
Analogie: C# ≈ JavaScript, .NET ≈ Node.js.

---

## 1. Nejmenší možný program

Soubor `Program.cs`:

```csharp
Console.WriteLine("Ahoj světe!");
```

To je celé. Spustíš `dotnet run` a v terminálu je `Ahoj světe!`.

> **Pozor:** starší tutoriály (a hodně odpovědí na StackOverflow) ukazují tohle:
>
> ```csharp
> using System;
>
> namespace MujProgram
> {
>     class Program
>     {
>         static void Main(string[] args)
>         {
>             Console.WriteLine("Ahoj světe!");
>         }
>     }
> }
> ```
>
> Je to **pořád platné** a dělá to úplně totéž. Od C# 9 (2020) to jen nemusíš psát –
> kompilátor si tu obálku doplní sám. Říká se tomu *top-level statements*.
> Nenech se tím zmást: když někde uvidíš `static void Main`, je to ta samá věc,
> jen vypsaná ručně.

---

## 2. Výpis do konzole

### `WriteLine` vs `Write`

```csharp
Console.WriteLine("první řádek");   // vypíše a odřádkuje
Console.WriteLine("druhý řádek");

Console.Write("bez");               // vypíše a NEodřádkuje
Console.Write("mezery");            // → "bezmezery"
Console.WriteLine();                // prázdný řádek / dokončí odřádkování
```

`Line` na konci = *přidej za to konec řádku*. Nic víc.

### Text musí být v dvojitých uvozovkách

```csharp
Console.WriteLine("Ahoj");   // ✅ text (string)
Console.WriteLine('A');      // ✅ JEDEN znak (char) – jednoduché uvozovky
Console.WriteLine('Ahoj');   // ❌ chyba: v 'x' smí být jen jeden znak
Console.WriteLine(Ahoj);     // ❌ chyba: C# to čte jako jméno proměnné
```

**V C# mají jednoduché a dvojité uvozovky různý význam** – na rozdíl od JS.
`"..."` = text libovolné délky. `'...'` = přesně jeden znak.

### Vypsat i něco jiného než text

`WriteLine` si poradí s čímkoli – čísla si sám přepíše na text:

```csharp
Console.WriteLine(42);        // 42
Console.WriteLine(3.5);       // 3,5   ← pozor, česká desetinná ČÁRKA
Console.WriteLine(true);      // True  ← s velkým T
```

To `3,5` není chyba. C# formátuje čísla podle jazyka systému, a ten máš český.
Zmínka o tom, jak to případně přepnout, je v [[CSharp/Časté chyby|Časté chyby]].

---

## 3. Vkládání hodnot do textu

Tři způsoby, od nejlepšího:

### a) Interpolace – `$` před uvozovkami (tohle používej)

```csharp
string jmeno = "Jonas";
int vek = 30;

Console.WriteLine($"Ahoj {jmeno}, je ti {vek} let.");
// Ahoj Jonas, je ti 30 let.
```

Ve složených závorkách může být i výpočet:

```csharp
Console.WriteLine($"Za rok ti bude {vek + 1}.");
```

**To `$` se nesmí zapomenout.** Bez něj se vypíše doslova `Ahoj {jmeno}` –
žádná chyba, žádné varování, jen špatný výstup. Nejčastější začátečnická chyba.

### b) Spojování plusem

```csharp
Console.WriteLine("Ahoj " + jmeno + ", je ti " + vek + " let.");
```

Funguje, ale je to hůř čitelné a snadno v tom zapomeneš mezeru.

### c) Číslované placeholdery (uvidíš ve starším kódu)

```csharp
Console.WriteLine("Ahoj {0}, je ti {1} let.", jmeno, vek);
```

Nepiš to nově, ale poznej to.

### Víceřádkový text

```csharp
Console.WriteLine("""
    Tenhle text
    je přes víc řádků
    a uvozovky "uvnitř" nic neřeší.
    """);
```

Tři uvozovky = *raw string literal*. Odsazení podle zavírajících `"""` se odřízne.

---

## 4. Proměnné a typy

```csharp
int pocet = 5;
string jmeno = "Jonas";
bool jeHotovo = false;
double cena = 19.99;
```

Zápis je vždycky **`typ jméno = hodnota;`**. V JS píšeš `let x = 5`, v C#
místo `let` napíšeš, co v tom bude: `int x = 5`.

### Typy, které potřebuješ na začátku

| Typ | Co v něm je | Příklad |
|---|---|---|
| `int` | celé číslo | `int x = 42;` |
| `double` | desetinné číslo | `double x = 3.14;` |
| `decimal` | desetinné číslo na peníze | `decimal x = 19.99m;` |
| `string` | text | `string s = "ahoj";` |
| `char` | jeden znak | `char c = 'A';` |
| `bool` | `true` / `false` | `bool b = true;` |

`decimal` na peníze proto, že `double` počítá v dvojkové soustavě a `0.1 + 0.2`
tam nedá přesně `0.3`. U ceny to chceš přesně. Za číslem musí být `m` (`19.99m`).

### Typ se nedá později změnit

```csharp
int pocet = 5;
pocet = 10;        // ✅ jiné číslo, pořád int
pocet = "deset";   // ❌ chyba při překladu
```

> `error CS0029: Nelze implicitně převést typ "string" na "int"`

Tohle je ta hlavní vlastnost C#. Proměnná `int` bude `int` až do konce programu.

### `var` – typ si domyslí kompilátor

```csharp
var pocet = 5;          // kompilátor vidí 5 → int
var jmeno = "Jonas";    // → string
```

`var` **není** JS `var`. Není to "bez typu" – typ tam pořád je, jen ho nepíšeš
dvakrát. Po přeložení je `var pocet = 5;` úplně totéž jako `int pocet = 5;`.

Proto tohle nejde:

```csharp
var x;          // ❌ z čeho by typ odhadl?
var x = 5;
x = "ahoj";     // ❌ x je int, tečka
```

Kdy co: `var` když je typ vidět z pravé strany (`var l = new List<string>();`),
plný typ když není (`int pocet = Spocitej();` – ať je v kódu vidět, co vrací).

### Konstanty

```csharp
const double Dph = 1.21;
Dph = 1.15;    // ❌ chyba při překladu
```

Když víš, že se hodnota nikdy nezmění, řekni to i kompilátoru.

---

## 5. Čtení vstupu od uživatele

```csharp
Console.Write("Jak se jmenuješ? ");
string? jmeno = Console.ReadLine();

Console.WriteLine($"Ahoj {jmeno}!");
```

`Console.ReadLine()` vrátí `string?`. **Ta otazník je důležitý** – znamená
*"tady může být i nic"* (`null`), protože vstup může skončit (Ctrl+D).

Když od uživatele chceš číslo, musíš text na číslo **převést**:

```csharp
Console.Write("Kolik je ti let? ");
string? vstup = Console.ReadLine();

if (int.TryParse(vstup, out int vek))
{
    Console.WriteLine($"Za rok ti bude {vek + 1}.");
}
else
{
    Console.WriteLine("To nebylo číslo.");
}
```

`int.TryParse` je kamarádská verze: **nespadne**, když uživatel napíše `abc`,
jen vrátí `false`. Existuje i `int.Parse(vstup)`, ale ta u nesmyslu shodí
celý program. Na vstup od člověka používej vždycky `TryParse`.

To `out int vek` znamená *"a výsledek mi ulož do nové proměnné `vek`"*.

---

## 6. Základní počítání

```csharp
int a = 7, b = 2;

Console.WriteLine(a + b);   // 9
Console.WriteLine(a - b);   // 5
Console.WriteLine(a * b);   // 14
Console.WriteLine(a / b);   // 3   ← !!!  ne 3.5
Console.WriteLine(a % b);   // 1   zbytek po dělení
```

**`7 / 2` je `3`.** Dělení dvou `int` dává `int` a zbytek se zahodí.
Když chceš `3.5`, musí být aspoň jedna strana desetinná:

```csharp
Console.WriteLine(7.0 / 2);          // 3,5
Console.WriteLine((double)a / b);    // 3,5
```

To `(double)` před proměnnou je *přetypování* – "ber tohle jako double".

Zkratky:

```csharp
pocet += 1;    // pocet = pocet + 1
pocet++;       // taky +1
pocet--;       // -1
```

---

## 7. Středníky a složené závorky

```csharp
Console.WriteLine("a");   // středník na konci KAŽDÉHO příkazu
```

Na rozdíl od JS to tady není volitelné. Zapomenutý středník = chyba při překladu.

Blok kódu je vždy v `{ }`:

```csharp
if (vek >= 18)
{
    Console.WriteLine("Dospělý");
}
```

Odsazení C# nezajímá (není to Python), ale piš ho – čte to člověk.
Formátování za tebe udělá `dotnet format`.

---

## 8. Komentáře

```csharp
// jednořádkový

/* víceřádkový
   komentář */

/// <summary>Dokumentační – ukáže se v nápovědě editoru.</summary>
```

---

## Co si odnést

1. `Console.WriteLine(...)` vypíše a odřádkuje.
2. `$"...{promenna}..."` je způsob, jak do textu dostat hodnotu – **`$` nezapomenout**.
3. Každá proměnná má typ a ten se nemění. `var` typ neruší, jen ho nepíše.
4. `"text"` vs `'z'` – dvojité a jednoduché uvozovky nejsou zaměnitelné.
5. `7 / 2 == 3`, protože obojí je `int`.
6. Vstup od uživatele čti přes `Console.ReadLine()` a číslo z něj dostaň `int.TryParse`.
