# C# – metody a třídy

Jak si pojmenovat kus kódu a jak si vyrobit vlastní typ.
Navazuje na [[CSharp/Základy|Základy]] a [[CSharp/Řízení toku|Řízení toku]].

---

## 1. Metoda = pojmenovaný kus kódu

V jiných jazycích *funkce*. V C# se tomu říká **metoda**, protože skoro vždycky
sedí uvnitř nějaké třídy.

```csharp
void Pozdrav(string jmeno)
{
    Console.WriteLine($"Ahoj {jmeno}!");
}

Pozdrav("Jonas");     // Ahoj Jonas!
Pozdrav("Anna");      // Ahoj Anna!
```

Rozebráno:

```
void      Pozdrav   (string jmeno)
↑         ↑          ↑
co vrací  jméno      co dostane (typ + jméno)
```

`void` = **nevrací nic**, jen něco udělá.

### Metoda, která vrací hodnotu

```csharp
int Secti(int a, int b)
{
    return a + b;
}

int vysledek = Secti(3, 4);       // 7
Console.WriteLine(Secti(10, 5));  // 15
```

Když místo `void` napíšeš typ, **musíš** v každé cestě kódem něco vrátit:

```csharp
int Vetsi(int a, int b)
{
    if (a > b) return a;
    // ❌ error CS0161: ne všechny cesty kódu vracejí hodnotu
}
```

`return` navíc metodu okamžitě ukončí – kód za ním se neprovede.

### Krátký zápis jednou výrazem

```csharp
int Secti(int a, int b) => a + b;
```

Totéž, jen bez závorek a `return`. Používá se to hodně.

### Pojmenované a nepovinné parametry

```csharp
void Vypis(string text, int kolikrat = 1)
{
    for (int i = 0; i < kolikrat; i++)
    {
        Console.WriteLine(text);
    }
}

Vypis("ahoj");                  // 1×   – kolikrat si vezme 1
Vypis("ahoj", 3);               // 3×
Vypis("ahoj", kolikrat: 3);     // totéž, ale je vidět, co to 3 znamená
```

### Stejné jméno, různé parametry (přetěžování)

```csharp
int Secti(int a, int b)         => a + b;
double Secti(double a, double b) => a + b;

Console.WriteLine(Secti(1, 2));       // 3    – vybere int verzi
Console.WriteLine(Secti(1.5, 2.5));   // 4    – vybere double verzi
```

Kompilátor si vybere podle typů, které mu dáš. Proto má `Console.WriteLine`
verzi pro text, pro číslo, pro `bool`…

### Pořadí v souboru

V top-level kódu (viz [[CSharp/Základy|Základy]]) nemusí být metoda nad voláním –
můžeš ji volat i dřív, než je v souboru napsaná:

```csharp
Pozdrav("Jonas");               // volání nahoře

void Pozdrav(string jmeno)      // definice dole – funguje
{
    Console.WriteLine($"Ahoj {jmeno}!");
}
```

Zvyk je psát metody **na konec souboru**, pod běžný kód. Drž se toho – hlavní tok
programu je pak vidět hned na prvních řádcích.

Co ale pořadí **hlídá**: `class` a `record` musí být až **za** běžným kódem.

```csharp
class Osoba { }                 // ❌ error CS8803
Console.WriteLine("ahoj");

Console.WriteLine("ahoj");      // ✅ nejdřív kód
class Osoba { }                 //    pak deklarace typů
```

---

## 3. Třída = vlastní typ

Když v programu pořád dokola tahám jméno + věk + email jedné osoby,
je čas si "osobu" pojmenovat.

```csharp
class Osoba
{
    public string Jmeno { get; set; } = "";
    public int Vek { get; set; }

    public void Predstav()
    {
        Console.WriteLine($"Jsem {Jmeno} a je mi {Vek}.");
    }
}
```

Použití:

```csharp
Osoba o = new Osoba();
o.Jmeno = "Jonas";
o.Vek = 30;
o.Predstav();          // Jsem Jonas a je mi 30.
```

Nebo kratší zápis rovnou při vzniku:

```csharp
Osoba o = new Osoba { Jmeno = "Jonas", Vek = 30 };
var o2 = new Osoba { Jmeno = "Anna", Vek = 25 };
```

### Třída vs. objekt

**Třída je předpis, objekt je konkrétní kus.** Třída `Osoba` je formulář,
`new Osoba()` je jeden vyplněný formulář. Z jedné třídy uděláš objektů kolik chceš:

```csharp
var lidi = new List<Osoba>
{
    new Osoba { Jmeno = "Anna", Vek = 25 },
    new Osoba { Jmeno = "Bob",  Vek = 40 }
};

foreach (Osoba clovek in lidi)
{
    clovek.Predstav();
}
```

To `new` je moment, kdy objekt vzniká v paměti.

### Vlastnost (property) vs. pole (field)

```csharp
class Osoba
{
    public string Jmeno { get; set; }     // vlastnost – tohle používej
    public string jmeno;                  // pole – jen pro vnitřní použití
}
```

`{ get; set; }` znamená *"dá se to přečíst i nastavit"*. Vypadá to jako zbytečná
ceremonie, ale později do toho můžeš přidat kontrolu, aniž bys měnil kód okolo.

Když se má hodnota nastavit jen jednou a pak už ne:

```csharp
public string Jmeno { get; init; }        // jen při vzniku objektu
```

---

## 4. Konstruktor – co se má stát při vzniku

