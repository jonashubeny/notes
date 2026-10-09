- otázky jsou z testu **ELEKTRONIKA – test T1** (Lineární prvky, Vakuové nelineární prvky), fotky: [[#Fotky testu]]
- správné odpovědi jsou podle klíče v sešitě (strana 35 a šablona na straně 41)
- podrobnější zápisy: [[Elektronika - přehled]]

## Nejdřív pár slov, bez kterých to nepůjde
- **proud (I)** = kolik elektřiny teče drátem, jako množství vody v trubce, jednotka **ampér (A)**, 20 mA = 0,02 A
- **napětí (U)** = jak silně se elektřina „tlačí“, jako tlak vody, jednotka **volt (V)**
- **odpor (R)** = jak moc součástka proudu brání, jako zúžení trubky, jednotka **ohm (Ω)**
- **stejnosměrný proud (DC, „ss“)** = teče pořád jedním směrem (baterie)
- **střídavý proud (AC, „stř“)** = pořád mění směr tam a zpátky (zásuvka), kolikrát za sekundu udává **frekvence (Hz)**
- tři základní součástky, kterých se test týká:

| Součástka | Značka | Co dělá | Pro stejnosměrný proud | Pro střídavý proud |
| --- | --- | --- | --- | --- |
| rezistor | R | brzdí proud, mění energii na teplo | brzdí | brzdí stejně |
| kondenzátor | C | uloží náboj (malá nabíjecí „nádržka“) | **nepustí** (jen se nabije) | **pustí**, čím vyšší frekvence, tím líp |
| cívka | L | navinutý drát, ukládá energii do magnetického pole | **pustí** (je to jen drát) | brzdí, čím vyšší frekvence, tím víc |

- více: [[Ohmův a Kirchhoffovy zákony]], [[Kondenzátory]], [[Cívky]]

---

## Část 1 - Lineární prvky (R, L, C)

### 1. Jaké vlastnosti má lineární prvek?
✅ **c) přímkovou VA charakteristiku**
- „VA charakteristika“ = graf, který ukazuje, jaký proud teče při jakém napětí (V = volty, A = ampéry)
- „lineární“ = „jako přímka“ - dvakrát větší napětí → dvakrát větší proud, graf je rovná čára
- přesně tak se chová rezistor podle Ohmova zákona, viz [[Ohmův a Kirchhoffovy zákony#Definice elektrického odporu]]

### 2. Vyjmenujte lineární prvky!
✅ **b) odpor, cívka, kondenzátor**
- to jsou tři základní „obyčejné“ součástky R, L, C
- pozor na a) - **dioda není lineární** (pouští proud jen jedním směrem, její graf není přímka)
- polovodiče (d) jsou typicky nelineární

### 3. Které prvky jsou frekvenčně závislé?
✅ **c) L, C**
- „frekvenčně závislý“ = chová se jinak při pomalém a jinak při rychlém střídavém proudu
- **kondenzátor** pouští vysoké frekvence líp, **cívka** naopak hůř → oba závisí na frekvenci
- **rezistor** brzdí proud pořád stejně, frekvence ho nezajímá → R tam nepatří
- vzorečky (jen pro představu): kondenzátor $X_C = \frac{1}{2\pi f C}$, cívka $X_L = 2\pi f L$ - v obou je $f$ (frekvence)

