# Fodboldtur 2027 — projektkontekst

Statisk hjemmeside til en fodboldtur for **11 mand** til **Budapest 16.–18. april 2027**.
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
| `oversigt.html` | 📋 Program | Forsiden: **åbent** program + praktisk info |
| `praktisk.html` | ⚠️ Vigtig info | Bindende tilmelding, rater, frister |
| `kamp.html` | ⚽ Kampen | Ferencváros–Újpest, kampdagens forløb |
| `billetter.html` | 🎟️ Billetter | VIP-planen, og hvorfor fankortet er droppet |
| `aktiviteter.html` | 🎯 Aktiviteter | **Alle** aktiviteter, grupperet efter tid/besvær |
| `barer.html` | 🍺 Barer | Barvalg pr. aften, fire retninger at vælge imellem |
| `spisesteder.html` | 🍖 Spisesteder | Restauranter grupperet efter prisleje |
| `budget.html` | 💶 Budget | Faste poster og valgfrie aktiviteter hver for sig |
| `rejsebureau.html` | 🧳 Rejsebureau | **Interne** arbejdsnoter — rejsebureauet er os selv |
| `hamborg.html` | *(skjult)* | Fravalgt by · `index.html#hamborg` |
| `paris.html` | *(skjult)* | Fravalgt by · `index.html#paris` |
| `sammenlign.html` | *(skjult)* | Byvalg og budgetter for alle tre byer · `index.html#sammenlign` |

### Det åbne program

Forsiden er **rammer, ikke aftaler** — kun kampen ligger fast. Huller, hvor der er
tid til en aktivitet, markeres med `class="itin-row is-open"` (grøn, stiplet kant)
plus et `<span class="open-label">`, og de linker til `aktiviteter.html`.
**Lav dem ikke om til faste punkter** uden at det er besluttet.

### Mærkater på kort

`aktiviteter.html` og `spisesteder.html` bruger `.meters`/`.meter` til at vise tid,
besvær og pris. Skalaerne står på siderne selv og skal holdes ens:
tid er **inkl. transport**, besvær er `meter-let`/`meter-middel`/`meter-tung`,
og pris er 💰 / 💰💰 / 💰💰💰 — med **forskellige grænser** på de to sider
(aktiviteter: 150/400 kr · spisesteder: op til 75 / 75–150 / over 150 kr pr. hovedret).
**Tjek grænserne, hvis kursen ændres** — et beløb kan skifte kategori.

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
- **Alle priser i danske kroner.** Kurs **EUR→DKK 7,5**; **HUF→DKK = 0,021**,
  altså ca. **48 Ft pr. krone** (tommelfingerregel: 1.000 Ft ≈ 21 kr). Forint-kursen
  er bevidst sat i den **øvre ende** af markedskursen (observeret 0,0206–0,0210 i
  2026), så beløbene hellere er lidt for høje end for lave. Alle forint-beløb på
  siderne er omregnet med den — der er ingen rester fra den gamle 0,01875.
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
- **Flybilletterne er købt** til alle 11 til **~1.300 kr pr. mand**. **Afgang
  København fredag kl. 12:50**, ~1t 50min, landing ~14:40. Samme tidszone. Det
  betyder, at **fredag eftermiddag ikke rummer en aktivitet** — kun bar og mad.
- **Fankortet er droppet. Vi køber VIP.** Kortet kan kun laves personligt på stadion
  og hænger ikke sammen med ankomsttiden. VIP kræver intet kort og giver egen
  indgang, **adgang to timer før kampstart** og forplejning.
  **Prisen er oplyst: 32.000 Ft ≈ 670 kr pr. mand** (~352.000 Ft ≈ 7.390 kr for 11).
- **Programmet er åbent.** Intet er booket ud over kampen. To huller er markeret:
  lørdag 10:30–15:00 og søndag formiddag.
- **To rater à 2.000 kr**, i alt 4.000 kr:
  1. **13. september 2026** — flybilletten. **Betalt af alle 11.**
  2. **6. december 2026** — lejligheden plus buffer. Næste og sidste frist.
  Raterne dækker **kun** fly og lejlighed. Fly + lejlighed er estimeret til
  1.310–2.180 kr pr. person, så der er luft; **overskuddet betales tilbage**.
- **Budget uden aktiviteter: 3.500–4.450 kr pr. person.** Fly (1.300) og VIP (~670)
  er nu faste tal, så spændet er faldet fra 1.950 til 950 kr. Fly + lejlighed koster
  1.710–1.980 kr, så der er **2.020–2.290 kr i luft** i raterne — og stadig
  1.350–1.620 kr tilbage, hvis VIP-billetten tages af puljen.
- **Folk må gerne foreslå barer, spisesteder og aktiviteter**, men skal selv
  researche dem. Kravene står ens på `aktiviteter.html`, `barer.html` og
  `spisesteder.html`: navn og adresse, link, pris i original valuta, plads til 11,
  og om det skal bookes. Forslag sendes i Facebook-gruppen.
  **Hold de tre blokke enslydende**, hvis en af dem ændres.
- **Tilmelding = penge og rejseoplysninger samtidig.** Mangler den ene, tæller man
  ikke som tilmeldt.
- **Egenbetaling undervejs:** **1.305–1.870 kr** med VIP-billetten, eller
  **635–1.200 kr** hvis billetten tages af rate-pengene. Samlet turpris
  **3.500–4.450 kr** pr. person — **det tal indeholder de 4.000 kr**, de skal
  ikke lægges oveni. (Den fejl er lavet én gang: bufferen blev talt med to gange
  og gjorde egenbetalingen 430–755 kr for høj.)
- **Skydebanen er ikke længere et fast punkt** — den står som én valgfri aktivitet
  blandt flere på `aktiviteter.html`, efter at fredag eftermiddag forsvandt.
- **Ølcykel er droppet**: forbudt i distrikt VI og VII. Står som afvist med
  begrundelse i `aktiviteter.html`.

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
- **Uenighed om VIP-forplejningen.** Klubben har **på mail** skrevet *fri bar*;
  **hjemmesiden** nævner kun *buffet* og intet om drikkevarer. Det er ikke afklaret,
  og siden skal blive ved med at sige det, indtil der foreligger en skriftlig
  bekræftelse.
- **Hjemrejsens afgangstid mangler.** Billetterne er købt, men tidspunktet står ikke
  på siden. Det afgør, om der er tid til noget søndag.

---

## Åbne spørgsmål

1. **Hvad omfatter VIP-forplejningen?** Prisen er på plads (32.000 Ft), men
   buffet-eller-fri-bar er ikke afklaret. Skal bekræftes skriftligt før betaling.
2. **Hvem lægger ud** for lejlighed og VIP-billetter, og hvordan afregnes det?
   VIP-billetten på ~670 kr kan rummes i rate-pengene — der er 2.020–2.290 kr i luft.
3. **Kampstart** — afventer tv-programlægning. Rykker den, rykker VIP-adgangen og
   hele lørdagen med.
4. **Hjemrejsens afgangstid** — købt, men ikke skrevet ind på siden.
5. **Aktiviteterne** — nul, én eller to? Skal afgøres inden januar, hvis det er
   noget, der skal bookes.
6. **Sengepladser til 11** — førstevalget har 5 soveværelser. Tjek det faktiske
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
