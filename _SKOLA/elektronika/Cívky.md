- navazuje na: [[Magnetické pole]] a [[Magnetické pole válcové cívky]] (odkud se bere indukčnost $L$)
- souvisí s: [[Kondenzátory]] (druhý akumulační prvek - v obvodu se chová „opačně“), [[Ohmův a Kirchhoffovy zákony]]
- pokračuje v: [[Transformátor]] (dvě cívky na společném jádře)

## Ideální cívka
- cívka je **akumulační obvodový prvek** - má schopnost hromadit a vydat energii (v magnetickém poli)
- uspořádání: **N závitů** vodiče okolo jádra
- materiál jádra:
	- vzduch
	- nemagnetický materiál
	- magnetický materiál
- základním parametrem je **indukčnost L**, jednotka **henry (H)**
$$Φ = L\cdot I \quad [Wb;\ H;\ A]$$
- indukčnost závisí na počtu závitů, rozměrech cívky a magnetických vlastnostech jádra:
$$L = N^2\cdot μ_0\cdot μ_r\cdot\frac{S}{l}$$
	- odvození a význam veličin: [[Magnetické pole válcové cívky#Vlastní indukčnost cívky L]]

## Parametry cívek
- jmenovitá hodnota indukčnosti
- tolerance
- činitel jakosti (jak moc se cívka blíží ideální - poměr reaktance $X_L$ k odporu vinutí)
- stejnosměrný odpor (odpor drátu vinutí)
- jmenovité napětí
- rozsah provozních teplot
- jmenovitý proud
- podobné parametry mají i [[Provozní parametry rezistorů|rezistory]] a [[Kondenzátory#Parametry|kondenzátory]]

## Konstrukce cívek
- **vzduchové** - bez jádra, malá indukčnost
- **s jádrem** - jádro z magnetického materiálu indukčnost zvětší (větší $μ_r$, viz [[Magnetické pole#Permeabilita]])

![[obrazky/prezentace/civky/civky-vzduchove-a-s-jadrem.jpg|500]]

- tvary jader: **toroidní** jádro, **„C“** jádro, **„EI“** jádro (složené z plechů tvaru E a I)

![[obrazky/prezentace/civky/jadra-toroid-c-ei.jpg|600]]

![[obrazky/prezentace/civky/konstrukcni-provedeni-civek.jpg|400]]

## Cívka v obvodu střídavého proudu
- cívka klade průchodu proudu elektrický odpor
- **ideální cívka má nulový stejnosměrný odpor** - pro stejnosměrný proud je to jen kus drátu
- střídavý odpor = **reaktance** $X_L$ - závisí na kmitočtu střídavého proudu a na indukčnosti $L$
$$X_L = ω\cdot L = 2\cdot\pi\cdot f\cdot L \quad [Ω]$$
	- $f$ - kmitočet (frekvence), $L$ - indukčnost, $ω$ - kruhový kmitočet
	- čím vyšší kmitočet, tím větší odpor cívka klade - přesně naopak než kondenzátor ($X_C$, viz [[Kondenzátory#Kondenzátor v obvodu]])
- napětí na cívce vzniká jen tehdy, když se proud mění:
$$u = L\cdot\frac{di}{dt}$$
- proud a napětí jsou posunuté o **90°** - u cívky napětí předbíhá proud (u kondenzátoru předbíhá proud napětí, u rezistoru jsou ve fázi - viz [[Ohmův a Kirchhoffovy zákony#Ohmův zákon]])

![[obrazky/sesit-vyrezy/civka-fazovy-posun.jpg|500]]

## Impedance
- skutečná cívka má indukčnost $L$ i odpor vinutí $R$, dohromady tvoří **impedanci**:
$$Z = R + j\cdot X_L$$
- ve fázorovém diagramu je $R$ na vodorovné (reálné) ose, $X_L$ na svislé (imaginární) ose a $Z$ je úhlopříčka
- **Z** - impedance $[Ω]$ - „celkový odpor“ pro střídavý proud
- **Y** - admitance, $Y = \frac{1}{Z}$
- **B** - susceptance, $B = \frac{1}{X}$

![[obrazky/sesit-vyrezy/impedance-fazor.jpg|300]]
