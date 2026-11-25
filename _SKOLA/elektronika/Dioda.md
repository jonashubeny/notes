- navazuje na: [[Ohmův a Kirchhoffovy zákony]], [[Kondenzátory]] (vyhlazení usměrněného napětí)
- pokračuje v: [[Tranzistor]] - tranzistor jsou vlastně dva PN přechody za sebou
- přehled všech zápisů: [[Elektronika - přehled]]

> [!summary] Dioda v jedné větě
> **Dioda je jednosměrka pro elektrický proud** - jedním směrem ho pustí, opačným ne.

![[obrazky/polovodice/znacky-polovodice.svg|600]]

---

## 1. Polovodič - materiál „něco mezi“
- **vodič** (měď) vede vždycky, **izolant** (plast) nevede nikdy
- **polovodič** (nejčastěji **křemík**, Si) vede jen **za určitých podmínek** - a právě proto je tak užitečný: jeho vodivost se dá ovládat
- čistý křemík vede špatně - každý atom má 4 elektrony a všechny drží v pevných vazbách se sousedy, žádný není „volný“
- vodivým ho uděláme **dotováním** = přimícháním trochy jiného prvku

### Typ N - přebytek elektronů
- přimícháme prvek s **5** elektrony (fosfor, arsen)
- 4 elektrony se zapojí do vazeb, **pátý přebývá** a může volně cestovat
- proud vedou **záporné** volné elektrony → proto **N** (negativní)

### Typ P - přebytek děr
- přimícháme prvek se **3** elektrony (bor, galium)
- v jedné vazbě **chybí elektron** → vznikne **díra**
- díra se chová jako **kladný** nosič náboje → proto **P** (pozitivní)

> [!example] Analogie: díra jako volné místo v kině
> V plné řadě sedadel je jedno volné. Soused si na něj přesedne, tím uvolní své místo, na to se přesune další… **Lidé se posouvají doleva, ale „volné místo“ putuje doprava.** Přesně tak „cestuje“ díra - ve skutečnosti přeskakují elektrony, ale je jednodušší sledovat tu díru.

| | Typ N | Typ P |
| --- | --- | --- |
| příměs | 5 elektronů (fosfor, arsen) | 3 elektrony (bor, galium) |
| čeho je navíc | **volné elektrony** (−) | **díry** (+) |
| hlavní (majoritní) nosiče | elektrony | díry |

- pozor: oba typy jsou samy o sobě **elektricky neutrální** - „N“ a „P“ říká jen, co v nich nese proud

---

## 2. PN přechod - hranice, kde se děje kouzlo
- když přiložíme kousek **P** a kousek **N** k sobě, elektrony z N přeskočí přes hranici a **zaplní díry** v P
- u hranice tak vznikne pásmo, kde **nejsou žádní volní nosiči** = **ochuzená (vyprázdněná) oblast**
- zůstanou tam jen nepohyblivé nabité atomy (ionty): na straně P záporné, na straně N kladné → vytvoří **bariéru**, která další elektrony odpuzuje
- bariéra má napětí asi **0,6–0,7 V u křemíku** (0,3 V u germania)

> [!example] Analogie: hraniční pásmo mezi dvěma státy
> Na hranici je pás „země nikoho“, kam se nikdo nesmí hnout. Kdo chce přejít, musí mít dost síly (napětí), aby se přes pásmo dostal. Ochuzená oblast je přesně takové pásmo - **izolant uprostřed polovodiče, který vznikl sám od sebe**.

![[obrazky/polovodice/pn-prechod.svg|700]]

> [!note] Co je na obrázku
> Vlevo P s dírami (○), vpravo N s volnými elektrony (●), uprostřed šedé pásmo jen s nepohyblivými ionty. Šipka pod ním ukazuje šířku bariéry.

---

## 3. Dioda = PN přechod s dvěma vývody
- **anoda (A)** = vývod na straně **P**
- **katoda (K)** = vývod na straně **N**
- na součástce je katoda označená **proužkem**; ve značce je katoda ta **čárka** a trojúhelník ukazuje směr, kudy proud jde

### Propustný směr - proud teče ✅
- **+ na anodu (P), − na katodu (N)**
- vnější napětí tlačí elektrony i díry **proti sobě k hranici** → ochuzená oblast se **zúží**
- jakmile napětí překročí bariéru (**≈ 0,7 V**), proud začne téct naplno
- na diodě pak zůstává zhruba stále stejný **úbytek napětí ≈ 0,7 V**

### Závěrný směr - proud neteče ❌
- **− na anodu (P), + na katodu (N)**
- vnější napětí odtahuje nosiče **od hranice** → ochuzená oblast se **rozšíří**
- teče jen nepatrný **závěrný proud** (μA, prakticky nic)
- když napětí přeženeme přes **průrazné napětí**, přechod se prorazí → obyčejná dioda se zničí (Zenerova dioda toho naopak využívá)

