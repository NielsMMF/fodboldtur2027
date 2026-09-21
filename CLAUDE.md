# Fodboldtur 2027 — projektkontekst

Statisk hjemmeside til en fodboldtur for 7–12 mand til **Budapest 16.–18. april 2027**.
Siden er gruppens fælles arbejdspapir: program, budget, betalingsfrister og noter.

Udgivet via GitHub Pages fra `main`: <https://nielsmmf.github.io/fodboldtur2027/>

---

## Sådan er sitet bygget

Rent HTML, CSS og vanilla JS. Ingen build, ingen dependencies, ingen framework.
Alt kan åbnes direkte i en browser.

- **`index.html`** er skallen. Den indeholder **al CSS**, menuen, routingen og footeren.
  De øvrige `.html`-filer er **fragmenter** — de har hverken `<html>`, `<head>` eller
  egen CSS, kun et `<div class="section">` med indhold.
- Routing sker med hash: `vis(side)` skjuler alle paneler, viser `#panel-<side>`,
  skriver `#<side>` i adressen og `fetch()`er `<side>.html` ind i panelet første gang.
  `hashchange` kalder samme funktion, så `index.html#kamp` virker som direkte link.
- Fordi indholdet hentes med `fetch()`, **skal siden serveres over HTTP** for at virke.
  Lokalt: `python3 -m http.server 8000` og åbn `http://localhost:8000/`.
  `file://` giver CORS-fejl og tomme paneler.

### Sider og menuen

To arrays i bunden af `index.html` styrer alt:

```js
const SYNLIG = [ ... ];   // har en knap i menuen
const SKJULT = [ ... ];   // ingen knap, men stadig tilgængelig via #hash
```

Menuen og panelerne bygges ud fra listerne. **Vil du genskabe en skjult side, flytter
du én linje fra `SKJULT` op i `SYNLIG` — det er alt.** Filerne er urørte og virker
med det samme.

| Fil | Menupunkt | Indhold |
|---|---|---|
| `oversigt.html` | 📋 Oversigt | Forsiden: overordnet program + praktisk info |
| `praktisk.html` | ⚠️ Vigtig info | Bindende tilmelding, rater, frister |
| `skydebane.html` | 🎯 Skydebane | To baner at vælge imellem på dagen |
| `donau.html` | 🛥️ Donau | Privat charter på Donau |
| `kamp.html` | ⚽ Kampen | Ferencváros–Újpest, kampdagens forløb |
| `fankort.html` | 🪪 Fankort | Guide til Fradi-fankort og VIP-vejen |
| `barer.html` | 🍺 Barer | Barvalg pr. aften, fire retninger at vælge imellem |
| `alternativer.html` | 🔄 Alternativer | **Erstatninger** for planlagte punkter, ikke ekstra punkter |
| `budget.html` | 💶 Budget | Alle poster i kroner |
| `rejsebureau.html` | 🧳 Rejsebureau | **Interne** arbejdsnoter — rejsebureauet er os selv |
| `hamborg.html` | *(skjult)* | Fravalgt by · `index.html#hamborg` |
| `paris.html` | *(skjult)* | Fravalgt by · `index.html#paris` |
| `sammenlign.html` | *(skjult)* | Byvalg og budgetter for alle tre byer · `index.html#sammenlign` |

### Forsiden som indgang

`oversigt.html` er den overordnede tidslinje. Punkter med en detaljeside er
`<button class="itin-row is-key" onclick="vis('...')">` og har en `Detaljer →`-markør.
Punkter uden mere at sige er en almindelig `<div class="itin-row">` — **der laves ikke
en detaljeside, hvis der ikke er noget at uddybe.**

### CSS-komponenter

Genbrug det, der er, frem for at skrive nyt. De vigtigste klasser:
`.section` · `.city-banner` · `.facts`/`.fact` · `.sec-title` · `.lead` ·
`.alert` + `.alert-ok`/`.alert-bud` · `.itin-day`/`.itin-row`/`.itin-time`/`.itin-more` ·
`.events`/`.event` + `.ev-active`/`.ev-match`/`.ev-night` · `.badge-*` ·
`.option-cards`/`.option-card`/`.option-pros-cons` · `.tips`/`.tip` ·
`.pay-step`/`.paybox` · `.back`.

