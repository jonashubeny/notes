- souvisí s: [[Elektrický potenciál a napětí]], [[Provozní parametry rezistorů]], [[Kondenzátory]]

## Základní veličiny
- **Elektrický proud** - tok elektronů vodičem, říká, kolik náboje proteče za určitý čas $I=\frac{Q}{t}$ $[A;\ C;\ s]$
	- směr proudu se kreslí od **+** k **−**, elektrony ve skutečnosti tečou opačně
- **Elektrické napětí** - „tlačí“ proud obvodem, je to rozdíl potenciálů mezi dvěma místy (viz [[Elektrický potenciál a napětí]]) $[V]$
- **Elektrický odpor** - jak moc materiál brání průchodu proudu $R=ρ\cdot\frac{l}{S}$ $[Ω]$
	- **ρ** = rezistivita - vlastnost materiálu (jak dobře vede proud)
	- **l** = délka vodiče - delší vodič má větší odpor
	- **S** = průřez vodiče - tlustší vodič má menší odpor
- **Proudová hustota** - kolik proudu teče jedním čtverečním milimetrem (metrem) průřezu $J = \frac{I}{S}$
	- **J** = proudová hustota - udává se v $A/m^2$, v praxi spíše v $A/mm^2$
	- **I** = elektrický proud, který teče vodičem $[A]$
	- **S** = obsah průřezu vodiče $[m^2]$ nebo $[mm^2]$

## Ohmův zákon
- velikost odporu je dána poměrem napětí a proudu
$$R=\frac{U}{I} \qquad I=\frac{U}{R} \qquad U=R\cdot I$$
$[Ω;\ V;\ A]$
- Jednotkou odporu je **Ohm - Ω**
	- odpor se často vyjadřuje v násobcích: $kΩ$ (kiloohm), $MΩ$ (megaohm)
- čím větší napětí, tím větší proud - čím větší odpor, tím menší proud
- napětí i proud na rezistoru mají stejný průběh - jsou **ve fázi** (u [[Kondenzátory|kondenzátoru]] to neplatí)

## Kirchhoffovy zákony
1. **Proudový zákon** - součet proudů v **uzlu je roven 0**
	- kolik proudu do uzlu přiteče, tolik ho musí i odtéct
	- např. proud $I$ se v uzlu rozdělí do dvou větví: $I=I_1+I_2$
2. **Napěťový zákon** - součet napětí v **uzavřené smyčce je roven 0**
	- napětí zdroje se rozdělí mezi spotřebiče ve smyčce
	- např. zdroj a dva rezistory za sebou: $U=U_1+U_2$

## Spojování rezistorů
- **Sériově** (za sebou) - všemi teče stejný proud, odpory se sčítají
$$R = R_1 + R_2$$
- **Paralelně** (vedle sebe) - na všech je stejné napětí, výsledný odpor je menší
$$\frac{1}{R} = \frac{1}{R_1} + \frac{1}{R_2}$$
