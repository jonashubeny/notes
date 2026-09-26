- vychází z fyziky: [[coulombuv-zakon|Coulombův zákon]] → [[elektricke-pole|Elektrické pole]] → [[El. potenciál a el. napětí]]
- souvisí s: [[Ohmův a Kirchhoffovy zákony]], [[Provozní parametry rezistorů]]

## Co je kondenzátor
- součástka, která umí **uchovat elektrický náboj** (energii) - něco jako malá, rychlá „nádrž“ na náboj
- skládá se ze **dvou vodivých desek**, mezi kterými je **izolant (dielektrikum)**
- když ho připojíme ke zdroji, na jedné desce se nahromadí kladný a na druhé záporný náboj a mezi deskami vznikne [[elektricke-pole|elektrické pole]]

## Kapacita
- **kapacita** říká, kolik náboje kondenzátor pojme
$$C = \frac{Q}{U}$$
- závisí na tom, jak je kondenzátor postavený:
$$C = ε_0\cdot ε_r\cdot\frac{S}{l}$$
	- $S$ - plocha desek → větší desky = větší kapacita
	- $l$ - vzdálenost desek → blíž u sebe = větší kapacita
	- $ε_0$ - permitivita vakua (konstanta)
	- $ε_r$ - relativní permitivita - vlastnost dielektrika, říká kolikrát lépe se dielektrikum „polarizuje“ než vakuum
- jednotkou je **farad (F)** - je hodně velký, proto se používají menší jednotky: $pF$, $nF$, $μF$

## Parametry
- **Jmenovitá kapacita** - hodnota kapacity, kterou udává výrobce
- **Tolerance** - v (%), o kolik se může skutečná kapacita lišit od jmenovité
- **Izolační odpor** - stejnosměrný odpor mezi vývody kondenzátoru (čím větší, tím méně se kondenzátor sám vybíjí)
- **Provozní napětí** - nejvyšší napětí, které na kondenzátor můžeme připojit
- **Dovolené oteplení** - zpravidla 40 °C
- **Jmenovité střídavé napětí** - udané výrobcem pro daný kmitočet

## Kondenzátor v obvodu
- **stejnosměrný proud (DC)** - kondenzátor se nenabije hned, ale **postupně** (přes rezistor)
	- rychlost nabíjení určuje **časová konstanta** $τ = R\cdot C$ - větší odpor nebo kapacita = pomalejší nabíjení
	- když je nabitý, proud už obvodem **neteče**
- **střídavý proud (AC)** - kondenzátor se pořád nabíjí a vybíjí, takže proud **propouští**
	- čím vyšší kmitočet, tím lépe proud propouští
	- proud a napětí nejsou ve fázi - proud předbíhá napětí (na rozdíl od rezistoru, viz [[Ohmův a Kirchhoffovy zákony#Ohmův zákon]])

## Hlavní typy kondenzátorů
- **Vzduchové** - dielektrikem je vzduch
- **Svitkové** - dielektrikem je papír, metalizovaný papír nebo plastová fólie
- **Keramické** - dielektrikem je keramika
- **Elektrolytické** - mají velkou kapacitu, **nutno dodržet polaritu** (+ a −), jinak se zničí

## Schematické značky
![[obrazky/prezentace/kondenzatory/3.jpg|400]]
