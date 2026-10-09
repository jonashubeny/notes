- vzorce, které se hodí na test T1 (rezistory, kondenzátory, cívky) - u každého je: **co počítá**, **kdy ho použiješ**, **jak si ho vymyslet, když zapomeneš**
- souvisí s: [[Test T1 - otázky a odpovědi]], [[Elektronika - přehled]]

## 0. Dva triky, které zachrání skoro každý vzorec

### Trik 1: jednotky ti řeknou vzorec
- **ohm je volt děleno ampérem**: $Ω = \frac{V}{A}$ → takže $R = \frac{U}{I}$
- **watt je volt krát ampér**: $W = V\cdot A$ → takže $P = U\cdot I$
- **coulomb je ampér krát sekunda**: $C = A\cdot s$ → takže $Q = I\cdot t$
- když nevíš, jestli násobit nebo dělit, zkontroluj, jestli ti vyjde správná jednotka

### Trik 2: vždycky nejdřív převeď na základní jednotky
| Předpona | Znamená | Příklad |
| --- | --- | --- |
| p (piko) | $10^{-12}$ | 47 pF = 0,000 000 000 047 F |
| n (nano) | $10^{-9}$ | |
| μ (mikro) | $10^{-6}$ (milióntina) | 10 μF = 0,000 01 F |
| m (mili) | $10^{-3}$ (tisícina) | 20 mA = 0,02 A |
| k (kilo) | $10^{3}$ (tisíc) | 0,2 kΩ = 200 Ω |
| M (mega) | $10^{6}$ (milion) | 1 MΩ = 1 000 000 Ω |

- nejčastější chyba v testu: počítat s 20 mA jako s 20 A

---

## 1. Ohmův zákon - nejdůležitější vzorec
$$U = R\cdot I \qquad I = \frac{U}{R} \qquad R = \frac{U}{I}$$
- **co počítá:** vztah mezi napětím (V), proudem (A) a odporem (Ω)
- **kdy:** skoro vždycky - znáš dvě veličiny a chceš třetí
- **jak si ho zapamatovat - trojúhelník:**
```
      U
    -----
    R | I
```
	- zakryj prstem, co hledáš: zakryješ U → zbude $R\cdot I$ (vedle sebe = krát), zakryješ I → zbude $\frac{U}{R}$ (nad sebou = děleno)