### 4. Jak rozdělujeme rezistory?
✅ **a) na vrstvové a drátové**
- **vrstvový** = na tělísku je tenká vrstva odporového materiálu
- **drátový** = na tělísku je namotaný odporový drát
- viz [[Provozní parametry rezistorů#Druhy rezistorů]]

### 5. Jaké jsou parametry rezistoru?
✅ **b) jmenovitý odpor, přesnost, výkon**
- **jmenovitý odpor** = kolik ohmů má mít (napsané na něm)
- **přesnost (tolerance)** = o kolik % se může skutečná hodnota lišit
- **výkon (zatížitelnost)** = kolik wattů tepla vydrží, než shoří
- kapacita (a) patří kondenzátoru, ne rezistoru
- viz [[Provozní parametry rezistorů#Parametry]]

### 6. Co je to potenciometr?
✅ **c) rezistor s proměnnou hodnotou odporu**
- je to rezistor s jezdcem, kterým otáčíš → mění se odpor (typicky knoflík hlasitosti)
- viz [[Provozní parametry rezistorů#Potenciometry a trimry]]

### 7. Jaké jsou vlastnosti kondenzátoru?
✅ **c) vede jen střídavý proud**
- uvnitř jsou dvě desky oddělené izolantem - proud tedy skrz **nemůže projít**
- při stejnosměrném proudu se kondenzátor jen nabije a pak už proud neteče
- při střídavém se pořád nabíjí a vybíjí, takže to vypadá, že proud „prochází“
- viz [[Kondenzátory#Kondenzátor v obvodu]]

### 8. Jaké jsou parametry kondenzátoru?
✅ **c) max. napětí, kapacita, izolační odpor**
- **kapacita** = kolik náboje pojme (ve faradech, F)
- **max. (provozní) napětí** = víc napětí nesmí dostat, jinak se prorazí
- **izolační odpor** = jak dobře izolant mezi deskami izoluje
- viz [[Kondenzátory#Parametry]]

### 9. Čím je cívka charakteristická?
✅ **b) má odpor a indukčnost**
- **indukčnost (L)** = hlavní vlastnost cívky, jednotka henry (H)
- a protože je to namotaný drát, má i nějaký **odpor** drátu
- kapacita (a) je vlastnost kondenzátoru; cívka nezadržuje nízké frekvence (c), ale naopak vysoké
- viz [[Cívky#Ideální cívka]]

### 10. Spočítejte R a jeho zatížitelnost (I = 20 mA, U = 4 V)
✅ **c) R = 0,2 kΩ, P = 80 mW**
- nejdřív převeď proud na ampéry: 20 mA = **0,02 A**
- **odpor** z Ohmova zákona:
$$R = \frac{U}{I} = \frac{4}{0{,}02} = 200\ Ω = 0{,}2\ kΩ$$
- **výkon** (kolik tepla rezistor musí vydržet):
$$P = U\cdot I = 4\cdot0{,}02 = 0{,}08\ W = 80\ mW$$
- **past:** 200 Ω vychází v c) i v d), ale jen c) má správný výkon (d) má 0,8 W, to je 10× víc)
- viz [[Ohmův a Kirchhoffovy zákony#Ohmův zákon]]

### 11. Kterou větví protéká největší proud? (U = 10 V stejnosměrné, R = 1 MΩ, cívka 10 mH / 10 Ω, C = 10 μF)
✅ **a) větví s indukčností**
- zdroj je **stejnosměrný** (Vss) - to je klíč k celé otázce
- **kondenzátor** stejnosměrný proud nepustí → proud **0**
- **rezistor** má obrovský odpor 1 MΩ = 1 000 000 Ω → $I = \frac{10}{1\,000\,000} = 0{,}00001\ A$ (skoro nic)
- **cívka** je pro stejnosměrný proud jen drát s odporem 10 Ω → $I = \frac{10}{10} = 1\ A$ → **největší**
- d) je chyták: napětí je opravdu všude stejné, ale proud záleží na odporu každé větve

---

## Část 2 - Elektronky a obrazovka (vakuové prvky)
- tohle v zápisech nemáme - je to stará technika (před tranzistory), stačí pochopit základní myšlenku
- **elektronka** = skleněná baňka, ze které je vysátý vzduch (vakuum), uvnitř jsou kovové elektrody:
	- **katoda** - rozžhavený kov, který „vypouští“ elektrony (minus)
	- **anoda** - elektrody, která elektrony přitahuje (plus)
	- **mřížka** - drátěná síťka mezi nimi, která „přivírá a otevírá“ cestu elektronům (jako kohoutek)

### 12. Jaký je princip elektronky?
✅ **b) emise elektronů z kovu**
- „emise“ = vypouštění - rozžhavená katoda ze sebe uvolňuje elektrony, a na tom celá elektronka stojí
- a) zní podobně, ale klíč chce b)

