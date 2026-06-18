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

## In ontwerp — Voortplanting in de verse (vervolgversie)

De Botty's in de verse kunnen zich voortplanten. De AI bepaalt zelf welke twee
Botty's matchen, er verschijnt een ei, en de eerste ouder vertrekt met een
afscheidszwaai. Bouwt voort op het bestaande bezoek-mechaniek (de IR-lichtstraal)
en is een knipoog naar het "paren via infrarood" uit Sprite_tm's Tamagotchi Singularity.

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
- `stage: "baby"`, palet van één ouder, `generatie: max(ouders)+1`, meters ~80.
- Beide genen erven apart over, elk met eigen mutatie (licht positief gebiast, bijv. −4..+6):
  - `datakwaliteit = (a + b)/2 + mutatie`
  - `efficiëntie   = (a + b)/2 + mutatie`

### Flow (visueel)
- Beam tussen het paar (hergebruik `trekBeam`).
- Een **ei** verschijnt op de plek van de vertrekkende ouder.
- Ouder A zwaait gedag (`hop` + nieuwe afscheids-animatie) en verdwijnt → aantal blijft stabiel.
- Het ei komt na een tijdje uit als het kind.

### Educatieve boodschap
Beide genen (datakwaliteit én efficiëntie) klimmen zichtbaar — de Botty's gaan steeds
langer mee én worden steeds gespierder/zuiniger. De hive wordt "beter", maar ook steeds
uniformer (paletten convergeren, iedereen even buff), de diversiteit daalt, en de
menselijke band blijft 0%. Perfect geoptimaliseerd, perfect verzorgd, maar er gaat iets verloren.

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
