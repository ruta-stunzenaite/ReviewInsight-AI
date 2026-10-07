# 1.2.1 – End-to-end pavyzdžiai ir testavimo prompt'as

Šioje dalyje parodyta, kaip **ReviewInsight AI** veikia nuo pradžios iki galo: sistema gauna kliento atsiliepimą (**X**) ir grąžina struktūrizuotą analizės rezultatą (**y**). Pavyzdžiai patikrinti testavimo prompt'u, pateiktu agentui.

## Turinys

1. [Užduoties formuluotė](#užduoties-formuluotė)
2. [Kas yra X ir y](#kas-yra-x-ir-y)
4. [(X, y) poros](#x-y-poros)
5. [Testavimo prompt'as](#testavimo-promptas)
6. [Rezultatai](#rezultatai)

---

## Užduoties formuluotė

> Pateikti kelis (min. 3) pavyzdžius, kurie demonstruotų end-to-end sprendimą. GitHub projekto puslapyje turi būti `data` direktorija, kurioje matytųsi (X, y) poros. GitHub turi būti direktorija `prompts`, joje turi būti testavimo prompt'as, kurį pateikus agentui matytųsi atsakymas į užduotą užklausą.

---

## Kas yra X ir y

**X – įvestis.** Vienas kliento atsiliepimas toks, kokį jį eksportuoja e. parduotuvės platforma:

| Laukas | Reikšmė |
|---|---|
| `review_id` | atsiliepimo ID |
| `product_id`, `product_name` | prekės ID ir pavadinimas |
| `rating` | kliento įvertinimas 1–5 |
| `date` | data |
| `text` | atsiliepimo tekstas |

**y – laukiamas sistemos rezultatas:**

| Laukas | Reikšmė | Kaip tikrinama |
|---|---|---|
| `status` | `ANALYZED` arba `REJECTED` (per trumpas tekstas) | tikslus sutapimas |
| `language` | atsiliepimo kalba (`lt`, `en`) | tikslus sutapimas |
| `sentiment` | `positive` / `neutral` / `negative` / `mixed` | tikslus sutapimas |
| `urgency` | `low` / `medium` / `high` / `critical` | tikslus sutapimas |
| `categories` | problemų kategorijos iš fiksuoto sąrašo | kiek kategorijų sutapo |
| `summary` | santrauka lietuvių kalba | vertina žmogus (laisvas tekstas) |
| `reply_draft` | atsakymo klientui juodraštis kliento kalba | vertina žmogus (laisvas tekstas) |

Leidžiamos kategorijos: `produkto_kokybe`, `pristatymas`, `pakuote`, `kaina`, `klientu_aptarnavimas`, `produkto_aprasymas`, `grazinimas_ir_garantija`, `saugumas`, `kita`.

> Testavimo prompt'as tikrina pagrindinius analizės laukus, kuriuos galima palyginti su y. Pasitikėjimo balas (`confidence`) naudojamas tik Colab prototipe (1.2.2), nes tai paties modelio įvertinimas, kuris tarp paleidimų kinta.
> **Apie `notes`:** šį lauką sistema negrąžina ir agentas jo neprašomas. Tai autoriaus komentaras y faile, padedantis suprasti, kokią sistemos taisyklę demonstruoja konkreti pora. Lyginant rezultatus jis ignoruojamas.

---

## (X, y) poros

| # | X – atsiliepimas (sutrumpintas) | ★ | y – laukiamas rezultatas | Ką demonstruoja | Failai |
|---|---|---|---|---|---|
| 1 | „Ausinės puikios! Garsas aiškus, baterija laiko visą dieną, o pristatė vos per dvi dienas…“ | 5 | `positive` · `low` · produkto_kokybe, pristatymas | paprastas teigiamas atvejis | [X](data/X/ex01_R001.json) · [y](data/y/ex01_R001.json) |
| 2 | „Užsakymo laukiau 3 savaites… Dėžė atvyko sulamdyta, aparatas neįsijungia… Noriu grąžinti pinigus!“ | 1 | `negative` · `high` · 5 kategorijos | kelios problemos viename atsiliepime | [X](data/X/ex02_R002.json) · [y](data/y/ex02_R002.json) |
| 3 | „Siurblys traukia gerai, bet daug triukšmingesnis, nei nurodyta aprašyme, o kaina per didelė.“ | 3 | `mixed` · `medium` · produkto_aprasymas, kaina, produkto_kokybe | mišri nuotaika | [X](data/X/ex03_R003.json) · [y](data/y/ex03_R003.json) |
| 4 | „Virdulio laidas įkaito ir pradėjo kvepėti degėsiais. Tai pavojinga…“ | 1 | `negative` · `critical` · saugumas, produkto_kokybe | saugumo rizika visada gauna aukščiausią prioritetą | [X](data/X/ex04_R004.json) · [y](data/y/ex04_R004.json) |
| 5 | „Good headphones with great sound, but the charging case feels cheap…“ | 4 | `mixed` · `low` · atsakymas **anglų** kalba | atsiliepimas kita kalba | [X](data/X/ex05_R005.json) · [y](data/y/ex05_R005.json) |
| 6 | „Super!!!“ | 5 | `REJECTED` | per trumpas tekstas atmetamas, DI nekviečiamas | [X](data/X/ex06_R006.json) · [y](data/y/ex06_R006.json) |

### Pavyzdys: pora Nr. 4

**X** – [`data/X/ex04_R004.json`](data/X/ex04_R004.json)

```json
{
  "review_id": "R004",
  "product_id": "P-3305",
  "product_name": "Elektrinis virdulys HeatUp",
  "rating": 1,
  "date": "2026-10-01",
  "text": "Po savaitės naudojimo virdulio laidas ties jungtimi įkaito ir pradėjo kvepėti degėsiais. Tai pavojinga, bijau juo naudotis!"
}
```

**y** – [`data/y/ex04_R004.json`](data/y/ex04_R004.json)

```json
{
  "review_id": "R004",
  "status": "ANALYZED",
  "language": "lt",
  "sentiment": "negative",
  "urgency": "critical",
  "categories": [
    "saugumas",
    "produkto_kokybe"
  ],
  "summary": "Po savaitės virdulio laidas įkaista ir skleidžia degėsių kvapą – galimas saugumo pavojus.",
  "reply_draft": "Labai ačiū, kad pranešėte. Prašome nedelsiant nebenaudoti virdulio ir atjungti jį nuo elektros tinklo. Šiandien su jumis susisieks mūsų komanda dėl nemokamo grąžinimo ir pinigų grąžinimo.",
  "notes": "Saugumo rizika visada → critical, net jei atsiliepimas trumpas."
}
```

---

## Testavimo prompt'as

📄 [`prompts/test_prompt.md`](prompts/test_prompt.md)

Prompt'as agentui aprašo visą sistemos apdorojimo grandinę:

1. **Paruošimas** – teksto valymas, kalbos nustatymas, per trumpų atsiliepimų atmetimas;
2. **Analizė** – nuotaika, kategorijos, santrauka, skubumas, atsakymo klientui juodraštis;
3. **Validacija** – ar visi laukai atitinka leidžiamas reikšmes.

Į prompt'ą įtraukti visi 6 atsiliepimai iš `data/X/`. Agentas turi grąžinti JSON masyvą, kurį galima palyginti su `data/y/`.

---

## Rezultatai

📄 Visas Claude atsakymas: [`prompts/test_prompt_response.md`](prompts/test_prompt_response.md)

**Modelis:** Claude (_Opus 5.5_) · 

### Palyginimas su laukiamais rezultatais

| review_id | sentiment sutampimas | urgency sutampimas | kategorijos sutapimas | pastabos |
|---|---|---|---|---|
| R001 | ✅ (positive) | ✅ (low) | 1.00 | Santrauka detalesnė (16 ž.), bet neviršija 30 žodžių limito. |
| R002 | ✅ (negative) | ✅ (high) | 1.00 | Laukiamame atsakyme nurodytas konkretus terminas („per 1 darbo dieną“) ir prekės paėmimas; faktiniame – „šiandien“, be paėmimo detalių. |
| R003 | ✅ (mixed) | ✅ (medium) | 1.00 | Kategorijų tvarka skiriasi, bet aibės identiškos. Laukiamas atsakymas siūlo grąžinimą per 14 d.; faktinis tik žada patikslinti aprašymą. |
| R004 | ✅ (negative) | ✅ (critical) | 1.00 | Abu atsakymai liepia nebenaudoti ir atjungti virdulį; laukiamas konkrečiau žada nemokamą grąžinimą. |
| R005 | ✅ (mixed) | ❌ (medium vs low) | 1.00 | Vienintelis neatitikimas. Faktinis rėmėsi taisykle „dalinis nepasitenkinimas → medium“; laukiamas dangtelį laiko smulkiu komentaru (4/5). Abu aiškinimai pagrįsti, tad verta patikslinti taisyklę. |
| R006 | ✅ (null, REJECTED) | ✅ (null) | 1.00 (abi tuščios) | Abu teisingai atmetė („Super“ = 5 simb. < 15). |

---

[← Grįžti į projekto aprašymą](../README.md)
