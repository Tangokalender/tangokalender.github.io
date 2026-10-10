# Tangokalender | Oslo

**Nettside: https://tangokalender.github.io/**

Statisk, responsiv og filtrerbar kalender for argentinsk tango i Oslo – milongaer, practicaer, kurs og festivaler.
Dette repoet har **dataene** for Oslo: arrangementene (`events/`), kartposisjonene (`venues.json`) og innstillingene
(`site.toml`). Siden bygges av Julia-pakken [TangoKalender](https://github.com/Tangokalender/TangoKalender.jl)
(versjonen står i `Project.toml`) og publiseres med GitHub Pages.

## Visninger og lenker

- **Liste** (forsiden): kompakt liste gruppert per dag. **Uke**: én uke om gangen med ‹ ›. **Kart**: stedene for
  arrangementene de neste 7 dagene (eller perioden du velger) på et kart, med de samme filtrene. **Kort**: utfyllende kort.
- Arrangementer med en presis adresse får et kart på sin egen side. Posisjonene ligger i `venues.json` og hentes fra
  OpenStreetMap (`tangokalender geocode`, kjøres automatisk for nye innsendinger). Står en nål feil, rett tallene i
  `venues.json` og sett `"source": "manual"` – da blir de ikke overskrevet.
- Hvert arrangement har egen side, `https://tangokalender.github.io/arrangement/<id>/`, med alle detaljer,
  «Legg i kalender (.ics)», «Del lenke» og «Rett opp».
- Filtrene ligger i adressen og kan deles eller bokmerkes, f.eks.
  https://tangokalender.github.io/?type=milonga,practica
- Abonner på hele kalenderen: `webcal://tangokalender.github.io/kalender.ics`.
  RSS: https://tangokalender.github.io/rss.xml
- **Bygg inn på egen nettside:** https://tangokalender.github.io/bygg-inn.html – dagens og ukens program som bilde
  med lenker (`<object>`, oppdateres hver natt), en liste med filtre for `<iframe>` (f.eks. bare én arrangørs
  arrangementer med `?arr=`), samt `kalender.ics`, `rss.xml` og `events.json`.
- ActivityPub står på [TODO](TODO.md).

## Språk

Siden finnes på norsk (`/`), engelsk (`/en/`) og spansk (`/es/`), med de samme filnavnene, f.eks.
https://tangokalender.github.io/en/uke.html. Første besøk på en norsk side følger nettleserens språk (engelsk for
andre språk enn norsk/skandinavisk og spansk); valget i språkvelgeren (NO · EN · ES) huskes i nettleseren.
Tittel og beskrivelse kan sendes inn på norsk eller engelsk («Tekstspråk»), og eventuelt også på det andre språket –
ett språk er nok. Spanske sider viser engelsk tekst når den finnes. Skjemaene på GitHub er på norsk; de engelske og
spanske «Legg til»-sidene forklarer feltene.

## Legge inn arrangementer

Alt om innsending står på én side: **https://tangokalender.github.io/legg-til.html**

- **Ett arrangement** (eller en enkel ukentlig serie): skjemaet «Nytt arrangement». Faste kvelder legges inn med
  «Gjentas: Ukentlig».
- **Flere datoer fra regneark:** første rad kolonnenavn, andre rad alt som er felles, neste rader bare det som er
  annerledes (typisk Dato og DJ; tom celle = som andre rad, `-` fjerner, «Avlyst» i Status). Kopier cellene fra
  Excel/Google Regneark/Numbers og lim inn i «Nytt arrangement (fra tabell)». Mal:
  https://tangokalender.github.io/mal/arrangementer-mal.csv
- **Med KI:** kopier ledeteksten på siden inn i Copilot/Gemini/ChatGPT sammen med arrangementsteksten, og lim
  svaret (JSON) inn i «Nytt arrangement (JSON fra KI)». Instruksjoner for KI-agenter:
  https://tangokalender.github.io/llms.txt
- **Rette opp:** «Rett opp» på hvert arrangement (tomt felt = ingen endring, `-` fjerner en opplysning; gjelder
  denne datoen eller også alle senere i serien; avlysning med «Status: Avlyst»).
- **Pull request:** legg til eller endre filer under `events/` direkte. CI validerer alle filer.

En redaktør ser over og merger; siden bygges og publiseres automatisk fra `main`.

## For redaktører

- `.github/workflows/validate.yml`: validering av alle PR-er og `main`.
- `.github/workflows/pages.yml`: bygger siden og publiserer til Pages ved endringer på `main` og hver natt.
- `.github/workflows/intake.yml`: skjema → filer → PR (gren `arrangement/issue-<nr>`). Saker med feil får
  etiketten `trenger-retting`. PR-er laget av roboten kjører ikke CI automatisk, men er validert av roboten.

Arbeidsflytene kaller de gjenbrukbare i kode-repoet, i versjonen `Project.toml` peker på. Ny versjon av koden: endre
`rev` i `Project.toml` og `@vX.Y.Z` i arbeidsflytene (Dependabot foreslår det siste), kjør
`julia --project=. -e 'using Pkg; Pkg.update()'` og `julia --project=. -m TangoKalender templates`, og commit.

Engangsoppsett på GitHub: Settings → Pages → Source: *GitHub Actions*; Settings → Actions → General →
Workflow permissions: *Read and write* og *Allow GitHub Actions to create and approve pull requests*;
etikettene `nytt-arrangement`, `rettelse` og `trenger-retting`; grenbeskyttelse på `main` som krever **Validate**.

## Redigere mange arrangementer

Last ned `tangoedit` (Linux, Windows, macOS) fra
[kode-repoets utgivelser](https://github.com/Tangokalender/TangoKalender.jl/releases/latest), eller bruk Julia:

```bash
julia --project=. -e 'using Pkg; Pkg.instantiate()'
julia --project=. -m TangoKalender edit milonga --until=2026-12-31                  # hvilke treffer?
julia --project=. -m TangoKalender edit milonga type:milonga price_nok=150 --preview # slik blir de
julia --project=. -m TangoKalender validate events
julia --project=. -m TangoKalender site events _site                              # bygg siden lokalt
```

## Arrangementer: én fil per arrangement

```
events/
  2026/10-october/2026-10-06-esa-2026-10-06.json   # <startdato>-<id>.json
```

Alle arrangementer har dato. Faste aktiviteter (ukentlige milongaer, kurs) er én fil per dato med felles
`series`-id, slik at DJ, pris osv. kan variere fra gang til gang. Avlyste kvelder markeres med
`"status": "cancelled"` (vises som «Avlyst») i stedet for at filen slettes. Formatet:
https://tangokalender.github.io/schema/tango-event.schema.json (legg det inn som `"$schema"` for autoutfylling).
