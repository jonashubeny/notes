- navazuje na: [[Dioda]] (polovodič P a N, PN přechod) a [[Ohmův a Kirchhoffovy zákony]]
- přehled všech zápisů: [[Elektronika - přehled]]

> [!summary] Tranzistor v jedné větě
> **Tranzistor je elektronicky ovládaný ventil** - malý signál na jednom vývodu řídí velký proud mezi dvěma dalšími.

---

## 1. K čemu tranzistor slouží
| Režim | Co dělá | Analogie | Kde se používá |
| --- | --- | --- | --- |
| **spínač** | buď úplně **zavřený** (nevede), nebo úplně **otevřený** (vede naplno) | **vypínač světla** - jen zapnuto/vypnuto | počítače (0 a 1), spínání motorů, relé, LED |
| **zesilovač** | otevře se **přesně tolik**, kolik řídicí signál určí | **plynový pedál** nebo stmívač - plynule | audio zesilovače, rádia, mikrofony |

- **zesílení** = poměr, kolikrát je změna na výstupu větší než změna na vstupu
- tranzistor energii **nevyrábí** - jen ovládá energii ze zdroje podle slabého signálu

> [!example] Analogie: hasičská hadice s ventilem
> Silnou vodu v hadici (velký proud) neotevíráš rukama, ale **malou páčkou**. Malá síla na páčce → velká síla vody. Páčka = řídicí vývod, hadice = hlavní proud.

- tranzistory jsou dvou hlavních druhů:
	- **bipolární** (BJT) - ovládá se **proudem**
	- **unipolární** (FET, hlavně MOSFET) - ovládá se **napětím**

---

## 2. Bipolární tranzistor (BJT)
- tři vrstvy polovodiče - buď **N-P-N**, nebo **P-N-P**
- tedy **dva PN přechody** a **tři elektrody** (vývody)
- „bipolární“ = proud vedou **oba druhy nosičů** - elektrony i díry

### Kolektor, báze, emitor
| Elektroda | Značka | Úkol | Analogie | Vlastnost vrstvy |
| --- | --- | --- | --- | --- |
| **emitor** | E | **vysílá** nosiče náboje do tranzistoru | čerpadlo, které tlačí vodu | silně dotovaný |
| **báze** | B | **řídí**, kolik nosičů projde | páčka ventilu / turniket | **velmi tenká**, slabě dotovaná |
| **kolektor** | C | **sbírá** nosiče, které prošly | sběrná nádrž | největší, odvádí teplo |

> [!tip] Obrázek, který by se sem hodil - vnitřní struktura
> Tři barevné vrstvy vedle sebe: široká **N** (kolektor) - úzký proužek **P** (báze) - **N** (emitor). Mezi nimi dvě hranice = dva PN přechody. Je to jako **dvě diody spojené proti sobě** (anodami k sobě v bázi). Samotné dvě diody ale tranzistor neudělají - kouzlo je v tom, že báze je **extrémně tenká**.

### Jak proud do báze otevírá cestu C → E (NPN)
1. přechod **báze-emitor** zapojíme v **propustném** směru (báze o ≈ **0,7 V** výš než emitor) → do báze teče **malý proud $I_B$**
2. emitor tím začne „chrlit“ elektrony do báze
3. báze je **tak tenká**, že se v ní elektrony nestihnou zachytit (jen pár z nich vytvoří proud báze) - drtivá většina proletí dál do kolektoru
4. kolektorem teče **velký proud $I_C$** - typicky **100× až 300×** větší než proud báze

