# Ansible – tahák pro úplného začátečníka

Všechno, co potřebuješ, abys přečetl cizí playbook, napsal vlastní a nebál se ho pustit.
Seřazeno tak, jak to dává smysl se učit: nejdřív pojmy, pak inventory, playbook,
spouštění, a až potom role, šablony a vault.

Hodí se vedle toho: [[Spouštění a testování]] (tam jsem poprvé narazil na slovo
*idempotentní*) a [[Struktura src]] (EJS šablony fungují stejně jako Jinja2 tady).

---

## 0. Co to je

**Ansible = nástroj, kterému popíšeš, jak má server vypadat, a on ho do toho stavu dostane.**

Místo „přihlas se přes SSH a napiš tyhle příkazy" napíšeš soubor, kde stojí
*„má být nainstalovaný nginx, má běžet, a tenhle konfigurák má vypadat takhle"*.

Tři věci, které je dobré vědět hned:

- **Bez agenta.** Na serveru nic neběží. Ansible se připojí přes obyčejné SSH,
  nahraje tam malý Python skript, pustí ho a smaže. Server potřebuje jen SSH a Python.
- **Popisuješ stav, ne kroky.** Nepíšeš „vytvoř složku", píšeš „složka má existovat".
- **Idempotence.** Pustíš to jednou → udělá změny. Pustíš to podruhé → neudělá nic,
  protože už je všechno tak, jak má být. **Playbook, který při druhém spuštění zase
  hlásí `changed`, je špatně napsaný.**

---

## 1. Slovníček

