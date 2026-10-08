- fyzikální základ je v zápisech z fyziky: [[coulombuv-zakon|Coulombův zákon]] → [[elektricke-pole|Elektrické pole]] → [[Elektrický potenciál a napětí]]
- pokračuje v: [[Kondenzátory]] (kapacita v praxi)
- stejný tvar vzorce „vlastnost prostředí · plocha / délka“ má i vodivost a magnetická vodivost - viz [[Magnetické pole#Analogie polí]]

## Elektrický náboj
- základní vlastnost elementárních částic hmoty
- značka $Q$, jednotka **coulomb (C)**, $1\ C = 1\ A\cdot s$ (viz [[Ohmův a Kirchhoffovy zákony#Základní veličiny|elektrický proud]])
- elementární náboj (náboj jednoho elektronu): $e = 1{,}602\cdot10^{-19}\ C$

## Coulombův zákon
- vyjadřuje silové působení mezi dvěma náboji
- dva bodové náboje $Q_1$, $Q_2$ se přitahují nebo odpuzují stejně velkými silami opačného směru
- síla je přímo úměrná součinu nábojů a nepřímo úměrná druhé mocnině jejich vzdálenosti $r$
$$F = k\cdot\frac{Q_1\cdot Q_2}{r^2} \qquad k=\frac{1}{4\cdot\pi\cdot ε}$$
- po dosazení za $k$:
$$F = \frac{Q_1\cdot Q_2}{4\cdot\pi\cdot ε_0\cdot ε_r\cdot r^2}$$
- příklady na výpočet jsou ve fyzice: [[coulombuv-zakon|Coulombův zákon]]

### Permitivita
- vlastnost prostředí (izolantu), ve kterém náboje jsou
$$ε = ε_0\cdot ε_r \quad [F\cdot m^{-1}]$$
	- $ε_0$ - permitivita vakua $= 8{,}854\cdot10^{-12}\ F\cdot m^{-1}$
	- $ε_r$ - relativní permitivita - kolikrát je permitivita prostředí větší než permitivita vakua (ve vakuu je $ε_r = 1$)
- v kondenzátoru je tím prostředím dielektrikum - viz [[Kondenzátory#Polarizace dielektrika]]

## Intenzita elektrického pole
- na náboj v el. poli působí síla, která je úměrná velikosti tohoto náboje a intenzitě el. pole
$$F = E\cdot Q \qquad E=\frac{F}{Q}$$
- pole bodového náboje $Q$ ve vzdálenosti $r$ (zkušební náboj $q$ se vykrátí):
$$E = \frac{F}{q} = \frac{Q\cdot q}{4\cdot\pi\cdot ε\cdot r^2}\cdot\frac{1}{q} = \frac{Q}{4\cdot\pi\cdot ε\cdot r^2}$$
- odvozená jednotka intenzity je $V\cdot m^{-1}$ (je to totéž co $N\cdot C^{-1}$ z fyziky - viz [[elektricke-pole|Elektrické pole]])

## Potenciál
- práce potřebná na přenesení náboje $q$ do daného místa pole, přepočtená na jednotkový náboj
$$V = \frac{W}{q} \quad [V]$$
- místa se stejným potenciálem tvoří **hladiny** ($V_1$, $V_2$ na obrázku)
- rozdíl potenciálů dvou míst je **napětí** - viz [[Elektrický potenciál a napětí]] (ve fyzice se potenciál značí $φ$)

![[obrazky/sesit-vyrezy/potencial-hladiny.jpg|450]]

## Elektrostatický indukční tok
- celkový „tok“ siločar, který vychází z náboje - je roven součtu nábojů
$$ψ = \sum Q \quad [C]$$
- siločáry vycházejí z kladného náboje a končí v záporném

![[obrazky/sesit-vyrezy/elektrostaticke-pole-silocary.jpg|450]]

## Elektrická indukce
- **hustota siločar** - kolik indukčního toku prochází jednotkovou plochou
- z intenzity bodového náboje ($S = 4\cdot\pi\cdot r^2$ je povrch koule o poloměru $r$):
$$E = \frac{Q}{4\cdot\pi\cdot ε\cdot r^2} \;\Rightarrow\; ε\cdot E = \frac{Q}{4\cdot\pi\cdot r^2} = \frac{Q}{S}$$
$$D = ε\cdot E = \frac{Q}{S} \quad [C\cdot m^{-2}]$$
- $D$ nezávisí na prostředí, $E$ ano (přes $ε$)

## Kapacita
- **schopnost vodiče pojmout náboj**
$$C = \frac{Q}{U} \quad [F]$$
- odvození pro dvě desky o ploše $S$ ve vzdálenosti $d$ (v homogenním poli je $E = \frac{U}{d}$):
$$D = ε\cdot E \;\Rightarrow\; \frac{Q}{S} = ε\cdot\frac{U}{d}$$
$$C = \frac{Q}{U} = ε\cdot\frac{S}{d} = ε_0\cdot ε_r\cdot\frac{S}{d}$$
- $ε_0\cdot ε_r$ popisuje prostředí mezi deskami
- součástka postavená přesně takhle je kondenzátor → [[Kondenzátory#Kapacita]], spojování více kapacit → [[Kondenzátory#Spojování kondenzátorů]]
