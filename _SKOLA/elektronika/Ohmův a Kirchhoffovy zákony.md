- souvisí s: [[Elektrický potenciál a napětí]], [[Provozní parametry rezistorů]], [[Kondenzátory]], [[Cívky]]

## Základní veličiny
- **Elektrický proud** - tok elektronů vodičem, říká, kolik náboje proteče za určitý čas $I=\frac{Q}{t}$ $[A;\ C;\ s]$
	- směr proudu se kreslí od **+** k **−**, elektrony ve skutečnosti tečou opačně
	- z toho plyne jednotka náboje: $1\ C = 1\ A\cdot s$ (víc o náboji: [[Elektrostatické pole#Elektrický náboj]])
- **Elektrické napětí** - „tlačí“ proud obvodem, je to rozdíl potenciálů mezi dvěma místy (viz [[Elektrický potenciál a napětí]]) $[V]$
- **Elektrický odpor** - jak moc materiál brání průchodu proudu $R=ρ\cdot\frac{l}{S}$ $[Ω]$
	- **ρ** = rezistivita - vlastnost materiálu (jak dobře vede proud)
	- **l** = délka vodiče - delší vodič má větší odpor
	- **S** = průřez vodiče - tlustší vodič má menší odpor
- **Elektrická vodivost** - opak odporu, jak dobře materiál proud vede $G=\frac{1}{R}=γ\cdot\frac{S}{l}$ $[S]$
	- jednotkou je **siemens (S)**
	- **γ** = konduktivita (měrná vodivost) - vlastnost materiálu, opak rezistivity
	- stejný tvar vzorce má kapacita i magnetická vodivost - viz [[Magnetické pole#Analogie polí]]
- **Proudová hustota** - kolik proudu teče jedním čtverečním milimetrem (metrem) průřezu $J = \frac{I}{S}$
	- **J** = proudová hustota - udává se v $A/m^2$, v praxi spíše v $A/mm^2$
	- **I** = elektrický proud, který teče vodičem $[A]$
	- **S** = obsah průřezu vodiče $[m^2]$ nebo $[mm^2]$

## Definice elektrického odporu
- **ideální rezistor** je pasivní **jednobran** (dvojice svorek = jedna brána), jehož základní vlastností je elektrický odpor
- elektrická energie se v něm mění na **teplo**
- závislost proudu a napětí popisují charakteristické rovnice:
$$I = f(U) \qquad I = G\cdot U \quad [A;\ S;\ V]$$
$$U = φ(I) \qquad U = R\cdot I \quad [V;\ Ω;\ A]$$
- grafem závislosti proudu na napětí je **přímka** - dvakrát větší napětí = dvakrát větší proud
- skutečný rezistor se od ideálního liší: [[Provozní parametry rezistorů#Model reálného rezistoru]]

![[obrazky/prezentace/ohm-kirchhoff/definice-odporu.jpg|450]]

## Ohmův zákon
- velikost odporu je dána poměrem napětí a proudu
$$R=\frac{U}{I} \qquad I=\frac{U}{R} \qquad U=R\cdot I$$
$[Ω;\ V;\ A]$
- Jednotkou odporu je **Ohm - Ω**
	- odpor se často vyjadřuje v násobcích: $kΩ$ (kiloohm), $MΩ$ (megaohm)
	- všechny odvozené jednotky: $mΩ = 10^{-3}\ Ω$ (miliohm), $kΩ = 10^{3}\ Ω$ (kiloohm), $MΩ = 10^{6}\ Ω$ (megaohm), $GΩ = 10^{9}\ Ω$ (gigaohm), $TΩ = 10^{12}\ Ω$ (teraohm)
- čím větší napětí, tím větší proud - čím větší odpor, tím menší proud
- napětí i proud na rezistoru mají stejný průběh - jsou **ve fázi** (u [[Kondenzátory|kondenzátoru]] ani u [[Cívky|cívky]] to neplatí)

### Příklad
- zdroj má napětí **15 V**, ampérmetr v obvodu ukazuje **100 mA**. Jak velký je odpor rezistoru $R_1$?
$$R_1 = \frac{U}{I} = \frac{15}{0{,}1} = 150\ Ω$$

## Kirchhoffovy zákony
1. **Proudový zákon** - součet proudů v **uzlu je roven 0**
	- kolik proudu do uzlu přiteče, tolik ho musí i odtéct
	- např. proud $I$ se v uzlu rozdělí do dvou větví: $I=I_1+I_2$
	- zápis se znaménky (přitékající +, odtékající −): $+I-I_1-I_2=0$
2. **Napěťový zákon** - součet napětí v **uzavřené smyčce je roven 0**
	- napětí zdroje se rozdělí mezi spotřebiče ve smyčce
	- např. zdroj a dva rezistory za sebou: $U=U_1+U_2$
	- zápis se znaménky: $+U-U_1-U_2=0$

![[obrazky/prezentace/ohm-kirchhoff/kirchhoffovy-zakony.jpg|600]]

## Vnitřní odpor zdroje
- skutečný zdroj se chová jako ideální zdroj napětí $U_i$ a **vnitřní odpor** $R_i$ zapojený s ním do série
- když do zátěže $R_z$ teče proud $I$, na vnitřním odporu vznikne úbytek napětí:
$$ΔU = R_i\cdot I$$
- napětí zdroje se podle 2. Kirchhoffova zákona rozdělí mezi vnitřní odpor a zátěž:
$$U_i = ΔU + U_z \qquad U_z = R_z\cdot I$$
- čím větší proud ze zdroje odebíráme, tím menší napětí zbude na zátěži

![[obrazky/sesit-vyrezy/vnitrni-odpor-zdroje.jpg|600]]

## Spojování rezistorů
- **Sériově** (za sebou) - všemi teče stejný proud, odpory se sčítají
$$R = R_1 + R_2$$
- **Paralelně** (vedle sebe) - na všech je stejné napětí, výsledný odpor je menší
$$\frac{1}{R} = \frac{1}{R_1} + \frac{1}{R_2}$$
- u kondenzátorů je to obráceně - viz [[Kondenzátory#Spojování kondenzátorů]]