Bemærk i `.option-pros-cons`: **kun** `> div > strong:first-child` er overskriften
(block, uppercase). Almindelige `<strong>` inde i teksten skal blive ved med at være
inline — det har været brækket før.

---

## Konventioner

- **Alt læservendt er på dansk.** Også commit-beskeder og PR-tekster.
- **Alle priser i danske kroner.** Kurs **EUR→DKK 7,5**; HUF→DKK ≈ 0,01875.
  Enkeltposter rundes til nærmeste 5 kr, totaler **op** til nærmeste 50 kr.
  Står der en original pris (`2.000 Ft`), sættes kronebeløbet i parentes efter.
- **15 % buffer** oven på budgettet, rundet op.
- **Én PR pr. ændring.** Eget branchnavn, PR mod `main`, aldrig stakkede PR'er —
  det er gået galt før (en PR blev merget ind i en anden feature-branch i stedet for
  `main`, og to sider blev strandet på en død branch).
- **Ingen proceskommentarer på læservendte sider.** Ingen "byen er valgt",
  "opdateret d. …" eller andre spor af, hvordan planen blev til. Den slags hører til
  i `rejsebureau.html`, som er interne noter.
- **Ret tal alle steder på én gang.** Samme beløb står typisk i `oversigt.html`,
  `budget.html`, `praktisk.html` og på detaljesiden. En opsummeringstabel er blevet
  glemt før.
- **Fællesudgifter deles på 11.** Lejligheden (2.250–3.750 kr pr. nat i alt) og
  bådcharteren (~3.375 kr for båden) er de eneste poster, hvor antallet indgår.
  Ændrer antallet sig, er det dem — og kun dem — der skal regnes om.
- **De skjulte bysider regnes ikke om.** `hamborg.html`, `paris.html` og
  `sammenlign.html` står med tallene fra dengang (gruppe på 10), fordi det er den
  sammenligning, beslutningen blev truffet på. `sammenlign.html` siger det selv.
- **Tjek adresser og priser mod to kilder.** Flere venue-adresser kom forkerte hjem
  fra første søgning. Skriv ikke detaljer, du ikke har verificeret — det er blevet
  fanget flere gange.
- **Verificér i browseren, ikke på fornemmelse.** Playwright er tilgængeligt:
  `NODE_PATH=/opt/node22/lib/node_modules node <script>`, Chromium ligger i
  `/opt/pw-browsers/chromium`. Påstå ikke, at siden er klikket igennem, hvis den ikke er.

### Privatliv — vigtigt

**Repoet er offentligt, og sitet er live.** MobilePay-nummer og mailadresse til
tilmelding er med vilje **flyttet væk fra sitet** og slået op i den private
Facebook-gruppe i stedet. `praktisk.html` skriver "står i Facebook-gruppen" og
forklarer hvorfor.

**Læg ikke telefonnumre, MobilePay-numre eller private mailadresser ind i repoet igen** —
heller ikke i commit-beskeder eller PR-tekster. Offentlige virksomhedsadresser
(restauranter, klubben, billetkontoret) er fine.

---

## Beslutninger, der ligger fast

- **Budapest** er valgt. Hamborg og Paris er fravalgt, men ligger skjult i repoet.
- **Ferencváros–Újpest, lørdag 17. april 2027, 29. runde**, Groupama Aréna —
  Budapest-derbyet. Passer præcis til lørdagen i programmet.
- **Kampstart er ikke fastlagt.** De 18:00 i programmet er et gæt; derbyer placeres
  efter tv-hensyn. Bliver det eftermiddagskamp, rykker både Donau-cruise og optakt.
- **Vi er 11.** Alle 11 har betalt 1. rate og sendt rejseoplysninger; tilmeldingen
  er lukket. Fællesudgifter (lejlighed, bådcharter) deles på 11 — ikke på 10.
