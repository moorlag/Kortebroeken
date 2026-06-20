# Botty — plan & roadmap

Dit document legt de plannen voor Botty en het Botty-verse vast en houdt ze bij.
Werk het bij zodra een feature af is (verplaats naar "Gereleased") of zodra een
ontwerpkeuze verandert.

Onderdelen:
- **`botty.html`** — de Tamagotchi: verzorg één Botty, met automatiseringsniveaus (0–5) en Uitlegbare AI.
- **`bottyverse.html`** — de Singularity: 9 Botty's die volledig automatisch verzorgd worden en onderling op bezoek gaan.
- **`kaarten.html`** — Pokémon-achtige verzamelkaarten met Botty in de hoofdrol.
- **`index.html`** — de kortebroekenmeter (startpagina).

---

## In ontwerp — Verveling & dynamische hive (vervolgversie)

Twee nieuwe processen die samen één idee vormen: de hive wordt een *levende, ademende*
populatie in plaats van een vaste 9, en de keerzijde van het optimaliseren wordt zichtbaar
als een groeiende lijst van Botty's die zijn vertrokken.

> **Thema-check (waarom dit past en het verhaal verdiept).** De kern van de verse is:
> de AI verzorgt perfect en kweekt op functie, maar de *menselijke band blijft 0%* — perfect
> geoptimaliseerd, perfect verzorgd, maar er gaat iets verloren. Verveling is precies datzelfde
> gat, nu mechanisch gemaakt: de AI kan `energie`/`data`/`fit` bijladen, maar **voldoening/nieuwheid
> kan ze niet fabriceren** — net zomin als de band. En omdat de AI per tick maar de 3 meest
> behoeftige Botty's haalt, kan ze bij een groeiende hive simpelweg **niet snel genoeg** iedereen
> blijven prikkelen. Wie te lang aan zijn lot wordt overgelaten verveelt zich en vertrekt. De
> "AI is niet snel genoeg"-gedachte is dan geen handwave maar volgt uit de bestaande 3-per-tick-limiet.
> Belangrijk voor de toon: het is **geen doodgaan**. Botty's leven voor altijd en "gaan iets anders
> doen" — de vertrokken-lijst is een *alumni-muur*, geen kerkhof.

### Proces 1: Verveling → vertrek (dynamische hoeveelheid)
- **Nieuwe meter `voldoening` (0–100).** Anders dan de andere meters lekt deze langzaam leeg en kan
  de AI hem maar *beperkt* bijvullen (een kleine "entertainment"-boost in `zorg`, met een plafond).
  Hij vult vooral via **nieuwheid**: een bezoekje (sociale prikkel), een geboorte in de buurt, of
  het opduiken van een mutatie. Dat de AI hem niet op commando kan vullen, ís het punt.
- **Health bars aanpassen.** `voldoening` komt als extra balk op de kaart (eigen kleur), zodat je
  ziet aankomen wie gaat vertrekken. Te overwegen: hem als *verveling* (vult op) tonen i.p.v.
  voldoening (lekt leeg), maar dat mengt vul- en leegloop-balken — voorstel houdt het op `voldoening`.
- **Vertrek-trigger.** Zakt `voldoening` lang genoeg onder een drempel (en is de Botty al een tijdje
  in de hive, via `leeftijd`), dan verveelt hij zich → `afscheid()` (de bestaande 👋-zwaai + `.weg`)
  → hij verdwijnt uit `bottys` en komt op de vertrokken-lijst.
- **Zelfregulerend.** Meer Botty's → minder aandacht per Botty (3-per-tick) → `voldoening` daalt
  gemiddeld sneller → meer vertrek. De populatie zoekt vanzelf een evenwicht tussen kweek en vertrek.
- **Kweek wordt netto +1.** Nu vervangt een geboorte de ouder (netto 0). Voor een dynamische hive
  laten we de ouders staan en *voegen* we het kind toe; **vertrek loopt voortaan alleen via verveling**.
  De mooie afscheids-animatie verhuist dus van "ouder bij geboorte" naar "verveelde Botty".
