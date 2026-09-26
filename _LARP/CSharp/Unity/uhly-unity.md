# Quaternion a Euler úhly v Unity

## Příklad: otočení hráče podle kamery

```csharp
float yaw = cameraTransform.eulerAngles.y;
Quaternion yawRotation = Quaternion.Euler(0f, yaw, 0f);
rb.MoveRotation(yawRotation);
```

Tenhle kód otočí hráčovo tělo tak, aby koukalo stejným směrem doleva/doprava jako kamera, ale nenakloní ho, když se díváš nahoru nebo dolů.

### Řádek po řádku

**`float yaw = cameraTransform.eulerAngles.y;`**
`cameraTransform` je pozice a natočení kamery. `eulerAngles` je natočení vyjádřené třemi čísly ve stupních:

- **x** = kývání hlavou nahoru/dolů (*pitch*)
- **y** = otáčení doleva/doprava (*yaw*)
- **z** = naklonění do strany, jako když nakloníš hlavu k rameni (*roll*)

Bereme si jen **y** a uložíme ho do proměnné `yaw`. `float` je desetinné číslo, třeba `87.5`.

**`Quaternion yawRotation = Quaternion.Euler(0f, yaw, 0f);`**
`Quaternion.Euler(x, y, z)` převede tři úhly ve stupních na quaternion, tedy na podobu, ve které si Unity ukládá rotace. Schválně dáváme `x = 0` a `z = 0`, takže vznikne rotace jen doleva/doprava. (`0f` je nula jako `float`.)

**`rb.MoveRotation(yawRotation);`**
`rb` je **Rigidbody**, komponenta, díky které se na postavu vztahuje fyzika. `MoveRotation` řekne fyzikálnímu systému, jak má těleso natočit.

### Proč to je takhle

V FPS hře se má postava otáčet s myší. Kdyby tělo dostalo **celou** rotaci kamery, při pohledu dolů by se překlopilo dopředu a pohyb by šel do země. Proto se rotace rozdělí: kamera dostane všechno, tělo jen yaw.

Proč `rb.MoveRotation` a ne `transform.rotation = ...`? Objekt s Rigidbody řídí fyzikální engine. Přímý zápis do `transform` fyziku obchází a může způsobit cukání nebo procházení zdmi.

### Tipy

- Kód s Rigidbody patří ideálně do `FixedUpdate()`, ne do `Update()`.
- Na Rigidbody hráče zamkni rotaci na osách X a Z (Inspector → *Constraints → Freeze Rotation X, Z*), jinak může postavu převrhnout srážka.

---

## Dva způsoby, jak zapsat natočení

### Euler úhly: tři čísla, kterým rozumíš

Euler (čti „ojler“) popisuje natočení jako **tři otočení za sebou**, každé kolem jedné osy:

- **X**: kývání nahoru/dolů („ano“)
- **Y**: otáčení doleva/doprava („ne“)
- **Z**: náklon do strany

Třeba `(0, 90, 0)` znamená „otoč se o 90° doprava“. Tahle čísla vidíš v Inspectoru u Transformu v políčku Rotation.

Výhoda: člověk tomu rozumí. Nevýhoda: má pár zákeřných problémů (viz níže).

### Quaternion: čtyři čísla pro počítač

Quaternion (čti „kvaternion“) zapisuje natočení jako **čtyři čísla** `(x, y, z, w)`. Zjednodušeně v sobě kóduje **osu**, kolem které se otáčí, a **o kolik**. Například „o 90° kolem svislé osy“ je zhruba `(0, 0.707, 0, 0.707)`.

Z těch čísel nic nevyčteš a **nikdy je nemusíš psát ručně**, Unity má funkce, které je vyrobí. Stačí vědět, že:

- Unity si uvnitř všechno ukládá jako quaternion (`transform.rotation` je `Quaternion`)
- quaternion nemá problémy, které mají Euler úhly
- dá se s ním dobře počítat a plynule přecházet mezi rotacemi