```csharp
class Osoba
{
    public string Jmeno { get; set; }
    public int Vek { get; set; }

    public Osoba(string jmeno, int vek)
    {
        Jmeno = jmeno;
        Vek = vek;
    }
}

var o = new Osoba("Jonas", 30);
```

Konstruktor = metoda, která **má stejné jméno jako třída** a nemá návratový typ.
Výhoda: nedá se vyrobit `Osoba` bez jména. Kompilátor tě k tomu donutí.

Od C# 12 jde zkráceně:

```csharp
class Osoba(string jmeno, int vek)
{
    public string Jmeno { get; } = jmeno;
    public int Vek { get; } = vek;
}
```

---

## 5. `public` a `private`

```csharp
class BankovniUcet
{
    private decimal zustatek = 0;          // zvenku nedostupné

    public void Vloz(decimal castka)
    {
        if (castka <= 0)
        {
            Console.WriteLine("Neplatná částka.");
            return;
        }
        zustatek += castka;
    }

    public decimal ZjistiZustatek() => zustatek;
}
```

```csharp
var ucet = new BankovniUcet();
ucet.Vloz(100);
Console.WriteLine(ucet.ZjistiZustatek());   // 100

ucet.zustatek = 1_000_000;    // ❌ error CS0122: je nepřístupné
```

- `public` – vidí na to kdokoli
- `private` – jen kód uvnitř téhle třídy (**tohle je výchozí**, když nenapíšeš nic)

**Tohle je jádro celého OOP:** data schovej jako `private` a dovnitř pusť jen přes
`public` metody, které si můžou ohlídat, že hodnota dává smysl. Nikdo pak nemůže
zůstatek přepsat na nesmysl, protože k němu nemá cestu.

Říká se tomu **zapouzdření** (encapsulation).

### `static` – patří to třídě, ne objektu

```csharp
class Matika
{
    public static int Dvojnasob(int x) => x * 2;
}

Console.WriteLine(Matika.Dvojnasob(5));     // 10 – žádné "new" není potřeba
```

Proto píšeš `Console.WriteLine(...)` a ne `new Console().WriteLine(...)` –
`WriteLine` je `static`. Použij, když metoda nepotřebuje žádná data objektu.

---

## 6. Dědičnost – stručně

```csharp
class Zvire
{
    public string Jmeno { get; set; } = "";

    public virtual void Zvuk()
    {
        Console.WriteLine("...");
    }
}

class Pes : Zvire
{
    public override void Zvuk()
    {
        Console.WriteLine("Haf!");
    }
}
```

```csharp
var pes = new Pes { Jmeno = "Rex" };
Console.WriteLine(pes.Jmeno);   // Rex  – zdědil z Zvire
pes.Zvuk();                     // Haf! – vlastní verze
```

- `: Zvire` = "Pes je Zvire a bere si od něj všechno"
- `virtual` = "potomek to smí předělat"
- `override` = "tady to předělávám"

`override` bez `virtual` v rodiči nepřeložíš. Kompilátor nechce, abys předělal
něco nechtěně.

Dědičnost se dnes používá **méně, než učí staré tutoriály**. Na začátku ji
poznej, ale běžně stačí prostá třída s `private` daty.

---

## 7. `record` – třída jen na data

Když třída nemá dělat nic než nést hodnoty:

```csharp
record Bod(int X, int Y);

var a = new Bod(1, 2);
var b = new Bod(1, 2);

Console.WriteLine(a);          // Bod { X = 1, Y = 2 }   ← rozumný výpis zdarma
Console.WriteLine(a == b);     // True  ← porovná OBSAH
```

Kdyby `Bod` byla obyčejná `class`, `a == b` vrátí `False` (porovnaly by se
adresy v paměti, ne hodnoty) a `Console.WriteLine(a)` vypíše `Bod` – jen jméno typu.

Na "kus dat" tedy `record`, na "něco, co má chování" `class`.

---

## 8. `null` a ten otazník

`null` = *"tady není nic"*. Nejčastější pád programu za běhu:

```
Unhandled exception. System.NullReferenceException:
Object reference not set to an instance of an object.
```

C# ti s tím pomáhá – když je v `.csproj` `<Nullable>enable</Nullable>`
(nový projekt to má), rozlišuje:

```csharp
string jmeno = null;      // ⚠️ varování: tady null být nemá
string? jmeno2 = null;    // ✅ otazník = počítám s tím, že tu nic nebude
```

Jak s tím pracovat:

```csharp
string? vstup = Console.ReadLine();

if (vstup is null)
{
    Console.WriteLine("Nic nepřišlo.");
    return;
}

Console.WriteLine(vstup.Length);      // tady už kompilátor ví, že null není
```

Užitečné zkratky:

```csharp
string jmeno = vstup ?? "neznámý";        // když je vlevo null, vezmi to vpravo
int? delka = vstup?.Length;               // když je vstup null, nevolej .Length
```

**Varování o `null` neignoruj.** Skoro vždycky ukazuje na místo, které by
za běhu spadlo.

---

## Co si odnést

1. Metoda = `návratovýTyp Jméno(parametry) { }`. `void` = nevrací nic.
2. Třída je předpis, `new` z ní vyrobí objekt.
3. Data drž `private`, přístup dej přes `public` metody – to je zapouzdření.
4. `static` = patří třídě, nepotřebuje `new`.
5. `record` na čistá data, `class` když má objekt i chování.
6. `?` u typu znamená "může tu být null" – a kompilátor to pak kontroluje za tebe.
