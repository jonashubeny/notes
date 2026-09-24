# C# – rozhodování a cykly

Jak program nechat vybrat mezi možnostmi a jak něco udělat víckrát.
Navazuje na [[CSharp/Základy|Základy]].

---

## 1. `if`

```csharp
int vek = 20;

if (vek >= 18)
{
    Console.WriteLine("Dospělý");
}
else if (vek >= 15)
{
    Console.WriteLine("Skoro");
}
else
{
    Console.WriteLine("Dítě");
}
```

Podmínka je **vždycky v kulatých závorkách** a musí být `bool`.

### Co C# nedovolí, i když jiné jazyky ano

```csharp
int pocet = 5;
if (pocet) { }       // ❌ chyba: int není bool
if (pocet != 0) { }  // ✅ takhle
```

V JS je `if (5)` pravda. V C# musí být v podmínce opravdu `bool`.
Trochu otravné, ale nemůžeš se splést.

### Porovnávání

```csharp
a == b     // rovná se
a != b     // nerovná se
a > b   a >= b   a < b   a <= b
```

**`=` přiřazuje, `==` porovnává.** Naštěstí to v C# většinou nepřeložíš,
takže si toho všimneš hned.

### Spojování podmínek

```csharp
if (vek >= 18 && maObcanku)   { }   // A ZÁROVEŇ
if (jePatek || jeSobota)      { }   // NEBO
if (!jeHotovo)                { }   // NEGACE
```

### Text se porovnává `==`

```csharp
string a = "ahoj";
if (a == "ahoj") { }                                  // ✅ funguje
if (a.Equals("ahoj", StringComparison.OrdinalIgnoreCase)) { }  // bez ohledu na velikost
```

Tady je C# hodnější než Java – `==` u `string` porovnává **obsah**, ne adresu.

---

## 2. `switch`

Když se rozhoduješ podle jedné hodnoty z mnoha:

```csharp
string den = "pondeli";

switch (den)
{
    case "sobota":
    case "nedele":
        Console.WriteLine("Víkend");
        break;
    case "pondeli":
        Console.WriteLine("Nejhorší den");
        break;
    default:
        Console.WriteLine("Všední den");
        break;
}
```

`break` na konci každé větve je **povinný** – jinak to nepřeložíš.

### Moderní zápis – `switch` jako výraz

Tohle je čitelnější a používej to:

```csharp
string popis = den switch
{
    "sobota" or "nedele" => "Víkend",
    "pondeli"            => "Nejhorší den",
    _                    => "Všední den"
};

Console.WriteLine(popis);
```

`_` je *"cokoli ostatního"* (obdoba `default`). Výsledek se přiřadí do proměnné,
nemusíš psát `break`. Funguje i s podmínkami:

```csharp
string kategorie = vek switch
{
    < 15  => "dítě",
    < 18  => "mladistvý",
    _     => "dospělý"
};
```

---

## 3. Cykly

### `for` – když víš, kolikrát

```csharp
for (int i = 0; i < 5; i++)
{
    Console.WriteLine($"Kolo číslo {i}");
}
// vypíše 0, 1, 2, 3, 4
```

Tři části oddělené středníky:

1. `int i = 0` – **start**, proběhne jednou
2. `i < 5` – **podmínka**, testuje se před každým kolem
3. `i++` – **krok**, proběhne po každém kole

Počítá se **od nuly** a `i < 5` znamená pět kol (0–4). Kdyby tam bylo `i <= 5`,
je to šest kol. Tady se dělá klasická chyba o jedničku.

### `foreach` – přes všechny prvky (tohle chceš častěji)

```csharp
string[] jmena = { "Anna", "Bob", "Cyril" };

foreach (string jmeno in jmena)
{
    Console.WriteLine($"Ahoj {jmeno}");
}
```

Žádný index, žádná chyba o jedničku. Když nepotřebuješ vědět *kolikátý* prvek to je,
ber `foreach`.

### `while` – dokud platí podmínka

```csharp
int zbyva = 3;

while (zbyva > 0)
{
    Console.WriteLine($"Zbývá {zbyva}");
    zbyva--;              // ← bez tohohle se to nikdy nezastaví
}
```

Nekonečný cyklus zastavíš `Ctrl+C`.

### `do ... while` – aspoň jednou

```csharp
string? vstup;
do
{
    Console.Write("Napiš 'konec': ");
    vstup = Console.ReadLine();
}
while (vstup != "konec");
```

Podmínka se testuje **až po** prvním průchodu. Hodí se přesně na tohle –
na vstup, který se musí zeptat minimálně jednou.

### `break` a `continue`

```csharp
for (int i = 0; i < 10; i++)
{
    if (i == 3) continue;   // tohle kolo přeskoč, jeď dál
    if (i == 6) break;      // úplně vypadni z cyklu
    Console.WriteLine(i);
}
// 0, 1, 2, 4, 5
```

---

## 4. Pole a seznamy (jen minimum)

### Pole – pevná velikost

```csharp
string[] jmena = { "Anna", "Bob" };
int[] cisla = new int[3];          // tři nuly

Console.WriteLine(jmena[0]);       // Anna – POČÍTÁ SE OD NULY
Console.WriteLine(jmena.Length);   // 2
```

`jmena[2]` u dvouprvkového pole program **shodí** za běhu:

> `Unhandled exception. System.IndexOutOfRangeException: Index was outside
> the bounds of the array.`

Do pole se nedá přidávat. Má velikost, jakou dostalo při vzniku.

### `List<T>` – umí růst (běžnější)

```csharp
List<string> jmena = new List<string>();

jmena.Add("Anna");
jmena.Add("Bob");
jmena.Remove("Anna");

Console.WriteLine(jmena.Count);       // 1   ← Count, ne Length!
Console.WriteLine(jmena.Contains("Bob"));   // True

foreach (string j in jmena)
{
    Console.WriteLine(j);
}
```

To `<string>` znamená *"seznam textů"*. `List<int>` je seznam čísel.
Do `List<string>` nedostaneš číslo – kompilátor to zastaví.

Pole má `.Length`, `List` má `.Count`. Nelogické, ale je to tak.

---

## 5. Když něco spadne – `try/catch`

```csharp
try
{
    int cislo = int.Parse("abc");
    Console.WriteLine(cislo);
}
catch (FormatException)
{
    Console.WriteLine("To nebylo číslo.");
}
```

`try` = zkus. `catch` = když to spadne, udělej tohle a program běží dál.
Bez `try` se vypíše hláška a program **skončí**.

Na parsování vstupu je ale lepší `int.TryParse` z [[CSharp/Základy|Základy]] – výjimky
si nech na věci, které se opravdu nedají předvídat (chybějící soubor, padlá síť).

Nikdy nepiš prázdný `catch`:

```csharp
catch { }   // ❌ chyba se tiše zahodí a ty nevíš, že se stala
```

---

## Co si odnést

1. V `if` musí být `bool`, ne číslo.
2. `switch` jako výraz (`=>`) je čitelnější než starý `switch` s `break`.
3. Indexy jdou **od nuly**; `i < délka`, ne `i <= délka`.
4. `foreach` když nepotřebuješ index, `for` když potřebuješ.
5. Pole má fixní velikost a `.Length`, `List<T>` roste a má `.Count`.
