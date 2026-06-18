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

### Matching (de AI optimaliseert)
1. **Poort** — alleen rijpe kandidaten: `stage === "volwassen" && !ziek && gem(b) >= 75`.
2. De AI scoort elk paar op `a.datakwaliteit + b.datakwaliteit` en kiest het hoogste (`kiesPaar`).
3. Gebeurt zeldzaam, in de geest van de bestaande `planBezoek`-loop.

### Het kind
- `stage: "baby"`, palet van één ouder, `generatie: max(ouders)+1`, meters ~80.
- `datakwaliteit = (a + b)/2 + mutatie` (licht positief gebiast, bijv. −4..+6).

### Flow (visueel)
- Beam tussen het paar (hergebruik `trekBeam`).
- Een **ei** verschijnt op de plek van de vertrekkende ouder.
- Ouder A zwaait gedag (`hop` + nieuwe afscheids-animatie) en verdwijnt → aantal blijft stabiel.
- Het ei komt na een tijdje uit als het kind.

### Educatieve boodschap
De gemiddelde datakwaliteit klimt zichtbaar, de hive wordt "beter" — maar ook steeds
uniformer (paletten convergeren), de diversiteit daalt, en de menselijke band blijft 0%.
Perfect geoptimaliseerd, perfect verzorgd, maar er gaat iets verloren.

### Nog open (te beslissen in de bouwfase)
- Exacte matchfrequentie / cooldown.
- Groeit de hive of blijft hij op 9 (1 ouder vertrekt per geboorte = stabiel)?
- Uitkomsttijd van het ei.
- Mutatiebreedte en of de mutatie positief gebiast is.

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