### 13. Jaké znáte druhy elektronek?
✅ **d) trioda, pentoda, hexoda atd.**
- jména podle počtu elektrod uvnitř: **tri** = 3, **penta** = 5, **hexa** = 6 (dioda = 2)
- germaniové a křemíkové (b) jsou polovodiče, ne elektronky

### 14. Jakou funkci má řídicí mřížka u triody nebo pentody?
✅ **a) zmenšuje tok elektronů mezi katodou a anodou**
- mřížka je mezi katodou a anodou a má záporné napětí → elektrony odpuzuje a tím **přiškrcuje** jejich tok
- malou změnou napětí na mřížce se ovládá velký proud → tak elektronka **zesiluje**

### 15. Co je to voltampérová charakteristika pentody?
✅ **c) závislost anodového proudu na napětí mezi anodou a katodou**
- VA charakteristika = vždycky graf **proud podle napětí** (stejně jako u otázky 1)
- tady konkrétně proud do anody podle napětí mezi anodou a katodou

### 16. Kolik vývodů má nepřímo žhavená pentoda?
✅ **d) 7**
- spočítej to: **2** vývody žhavení (topné vlákno) + **1** katoda + **3** mřížky + **1** anoda = **7**
- „nepřímo žhavená“ = vlákno jen ohřívá samostatnou katodu, proto má katoda vlastní vývod
- pentoda = 5 elektrod (katoda, 3 mřížky, anoda), ale **vývodů** je víc kvůli žhavení

### 17. Jaký je princip obrazovky?
✅ **d) speciální elektronka**
- stará „tlustá“ televize (CRT) je vlastně obří elektronka - vystřeluje paprsek elektronů na stínítko
- obrazovka je vakuová, ne polovodičová (proto a, b, c ne)

### 18. K čemu slouží obvody vychylování u obrazovky?
✅ **b) k řízení dopadu elektronů na libovolné místo na stínítku**
- paprsek elektronů letí rovně; vychylování ho „ohýbá“ doleva, doprava, nahoru a dolů
- tak paprsek postupně „nakreslí“ celý obraz bod po bodu

### 19. Jaké znáte způsoby vychylování?
✅ **c) elektrostatické a elektromagnetické**
- **elektrostatické** = paprsek ohýbá elektrické pole mezi destičkami (viz [[Elektrostatické pole]])
- **elektromagnetické** = paprsek ohýbá magnetické pole cívek (viz [[Magnetické pole]])

### 20. Čím se liší barevná obrazovka od černobílé?
✅ **d) má tři el. trysky, tři luminofory a masku**
- barevný obraz se skládá ze tří barev: **červená, zelená, modrá**
- **3 trysky** = tři paprsky elektronů, jeden pro každou barvu
- **3 luminofory** = tři druhy svítících teček na stínítku (každá svítí jinou barvou)
- **maska** = děrovaná plechová síť, která zajistí, že každý paprsek trefí jen tečky své barvy
- odpovědi a), b), c) jsou jen kousek pravdy - správně je až ta úplná

---

## Rychlý tahák na poslední chvíli

| Otázka | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Odpověď | c | b | c | a | b | c | c | c | b | c |

| Otázka | 11 | 12 | 13 | 14 | 15 | 16 | 17 | 18 | 19 | 20 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Odpověď | a | b | d | a | c | d | d | b | c | d |

- **co si pamatovat, ne se biflovat písmenka:**
	- lineární = přímka → R, L, C (dioda ne)
	- na frekvenci závisí jen L a C
	- kondenzátor = jen střídavý, cívka pro stejnosměrný = obyčejný drát
	- $R = \frac{U}{I}$, $P = U\cdot I$ - a vždycky nejdřív převeď mA na A
	- elektronka = žhavá katoda pouští elektrony, mřížka je přiškrcuje, anoda chytá

## Fotky testu
![[obrazky/prochy-mozna-test/3.jpg|400]] ![[obrazky/prochy-mozna-test/4..jpg|400]]
