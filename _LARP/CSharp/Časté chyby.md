# Časté chyby v C# (pro začátečníka)

Chyby, na které se narazí hned na začátku, i s hláškou, kterou vyhodí.
Navazuje na [[CSharp/Základy|Základy]], [[CSharp/Řízení toku|Řízení toku]] a [[CSharp/Metody a třídy|Metody a třídy]].

---

## Rychlá kontrola, než začneš hledat

- [ ] Středník na konci **každého** příkazu
- [ ] Před uvozovkami s `{proměnnou}` je `$`
- [ ] Text `"dvojité"`, jeden znak `'j'`
- [ ] Sedí typy vlevo a vpravo od `=`
- [ ] Každá `{` má svou `}`
- [ ] Pustil jsem `dotnet run` **po** uložení souboru?
- [ ] Jsem ve složce s `.csproj`?
- [ ] Je to `error`, nebo jen `warning`?

---

## 1. Zapomenuté `$` u interpolace

```csharp
string jmeno = "Jonas";
Console.WriteLine("Ahoj {jmeno}");    // vypíše doslova:  Ahoj {jmeno}
Console.WriteLine($"Ahoj {jmeno}");   // Ahoj Jonas
```

**Žádná chyba, žádné varování** – jen špatný výstup. Proto je to nejzrádnější.
Když se ti ve výstupu objeví složené závorky, hledej chybějící `$`.

---

## 2. `7 / 2` dává `3`

```csharp
int a = 7, b = 2;
Console.WriteLine(a / b);             // 3    ← zbytek se zahodil
Console.WriteLine((double)a / b);     // 3,5
```

Dělení dvou `int` je celočíselné. Nic nespadne, jen počítáš špatně.
Zrádné u průměrů:

```csharp
int soucet = 7, pocet = 2;
double prumer = soucet / pocet;         // 3   ← dělení proběhlo PŘED převodem
double prumer2 = (double)soucet / pocet; // 3,5  ✅
```

---

## 3. `error CS0029: Nelze implicitně převést typ "string" na "int"`

```csharp
int pocet = "5";              // ❌
int pocet = 5;                // ✅
int pocet = int.Parse("5");   // ✅ text → číslo ručně
```

Typický případ: vstup z konzole je vždycky `string`.

```csharp
int vek = Console.ReadLine();                    // ❌
int.TryParse(Console.ReadLine(), out int vek);   // ✅
```

---

## 4. `error CS0103: Název "x" neexistuje v aktuálním kontextu`

Tři důvody, v tomhle pořadí pravděpodobnosti:

```csharp
Console.WriteLine(pocet);      // proměnná ještě nevznikla
int pocet = 5;
```

```csharp
int pocet = 5;
Console.WriteLine(Pocet);      // velké P – jiné jméno
```

```csharp
if (true)
{
    int x = 5;
}
Console.WriteLine(x);          // x skončilo se složenou závorkou
```

**C# rozlišuje velká a malá písmena.** `pocet` ≠ `Pocet` ≠ `POCET`.
A proměnná platí jen uvnitř bloku `{ }`, kde vznikla.

---

## 5. `error CS0165: Použití nepřiřazené lokální proměnné`

```csharp
int pocet;
Console.WriteLine(pocet);     // ❌ co v tom má být?

int pocet = 0;                // ✅
Console.WriteLine(pocet);
```

C# nemá `undefined`. Proměnná se musí nastavit, než ji přečteš.

---

## 6. `NullReferenceException` za běhu

```
Unhandled exception. System.NullReferenceException:
Object reference not set to an instance of an object.
```

Přeložilo se, ale spadlo. Někde jsi sáhl na `.něco` u hodnoty, která byla `null`.

```csharp
string? vstup = Console.ReadLine();
Console.WriteLine(vstup.Length);        // ⚠️ spadne, když vstup skončí

if (vstup is not null)                  // ✅
{
    Console.WriteLine(vstup.Length);
}

Console.WriteLine(vstup?.Length);       // ✅ nebo takhle
```

Když ti kompilátor u něčeho hlásí varování o `null`, tohle je přesně to, co
předpovídá. Neignoruj to.

---

## 7. `IndexOutOfRangeException`

```csharp
string[] jmena = { "Anna", "Bob" };
Console.WriteLine(jmena[2]);       // ❌ spadne
Console.WriteLine(jmena[1]);       // ✅ Bob – poslední prvek
```

Dva prvky mají indexy **0 a 1**. Poslední index je vždycky `Length - 1`.

Stejná chyba v cyklu:

```csharp
for (int i = 0; i <= jmena.Length; i++)   // ❌ o jedno kolo víc
for (int i = 0; i <  jmena.Length; i++)   // ✅
```

Nebo se tomu úplně vyhni:

```csharp
foreach (string j in jmena) { }           // ✅ index vůbec nepotřebuješ
```

