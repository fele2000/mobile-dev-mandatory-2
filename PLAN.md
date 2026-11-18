# Projektplan – "Guess the Card" (MTG-gættespil)

Mandatory 2 / eksamensprojekt – Mobiludvikling (React Native), efterår 2026.
Soloprojekt. **Emnet er ikke godkendt af underviser endnu.**

## 1. Idé

Et gættespil i React Native (Expo). Spilleren får vist et helt Magic: The Gathering-kort, men
kortets navn er dækket til. Spilleren skriver et gæt på navnet. Efter gættet afslører en kort
animation navnet (det, der dækker, glider eller toner væk), uanset om svaret var rigtigt eller forkert.

- Spilleren starter med **5 liv**.
- Et forkert gæt koster 1 liv. Et rigtigt gæt giver point.
- Ved 0 liv er spillet slut, og spilleren ser sin score.

> Åbent spørgsmål: Får man ét gæt pr. kort (forkert = mister liv og går videre til næste kort),
> eller flere gæt pr. kort, hvor hvert forkert gæt koster et liv? Planen går ud fra
> **ét gæt pr. kort** indtil andet er besluttet.

## 2. Teknologi

| Område | Valg | Begrundelse |
|---|---|---|
| Framework | React Native med Expo | Som i kurset |
| Test-enhed | Fysisk Android-telefon via Expo Go | Hurtigt at teste |
| Kortdata og billeder | [Scryfall API](https://scryfall.com/docs/api) | Gratis, ingen API-nøgle, har billeder i høj kvalitet |
| Navigation | `@react-navigation/native` + `native-stack` | Som i kurset (uge 37) |
| Animation | `react-native-reanimated` (eller den indbyggede `Animated`) | Kursets emne: bevægelser og animation (uge 45) |
| Cloud-database | Firebase Firestore (alternativt Supabase) | Highscores, så niveauet passer til kursets cloud-emner |
| Login | Firebase Auth (e-mail/adgangskode) | Kursets emne: brugeradministration (uge 43) |
| Lokal lagring | AsyncStorage | Lokal bedste score og indstillinger |

### Scryfall – vigtige detaljer
- Tilfældigt kort: `GET https://api.scryfall.com/cards/random?q=<query>`
- Billede: `image_uris.normal` (488×680) eller `image_uris.large`. Kort med to sider har billeder i `card_faces[].image_uris`.
- Krav: Send headerne `User-Agent` (et appnavn) og `Accept: application/json`. Hold under ca. 10 kald i sekundet.
- **Faldgrube:** Mange kort nævner deres eget navn i regelteksten. Brug `-o:~` i forespørgslen for at
  udelukke dem (`~` står for kortets eget navn i Scryfalls søgesyntaks).
- Forslag til basisforespørgsel: `-o:~ -is:dfc -t:token -is:funny game:paper` (skal testes og justeres).
- Mulig sværhedsgrad: Let = `is:commander` eller kendte/populære kort, svær = alle kort.

### Tildækning af navnet
- Kortbilledet vises med fast billedformat (488:680 ≈ 0,717).
- Et `View` lægges absolut oven på billedet med **procentbaseret** position (navnebjælken ligger ca. 4–10 %
  fra toppen, ca. 7–80 % i bredden; skal finjusteres). Så passer overlayet uanset skærmstørrelse.
- Kort med andre layouts (gamle kanter, "showcase", fuld kunst) kan have navnet et andet sted.
  Afgræns med f.eks. `frame:2015` i forespørgslen.

### Sammenligning af gæt
- Normalisér: små bogstaver, trim mellemrum, fjern tegnsætning og accenter (`Æther` → `aether`).
- Overvej en lille fejlmargin (Levenshtein-afstand ≤ 1–2) for tastefejl. Beslut senere.

## 3. Skærme

1. **Login / opret bruger** (Firebase Auth)
2. **Hjem**: start spil, se highscores, log ud
3. **Spil**: kortbillede med tildækning, tekstfelt, knappen "Gæt", liv (♥×5), score
4. **Afslør** (del af spilskærmen): animation, der fjerner tildækningen, og visning af rigtigt/forkert, derefter "Næste kort"
5. **Game over**: score, gemmes i Firestore
6. **Highscores**: liste (FlatList) fra Firestore, sorteret efter score

## 4. Datamodel (Firestore)

```
highscores/{autoId}
  userId: string
  displayName: string
  score: number
  createdAt: timestamp
```
CRUD-dækning: create (gem score), read (leaderboard), update (f.eks. brugerens visningsnavn), delete (slet egne scores).

## 5. Milepæle

| Uge | Dato | Mål |
|---|---|---|
| 47 | 16/11–22/11 | Plan og CLAUDE.md. **Få emnet godkendt af Jon.** Expo-projekt oprettes og kører på Android |
| 48 | 23/11–29/11 | Scryfall-kald, kortvisning med tildækning, gæt-logik, liv og score |
| 49 | 30/11–4/12 | Afslør-animation, navigation, game over. **Video under 1 min. sendes til jone@ek.dk senest fre. 4/12 kl. 23.59** |
| 50 | 7/12–13/12 | Firebase Auth og Firestore-highscores |
| 51 | 14/12– | Polering, fejlhåndtering, screenshots og skriftlig forklaring |
| — | 12.–15/1 2027 | Eksamen |

## 6. Aflevering
- Screenshots af koden og af appen
- Forklaring af det vigtigste i koden
- Demo-video under 1 minut (frist 4/12)

## 7. Mulige udvidelser (kun hvis der er tid)
- Hints: ét liv for at se manaomkostning eller korttype
- Sværhedsgrader og tidsbegrænsning
- Dagligt kort (samme kort for alle den dag)
- Gestus: swipe for at springe et kort over
- Haptisk feedback ved rigtigt/forkert svar (`expo-haptics`)