- **To rater à 2.000 kr**, i alt 4.000 kr:
  1. **13. september 2026** — flybilletten. **Betalt af alle 11.**
  2. **6. december 2026** — lejligheden plus buffer. Næste og sidste frist.
  Raterne dækker **kun** fly og lejlighed. Fly + lejlighed er estimeret til
  1.310–2.180 kr pr. person, så der er luft; **overskuddet betales tilbage**.
- **Aktiviteterne kan formentlig tages af rate-pengene** (skydebane + cruise =
  680–980 kr). Skal besluttes, inden der bookes.
- **Tilmelding = penge og rejseoplysninger samtidig.** Mangler den ene, tæller man
  ikke som tilmeldt.
- **Egenbetaling undervejs:** ca. **710–1.390 kr** til mad, drikke, kampbillet og
  transport (uændret af antallet — ingen af de poster deles). Samlet turpris
  **3.150–5.250 kr** pr. person — **det tal indeholder de 4.000 kr**, de skal
  ikke lægges oveni. (Den fejl er lavet én gang: bufferen blev
  talt med to gange og gjorde egenbetalingen 430–755 kr for høj.)
- **Skydebanen holdes som to muligheder**, der vælges på dagen — ikke booket på forhånd.
- **Ølcykel er droppet**: forbudt i distrikt VI og VII. Står som afvist med
  begrundelse i `alternativer.html`.

---

## Forbehold, der let bliver glemt

- **Fankortet kan ikke laves hjemmefra.** Registreringen er biometrisk og foregår
  udelukkende personligt på Groupama Aréna. Der findes ingen onlineløsning.
  (Det er tidligere blevet skrevet forkert på siden — ret det ikke tilbage.)
  VIP-billetter (Telekom, VIP Gold, VVK) kræver til gengæld intet kort og kan
  købes hjemmefra.
- **Kilderne er uenige om udlændinge.** MLSZ skriver, at klubkortkravet *ikke*
  gælder udenlandske statsborgere; Ferencváros' egne sider skriver, at alle fra 6 år
  skal have kort. Siden følger den **strengeste** udlægning. Klubbens svar på mailen
  til `jegypenztar@fradi.hu` afgør sagen.
- **Kampdatoen er læst i kampprogrammet, ikke bekræftet hos klubben.** Det lykkedes
  ikke at nå en primær kilde (nb1.hu, fradi.hu, m4sport.hu var alle blokeret fra
  dette miljø). Forbeholdet står på siden.
- **Billetkontorets åbningstid** er **tir–fre 10:00–18:00 og lør 09:00–13:00 —
  søndag og mandag lukket.** (Stod tidligere som "man–fre" og er rettet; skriv det
  ikke tilbage.) Fredag eftermiddag er derfor det eneste reelle vindue til fankort.
- **A/B-fankort er ikke beskrevet.** Type A giver adgang til alle sektioner,
  type B kun til B1/B2/B3/C1 (ultra-sektionen). Mangler på `fankort.html`.
- **Priser er estimater.** Flypriserne for april 2027 er ikke lagt ud endnu;
  900–1.500 kr er fundet ved at søge tilsvarende datoer tidligere på året.

---

## Åbne spørgsmål

1. **Hvem lægger ud** for lejlighed, charter og kampbilletter, og hvordan afregnes
   det? Mest presserende, nu 1. rate er i hus og flyet skal købes.
2. **Er kampen en betingelse for turen,** hvis vi ikke kan få billetter til derbyet?
3. **Kampstart** — afventer tv-programlægning.
4. **Aktiviteterne** — som planlagt, eller et af alternativerne? Skal afgøres,
   inden der bookes i januar.
5. **Sengepladser til 11** — førstevalget har 5 soveværelser. Tjek det faktiske
   antal senge frem for udlejerens kapacitetstal.

*Antallet er ikke længere et åbent spørgsmål: vi er 11.*

---

## Arbejdsgang

1. Branch fra `main`, ét emne pr. branch.
2. Ret fragmentet (og `index.html`, hvis der skal en ny side i menuen).
3. Server lokalt og klik igennem, inkl. mobilbredde.
4. Commit på dansk, push, PR mod `main`.
5. GitHub Pages bygger selv fra `main`, når PR'en er merget — der skal intet gøres
   manuelt bagefter.
