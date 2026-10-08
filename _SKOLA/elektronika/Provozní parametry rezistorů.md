- navazuje na [[Ohmův a Kirchhoffovy zákony]]
- **Rezistor** - součástka, která v obvodu klade odpor průchodu proudu (omezuje proud, dělí napětí)
- ideální rezistor a jeho charakteristika: [[Ohmův a Kirchhoffovy zákony#Definice elektrického odporu]]

## Model reálného rezistoru
- reálný rezistor má kromě činného odporu i **parazitní indukčnost a kapacitu**
	- tyto nežádoucí vlastnosti závisí na typu odporového materiálu, konstrukčním uspořádání, rozměrech i povrchové úpravě
- náhradní schéma: $R$ a $L_{PARAZ}$ za sebou, k nim paralelně $C_{PARAZ}$
- reálné rezistory mají odpor **závislý na teplotě**
- jsou zdrojem **šumových napětí** - tepelné šumy
- projevuje se u nich tzv. **skin efekt** (povrchový jev) - při vysokých kmitočtech teče proud pouze povrchem vodičů
- na vysokých kmitočtech má rezistor kapacitní charakter
- na nízkých kmitočtech převládá induktivní charakter
- stejně se modeluje i kondenzátor: [[Kondenzátory#Reálný kondenzátor]]

![[obrazky/prezentace/rezistory/model-realneho-rezistoru.jpg|450]]

## Mezní parametry
- hodnoty, které se nesmí překročit, jinak se rezistor zničí
- **maximální svorkové napětí** - největší napětí, které je možné přiložit ke svorkám rezistoru
- **rozsah provozních teplot** - interval teplot, ve kterém výrobce zaručuje vlastnosti a parametry rezistoru (−55 °C až +125 °C)
- **maximální teplota při pájení** - např. 280 °C po dobu 10 s

## Parametry
- **jmenovitý odpor** - hodnota odporu, kterou udává výrobce, v **Ω**
	- vyrábí se jen hodnoty dané řadou vyvolených čísel - viz [[#Řady jmenovitých hodnot a značení - bonus]]
- **tolerance** - o kolik se může skutečný odpor lišit od jmenovitého, v (**%**)
- **zatížení (výkon)** - kolik tepla rezistor vydrží, než se zničí, ve **W**
- **teplotní koeficient odporu** - udává, jak moc se změní odpor, když se teplota změní o 1 °C (např. 15 ppm/°C)
- **typ rezistoru** - vrstvový, drátový, pevný, proměnný (potenciometr)
- **materiál** - uhlík, metal-oxid, kov, slitiny kovů, cermet, uhlíková směs s pojivem

- podobné parametry mají i [[Kondenzátory#Parametry|kondenzátory]] a [[Cívky#Parametry cívek|cívky]]

## Řady jmenovitých hodnot a značení - bonus
- rezistory (i kondenzátory) se vyrábí v **řadách E** - číslo říká, kolik hodnot je v jedné dekádě
	- **E6**: 1,0 - 1,5 - 2,2 - 3,3 - 4,7 - 6,8
	- **E12**: 1,0 - 1,2 - 1,5 - 1,8 - 2,2 - 2,7 - 3,3 - 3,9 - 4,7 - 5,6 - 6,8 - 8,2
	- **E24**, **E48** - jemnější dělení pro přesnější součástky
- **alfanumerický kód** - písmeno stojí na místě desetinné čárky a zároveň určuje násobek (R = Ω, K = kΩ, M = MΩ):
	- `R33` = 0,33 Ω, `3R3` = 3,3 Ω, `33R` = 33 Ω
	- `K33` = 0,33 kΩ, `3K3` = 3,3 kΩ, `33K` = 33 kΩ
	- `M33` = 0,33 MΩ, `3M3` = 3,3 MΩ, `33M` = 33 MΩ
- kondenzátory se značí stejně, jen s předponami p, n, μ, m - viz [[Kondenzátory#Značení kondenzátorů - bonus]]

![[obrazky/prezentace/rezistory/rady-e-a-znaceni.jpg|500]]

## Druhy rezistorů
- podle **velikosti** odporu:
	- pevná
	- proměnná (potenciometr, trimr)
- podle **provedení**:
	- vrstvové
	- drátové

## Druhy rezistorů Pevná velikost
### Metal-oxidové rezistory
- malé rozměry
- teplotní stabilita
- vysoká přesnost
### Metalizované rezistory
- povrch je chráněn lakem
- slitiny kovů - chromnikl
- široký rozsah
### Drátové rezistory
- na keramickém tělísku je navinutý odporový drát potřebné délky a průřezu - NiCr, Cr-Ni-Fe
- vývody jsou přivařeny ke koncům drátu
- povrch je chráněn laky, smalty nebo tmely
- výkonové rezistory mají hliníkové pouzdro pro lepší odvod tepla
- odpor vychází z délky a průřezu drátu (viz [[Ohmův a Kirchhoffovy zákony#Základní veličiny]]):
$$R = ρ\cdot\frac{l}{S}$$

![[obrazky/prezentace/rezistory/dratove-rezistory.jpg|450]]

### Rezistory pro povrchovou montáž - SMD
- **typ MELF** - válcovitý tvar
	- slitiny kovů - chromnikl (CrNi)
	- pájecí plošky na vrcholu válečku
	- široký rozsah vyráběných hodnot
	- vysoká stabilita odporu, velmi dobrá teplotní stabilita
- **čipové rezistory** - destičkový tvar
	- tantal-nitrid ($Ta_2N$), chromnikl (CrNi)
	- vysoká spolehlivost, nízký teplotní koeficient
	- malý šum, vysoká stabilita

![[obrazky/prezentace/rezistory/smd-rezistory.jpg|450]]

## Druhy rezistorů Proměnná velikost
### Potenciometry a trimry
- po odporové dráze se pohybuje jezdec, který ji dělí na dvě části $R_1$ a $R_2$ → napětí na jezdci $U_{\text{výst}}$ se dá plynule nastavit mezi $U_1$ a $U_2$
- **potenciometr** - ovládá se hřídelí (knoflíkem), mění se často
- **trimr** - malý, nastavuje se šroubovákem jen občas

![[obrazky/prezentace/rezistory/potenciometry-a-trimry.jpg|450]]

### Výkonové drátové rezistory s odbočkou a drátové potenciometry
- drátový rezistor s posuvnou objímkou (odbočkou), kterou se nastaví část odporu
- drátové potenciometry jsou stavěné na větší výkon

![[obrazky/prezentace/rezistory/vykonove-dratove-s-odbockou.jpg|450]]
