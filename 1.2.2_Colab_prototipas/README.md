# 1.2.2 – ReviewInsight AI prototipo paleidimas

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/ruta-stunzenaite/ReviewInsight-AI/blob/main/1.2.2_Colab_prototipas/ReviewInsight_AI_Gemini.ipynb)

Sistemos apžvalga, kodo paaiškinimai ir rezultatai pateikti pačioje užrašinėje: [`ReviewInsight_AI_Gemini.ipynb`](ReviewInsight_AI_Gemini.ipynb).

## Reikalavimai

- Google paskyra (Colab ir AI Studio)
- Gemini API raktas
- Papildomai nieko diegti nereikia – užrašinė pati įdiegia `google-genai` ir `langdetect`

## Paleidimas

1. **Atidarykite užrašinę.** Spauskite **Open in Colab** ženkliuką viršuje.

2. **Gaukite API raktą.** Eikite į [Google AI Studio](https://aistudio.google.com/apikey) → **Create API key** → nukopijuokite raktą.

3. **Įrašykite raktą į Colab Secrets.** Colab kairėje spauskite 🔑 **Secrets** → **Add new secret**:
   - **Name:** `GOOGLE_API_KEY`
   - **Value:** jūsų API raktas
   - įjunkite **Notebook access**

4. **Paleiskite.** **Runtime → Run all**. Jei Colab paklaus dėl prieigos prie Secrets, spauskite **Grant access**.

5. **Patikrinkite rezultatą.** Ląstelėje „🚀 ReviewPipeline“ – 5 išanalizuoti atsiliepimai (✅) ir 1 atmestas (⏭️ R006).

Visas paleidimas trunka apie 1–2 minutes: tarp užklausų daroma 5 s pauzė dėl nemokamo plano limitų.

## Įvesties duomenys

Užrašinė automatiškai atsisiunčia duomenis iš aplanko [`input_data/`](input_data/):

| Failas | Turinys |
|---|---|
| `reviews_sample.csv` | įvestys X – 6 kliento atsiliepimai |
| `expected_outputs.json` | laukiami rezultatai y – palyginimui |

Jei GitHub nepasiekiamas, naudojama užrašinėje įterpta tų pačių duomenų kopija.

## Dažniausios problemos

| Klaida | Priežastis ir sprendimas |
|---|---|
| `Colab Secrets nepasiekiami` | patikrinkite, ar rakto pavadinimas tiksliai `GOOGLE_API_KEY` ir ar įjungtas **Notebook access** |
| `401` / `API key not valid` | neteisingai nukopijuotas raktas – sukurkite naują AI Studio |
| `404 NOT_FOUND` | modelis nepasiekiamas – užrašinė automatiškai bando kitą iš `MODEL_CANDIDATES` |
| `429 RESOURCE_EXHAUSTED` | viršytas nemokamo plano limitas – palaukite minutę ir paleiskite iš naujo |
| `403 PERMISSION_DENIED` | Google užblokavo prieigą paskyrai ar projektui – patikrinkite AI Studio **Projects** / **Billing** puslapius |
| `503 UNAVAILABLE` | Modelis laikinai perkrautas (didelė apkrova) – užrašinė automatiškai bando kitą modelį |
| `504 DEADLINE_EXCEEDED` | Modelis neatsakė per 30 s (dažniausiai perkrautas arba gemini-flash-latest alias) – užrašinė bando kitą modelį. |

---

[← Grįžti į projekto aprašymą](../README.md)