## Proč Unity nepoužívá jen Euler

**1. Gimbal lock (zamčení os).** Když se jedna osa otočí o 90°, dvě zbylé se překryjí a dělají totéž, takže přijdeš o jeden směr otáčení. Zkus to: podívej se úplně ke stropu (X = 90°). Vrtění hlavou (Y) a náklon (Z) jsou teď skoro to samé. Quaternion tenhle problém nemá.

**2. Jedno natočení, víc zápisů.** `(0, 360, 0)` je totéž co `(0, 0, 0)`. `(180, 0, 180)` vypadá stejně jako `(0, 180, 0)`. Když čteš `transform.eulerAngles`, Unity ti dá **nějakou** z možných verzí, ne nutně tu, kterou čekáš. Místo 85 tak můžeš dostat 275.

## Kdy co používat

Pravidlo: **přemýšlej v Eulerech, ukládej a počítej v quaternionech.**

Euler je dobrý na:

- zadání rotace člověkem („chci být otočený o 45° doprava“)
- práci s jednou osou, jako v příkladu nahoře
- vlastní proměnné, které si sám hlídáš (třeba úhel kamery nahoru/dolů)

Quaternion je dobrý na:

- nastavování `transform.rotation` nebo `rb.MoveRotation`
- plynulé otáčení z jednoho natočení do druhého
- skládání rotací a otáčení vektorů (směrů)

## Nejužitečnější funkce v Unity

```csharp
// Z Euler úhlů udělej quaternion
Quaternion r = Quaternion.Euler(0f, 90f, 0f);

// Z quaternionu zpátky Euler (pozor na problém č. 2)
Vector3 uhly = transform.rotation.eulerAngles;

// "Žádná rotace", výchozí natočení
Quaternion nic = Quaternion.identity;

// Natočení, které kouká daným směrem
Quaternion kNepriteli = Quaternion.LookRotation(nepritel.position - transform.position);

// Plynulý přechod z jedné rotace do druhé
transform.rotation = Quaternion.Slerp(transform.rotation, cil, 5f * Time.deltaTime);

// Poskládání rotací: NÁSOBENÍ, ne sčítání!
transform.rotation = transform.rotation * Quaternion.Euler(0f, 10f, 0f); // otoč o 10° navíc

// Otočení směru: quaternion * vektor
Vector3 doprava = Quaternion.Euler(0f, 90f, 0f) * Vector3.forward;
```

Pozor: rotace se u quaternionů **skládají násobením** a záleží na pořadí, `a * b` není totéž co `b * a`.

## Jak to souvisí s příkladem

```csharp
float yaw = cameraTransform.eulerAngles.y;              // quaternion → Euler, vezmu jen Y
Quaternion yawRotation = Quaternion.Euler(0f, yaw, 0f); // Euler → quaternion, jen s Y
rb.MoveRotation(yawRotation);                           // quaternion předám fyzice
```

Rotaci kamery převedeš do Eulerů, protože tak jde snadno vytáhnout jednu osu. Pak ji převedeš zpátky na quaternion, protože ho Rigidbody potřebuje.

## Typická past u FPS kamery

Kvůli problému č. 2 se kamera nahoru/dolů **nedělá** čtením `eulerAngles.x` a přičítáním. Úhel si drž ve vlastní proměnné:

```csharp
float pitch = 0f; // vlastní úhel nahoru/dolů

void Update()
{
    pitch -= Input.GetAxis("Mouse Y") * citlivost;
    pitch = Mathf.Clamp(pitch, -85f, 85f); // nedovol přetočit se přes hlavu
    cameraTransform.localRotation = Quaternion.Euler(pitch, 0f, 0f);
}
```

Tady víš jistě, jaké číslo v `pitch` je, protože ho spravuješ ty. `Mathf.Clamp` ho drží mezi -85° a 85°.