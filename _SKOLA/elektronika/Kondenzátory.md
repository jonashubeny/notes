- vychází z fyziky: [[coulombuv-zakon|Coulombův zákon]] → [[elektricke-pole|Elektrické pole]] → [[Elektrický potenciál a napětí]]
- v elektronice navazuje na: [[Elektrostatické pole]] (náboj, permitivita, odvození kapacity)
- souvisí s: [[Ohmův a Kirchhoffovy zákony]], [[Provozní parametry rezistorů]], [[Cívky]] (druhý akumulační prvek - v obvodu se chová „opačně“)

## Co je kondenzátor
- součástka, která umí **uchovat elektrický náboj** (energii) - něco jako malá, rychlá „nádrž“ na náboj
- skládá se ze **dvou vodivých desek**, mezi kterými je **izolant (dielektrikum)**
- když ho připojíme ke zdroji, na jedné desce se nahromadí kladný a na druhé záporný náboj a mezi deskami vznikne [[elektricke-pole|elektrické pole]]

![[obrazky/prezentace/kondenzatory/1.jpg|400]]

## Kapacita
- **kapacita** říká, kolik náboje kondenzátor pojme - je to míra schopnosti pojmout a uchovávat el. náboj
$$C = \frac{Q}{U}$$
- závisí na tom, jak je kondenzátor postavený:
$$C = ε_0\cdot ε_r\cdot\frac{S}{l}$$
	- $S$ - plocha desek → větší desky = větší kapacita
	- $l$ - vzdálenost desek (tloušťka dielektrika) → blíž u sebe = větší kapacita
	- $ε_0$ - permitivita vakua (konstanta) $= 8{,}854\cdot10^{-12}\ F\cdot m^{-1}$
	- $ε_r$ - relativní permitivita - vlastnost dielektrika, říká kolikrát lépe se dielektrikum „polarizuje“ než vakuum
- jednotkou je **farad (F)** - je hodně velký, proto se používají menší jednotky: $pF$, $nF$, $μF$
	- běžné kondenzátory mají kapacitu nejvýš v jednotkách $mF$