- **Grenzen.** Ondergrens (~4–6: hive raakt nooit leeg; bij weinig Botty's remt vertrek / versnelt kweek)
  en bovengrens (~12–16: grid/performance; daarboven intensiveert verveling / pauzeert kweek).
- **Flavor "iets anders gaan doen".** Bij vertrek een licht, niet-fataal bestemmingszinnetje
  ("begon een moestuin 🌱", "werd straat-dj 🎧", "opent een datacafé ☕"). Houdt de toon luchtig.

### Proces 2: Vertrokken-lijst (automatisch aanvullende tabel)
- **Onderaan de verse** een tabel die zichzelf vult zodra een Botty vertrekt.
- **Kolommen:** volgnummer, mini-foto (sprite), naam, generatie, stats (🧬 datakwaliteit, ⚙️ efficiëntie,
  evt. eindmeters), mutaties, en het "ging … doen"-zinnetje.
- **Mini-foto:** hergebruik de sprite-render (kleur/genen/mutaties) op klein formaat — een statische SVG-snapshot.
- **Opslag:** array `vertrokken[]` mee in `bewaar()` (lengte cappen, bijv. laatste ~50, met een totaalteller).
- **Waarom het past:** dit is het *kostenboekhoudkundige tegenwicht* van de genoom-KPI's. Terwijl de
  hive "beter" wordt (stijgende datakwaliteit/efficiëntie), groeit eronder zichtbaar de lijst van wie
  er onderweg verloren ging. De prijs van optimaliseren wordt letterlijk afleesbaar.

### Nog open (te beslissen vóór de bouwfase)
- Meter als `voldoening` (leegloop) of `verveling` (oploop)? Voorstel: `voldoening`.
- Refill-bronnen en -tempo van `voldoening`; hoe klein de AI-boost is (plafond).
- Vertrekdrempel + hoe lang eronder + minimale `leeftijd` vóór vertrek mogelijk is.
- Onder-/bovengrens van de populatie en wat er op de grenzen gebeurt (kweek/vertrek temperen).
- Blijft de "ouder vertrekt bij geboorte" bestaan, of wordt kweek puur netto +1? Voorstel: netto +1.
- Tabel: alle vertrokkenen of laatste N? Sortering (nieuwste boven?) en mobiel-layout.
- Wel/geen "iets anders gaan doen"-flavor, en de lijst bestemmingen.

---

## Gereleased (uitgewerkt) — Voortplanting in de verse

De Botty's in de verse planten zich voort. De AI bepaalt zelf welke twee
Botty's matchen, er verschijnt een ei, en de eerste ouder vertrekt met een
afscheidszwaai. Bouwt voort op het bestaande bezoek-mechaniek (de IR-lichtstraal)
en is een knipoog naar het "paren via infrarood" uit Sprite_tm's Tamagotchi Singularity.
*(Gebouwd; details + afgewezen alternatieven hieronder bewaard. NB: de "ouder vertrekt bij
geboorte" wordt mogelijk herzien — zie "Verveling & dynamische hive".)*

### Nieuwe variabele: `datakwaliteit` (0–100, erfelijk)
- Sluit aan op het datathema uit Botty (duurzame vs. vervuilde data): hoe "schoon/duurzaam"
  het bouwmateriaal van een Botty is.
- **Effect:** hoge `datakwaliteit` → de meters zakken langzamer (verval × een factor
  afhankelijk van `datakwaliteit`). "Betere genen" is dus een echt voordeel, waardoor
  de AI-optimalisatie betekenis krijgt.
- Startpopulatie krijgt een gespreide waarde (bijv. `rnd(40,70)`), zodat er ruimte is
  om te "verbeteren" over generaties.
- Zichtbaar als klein badge op de kaart, zodat je het gemiddelde over generaties ziet klimmen.

### Tweede variabele: `efficiëntie` (0–100, erfelijk) — tweede kweekfactor
Bepaalt hoe goed een Botty **data omzet in energie**. Een tweede erfelijke as naast
`datakwaliteit`, dus de hive heeft nu een klein "genoom" van twee genen.
- **Voeren:** efficiënte Botty's halen méér energie uit dezelfde portie data
  (`energie += dataPortie × (1 + efficiëntie-bonus)`).
- **Bewegen:** kost energie; efficiënte Botty's hebben **minder energie nodig per beweging**
  (`energiekost = basis × (1 − efficiëntie-korting)`).
- **Fenotype → spiermassa:** de winst zet zich om in **grotere spiermassa**. Maak de
  bestaande `.spier` op de sprite gradueel (schaalbaar) i.p.v. binair, zodat je het gen
  letterlijk ziet: een efficiënte Botty is zichtbaar gespierder. Zo wordt genotype → fenotype tastbaar.
- **Netto fokvoordeel:** efficiënte Botty's bewegen goedkoper, bouwen meer spiermassa op en
  zijn goedkoper te onderhouden — dus een echte reden voor de AI om erop te selecteren.

### Derde variabele: `uiterlijk` (erfelijk, NIET geselecteerd) — zichtbare afstamming
Anders dan de twee kweekfactoren stuurt de AI hier **niet** op; uiterlijk erft alleen over.
Juist daardoor wordt de boodschap zichtbaar: omdat steeds dezelfde functionele elite paart,
vervaagt het uiterlijk vanzelf naar een gemiddelde — diversiteit sterft af als *bijproduct*
van het optimaliseren op functie. De AI heeft het nooit "besloten"; het is collaterale schade.
- **Kleur:** het kind krijgt een **mengkleur** van beide ouders (per kanaal het gemiddelde van
  `broek` / `scherm` / `accent` / …). Vereist dat een Botty een palet-*object* draagt i.p.v.
  een vaste index, zodat nieuwe tussenkleuren kunnen ontstaan. Startpopulatie: de 7 bestaande paletten.
- **Compositie:** een setje erfelijke vormgenen (bijv. antenne-lengte, oor-grootte, oog-stijl,
  scherm-/lichaamsvorm). Elk gen komt van één ouder (of gemengd waar numeriek), zodat het kind
  een herkenbare combinatie van beide ouders is.
- **Effect:** puur visueel — geen invloed op verval of energie. Maakt afstamming én het verlies
  van diversiteit tastbaar: je ziet de hive letterlijk naar één look toe kruipen.

### Vierde dimensie: `mutaties` (willekeurig, gemengd effect) — de tegenkracht
De eerste drie dimensies duwen de hive allemaal naar **uniformiteit** (selectie + mengen eroderen
variatie). Mutatie is de enige kracht die de andere kant op werkt: de enige bron van écht nieuwe
variatie. Samen vormen ze de complete evolutie-lus — *selectie + overerving eroderen, mutatie injecteert*.
- **Discreet en willekeurig:** anders dan de continue genen verschijnt een mutatie bij de geboorte
  (uitkomen van het ei) met een kleine kans (≈3–5%) en zit hij in *geen van beide ouders* — het is nieuw.
- **Erft verder over:** zodra een mutatie bestaat, geeft de drager hem door. Een *nuttige* mutatie
  wordt dus opgepikt door de gulzige AI en verspreidt zich door de hive — precies hoe aanpassing werkt.
- **Gemengd effect (gekozen):** de meeste mutaties zijn neutraal/cosmetisch (zoals in de natuur) en
  driften willekeurig; héél af en toe is er een functionele die zich via selectie door de hive verspreidt.
- **Voorbeelden** (haken in de bestaande mechaniek):
  - *Wifi-module* → versterkt het kennis-delen bij bezoekjes (deelt verder/sneller data). [functioneel]
  - *Zonnepaneel* → genereert eigen energie, minder afhankelijk van voeren. [functioneel]
  - *Extra antenne* → betere "ontvangst", leert sneller van een bezoek. [functioneel]
  - *Tweede scherm / extra oog* → cosmetisch (of licht: ziet meer data). [meestal cosmetisch]
  - *Quantum-chip* → zeldzame "legendarische" mutatie met groot effect.
- **Educatief:** je ziet meestal nutteloze rariteiten opduiken en verdwijnen, en zelden iets dat de
  hele hive verovert. Mutatie houdt de hive ook visueel verrassend, tegen de homogenisatie in.

### Matching (de AI optimaliseert) — gulzige optimizer
**Gekozen filosofie:** elitaire, gulzige selectie op één eigenschap. De AI kiest
niet echt een *koppel* dat bij elkaar past, maar simpelweg de twee beste individuen.
Dat is bewust: het laat zien wat er mísgaat als een AI blind op één getal optimaliseert.

1. **Poort** — alleen rijpe kandidaten: `stage === "volwassen" && !ziek && gem(b) >= 75`.
2. De AI scoort elk paar op de som van **beide** genen:
   `score = (a.datakwaliteit + a.efficiëntie) + (b.datakwaliteit + b.efficiëntie)` en kiest
   het hoogste (`kiesPaar`). Nog steeds gulzig/elitair, maar nu over twee assen — de hive
   convergeert naar Botty's die én lang meegaan (datakwaliteit) én zuinig + gespierd zijn (efficiëntie).
3. Gebeurt zeldzaam, in de geest van de bestaande `planBezoek`-loop.

> Open: weging van de twee genen in de score (nu gelijk; eventueel één gen zwaarder laten meetellen).

**Overwogen en (voorlopig) afgewezen alternatieven** — bewaard zodat we ze niet opnieuw hoeven uit te zoeken:
- *Diversiteitswacht* — straf op gelijk `palet`, zodat de hive niet homogeniseert.
  Afgewezen: zwakt juist de boodschap af die we willen laten zien.
- *Verwantschapswacht* — naaste familie (ouder–kind, broer/zus) niet laten matchen
  via afstammingsregistratie. Afgewezen voor nu; past bij de diversiteitswacht.
- *Kansgewogen selectie (roulettewiel)* — betere Botty's meer kans i.p.v. altijd de
  absolute top-2, zoals echte genetische algoritmes. Afgewezen: behoudt te veel variatie
  voor het verhaal dat we willen vertellen.

> Deze alternatieven vertellen het "verstandige selector"-verhaal i.p.v. de "waarschuwende
> optimizer". Bewaard als mogelijke variant/uitbreiding (bv. een toggle die beide laat zien).

### Het kind
- `stage: "baby"`, `generatie: max(ouders)+1`, meters ~80.
- **Twee kweekgenen** erven apart over, elk met eigen mutatie (licht positief gebiast, bijv. −4..+6):
  - `datakwaliteit = (a + b)/2 + mutatie`
  - `efficiëntie   = (a + b)/2 + mutatie`
- **Uiterlijk** erft over (niet geselecteerd):
  - kleur = mengkleur van beide ouders (per kanaal het gemiddelde), met eventueel lichte mutatie
  - compositie = elk vormgen van één van de ouders
- **Mutatie:** bij het uitkomen ≈3–5% kans op een willekeurig nieuw kenmerk dat in geen van beide
  ouders zat (zie "Vierde dimensie"); erft daarna verder over.

### Flow (visueel)
- Beam tussen het paar (hergebruik `trekBeam`).
- Een **ei** verschijnt op de plek van de vertrekkende ouder.
- Ouder A zwaait gedag (`hop` + nieuwe afscheids-animatie) en verdwijnt → aantal blijft stabiel.
- Het ei komt na een tijdje uit als het kind.

### Educatieve boodschap
Beide kweekgenen (datakwaliteit én efficiëntie) klimmen zichtbaar — de Botty's gaan steeds
langer mee én worden steeds gespierder/zuiniger. En omdat steeds dezelfde elite paart, mengen
hun **kleuren en composities** zich generatie na generatie naar een gemiddelde: je ziet de hive
letterlijk naar één look toe kruipen. De hive wordt "beter", maar ook steeds uniformer, de
diversiteit sterft af — en de menselijke band blijft 0%. Het mooie/wrange: de AI optimaliseerde
nooit op uiterlijk; die diversiteit ging verloren als bijproduct van optimaliseren op functie.
Mutatie is de enige tegenkracht: af en toe duikt er iets nieuws op, en zelden verovert dat de hele
hive. Zo zie je de volledige evolutie-spanning — eroderen vs. injecteren — in actie.
Perfect geoptimaliseerd, perfect verzorgd, maar er gaat iets verloren.

### Nog open (te beslissen in de bouwfase)
- Exacte matchfrequentie / cooldown.
- Groeit de hive of blijft hij op 9 (1 ouder vertrekt per geboorte = stabiel)?
- Uitkomsttijd van het ei.
- Mutatiebreedte en of de mutatie positief gebiast is.
- Weging van de twee genen in de matchscore (gelijk of één zwaarder).
- Precieze formules/constanten voor de efficiëntie-bonus (voeren), -korting (bewegen) en de
  schaal van de zichtbare spiermassa.
- Of de efficiëntie/energie-economie ook al in de **huidige** verse gaat draaien, of pas
  meekomt met de voortplanting-update.
- **Sprite-refactor:** palet van een vaste index → een palet-*object* per Botty, zodat mengkleuren mogelijk zijn.
- Welke compositie-/vormgenen we erfelijk maken (antenne, oren, ogen, scherm-/lichaamsvorm) en hoe ver ze variëren.
- Hoeveel kleurmutatie we toestaan (puur middelen = snelle convergentie; iets mutatie = wat meer leven).
- Mutatie-kans bij geboorte (≈3–5%) en de complete lijst mutaties + hun (cosmetische/functionele) effecten.
- Hoe we een verschenen mutatie als sprite-onderdeel tekenen en erfelijk doorgeven.

---

## Ideeën / backlog
- **Uitlegbare AI verdiepen** — bijv. een logboek of tijdlijn van AI-beslissingen in `botty.html`.

---

## Gereleased
- **Molenaar-niveaus (0–5)** correct gemapt in `botty.html` (Signalering en Advisering gesplitst).
- **Botty-verse zorgbalans** — AI verzorgt de 3 meest behoeftige Botty's per tick.
- **Verzamelkaarten** (`kaarten.html`) — Pokémon-achtige TCG-kaarten met holografisch effect.
- **3×3 hive met onderlinge bezoekjes** — 9 Botty's, IR-lichtstraal, kennis delen (geen band).
- **Uitlegbare AI** (`botty.html`) — toggle die in de tekstballon laat zien waarom de AI handelt.
- **AI-uitleg in de verse** — verwijst naar de automatiseringsniveaus en de Uitlegbare AI-feature.
- **Voortplanting in de verse** — gulzige AI-matching op vier erfelijke dimensies (datakwaliteit,
  efficiëntie, uiterlijk/mengkleur, mutaties); ei-flow met afscheidszwaai. Zie het detailblok hierboven.
- **Tijdversneller** — ⏸/1×/4×/16×/60× met één geünificeerde `motor()`-loop; veroudering via
  `leeftijd`-accumulator; stille datamodus boven 4× (animaties uit voor soepele rendering).