$$I_C = β\cdot I_B \qquad I_E = I_B + I_C$$
- **β** (beta, v katalozích $h_{21E}$) = **proudový zesilovací činitel** - kolikrát je $I_C$ větší než $I_B$
- $I_E = I_B + I_C$ je obyčejný 1. Kirchhoffův zákon - co přiteče do tranzistoru, musí odtéct ([[Ohmův a Kirchhoffovy zákony#Kirchhoffovy zákony]])
- protože $I_B$ je malinký, je **emitorový a kolektorový proud téměř stejný**
- **bez proudu do báze tranzistor nevede** (je zavřený)

> [!example] Analogie: vrata na přehradě s malou páčkou
> Za vraty (kolektor) je spousta vody, která chce protéct dolů (k emitoru). Vrata ale otevřou jen tehdy, když do malé páky (báze) pošleš **trochu vody**. Čím víc vody do páky, tím víc se vrata otevřou - a protéká **stokrát víc vody**, než kolik jsi poslal do páky. Přestaneš-li posílat, vrata se zavřou.

![[obrazky/polovodice/npn-spinac.svg|600]]

> [!note] Co je na obrázku
> Klasické zapojení **NPN jako spínač**: slabý signál 5 V (třeba z Arduina) pustí přes rezistor $R_B$ malý proud do báze a tranzistor sepne silný obvod 12 V se žárovkou nebo motorem.

### NPN vs PNP
| | NPN | PNP |
| --- | --- | --- |
| vrstvy | N - P - N | P - N - P |
| šipka ve značce (vždy na emitoru) | **ven** | **dovnitř** |
| otevře se, když je báze proti emitoru | **kladnější** (o ≈ 0,7 V) | **zápornější** (o ≈ 0,7 V) |
| proud teče | z kolektoru do emitoru | z emitoru do kolektoru |
| používá se | častěji | zrcadlová verze NPN |

- šipka ve značce ukazuje **směr proudu emitorem**

### Tři stavy bipolárního tranzistoru
| Stav | Co se děje | Použití |
| --- | --- | --- |
| **zavřený** (nevede) | $I_B = 0$ → $I_C = 0$ | spínač vypnutý |
| **aktivní** | $I_C = β\cdot I_B$, plynule | **zesilovač** |
| **saturace** (nasycený) | báze je „přecpaná“, víc už se otevřít nedá | spínač sepnutý |

### Tři základní zapojení (SE, SB, SK)
- podle toho, která elektroda je **společná** pro vstup i výstup:

| Zapojení | Společná elektroda | Zesiluje | Použití |
| --- | --- | --- | --- |
| **SE** - se společným emitorem | emitor | **proud i napětí** (nejvíc výkonu), otáčí fázi | nejčastější, běžný zesilovač i spínač |
| **SB** - se společnou bází | báze | **napětí**, proud ne (≈ 1) | vysoké frekvence |
| **SK** - se společným kolektorem („emitorový sledovač“) | kolektor | **proud**, napětí ne (≈ 1) | oddělovač, výstup se bere z emitoru |

### VA charakteristiky tranzistoru
- **výstupní charakteristiky** = závislost **kolektorového proudu $I_C$** na **napětí kolektor-emitor $U_{CE}$**, každá čára pro jiný (stálý) proud báze $I_B$
- vypadají jako několik vodorovných čar nad sebou - čím větší $I_B$, tím výš → ukazuje, že $I_C$ řídí hlavně báze, ne napětí na kolektoru

### Příklad: jak velký rezistor do báze
- tranzistor má spínat **100 mA**, má **β = 100**, řídicí signál má **5 V**
- minimální proud báze: $I_B = \frac{I_C}{β} = \frac{0{,}1}{100} = 1\ mA$
- pro jistotu (aby byl spolehlivě sepnutý v saturaci) dáme asi **2×** víc → **2 mA**
- na rezistoru zbyde $5 - 0{,}7 = 4{,}3\ V$ (0,7 V spadne na přechodu B-E, stejně jako u [[Dioda|diody]])
$$R_B = \frac{4{,}3}{0{,}002} = 2150\ Ω \;\Rightarrow\; \text{nejbližší z řady E12: } 2{,}2\ kΩ$$
- řada E12: [[Provozní parametry rezistorů#Řady jmenovitých hodnot a značení - bonus]]

### Příklad: výpočet zesílení
- změna proudu báze z **50 μA na 150 μA** vyvolá změnu kolektorového proudu z **1,5 mA na 4 mA**
$$β = \frac{ΔI_C}{ΔI_B} = \frac{(4 - 1{,}5)\ mA}{(150 - 50)\ μA} = \frac{2{,}5\ mA}{0{,}1\ mA} = 25$$
- pozor na jednotky: 100 μA = 0,1 mA

---

## 3. Unipolární tranzistor - MOSFET
- „unipolární“ = proud vede **jen jeden druh nosičů** (buď jen elektrony, nebo jen díry)
- **FET** = Field Effect Transistor = tranzistor řízený **elektrickým polem**
- **MOSFET** = Metal-Oxide-Semiconductor FET - podle vrstev: kov (brána) - oxid (izolant) - polovodič

### Elektrody
| MOSFET | Česky | Odpovídá u bipolárního | Úkol |
| --- | --- | --- | --- |
| **G** - gate | **hradlo** (brána) | báze | **řídí** - ale je **izolované**! |
| **S** - source | zdroj (emitor) | emitor | odtud nosiče vycházejí |
| **D** - drain | odtok (kolektor) | kolektor | sem nosiče tečou |

### Hlavní rozdíl: ovládání napětím, ne proudem
- hradlo (G) je od polovodiče oddělené **tenoučkou vrstvou izolantu** (oxid křemíku)
- do hradla proto **neteče žádný proud** (prakticky nula) - stačí na něj přivést **napětí**
- když je napětí $U_{GS}$ větší než **prahové napětí $U_{th}$** (typicky 1-4 V), elektrické pole **přitáhne elektrony** těsně pod izolant
- tam vznikne tenký vodivý **kanál** mezi S a D → tranzistor vede
- čím větší napětí na hradle, tím širší kanál → tím větší proud (vodivost kanálu závisí na **napětí na hradle**)
- hradlo s izolantem a polovodičem je vlastně malý **kondenzátor** - proud teče jen chvilku, když se nabíjí nebo vybíjí ([[Kondenzátory]])

![[obrazky/polovodice/mosfet-struktura.svg|700]]

> [!note] Co je na obrázku
> Vlevo bez napětí na hradle: mezi dvěma ostrůvky N+ (S a D) je jen materiál P → cesta je přerušená. Vpravo s napětím na hradle: kladné hradlo přitáhne elektrony a pod izolantem vznikne modrý **N kanál**, kterým proud proteče.

> [!example] Analogie: tlačené dveře vs. automatické dveře
> **Bipolární tranzistor** jsou dveře, které musíš **pořád tlačit** - dokud do nich tlačíš (proud do báze), jsou otevřené, a stojí tě to sílu (energii). **MOSFET** jsou **automatické dveře s čidlem** - stačí, že stojíš před nimi (napětí, pole), a otevřou se. Ničeho se nedotýkáš a nic tě to nestojí.

> [!example] Analogie: most, který vznikne jen na povel
> Mezi dvěma břehy (S a D) je řeka. Napětí na hradle je jako povel, na který se z vody vynoří **pontonový most** (kanál). Bez povelu most zmizí a nikdo nepřejde.

### Druhy MOSFETů (stačí vědět, že existují)
- **N-kanálový** (vedou elektrony, častější) a **P-kanálový** (vedou díry) - obdoba NPN a PNP
- **s indukovaným kanálem** - bez napětí na hradle je zavřený (nejběžnější, ten na obrázku)
- **s vodivým kanálem** - bez napětí vede a napětím se zavírá
- existuje i **JFET** - starší typ, kde hradlo není izolované oxidem, ale PN přechodem

### Proč je MOSFET tak důležitý
- **téměř nulová spotřeba na ovládání** - do hradla neteče proud, takže miliony tranzistorů se nezahřejí jen tím, že „drží“ stav
- **dá se zmenšit na neuvěřitelně malé rozměry** - moderní procesor v mobilu nebo počítači obsahuje **miliardy až desítky miliard** MOSFETů
- **velmi rychle spíná** - základ všech digitálních obvodů (0 a 1)
- **technologie CMOS** = dvojice N-kanálového a P-kanálového MOSFETu, z nichž je vždy jeden zavřený → v klidu skoro neteče proud. Na tom stojí procesory, paměti i flash disky
- **výkonové MOSFETy** spínají velké proudy s malými ztrátami - nabíječky, elektroauta, řízení motorů, LED pásky
- ⚠️ **slabina:** izolant pod hradlem je tak tenký, že ho může prorazit i **statická elektřina** z ruky → MOSFETy se berou za pouzdro, ne za nožičky

---

## 4. Bipolární vs. unipolární - velké porovnání
| | Bipolární (BJT) | Unipolární (MOSFET) |
| --- | --- | --- |
| ovládá se | **proudem** do báze | **napětím** na hradle |
| proud do řídicí elektrody | malý, ale **stále teče** | prakticky **nulový** |
| nosiče náboje | **oba** (elektrony i díry) | **jeden** druh |
| elektrody | báze, kolektor, emitor (B, C, E) | hradlo, drain, source (G, D, S) |
| typy | NPN, PNP | N-kanál, P-kanál |
| spotřeba na ovládání | vyšší | téměř žádná |
| hustota v čipu | menší | **obrovská** (miliardy v procesoru) |
| citlivost na statiku | odolnější | **citlivý** |
| typické použití | analogové zesilovače, jednoduché spínání | procesory, paměti, výkonové spínání |
| analogie | dveře, které musíš tlačit | automatické dveře |

---

## 5. Na co se může ptát test (sbírka, test T3)
- podle klíče ve sbírce, kterou jsi fotil:

| Otázka | Správná odpověď | Proč |
| --- | --- | --- |
| Co je to tranzistor? | **polovodičový prvek se dvěma přechody** | NPN/PNP = 3 vrstvy = 2 PN přechody |
| Co znamená bipolární? | **využívá oba druhy nosičů - elektrony i díry** | „bi“ = dva |
| Kolik přechodů a elektrod má NPN? | **dva přechody a tři elektrody** | E, B, C |
| Jak se liší emitorový proud od kolektorového? | **jsou téměř stejné** | $I_E = I_B + I_C$ a $I_B$ je malinký |
| Proč tranzistor zesiluje? | **kolektorový proud je řízen malým proudem báze** | $I_C = β\cdot I_B$ |
| Co znamená zesílení? | **poměr změny výstupní hodnoty ku změně vstupní** | |
| Proudové zesílení: $I_B$ 50 → 150 μA, $I_C$ 1,5 → 4 mA | **25** | výpočet výše |
| Základní zapojení tranzistoru? | **SE, SB, SK** | podle společné elektrody |
| Co znázorňují VA (výstupní) charakteristiky? | **závislost $I_C$ na napětí E-K při konstantním proudu báze** | |
| Princip tranzistoru FET? | **vodivost je řízena elektrickým polem** | FET = Field Effect |
| Elektrody tranzistoru MOS-FET? | **emitor, hradlo, kolektor** | staré české názvy pro S, G, D |
| Na čem závisí vodivost kanálu unipolárního tranzistoru? | **na velikosti napětí na hradle** | |

- u otázky s obrázkem „vede či nevede?“ se dívej na **polaritu mezi bází a emitorem**: u NPN musí být báze **kladnější** než emitor, jinak tranzistor **nevede**
- u otázky „jakého typu je zapojení dle obrázku“: zjisti, **která elektroda je společná** pro vstup i výstup; když se výstup bere z rezistoru v emitoru a kolektor je přímo na napájení, je to **SK**

---

## Shrnutí na kartičku
- tranzistor = **ventil** ovládaný malým signálem → **spínač** (vypínač) nebo **zesilovač** (plynový pedál)
- **bipolární**: 3 vrstvy (NPN/PNP), elektrody **E, B, C**, ovládá se **proudem báze**: $I_C = β\cdot I_B$, $I_E = I_B + I_C$, přechod B-E potřebuje ≈ 0,7 V
- zapojení **SE, SB, SK**
- **MOSFET**: elektrody **G, S, D**, hradlo je **izolované**, ovládá se **napětím** → pole vytvoří **kanál**
- MOSFET = skoro nulová spotřeba na ovládání + miniaturní → **základ procesorů a celé moderní elektroniky**
