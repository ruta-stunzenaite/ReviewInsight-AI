# Laukiamas testavimo prompt'o atsakymas

Žemiau – atsakymas pateikus `test_prompt.md` agentui. Struktūriniai laukai
(`status`, `language`, `sentiment`, `urgency`, `categories`) turi sutapti su `data/y/` failais;
`summary` ir `reply_draft` yra laisvas tekstas, todėl agento formuluotės gali skirtis, bet prasmė turi būti ta pati.

## Laukiamas atsakymas (y)

```json
[
  {
    "review_id": "R001",
    "status": "ANALYZED",
    "language": "lt",
    "sentiment": "positive",
    "categories": [
      "produkto_kokybe",
      "pristatymas"
    ],
    "summary": "Klientas labai patenkintas ausinių garsu, baterija ir greitu pristatymu.",
    "urgency": "low",
    "reply_draft": "Labai ačiū už puikų įvertinimą! Džiaugiamės, kad ausinės SoundAir jums patiko ir pristatymas buvo greitas. Gero klausymosi!"
  },
  {
    "review_id": "R002",
    "status": "ANALYZED",
    "language": "lt",
    "sentiment": "negative",
    "categories": [
      "pristatymas",
      "pakuote",
      "produkto_kokybe",
      "klientu_aptarnavimas",
      "grazinimas_ir_garantija"
    ],
    "summary": "Vėlavęs 3 savaites pristatymas, sulamdyta pakuotė, neveikiantis aparatas ir neatsakyti laiškai; klientas nori grąžinti pinigus.",
    "urgency": "high",
    "reply_draft": "Atsiprašome už vėlavimą ir sugadintą prekę – tai neatitinka mūsų standartų. Jūsų grąžinimą jau perdavėme vadybininkui: per 1 darbo dieną susisieksime el. paštu dėl pinigų grąžinimo ir prekės paėmimo."
  },
  {
    "review_id": "R003",
    "status": "ANALYZED",
    "language": "lt",
    "sentiment": "mixed",
    "categories": [
      "produkto_aprasymas",
      "kaina",
      "produkto_kokybe"
    ],
    "summary": "Siurblys traukia gerai, tačiau triukšmingesnis nei aprašyme, o kaina atrodo per didelė.",
    "urgency": "medium",
    "reply_draft": "Ačiū už atvirą atsiliepimą! Patikrinsime, ar aprašyme nurodytas triukšmo lygis tikslus, ir jį pataisysime. Jei siurblio triukšmas trukdo, galite jį grąžinti per 14 dienų."
  },
  {
    "review_id": "R004",
    "status": "ANALYZED",
    "language": "lt",
    "sentiment": "negative",
    "categories": [
      "saugumas",
      "produkto_kokybe"
    ],
    "summary": "Po savaitės virdulio laidas įkaista ir skleidžia degėsių kvapą – galimas saugumo pavojus.",
    "urgency": "critical",
    "reply_draft": "Labai ačiū, kad pranešėte. Prašome nedelsiant nebenaudoti virdulio ir atjungti jį nuo elektros tinklo. Šiandien su jumis susisieks mūsų komanda dėl nemokamo grąžinimo ir pinigų grąžinimo."
  },
  {
    "review_id": "R005",
    "status": "ANALYZED",
    "language": "en",
    "sentiment": "mixed",
    "categories": [
      "produkto_kokybe",
      "pristatymas"
    ],
    "summary": "Ausinių garsas puikus ir pristatymas greitas, bet įkrovimo dėklas atrodo pigus, dangtelis kliba.",
    "urgency": "low",
    "reply_draft": "Thank you for your feedback! We're glad you enjoy the sound and fast delivery. We're sorry about the charging case lid – please contact us and we'll arrange a replacement case under warranty."
  },
  {
    "review_id": "R006",
    "status": "REJECTED",
    "language": null,
    "sentiment": null,
    "categories": [],
    "summary": null,
    "urgency": null,
    "reply_draft": null
  }
]
```

## Faktinis ChatGPT atsakymas

_(claude opus 5.5)_

