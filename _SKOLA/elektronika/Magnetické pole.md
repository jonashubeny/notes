- pokračuje v: [[Magnetické pole válcové cívky]] → [[Cívky]] → [[Transformátor]]
- je to obdoba [[Elektrostatické pole|elektrostatického pole]] - viz [[#Analogie polí]]

## Fyzikální úvod
- magnetické pole se projevuje **silovými účinky**
- vytváří se v prostoru kolem:
	- magnetických látek (např. permanentní magnet)
	- vodiče, kterým prochází elektrický proud
	- v cívce (vinutím prochází proud)
- znázorňuje se pomocí **indukčních čar**
- má dva póly - **sever a jih (N a S)**
	- indukční čáry vycházejí ze severního pólu
	- uvnitř cívky nebo magnetu směřují indukční čáry od jižního pólu k severnímu

![[obrazky/prezentace/magneticke-pole/magneticke-pole-uvod.jpg|450]]

## Magnetické napětí
- je dané součtem proudů, které indukční čára obepíná
$$U_m = \sum I \quad [A]$$
	- na obrázcích: křížek = proud teče od nás, tečka = proud teče k nám
- cívkou o $N$ závitech teče stejný proud $N$-krát:
$$U_m = N\cdot I$$
- magnetickému napětí „zdroje“ (vinutí) se říká magnetomotorické napětí $F_m$ - viz [[Magnetické pole válcové cívky#Magnetomotorické napětí]]

## Intenzita magnetického pole
- magnetické napětí připadající na jednotku délky indukční čáry
$$H = \frac{U_m}{l} \quad [A\cdot m^{-1}]$$
- pro cívku: [[Magnetické pole válcové cívky]]

## Magnetická indukce B
- říká, jak „husté“ pole je - magnetický tok na jednotku plochy
$$B = μ_0\cdot μ_r\cdot H = \frac{Φ}{S}$$
$[T;\ H\cdot m^{-1};\ -;\ A\cdot m^{-1}] = [Wb;\ m^2]$
	- $B$ - magnetická indukce, jednotka **tesla (T)**
	- $H$ - intenzita magnetického pole
	- $Φ$ - magnetický tok, jednotka **weber (Wb)**
	- $S$ - plocha (kolmá ke směru $B$)

### Permeabilita
- vlastnost prostředí (materiálu jádra) - jak dobře „vede“ magnetické pole
$$μ = μ_0\cdot μ_r \qquad B = μ\cdot H$$
	- $μ_0$ - permeabilita vakua $= 4\cdot\pi\cdot10^{-7}\ H\cdot m^{-1}$
	- $μ_r$ - relativní permeabilita materiálu jádra cívky - kolikrát materiál pole zesílí oproti vakuu (bez jednotky)

## Magnetická vodivost (permeance) - bonus
$$G_m = \frac{1}{R_m} = μ_0\cdot μ_r\cdot\frac{S}{l}$$
$[H;\ H^{-1};\ H\cdot m^{-1};\ -;\ m^2;\ m]$
- $G_m$ - magnetická vodivost (v sešitě značená $Λ$)
- $R_m$ - magnetický odpor
- $S$ - průřez jádra cívky
- $l$ - střední délka materiálu, kudy prochází magnetický tok
- z magnetické vodivosti se počítá indukčnost cívky: $L = N^2\cdot Λ$ - viz [[Magnetické pole válcové cívky#Vlastní indukčnost cívky L]]

## Analogie polí
- všechna tři pole se popisují stejně: vlastnost prostředí · plocha / délka

| Pole | Vlastnost prostředí | „Vodivost“ | Hustota a intenzita |
| --- | --- | --- | --- |
| elektrostatické | permitivita $ε$ | kapacita $C = ε\cdot\frac{S}{d}$ | $D = ε\cdot E$ |
| proudové (vodič) | konduktivita $γ$ | vodivost $G = γ\cdot\frac{S}{l}$ | - |
| magnetické | permeabilita $μ$ | magnetická vodivost $Λ = μ\cdot\frac{S}{l}$ | $B = μ\cdot H$ |

- podrobněji: [[Elektrostatické pole#Kapacita]], [[Ohmův a Kirchhoffovy zákony#Základní veličiny]]