- **jak si ho odvodit selským rozumem:** víc tlaku (U) = víc vody (I), užší trubka (víc R) = méně vody → $I = \frac{U}{R}$
- **příklad:** U = 4 V, I = 20 mA = 0,02 A → $R = \frac{4}{0{,}02} = 200\ Ω$
- víc: [[Ohmův a Kirchhoffovy zákony#Ohmův zákon]]

## 2. Výkon - kolik tepla rezistor musí vydržet
$$P = U\cdot I \quad [W]$$
- **co počítá:** výkon ve wattech = „zatížitelnost“, kterou musí rezistor vydržet, jinak shoří
- **kdy:** v otázce je slovo „výkon“, „zatížitelnost“, „W“
- **jak si ho odvodit:** watt = volt · ampér (trik 1)
- **když neznáš U nebo I**, dosaď za něj z Ohmova zákona:
	- znáš I a R: $P = U\cdot I = (R\cdot I)\cdot I = R\cdot I^2$
	- znáš U a R: $P = U\cdot I = U\cdot\frac{U}{R} = \frac{U^2}{R}$
	- nemusíš se je učit - stačí $P = U\cdot I$ a Ohmův zákon
- **příklad:** U = 4 V, I = 0,02 A → $P = 4\cdot0{,}02 = 0{,}08\ W = 80\ mW$

## 3. Spojování rezistorů
- **za sebou (sériově)** - odpory se prostě sečtou:
$$R = R_1 + R_2 + \dots$$
	- odvození: proud musí projít jedním **a pak** druhým → brzdí víc → sčítá se
- **vedle sebe (paralelně)** - výsledek je **menší** než nejmenší z nich:
$$\frac{1}{R} = \frac{1}{R_1} + \frac{1}{R_2}$$
	- pro **dva** rezistory zkratka: $R = \frac{R_1\cdot R_2}{R_1 + R_2}$ („součin lomeno součet“)
	- odvození: proud má víc cest → teče ho víc → odpor je menší
	- kontrola: dva stejné rezistory paralelně = **polovina** (2× 100 Ω → 50 Ω)
- víc: [[Ohmův a Kirchhoffovy zákony#Spojování rezistorů]]

## 4. Kirchhoffovy zákony
- **1. zákon (uzel)** - kolik proudu přiteče, tolik odteče:
$$I = I_1 + I_2$$
	- představ si rozdvojení trubky - voda se rozdělí, ale žádná se neztratí
- **2. zákon (smyčka)** - napětí zdroje se rozdělí mezi součástky:
$$U = U_1 + U_2$$
	- představ si, že jdeš z kopce: celková výška (U) = součet jednotlivých schodů ($U_1$, $U_2$)
- **kdy:** obvody s více součástkami, děliče napětí
- víc: [[Ohmův a Kirchhoffovy zákony#Kirchhoffovy zákony]]

## 5. Odpor drátu
$$R = ρ\cdot\frac{l}{S}$$
- **co počítá:** odpor vodiče podle materiálu ($ρ$), délky ($l$) a průřezu ($S$)
- **jak si ho odvodit:** delší drát = víc odporu → $l$ je **nahoře**; tlustší drát = méně odporu → $S$ je **dole**
- **vodivost** je jen opak odporu: $G = \frac{1}{R}$, jednotka siemens (S)

---

## 6. Kapacita kondenzátoru
$$C = \frac{Q}{U} \quad [F]$$
- **co počítá:** kolik náboje ($Q$) se do kondenzátoru vejde při daném napětí
- **jak si ho odvodit:** „kapacita = kolik se vejde na jeden volt“ → náboj děleno napětím
- z toho: $Q = C\cdot U$

$$C = ε\cdot\frac{S}{d} = ε_0\cdot ε_r\cdot\frac{S}{d}$$
- **co počítá:** kapacitu podle toho, jak je kondenzátor postavený
- **jak si ho odvodit:** větší desky ($S$) = víc místa na náboj → nahoře; desky dál od sebe ($d$) = horší → dole; $ε$ = jak dobrý je izolant mezi deskami
- **stejný tvar jako odpor drátu** (materiál · plocha / délka), jen obráceně je plocha nahoře
- víc: [[Kondenzátory#Kapacita]]

## 7. Spojování kondenzátorů - přesně OBRÁCENĚ než rezistory
- **paralelně (vedle sebe)** - kapacity se sčítají:
$$C = C_1 + C_2$$
	- odvození: vedle sebe = jako by se desky zvětšily → víc kapacity
- **sériově (za sebou)** - výsledek je menší:
$$\frac{1}{C} = \frac{1}{C_1} + \frac{1}{C_2}$$
	- odvození: za sebou = jako by se desky od sebe vzdálily → méně kapacity
- **jak si to nesplést:** pamatuj si rezistory a u kondenzátorů to otoč
- víc: [[Kondenzátory#Spojování kondenzátorů]]

## 8. Časová konstanta - jak rychle se kondenzátor nabije
$$τ = R\cdot C \quad [s]$$
- **co počítá:** čas, za který se kondenzátor přes rezistor nabije zhruba na 2/3 (63 %); úplně nabitý je asi po $5\cdot τ$
- **jak si ho odvodit:** větší odpor = pomaleji teče proud = déle se nabíjí; větší kapacita = větší „nádrž“ = déle se plní → obojí násobí
- **příklad:** R = 1 kΩ = 1000 Ω, C = 1000 μF = 0,001 F → $τ = 1000\cdot0{,}001 = 1\ s$
- víc: [[Kondenzátory#Kondenzátor v obvodu]]

---

## 9. Střídavý proud - kruhový kmitočet
$$ω = 2\cdot\pi\cdot f$$
- **co počítá:** převádí frekvenci $f$ (Hz) na „kruhový kmitočet“ $ω$ (rad/s), který se objevuje ve vzorcích níže
- **odvození:** jedna celá otáčka (perioda) je $2\pi$ radiánů, a těch je za sekundu $f$

## 10. Reaktance - „odpor“ kondenzátoru a cívky pro střídavý proud
$$X_C = \frac{1}{ω\cdot C} = \frac{1}{2\pi f C} \qquad X_L = ω\cdot L = 2\pi f L \qquad [Ω]$$
- **co počítá:** jak moc kondenzátor ($X_C$) nebo cívka ($X_L$) brzdí střídavý proud - v ohmech, takže pak můžeš použít Ohmův zákon: $I = \frac{U}{X}$
- **jak si to odvodit, když zapomeneš, co je nahoře a co dole:**
	- **cívka** stejnosměrný proud ($f = 0$) pouští jako drát → odpor musí být 0 → $X_L = 2\pi \cdot 0 \cdot L = 0$ ✔ → $f$ je **nahoře**
	- **kondenzátor** stejnosměrný proud ($f = 0$) nepustí → odpor musí být nekonečný → dělení nulou → $f$ je **dole** ✔
	- **rezistor** frekvence nezajímá → ve vzorci $f$ není
- víc: [[Cívky#Cívka v obvodu střídavého proudu]], [[Kondenzátory#Kondenzátor v obvodu]]

## 11. Impedance - celkový „odpor“ pro střídavý proud
$$Z = R + j\cdot X$$
- **co počítá:** když má součástka odpor $R$ i reaktanci $X$ najednou (např. skutečná cívka má odpor drátu)
- „$j$“ jen značí, že $X$ míří jiným směrem (ve fázorovém diagramu svisle)
- velikost se počítá Pythagorovou větou: $|Z| = \sqrt{R^2 + X^2}$ - $R$ a $X$ jsou odvěsny, $Z$ přepona
- víc: [[Cívky#Impedance]]

---

## 12. Základ z elektrostatiky (kdyby přišel)
- **proud:** $I = \frac{Q}{t}$ - kolik náboje proteče za sekundu (trik 1: A = C/s)
- **Coulombův zákon:** $F = k\cdot\frac{Q_1\cdot Q_2}{r^2}$, $k = 9\cdot10^9$
	- odvození selským rozumem: větší náboje = větší síla (nahoře); dál od sebe = slabší, a to **s druhou mocninou** (2× dál = 4× slabší)
- **intenzita pole:** $E = \frac{F}{Q}$ - síla na jeden coulomb
- **napětí v homogenním poli:** $U = E\cdot d$
- víc: [[Elektrostatické pole]]

---

## Shrnutí na jednu obrazovku
| Co hledám | Vzorec | Pamatovák |
| --- | --- | --- |
| napětí, proud, odpor | $U = R\cdot I$ | trojúhelník U nahoře |
| výkon | $P = U\cdot I$ | W = V · A |
| rezistory za sebou | $R = R_1 + R_2$ | sčítám |
| rezistory vedle sebe | $R = \frac{R_1 R_2}{R_1 + R_2}$ | menší než nejmenší |
| kondenzátory | obráceně než rezistory | vedle sebe sčítám |
| kapacita | $C = \frac{Q}{U} = ε\frac{S}{d}$ | velké desky, blízko u sebe |
| nabíjení | $τ = R\cdot C$ | ~5τ = nabitý |
| kruhový kmitočet | $ω = 2\pi f$ | |
| cívka v AC | $X_L = 2\pi f L$ | DC → 0 → f nahoře |
| kondenzátor v AC | $X_C = \frac{1}{2\pi f C}$ | DC → ∞ → f dole |
| proud | $I = \frac{Q}{t}$ | A = C/s |

- **postup u každého příkladu:** 1) vypiš, co znáš, 2) převeď na základní jednotky, 3) vyber vzorec, 4) dosaď, 5) zkontroluj jednotku a jestli výsledek dává smysl
