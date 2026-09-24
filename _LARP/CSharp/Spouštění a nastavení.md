# C# – spouštění a nastavení (Fedora)

Praktická část k [[CSharp/Základy|Základy]] – jak dostat C# na disk, jak udělat nový projekt
a jak ho pustit. Psáno na Fedoře 44.

---

## 0. Instalace .NET SDK

**SDK** = kompilátor + nástroj `dotnet` + knihovny. To chceš.
(Existuje i samotný *Runtime*, ale ten umí programy jen spouštět, ne překládat.)

```bash
sudo dnf install dotnet-sdk-10.0
dotnet --version        # 10.0.x → hotovo
```

Ve Fedoře 44 jsou v repozitářích verze 8.0, 9.0 a 10.0. Ber **10.0** – je nejnovější.
Nepotřebuješ nic z microsoftího webu, `dnf` stačí.

```bash
dotnet --info            # co všechno je nainstalované
dotnet --list-sdks
```

---

## 1. Nový projekt

```bash
mkdir -p ~/kod/cvicimcsharp && cd ~/kod/cvicimcsharp
dotnet new console
```

Vznikne:

```
cvicimcsharp/
├── Program.cs                  ← tvůj kód
├── cvicimcsharp.csproj         ← nastavení projektu
└── obj/                        ← mezivýsledky překladu, needituj
```

`dotnet new console` vezme jméno projektu **z názvu složky**. Když chceš jiné:

```bash
dotnet new console -o MujProjekt
```

### Co je `.csproj`

```xml
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <OutputType>Exe</OutputType>
    <TargetFramework>net10.0</TargetFramework>
    <ImplicitUsings>enable</ImplicitUsings>
    <Nullable>enable</Nullable>
  </PropertyGroup>
</Project>
```

Obdoba `package.json`. Nejdůležitější řádky:

- `TargetFramework` – pro jakou verzi .NET se překládá
- `ImplicitUsings` – proto nemusíš psát `using System;` (viz [[CSharp/Základy|Základy]])
- `Nullable` – proto `Console.ReadLine()` vrací `string?` a ne `string`

---

## 2. Spuštění

```bash
dotnet run
```

Přeloží **a** spustí. To je příkaz, který budeš psát pořád.

```bash
dotnet build      # jen přelož, nespouštěj (kontrola chyb)
dotnet clean      # smaž výsledky překladu
dotnet format     # naformátuj kód
```

**Musíš být ve složce s projektem** (nebo použij `dotnet run --project cesta/`).
Jinak:

> `Nebyl nalezen žádný soubor projektu ani řešení`

### Argumenty programu

```bash
dotnet run -- prvni druhy
```

To `--` odděluje argumenty pro `dotnet run` od argumentů pro **tvůj** program.
V kódu je najdeš v `args`:

```csharp
Console.WriteLine($"Dostal jsem {args.Length} argumentů.");
foreach (string a in args)
{
    Console.WriteLine(a);
}
```

Ano, `args` funguje i bez `static void Main(string[] args)` – kompilátor
ho v top-level kódu nachystá sám.

---

## 3. Jak čteme chybu překladu

Tohle je skill sám pro sebe. Zkus:

```csharp
int pocet = "pet";
```

```
/home/jonas/kod/cvicimcsharp/Program.cs(1,13): error CS0029: Nelze implicitně
převést typ "string" na "int"

Sestavení selhalo. Opravte chyby sestavení a spusťte znovu.
```

Čti to po částech:

| Část | Význam |
|---|---|
| `Program.cs(1,13)` | soubor, **řádek 1, znak 13** |
| `error CS0029` | kód chyby – dobré do googlu |
| zbytek | co se kompilátoru nezdá |

`error` = program se nepřeložil, vůbec nepoběží.
`warning` = přeložilo se, ale něco je podezřelé. **Varování čti**, obzvlášť
ta o `null` – většinou mají pravdu.

Kódy chyb (`CS0029`, `CS0103`, …) se googlí dobře, jsou dokumentované.
Ty nejčastější mám vypsané v [[CSharp/Časté chyby|Časté chyby]].

---

## 4. Editor

Bez napovídání je C# utrpení – dej si na to čas, ušetří ti to hodiny.

**VS Code:** rozšíření **C# Dev Kit** (Microsoft). Nainstaluje si i jazykový server.
**Neovim:** LSP `omnisharp` nebo `csharp_ls` přes `mason`.
**JetBrains Rider:** pro nekomerční použití zdarma, nejvíc umí.

Co z toho chceš:

- červené podtržení chyby **před** spuštěním
- `Ctrl+Space` → co všechno jde po tečce napsat (tohle je hlavní způsob učení)
- nájezd myší na proměnnou → jaký má typ

---

## 5. Zkusit kód bez zakládání projektu

Od .NET 10 jde jeden soubor pustit přímo:

```bash
echo 'Console.WriteLine(7 / 2);' > pokus.cs
dotnet run pokus.cs
```

Na rychlé "jak se to zachová, když…" je to ideální.
Pro cokoli většího udělej normální projekt.

---

## 6. Obvyklý pracovní cyklus

```bash
cd ~/kod/cvicimcsharp
$EDITOR Program.cs
dotnet run
```

Uložit soubor **nestačí** – `dotnet run` musí proběhnout znovu.
Když chceš, aby se to přeložilo samo po každém uložení:

```bash
dotnet watch run
```

---

## Checklist, když něco nejde

- [ ] Jsem ve složce s `.csproj`?
- [ ] `dotnet --version` něco vypíše?
- [ ] Pustil jsem `dotnet run` **po** uložení souboru?
- [ ] Je to `error`, nebo jen `warning`?
- [ ] Přečetl jsem číslo řádku v závorce a kouknul přesně tam?