![[obrazky/polovodice/dioda-propustny-zaverny.svg|700]]

> [!example] Analogie: turniket v metru / zpětná klapka
> Turniketem projdeš jen jedním směrem - a ještě ho musíš trochu **zatlačit** (to je těch 0,7 V). Opačným směrem se ani nehne, ať tlačíš jak chceš… dokud ho neurveš (průraz). Ve vodovodu stejnou práci dělá **zpětná klapka**.

### VA charakteristika - graf diody
- **VA charakteristika** = graf, jaký proud teče při jakém napětí
- u rezistoru je to přímka (proto je **lineární**, viz [[Ohmův a Kirchhoffovy zákony#Definice elektrického odporu]])
- u diody **není** - proto je dioda **nelineární** prvek:
	- v propustném směru do ≈ 0,7 V skoro nic, pak proud prudce vystřelí nahoru
	- v závěrném směru téměř nula, až u průrazného napětí prudce dolů

> [!tip] Obrázek, který by se sem hodil
> Osa X = napětí (vpravo propustný, vlevo závěrný směr), osa Y = proud. Vpravo křivka ležící u nuly, která kolem 0,7 V ostře zatočí nahoru („hokejka“). Vlevo křivka těsně pod nulou, která u průrazného napětí spadne kolmo dolů.

---

## 4. Dioda a střídavý proud - usměrňování
- **střídavý proud** (zásuvka, 50 Hz) pořád mění směr: chvíli teče tam, chvíli zpátky
- elektronika (mobil, počítač) ale potřebuje **stejnosměrný** proud
- dioda pustí jen jeden směr → ze střídavého udělá „jednosměrný“ = **usměrní** ho

| Zapojení | Co udělá | Výsledek |
| --- | --- | --- |
| **jednocestný usměrňovač** (1 dioda) | pustí jen kladné půlvlny, záporné zahodí | jen „hrby“ s mezerami, polovina energie pryč |
| **dvoucestný - Graetzův můstek** (4 diody) | zápornou půlvlnu „otočí“ nahoru | hrby hned za sebou, využije se všechno |
| **můstek + kondenzátor** | kondenzátor se v hrbech nabije a v mezerách vybíjí | skoro rovné stejnosměrné napětí |

![[obrazky/polovodice/usmerneni.svg|700]]

> [!example] Analogie: houpačka a nádrž
> Střídavý proud je jako voda, která v trubce teče sem a tam. Dioda je klapka, která pustí jen pohyb jedním směrem. Kondenzátor je pak **nádrž**, která vyrovná nárazy - když přitéká, plní se, když nic nepřitéká, sama dodává (viz [[Kondenzátory#Kondenzátor v obvodu]]).

- přesně tohle je uvnitř každé **nabíječky** a **zdroje**: transformátor ([[Transformátor]]) sníží napětí → Graetzův můstek usměrní → kondenzátor vyhladí

---

## 5. Druhy diod
| Dioda | Co umí | Kde ji potkáš |
| --- | --- | --- |
| **usměrňovací** | obyčejná jednosměrka | zdroje, nabíječky |
| **Zenerova** | v závěrném směru při určitém napětí začne vést a **drží stálé napětí** | stabilizátory napětí |
| **LED** (svítivá) | při průchodu proudu **svítí**, úbytek ≈ 1,8–3,3 V podle barvy | kontrolky, displeje, osvětlení |
| **fotodioda** | světlo v ní vytváří proud | senzory světla, dálkové ovladače |
| **Schottkyho** | menší úbytek (≈ 0,3 V) a rychlejší spínání | spínané zdroje |

### Příklad: předřadný odpor k LED
- LED na **5 V**, úbytek LED **2 V**, chceme proud **20 mA**
- na rezistoru musí zbýt $5 - 2 = 3\ V$ (2. Kirchhoffův zákon, viz [[Ohmův a Kirchhoffovy zákony#Kirchhoffovy zákony]])
$$R = \frac{U}{I} = \frac{3}{0{,}02} = 150\ Ω$$
- bez rezistoru by LED odebrala tolik proudu, že by shořela

---

## Shrnutí na kartičku
- **polovodič** = vodivost se dá ovládat; dotováním vzniká **N** (elektrony) a **P** (díry)
- **PN přechod** = na hranici vznikne **ochuzená oblast** = bariéra ≈ 0,7 V
- **propustný směr**: + na P → zóna se zúží → **proud teče**
- **závěrný směr**: + na N → zóna se rozšíří → **proud neteče**
- dioda je **nelineární** prvek (VA charakteristika není přímka)
- se střídavým proudem: **usměrňuje** - 1 dioda = jednocestně, 4 diody (Graetz) = dvoucestně, kondenzátor = vyhladí