- odkud se vzorec vzal: [[Elektrostatické pole#Kapacita]]
- okamžitý proud kondenzátorem závisí na tom, jak rychle se mění napětí:
$$i = C\cdot\frac{du}{dt}$$

## Polarizace dielektrika
- **dielektrikum** je speciální izolant
- jeho parametrem je **relativní permitivita** $ε_r$ - udává, kolikrát má daný materiál větší polarizovatelnost než vakuum
- celková permitivita dielektrika:
$$ε = ε_0\cdot ε_r \quad [F\cdot m^{-1}]$$
- víc o permitivitě: [[Elektrostatické pole#Permitivita]]

![[obrazky/prezentace/kondenzatory/2.jpg|400]]

## Parametry
- **Jmenovitá kapacita** - hodnota kapacity, kterou udává výrobce (s danou tolerancí)
- **Tolerance** - v (± %), o kolik se může skutečná kapacita lišit od jmenovité
- **Izolační odpor** - stejnosměrný odpor mezi vývody kondenzátoru při dané teplotě (čím větší, tím méně se kondenzátor sám vybíjí)
- **Provozní napětí** - nejvyšší napětí, které může být na kondenzátor trvale připojené
- **Dovolené oteplení** - zpravidla 40 °C
- **Jmenovité střídavé napětí** - udané výrobcem pro daný kmitočet, je menší než stejnosměrné (DC) napětí
- podobně se popisují i [[Provozní parametry rezistorů|rezistory]] a [[Cívky#Parametry cívek|cívky]]

## Reálný kondenzátor
- chování reálných součástek se modeluje pomocí ideálních součástek
- reálný kondenzátor má vedle kapacity $C$ také **parazitní vlastnosti**:
	- **parazitní indukčnost L** - indukčnost desek a přívodů
	- **ekvivalentní sériový odpor ESR** - ztráty v dielektriku, odpor elektrod a přívodů, povrchový jev, izolační odpor dielektrika
- náhradní schéma: **ESR - C - L** zapojené za sebou
- parazitní vlastnosti závisí na teplotě, kmitočtu napětí, vlhkosti a stárnutí dielektrika
- stejně se modeluje i rezistor: [[Provozní parametry rezistorů#Model reálného rezistoru]]

![[obrazky/prezentace/kondenzatory/4.jpg|400]]

## Kondenzátor v obvodu
- **stejnosměrný proud (DC)** - kondenzátor se nenabije hned (skokem), ale **postupně** podle exponenciální křivky (přes rezistor)
	- rychlost nabíjení určuje **časová konstanta** $τ = R\cdot C$ $[s]$ - větší odpor nebo kapacita = pomalejší nabíjení
	- napětí na kondenzátoru při nabíjení ze zdroje $U$:
$$u_c(t) = U\cdot\left(1-e^{-\frac{t}{τ}}\right)$$
	- když je nabitý, proud už obvodem **neteče**

![[obrazky/prezentace/kondenzatory/5.jpg|400]]

- **střídavý proud (AC)** - kondenzátor se pořád nabíjí a vybíjí, takže proud **propouští**
	- klade ale proudu odpor - **kapacitní reaktanci**:
$$X_C = \frac{1}{ω\cdot C} = \frac{1}{2\cdot\pi\cdot f\cdot C} \quad [Ω]$$
	- $f$ - kmitočet signálu $[Hz]$, $C$ - kapacita $[F]$, $ω$ - kruhový kmitočet $[rad/s]$
	- čím vyšší kmitočet, tím lépe proud propouští ($X_C$ klesá)
	- platí obdoba Ohmova zákona: $U = I\cdot X_C$
	- proud a napětí nejsou ve fázi - proud předbíhá napětí, u ideálního kondenzátoru o **90°** (na rozdíl od rezistoru, viz [[Ohmův a Kirchhoffovy zákony#Ohmův zákon]])
	- průběhy: $u = U_{max}\cdot\sin(ω\cdot t)$, $i = I_{max}\cdot\cos(ω\cdot t)$
	- u cívky je to obráceně - reaktance s kmitočtem roste a napětí předbíhá proud: [[Cívky#Cívka v obvodu střídavého proudu]]

![[obrazky/prezentace/kondenzatory/6.jpg|400]] ![[obrazky/prezentace/kondenzatory/7.jpg|400]]

## Spojování kondenzátorů
- **Paralelně** (vedle sebe) - na všech je stejné napětí, náboje se sčítají → kapacity se sčítají
$$Q = Q_1 + Q_2 + Q_3 \;\Rightarrow\; C\cdot U = C_1\cdot U + C_2\cdot U + C_3\cdot U$$
$$C = C_1 + C_2 + C_3$$
- **Sériově** (za sebou) - na všech je stejný náboj, napětí se sčítají → výsledná kapacita je menší
$$U = U_1 + U_2 + U_3 \;\Rightarrow\; \frac{Q}{C} = \frac{Q}{C_1} + \frac{Q}{C_2} + \frac{Q}{C_3}$$
$$\frac{1}{C} = \frac{1}{C_1} + \frac{1}{C_2} + \frac{1}{C_3}$$
- je to přesně obráceně než u rezistorů - viz [[Ohmův a Kirchhoffovy zákony#Spojování rezistorů]]

## Hlavní typy kondenzátorů
- **Vzduchové** - dielektrikem je vzduch
- **Svitkové** - dielektrikem je papír, metalizovaný papír nebo plastová fólie
- **Keramické** - dielektrikem je keramická hmota (např. oxid titaničitý)
- **Elektrolytické** - mají velkou kapacitu, **nutno dodržet polaritu** (+ a −), jinak se zničí
	- hliníkové
	- tantalové
	- záporný vývod (−) bývá na pouzdře označený pruhem
	- provedení: V-Chip (pro povrchovou montáž), axiální, radiální, snap-in
- **Proměnné** - kapacita se dá měnit: otočné (ladicí) kondenzátory a kapacitní trimry

![[obrazky/prezentace/kondenzatory/keramicky-kondenzator.jpg|400]] ![[obrazky/prezentace/kondenzatory/elektrolyticke-polarita.jpg|400]]

![[obrazky/prezentace/kondenzatory/promenne-kondenzatory.jpg|400]]

## Značení kondenzátorů - bonus
- **číslicovým potiskem** - u svitkových a elektrolytických kondenzátorů (výjimečně u keramických)
- **barevným kódem** - u keramických kondenzátorů
- značicí písmena pro toleranci: **J** = ±5 %, **K** = ±10 %, **M** = ±20 %
- alfanumerický kód - písmeno předpony stojí na místě desetinné čárky (stejně jako u rezistorů, viz [[Provozní parametry rezistorů#Řady jmenovitých hodnot a značení - bonus]]):
	- `4p7` = 4,7 pF, `47p` = 47 pF
	- `n47` = 0,47 nF, `4n7` = 4,7 nF, `47n` = 47 nF
	- `μ47` = 0,47 μF, `4μ7` = 4,7 μF, `47μ` = 47 μF
	- `m47` = 0,47 mF

![[obrazky/prezentace/kondenzatory/8.jpg|400]]

## Použití kondenzátorů - bonus
- **keramické** - vysokofrekvenční obvody: vazba a blokování, ladění oscilátorů, filtry, teplotní kompenzace
- **fóliové (svitkové)** - časování, přesné obvody (A/D převodníky), odrušení, rozběh a chod motorů
- **elektrolytické** - vyhlazování a filtrace napájení, zdroje a měniče, zálohování (UPS), rozběh motorů
- oblasti se překrývají - na vazbu, blokování a filtraci šumu se hodí všechny tři

![[obrazky/prezentace/kondenzatory/pouziti-kondenzatoru.jpg|400]]

## Schematické značky
![[obrazky/prezentace/kondenzatory/3.jpg|400]]