| pojem | co to je |
|---|---|
| **control node** | počítač, ze kterého Ansible pouštíš (tvůj notebook nebo vyhrazený server) |
| **managed node / host** | server, který Ansible nastavuje |
| **inventory** | seznam serverů a skupin, do kterých patří |
| **modul** | jedna „schopnost" – nainstaluj balík, zkopíruj soubor, restartuj službu |
| **task** | jedno volání modulu s parametry („nainstaluj nginx") |
| **play** | „na těchhle serverech proveď tyhle tasky" |
| **playbook** | soubor s jedním nebo víc playi |
| **role** | znovupoužitelný balíček tasků, šablon a proměnných s pevnou strukturou složek |
| **handler** | task, který se pustí jen když ho někdo „zavolá" (typicky restart po změně konfigurace) |
| **facts** | informace, které si Ansible o serveru zjistí sám (OS, IP, RAM, …) |
| **šablona (template)** | soubor s dírami `{{ ... }}`, které se vyplní proměnnými |
| **vault** | zašifrovaný soubor s hesly, který smí do gitu |
| **collection** | balík modulů a rolí od někoho jiného (`community.docker`, …) |

Hierarchie: **playbook → play → task → modul**. Role je jen způsob, jak tasky uklidit.

---

## 2. Instalace a první pokus

```bash
sudo dnf install ansible
ansible --version
```

Nepotřebuješ žádný server – všechno se dá zkoušet na vlastním počítači:

```bash
ansible localhost -m ping
```

```
localhost | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

`ping` tady není síťový ping. Znamená „umím se připojit a pustit tam Python".

---

## 3. Inventory – na co se to bude pouštět

```yaml
# inventory/inventory.yml
all:
  children:
    webservery:
      hosts:
        web01:
          ansible_host: 192.0.2.10
        web02:
          ansible_host: 192.0.2.11
    databaze:
      hosts:
        db01:
          ansible_host: 192.0.2.20
```

- `all` – skupina, do které patří úplně všechno
- `children` – podskupiny
- `web01` – jméno, pod kterým server znáš ty; `ansible_host` je adresa, kam se opravdu připojí

Proměnné k serverům se nepíšou do inventory, ale do složek vedle něj:

```
inventory/
├── inventory.yml
├── group_vars/
│   ├── all/            ← platí pro všechny
│   │   ├── vars.yml
│   │   └── vault.yml   ← zašifrovaná hesla
│   └── webservery.yml  ← platí jen pro skupinu webservery
└── host_vars/
    └── web01.yml       ← platí jen pro web01
```

Jméno souboru nebo složky **musí přesně sedět** na jméno skupiny nebo hosta.
Překlep = proměnné se tiše nenačtou.

```bash
ansible-inventory --graph              # strom skupin a hostů
ansible-inventory --host web01         # všechny proměnné, které web01 dostane
ansible all --list-hosts               # na koho by se to pustilo
```

---

## 4. Ad-hoc příkazy – jedna věc bez playbooku

```bash
ansible webservery -m ping
ansible all -m command -a "uptime"
ansible web01 -b -m dnf -a "name=htop state=present"
```

```
ansible  webservery  -b  -m dnf  -a "name=htop state=present"
   │         │        │     │        │
   │         │        │     │        └── argumenty modulu
   │         │        │     └─────────── modul
   │         │        └───────────────── become = pusť to jako root (sudo)
   │         └────────────────────────── na koho (host, skupina, all)
   └──────────────────────────────────── ad-hoc režim
```

Dobré na rychlé zjištění něčeho. Cokoliv, co chceš zopakovat, patří do playbooku.

---

## 5. YAML za dvě minuty

Playbooky jsou YAML. Devadesát procent chyb začátečníka je YAML, ne Ansible.

```yaml
klic: hodnota              # slovník (klíč: hodnota)
seznam:                    # seznam
  - prvni
  - druhy
vnoreny:
  jmeno: web01
  port: 22
text: "s dvojtečkou: musí být v uvozovkách"
```

Pravidla:

- **Odsazuje se mezerami, nikdy tabulátorem.** Dvě mezery na úroveň.
- Odsazení **je** syntaxe. O mezeru vedle = jiný význam nebo chyba.
- Za dvojtečkou a za pomlčkou je **mezera**.
- Hodnota, která **začíná** na `{{`, musí být celá v uvozovkách:

```yaml
# ŠPATNĚ – YAML si myslí, že { začíná slovník
dest: {{ cesta }}/app.ini

# SPRÁVNĚ
dest: "{{ cesta }}/app.ini"
```

- Práva souborů piš jako text v uvozovkách: `mode: '0644'`. Bez uvozovek a bez
  úvodní nuly (`mode: 644`) z toho vyleze nesmysl.
- `yes`, `no`, `on`, `off`, `true`, `false` jsou boolean. Když chceš text „no", dej uvozovky.

---

## 6. Playbook

```yaml
---
- name: Nastav webserver
  hosts: webservery
  become: true
  vars:
    http_port: 8080

  tasks:
    - name: Nainstaluj nginx
      ansible.builtin.dnf:
        name: nginx
        state: present

    - name: Nahraj konfiguraci
      ansible.builtin.template:
        src: nginx.conf.j2
        dest: /etc/nginx/nginx.conf
        mode: '0644'
      notify: Restartuj nginx

    - name: Nginx má běžet a startovat po bootu
      ansible.builtin.service:
        name: nginx
        state: started
        enabled: true

  handlers:
    - name: Restartuj nginx
      ansible.builtin.service:
        name: nginx
        state: restarted
```

Anatomie jednoho tasku:

```yaml
- name: Nahraj konfiguraci            ← popis pro lidi, vypíše se při běhu
  ansible.builtin.template:           ← modul (celé jméno = kolekce.modul)
    src: nginx.conf.j2                ┐
    dest: /etc/nginx/nginx.conf       ├ parametry modulu (odsazené POD modul)
    mode: '0644'                      ┘
  notify: Restartuj nginx             ┐
  when: ansible_os_family == "RedHat" ├ nastavení tasku (na úrovni modulu, ne pod ním)
  tags: nginx                         ┘
```

To odsazení je nejčastější chyba: `notify`, `when`, `tags`, `become`, `register`,
`loop` patří **tasku**, ne modulu. Když je odsadíš pod modul, dostaneš
`Unsupported parameters`.

`state` je klíčové slovo skoro každého modulu – říká cílový stav:
`present` / `absent`, `started` / `stopped` / `restarted`, `directory` / `file` / `link`.

---

## 7. Spouštění

```bash
ansible-playbook -i inventory/inventory.yml playbooks/web.yml
```

| přepínač | co dělá |
|---|---|
| `--check` | **nanečisto** – nic nezmění, jen řekne, co by změnil |
| `--diff` | ukáže rozdíl v souborech (před → po) |
| `--check --diff` | **tohle pouštěj vždycky jako první** |
| `--limit web01` | jen na jeden host (nebo skupinu) |
| `--tags nginx` | jen tasky s tímhle tagem |
| `--skip-tags nginx` | všechno kromě nich |
| `--list-hosts` | na koho by to šlo, nic nespustí |
| `--list-tasks` | jaké tasky by běžely, nic nespustí |
| `--syntax-check` | zkontroluje jen syntaxi |
| `--start-at-task "Jméno tasku"` | začni až od tohohle tasku |
| `--step` | ptá se před každým taskem |
| `-e "promenna=hodnota"` | proměnná z příkazové řádky (přebije všechno) |
| `-K` | zeptá se na sudo heslo |
| `--ask-vault-pass` | zeptá se na heslo k vaultu |
| `-v` / `-vv` / `-vvv` | víc výpisů (tři `v` = i SSH detaily) |

**Pozor u `--check`:** moduly `command` a `shell` se v něm přeskočí – Ansible neumí
odhadnout, co by libovolný příkaz udělal. A `--diff` vypíše do terminálu i hesla,
pokud jsou v souboru, který se mění.

Nastavení, aby se nemuselo psát `-i` pořád, je v `ansible.cfg` v kořeni repozitáře:

```ini
[defaults]
inventory = inventory/inventory.yml
roles_path = roles
```

```bash
ansible-config dump --only-changed     # co je nastavené jinak než výchozí
```

---

## 8. Jak číst výstup

```
TASK [Nainstaluj nginx] ***************
ok: [web01]
changed: [web02]

PLAY RECAP ****************************
web01 : ok=3  changed=0  unreachable=0  failed=0  skipped=1
web02 : ok=3  changed=1  unreachable=0  failed=0  skipped=1
```

| stav | význam |
|---|---|
| `ok` | už to tak bylo, nic se nedělalo |
| `changed` | něco se změnilo |
| `skipped` | task se přeskočil (nesplněné `when`) |
| `failed` | task spadl, na tomhle hostu se dál nepokračuje |
| `unreachable` | nejde se připojit (SSH, síť, klíč) |
| `ignored` | spadl, ale bylo tam `ignore_errors` |

Hledáš `failed=0` a `unreachable=0`. Po druhém spuštění chceš i `changed=0`.

---

## 9. Moduly, které použiješ pořád

| modul | k čemu |
|---|---|
| `ansible.builtin.file` | složka, práva, vlastník, symlink, smazání |
| `ansible.builtin.copy` | nahraj soubor tak, jak je |
| `ansible.builtin.template` | nahraj soubor a vyplň v něm proměnné |
| `ansible.builtin.lineinfile` | zajisti jeden řádek v existujícím souboru |
| `ansible.builtin.blockinfile` | zajisti blok řádků v existujícím souboru |
| `ansible.builtin.dnf` / `apt` | balíky (Fedora/RHEL / Debian/Ubuntu) |
| `ansible.builtin.package` | balíky bez ohledu na distribuci |
| `ansible.builtin.service` | start, stop, restart, enable služby |
| `ansible.builtin.user` / `group` | uživatelé a skupiny |
| `ansible.builtin.git` | naklonuj repozitář |
| `ansible.builtin.get_url` | stáhni soubor |
| `ansible.builtin.unarchive` | rozbal archiv |
| `ansible.builtin.command` | pusť příkaz (bez shellu – žádné roury ani `>`) |
| `ansible.builtin.shell` | pusť příkaz v shellu (roury jdou) |
| `ansible.builtin.stat` | zjisti, jestli soubor existuje a jaký je |
| `ansible.builtin.debug` | vypiš proměnnou nebo zprávu |
| `ansible.builtin.assert` | ověř podmínku, jinak spadni |
| `ansible.builtin.set_fact` | vytvoř proměnnou za běhu |
| `ansible.builtin.include_role` | pusť roli uprostřed tasků |
| `community.docker.docker_compose_v2` | `docker compose up` / `down` |

**`command` a `shell` až jako poslední možnost.** Nejsou idempotentní – pokaždé
hlásí `changed`. Skoro na všechno existuje modul.

Nápověda bez internetu:

```bash
ansible-doc ansible.builtin.template     # parametry + příklady
ansible-doc -l | grep docker             # hledání modulu
```

`copy` nebo `template`? Když v souboru potřebuješ proměnnou, `template`. Jinak `copy`.

---

## 10. Proměnné

```yaml
vars:
  service_name: forgejo
  base_dir: /opt/docker

tasks:
  - name: Vytvoř složku služby
    ansible.builtin.file:
      path: "{{ base_dir }}/{{ service_name }}"
      state: directory
      mode: '0755'
```

Kde se berou a **kdo vyhrává** (dole přebíjí horní):

```
defaults role  (roles/x/defaults/main.yml)     ← nejslabší, „výchozí hodnota"
group_vars/all
group_vars/<skupina>
host_vars/<host>
vars: v playi
vars role      (roles/x/vars/main.yml)
vars: u tasku
set_fact / register
-e na příkazové řádce                          ← nejsilnější, přebije všechno
```

Prakticky: **výchozí hodnoty do `defaults`, rozdíly mezi servery do `group_vars`
a `host_vars`.** Zbytek používej málo.

### Filtry

```yaml
"{{ port | default(8080) }}"          # když není definovaná, vezmi 8080
"{{ jmeno | upper }}"
"{{ seznam | join(', ') }}"
"{{ cesta | basename }}"
"{{ slovnik | to_nice_yaml }}"
```

### Výsledek tasku do proměnné

```yaml
- name: Zjisti, jestli existuje konfigurák
  ansible.builtin.stat:
    path: /etc/app/app.ini
  register: konfig

- name: Vypiš to
  ansible.builtin.debug:
    var: konfig.stat.exists
```

### Facts – co Ansible ví o serveru

```bash
ansible web01 -m setup                              # úplně všechno
ansible web01 -m setup -a "filter=ansible_distribution*"
```

Nejčastější: `ansible_hostname`, `ansible_os_family`, `ansible_distribution`,
`ansible_default_ipv4.address`, `inventory_hostname` (jméno z inventory).

---

## 11. Podmínky, cykly, chyby

```yaml
- name: Jen na Fedoře
  ansible.builtin.dnf:
    name: htop
    state: present
  when: ansible_distribution == "Fedora"
```

Ve `when` se **nepíšou** `{{ }}` – je to výraz už sám o sobě.

```yaml
- name: Vytvoř víc složek
  ansible.builtin.file:
    path: "/opt/app/{{ item }}"
    state: directory
    mode: '0755'
  loop:
    - data
    - logs
    - config
```

Když příkaz musíš pustit přes `command`, řekni Ansiblu, kdy je to změna a kdy chyba:

```yaml
- name: Zjisti verzi
  ansible.builtin.command: app --version
  register: verze
  changed_when: false                 # jen čtu, nikdy to není změna
  failed_when: verze.rc not in [0, 1]
```

Ošetření chyb (obdoba try / catch / finally):

```yaml
- name: Nasazení s pojistkou
  block:
    - name: Nasaď novou verzi
      ansible.builtin.command: /opt/deploy.sh
  rescue:
    - name: Vrať starou verzi
      ansible.builtin.command: /opt/rollback.sh
  always:
    - name: Ukliď
      ansible.builtin.file:
        path: /tmp/deploy
        state: absent
```

`ignore_errors: true` existuje, ale je to jako prázdný `catch { }` – chyba se
zahodí a ty nevíš, že se stala.

---

## 12. Handlery

```yaml
tasks:
  - name: Nahraj konfiguraci
    ansible.builtin.template:
      src: app.ini.j2
      dest: /etc/app/app.ini
      mode: '0600'
    notify: Restartuj aplikaci

handlers:
  - name: Restartuj aplikaci
    ansible.builtin.service:
      name: app
      state: restarted
```

- Handler se pustí **jen když je task `changed`**. Konfigurace se nezměnila → žádný restart.
- Pustí se **až na konci playe** a **jen jednou**, i kdyby ho zavolalo pět tasků.
- Jméno v `notify` musí **přesně** sedět na `name` handleru, včetně velkých písmen.
- Když task spadne dřív, než play doběhne, handler se nepustí. Soubor je změněný,
  služba nerestartovaná.

---

## 13. Šablony (Jinja2)

Soubor `templates/app.ini.j2`:

```jinja
[server]
DOMAIN = {{ app_domain }}
HTTP_PORT = {{ app_port | default(3000) }}

[database]
PASSWD = {{ vault_app_db_pass }}

{% if app_debug %}
LOG_LEVEL = debug
{% endif %}

{% for host in groups['webservery'] %}
upstream {{ host }};
{% endfor %}
```

| zápis | význam |
|---|---|
| `{{ x }}` | vypiš hodnotu |
| `{% ... %}` | logika (`if`, `for`), nic nevypisuje |
| `{# ... #}` | komentář, do výsledku se nedostane |

Postup, jak z existujícího konfiguráku udělat šablonu:

1. Zkopíruj soubor ze serveru do `templates/` a přidej `.j2`.
2. Hesla a tokeny přesuň do vaultu, v šabloně nech jen `{{ vault_... }}`.
3. Pusť playbook s `--check --diff`.
4. **Diff musí být prázdný.** Jakýkoliv rozdíl znamená, že šablona generuje něco
   jiného než originál – mezera navíc, chybějící řádek, překlep v názvu proměnné.

---

## 14. Role

Když má playbook padesát tasků, rozděl ho do rolí. Role je složka s pevnou strukturou:

```
roles/
└── nginx/
    ├── defaults/main.yml    ← výchozí proměnné (dají se snadno přebít)
    ├── vars/main.yml        ← proměnné, které se přebíjet nemají
    ├── tasks/main.yml       ← tady role začíná
    ├── handlers/main.yml
    ├── templates/           ← .j2 soubory pro modul template
    ├── files/               ← soubory pro modul copy
    └── meta/main.yml        ← závislosti na jiných rolích
```

Nic z toho není povinné kromě `tasks/main.yml`. Ansible ty soubory najde sám
podle názvu – proto v `template` stačí `src: nginx.conf.j2` bez cesty.

```bash
ansible-galaxy role init roles/nginx       # vygeneruje kostru
```

Použití:

```yaml
- name: Nastav webserver
  hosts: webservery
  become: true
  roles:
    - nginx

# nebo uprostřed tasků
  tasks:
    - name: Nasaď nginx
      ansible.builtin.include_role:
        name: nginx
```

Cizí role a kolekce:

```bash
ansible-galaxy collection install community.docker
ansible-galaxy install -r requirements.yml
```

---

## 15. Vault – hesla v gitu

```bash
ansible-vault create  inventory/group_vars/all/vault.yml
ansible-vault edit    inventory/group_vars/all/vault.yml    # tohle používej
ansible-vault view    inventory/group_vars/all/vault.yml
ansible-vault encrypt soubor.yml
ansible-vault decrypt soubor.yml                            # pozor, viz níže
ansible-vault rekey   soubor.yml                            # změna hesla
```

Zašifrovaný soubor začíná řádkem `$ANSIBLE_VAULT;1.1;AES256` a pak jsou jen čísla.

Zvyk, který se vyplatí:

```yaml
# vault.yml (zašifrované)
vault_db_password: "tajne-heslo"

# vars.yml (čitelné)
db_password: "{{ vault_db_password }}"
```

Díky prefixu `vault_` jde přes `grep` zjistit, že proměnná existuje a kde se používá,
i když soubor s hodnotou nepřečteš.

**Pravidla, která nejdou porušit:**

- Před každým commitem: `head -1 vault.yml` → musí začínat `$ANSIBLE_VAULT`.
- Nikdy `decrypt` → úprava → commit. Na `encrypt` se zapomene. Používej `edit`.
- Jednou pushnuté heslo je **prozrazené**, i když commit smažeš. Musí se změnit
  na serveru (rotace), ne jen v gitu.
- U tasků, které pracují s hesly, dej `no_log: true`, ať se nevypíšou do výstupu.

Stejné pravidlo jako u `.env` v [[Struktura src]]: tajné hodnoty do gitu nepatří –
vault je jediná výjimka, protože je zašifrovaný.

---

## 16. Tagy a become

```yaml
- name: Nahraj konfiguraci
  ansible.builtin.template:
    src: app.ini.j2
    dest: /etc/app/app.ini
    mode: '0600'
  tags: config
```

```bash
ansible-playbook web.yml --tags config
ansible-playbook web.yml --list-tags
```

Task bez tagu se při `--tags neco` **nepustí**. Když dáváš tagy, dej je všem
taskům v roli, jinak se ti půlka role přeskočí.

`become: true` = pusť to přes sudo. Jde dát na play (všechny tasky) nebo na
jeden task. `become_user: postgres` = staň se konkrétním uživatelem.

---

## 17. Když to nejde

| hláška | příčina |
|---|---|
| `UNREACHABLE! ... Permission denied (publickey)` | špatný SSH klíč nebo uživatel; zkus ručně `ssh uzivatel@host` |
| `UNREACHABLE! ... Connection timed out` | špatná IP, port, firewall, VPN |
| `Missing sudo password` | chybí `-K`, nebo uživatel nemá sudo |
| `'xyz' is undefined` | překlep v názvu proměnné, nebo je definovaná pro jinou skupinu |
| `Unsupported parameters for (modul)` | špatně odsazené `when`/`notify`/`tags`, nebo překlep v parametru |
| `mapping values are not allowed here` | chyba YAML – odsazení nebo dvojtečka v textu bez uvozovek |
| `found unquoted template expression` / `did not find expected key` | hodnota začíná `{{` a není v uvozovkách |
| `couldn't resolve module/action` | chybí kolekce (`ansible-galaxy collection install ...`) nebo překlep |
| `Could not find or access 'x.j2'` | šablona není v `templates/` role, nebo jiné jméno |
| `Attempting to decrypt but no vault secrets found` | chybí `--ask-vault-pass` nebo soubor s heslem |
| `Decryption failed` | špatné heslo k vaultu |
| handler se nepustil | task nebyl `changed`, nebo jméno v `notify` nesedí |
| task je pokaždé `changed` | `command`/`shell` bez `changed_when`, nebo šablona s časem či náhodou |

Ladicí nástroje:

```yaml
- name: Co je v té proměnné?
  ansible.builtin.debug:
    var: moje_promenna
```

```bash
ansible-playbook web.yml --syntax-check
ansible-playbook web.yml --check --diff --limit web01
ansible-playbook web.yml -vvv
ansible-inventory --host web01              # odkud se vzala ta hodnota?
```

**Čti první chybu, ne poslední** – stejně jako v [[CSharp/Časté chyby|C#]].
A u `undefined` proměnné nejdřív hledej překlep.

---

## 18. Lint

```bash
ansible-lint            # kontroluje zvyklosti Ansiblu
yamllint .              # kontroluje YAML (odsazení, mezery)
```

Co `ansible-lint` nejčastěji vytkne:

- task nemá `name`
- modul nemá celé jméno (`dnf` místo `ansible.builtin.dnf`)
- `file`/`copy`/`template` nemá `mode`
- `command`/`shell` nemá `changed_when`
- `yes`/`no` místo `true`/`false`
- mezery na konci řádku, chybějící prázdný řádek na konci souboru

---

## 19. Checklist před pushem

- [ ] `ansible-playbook ... --syntax-check` prošel
- [ ] `ansible-lint` a `yamllint .` jsou čisté
- [ ] `--check --diff` ukazuje **jen to, co jsem chtěl změnit**
- [ ] Každý task má `name`, celé jméno modulu a (u souborů) `mode`
- [ ] Žádné heslo není v šabloně, v `vars.yml` ani v `defaults`
- [ ] `head -1 vault.yml` začíná `$ANSIBLE_VAULT`
- [ ] Zkusil jsem to nejdřív na jednom hostu (`--limit`)
- [ ] Druhé spuštění hlásí `changed=0`

---

## 20. Jak se to naučit

1. `ansible localhost -m ping` – ověř, že to běží.
2. Napiš playbook na `hosts: localhost` s `connection: local`, který vytvoří složku
   a v ní soubor. Pusť ho dvakrát a sleduj `changed` → `ok`.
3. Udělej ze souboru šablonu s jednou proměnnou. Změň proměnnou, pusť `--check --diff`.
4. Přidej handler, který vypíše zprávu přes `debug`. Zjisti, kdy se pustí a kdy ne.
5. Přesuň to celé do role (`ansible-galaxy role init`).
6. Přidej `vault.yml` s jedním „heslem" a použij ho v šabloně.
7. Teprve potom to pusť na opravdový server – virtuálku nebo kontejner, ne produkci.

```yaml
# cviceni.yml – kostra pro kroky 2 a 3
---
- name: Cvičení na vlastním počítači
  hosts: localhost
  connection: local
  vars:
    pozdrav: ahoj
  tasks:
    - name: Vytvoř složku
      ansible.builtin.file:
        path: /tmp/ansible-cviceni
        state: directory
        mode: '0755'

    - name: Vytvoř soubor
      ansible.builtin.copy:
        content: "{{ pozdrav }}\n"
        dest: /tmp/ansible-cviceni/pozdrav.txt
        mode: '0644'
```

```bash
ansible-playbook cviceni.yml --check --diff
ansible-playbook cviceni.yml
ansible-playbook cviceni.yml          # podruhé: changed=0
```

---

## Co si odnést

1. Popisuješ **stav**, ne kroky. Druhé spuštění nesmí nic měnit.
2. Hierarchie je playbook → play → task → modul; role je jen úklid.
3. **`--check --diff` před každým opravdovým spuštěním.**
4. Odsazení rozhoduje: parametry modulu pod modul, `when`/`notify`/`tags` vedle něj.
5. Hodnota začínající `{{` patří do uvozovek, `mode` taky.
6. `command` a `shell` až když na to není modul.
7. Hesla jen do vaultu a před commitem zkontrolovat, že je zašifrovaný.
8. Nevíš, co modul umí? `ansible-doc jmeno.modulu`.

---

Související: [[Spouštění a testování]] · [[Struktura src]] · [[CSharp/Časté chyby|Časté chyby (C#)]]