```json
[
  {
    "review_id": "R001",
    "status": "ANALYZED",
    "language": "lt",
    "sentiment": "positive",
    "categories": ["produkto_kokybe", "pristatymas"],
    "summary": "Klientas labai patenkintas ausinėmis: aiškus garsas, ilgai veikianti baterija ir greitas pristatymas per dvi dienas. Prekę rekomenduoja.",
    "urgency": "low",
    "reply_draft": "Labai ačiū už puikų įvertinimą! Džiaugiamės, kad SoundAir ausinės Jus džiugina garsu ir baterijos ištverme, o pristatymas buvo greitas. Malonaus klausymosi!"
  },
  {
    "review_id": "R002",
    "status": "ANALYZED",
    "language": "lt",
    "sentiment": "negative",
    "categories": ["pristatymas", "pakuote", "produkto_kokybe", "klientu_aptarnavimas", "grazinimas_ir_garantija"],
    "summary": "Pristatymas vėlavo tris savaites, dėžė sulamdyta, aparatas neįsijungia, į el. laiškus neatsakyta. Klientas reikalauja grąžinti pinigus.",
    "urgency": "high",
    "reply_draft": "Nuoširdžiai atsiprašome už vėlavusį pristatymą, sugadintą pakuotę ir tai, kad neatsakėme į Jūsų laiškus. Neveikiantis aparatas yra nepriimtinas, todėl nedelsdami pradėsime pinigų grąžinimo procesą. Mūsų komanda susisieks su Jumis šiandien dėl tolimesnių veiksmų."
  },
  {
    "review_id": "R003",
    "status": "ANALYZED",
    "language": "lt",
    "sentiment": "mixed",
    "categories": ["produkto_kokybe", "produkto_aprasymas", "kaina"],
    "summary": "Siurblys traukia gerai, tačiau yra triukšmingesnis, nei nurodyta aprašyme, o kaina, kliento nuomone, per didelė.",
    "urgency": "medium",
    "reply_draft": "Ačiū už išsamų atsiliepimą apie CleanMax 300. Džiaugiamės, kad siurblio galia Jus tenkina, o pastabą dėl triukšmo lygio perduosime patikrinti ir patikslinti prekės aprašymą. Į Jūsų nuomonę dėl kainos taip pat atsižvelgsime."
  },
  {
    "review_id": "R004",
    "status": "ANALYZED",
    "language": "lt",
    "sentiment": "negative",
    "categories": ["saugumas", "produkto_kokybe"],
    "summary": "Po savaitės naudojimo virdulio laidas ties jungtimi įkaito ir pradėjo skleisti degėsių kvapą. Klientas laiko prekę pavojinga.",
    "urgency": "critical",
    "reply_draft": "Labai ačiū, kad mus informavote, ir prašome nebenaudoti virdulio bei atjungti jį nuo elektros tinklo. Jūsų saugumas mums svarbiausias, todėl šį atvejį perduodame skubiam tyrimui. Susisieksime su Jumis artimiausiu metu dėl prekės pakeitimo arba pinigų grąžinimo."
  },
  {
    "review_id": "R005",
    "status": "ANALYZED",
    "language": "en",
    "sentiment": "mixed",
    "categories": ["produkto_kokybe", "pristatymas"],
    "summary": "Ausinės geros, garsas puikus, pristatymas greitas, tačiau įkrovimo dėklas atrodo pigus, o jo dangtelis klibantis.",
    "urgency": "medium",
    "reply_draft": "Thank you for your feedback and for enjoying the sound quality of the SoundAir headphones! We're sorry the charging case lid feels loose, and we'll pass this on to our quality team. If the lid affects charging, please contact us and we'll be happy to help with a replacement."
  },
  {
    "review_id": "R006",
    "status": "REJECTED",
    "language": null,
    "sentiment": null,
    "categories": [],
    "summary": null,
    "urgency": null,
    "reply_draft": null
  }
]
```

## Palyginimas

| review_id | sentiment sutampimas | urgency sutampimas | kategorijos sutapimas | pastabos |
|---|---|---|---|---|
| R001 | ✅ (positive) | ✅ (low) | 1.00 | Santrauka detalesnė (16 ž.), bet neviršija 30 žodžių limito. |
| R002 | ✅ (negative) | ✅ (high) | 1.00 | Laukiamame atsakyme nurodytas konkretus terminas („per 1 darbo dieną“) ir prekės paėmimas; faktiniame – „šiandien“, be paėmimo detalių. |
| R003 | ✅ (mixed) | ✅ (medium) | 1.00 | Kategorijų tvarka skiriasi, bet aibės identiškos. Laukiamas atsakymas siūlo grąžinimą per 14 d.; faktinis tik žada patikslinti aprašymą. |
| R004 | ✅ (negative) | ✅ (critical) | 1.00 | Abu atsakymai liepia nebenaudoti ir atjungti virdulį; laukiamas konkrečiau žada nemokamą grąžinimą. |
| R005 | ✅ (mixed) | ❌ (medium vs low) | 1.00 | Vienintelis neatitikimas. Faktinis rėmėsi taisykle „dalinis nepasitenkinimas → medium“; laukiamas dangtelį laiko smulkiu komentaru (4/5). Abu aiškinimai pagrįsti, tad verta patikslinti taisyklę. |
| R006 | ✅ (null, REJECTED) | ✅ (null) | 1.00 (abi tuščios) | Abu teisingai atmetė („Super“ = 5 simb. < 15). |