---

## 8. `if (pocet)` nejde

```csharp
int pocet = 5;
if (pocet) { }          // ❌ error CS0029: int na bool
if (pocet != 0) { }     // ✅
if (pocet > 0) { }      // ✅
```

Podmínka musí být `bool`. Zvyk z JS/Pythonu tady nefunguje.

---

## 9. `=` místo `==`

```csharp
if (pocet = 5) { }      // ❌ přiřazení, ne porovnání
if (pocet == 5) { }     // ✅
```

C# to zastaví (`int` není `bool`) – u `bool` proměnné by ale prošlo:

```csharp
bool hotovo = false;
if (hotovo = true) { }   // ⚠️ přeloží se! nastaví hotovo na true a je to vždy pravda
if (hotovo)      { }     // ✅
```

---

## 10. `error CS0161: Ne všechny cesty kódu vracejí hodnotu`

```csharp
int Vetsi(int a, int b)
{
    if (a > b) return a;      // ❌ a co když a <= b?
}

int Vetsi(int a, int b)
{
    if (a > b) return a;
    return b;                 // ✅
}
```

---

## 11. Zapomenutý `break` ve starém `switch`

```csharp
switch (den)
{
    case "sobota":
        Console.WriteLine("Víkend");    // ❌ error CS0163
    case "pondeli":
        Console.WriteLine("Práce");
        break;
}
```

Buď dopiš `break` do každé větve, nebo použij `switch` jako výraz z [[CSharp/Řízení toku|Řízení toku]] –
tam se `break` nepíše vůbec.

---

## 12. Desetinná čárka místo tečky ve výstupu

```csharp
Console.WriteLine(3.5);       // 3,5
```

**Není to chyba.** C# formátuje čísla podle jazyka systému a ten máš český.
V kódu se **vždy** píše tečka (`3.5`), ve výstupu se objeví čárka.

Když chceš ve výstupu tečku (třeba do CSV nebo do JSONu):

```csharp
using System.Globalization;
Console.WriteLine(3.5.ToString(CultureInfo.InvariantCulture));   // 3.5
```

Stejná past u parsování – `double.Parse("3.5")` v českém prostředí selže,
protože čeká `"3,5"`. U dat ze souboru nebo z API dej vždycky `InvariantCulture`.

---

## 13. `'ahoj'` v jednoduchých uvozovkách

```csharp
char c = 'A';        // ✅ jeden znak
string s = "ahoj";   // ✅ text
char c2 = 'ahoj';    // ❌ error CS1012: příliš mnoho znaků
string s2 = 'ahoj';  // ❌
```

V JS je to jedno, v C# ne.

---

## 14. `Length` vs `Count`

```csharp
string[] pole = { "a", "b" };
List<string> seznam = new List<string> { "a", "b" };

pole.Length      // ✅ pole a string mají Length
seznam.Count     // ✅ List a kolekce mají Count

pole.Count       // ❌
seznam.Length    // ❌
```

Není v tom logika, prostě si to pamatuj.

---

## 15. `string` se nedá měnit

```csharp
string s = "ahoj";
s.ToUpper();                       // ⚠️ nic se nestalo, s je pořád "ahoj"
Console.WriteLine(s);              // ahoj

s = s.ToUpper();                   // ✅
Console.WriteLine(s);              // AHOJ
```

Metody u `string` **vracejí nový text**, původní nemění. `Replace`, `Trim`,
`ToLower`, `Substring` – u všech musíš výsledek přiřadit, jinak zmizí.

---

## 16. Změna souboru nic nedělá

Přeložený program je něco jiného než soubor na disku. Uložení v editoru nestačí –
po každé úpravě musí proběhnout:

```bash
dotnet run
```

Nebo si nech překládat automaticky:

```bash
dotnet watch run
```

---

## 17. `Nebyl nalezen žádný soubor projektu ani řešení`

Jsi ve špatné složce. `dotnet run` chce být tam, kde je `.csproj`:

```bash
ls *.csproj          # když nic, jsi jinde
cd ~/kod/cvicimcsharp
dotnet run
```

---

## Jak vůbec hledat chybu

1. **Přečti první chybu, ne poslední.** Jedna chybějící `}` vygeneruje deset hlášek.
   Oprav první, přelož znovu.
2. **Kouknout na řádek a znak v závorce** – `Program.cs(12,9)` je řádek 12, znak 9.
3. **Kód chyby do googlu** – `CS0029`, `CS0103` atd. jsou dokumentované na
   learn.microsoft.com.
4. **Rozlišuj `error` a `warning`.** `error` = nepřeložilo se. `warning` = běží,
   ale něco je divné (a u `null` má varování většinou pravdu).
5. **Nerozumíš, jaký typ tam je?** Nechej na proměnné myš v editoru, nebo si dej
   `Console.WriteLine(x.GetType());`.
