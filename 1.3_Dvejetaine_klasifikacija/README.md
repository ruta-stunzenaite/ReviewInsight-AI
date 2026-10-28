# 1.3 – Dvejetainė klasifikacija (Telco Customer Churn)

| Užrašinė | Modelis | Atidaryti |
|---|---|---|
| `AdventureWorksDW_Decision_Forests.ipynb` | Random Forest (sprendimų miškas) | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/ruta-stunzenaite/ReviewInsight-AI/blob/main/1.3_Dvejetaine_klasifikacija/AdventureWorksDW_Decision_Forests.ipynb) |
| `AdventureWorksDW_NN.ipynb` | Neuroninis tinklas (Keras) | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/ruta-stunzenaite/ReviewInsight-AI/blob/main/1.3_Dvejetaine_klasifikacija/AdventureWorksDW_NN.ipynb) |

Abi užrašinės sprendžia tą pačią **dvejetainės klasifikacijos** užduotį ir leidžia palyginti du požiūrius: medžius ir neuroninį tinklą. Modelis prognozuoja, ar telekomunikacijų klientas **nutrauks sutartį** (`Churn`: 1 – išeis, 0 – liks).

## Reikalavimai

- Google paskyra (Colab)
- Interneto prieiga (duomenys atsisiunčiami iš Kaggle per `kagglehub`, Kaggle paskyros nereikia)
- Papildomai nieko diegti nereikia – užrašinės pačios įdiegia `kagglehub`, `tensorflow_decision_forests` ir `dtreeviz`

## Paleidimas

1. **Atidarykite užrašinę.** Spauskite **Open in Colab** ženkliuką lentelėje viršuje.

2. **(Tik Decision Forests)** Pasirinkite senesnę Colab aplinką, nes `tensorflow_decision_forests` nepalaiko naujausio Python/TensorFlow: **Runtime → Change runtime type → Runtime version → 2025.07**, tada **Runtime → Restart session**.

3. **Paleiskite.** **Runtime → Run all** arba ląsteles paeiliui (**Shift + Enter**). Pirma ląstelė atsisiunčia duomenis ir sukuria `df_all_col`.

4. **Patikrinkite rezultatą.**
   - Decision Forests: pabaigoje išspausdinamas testavimo **accuracy**, nupiešiamas vienas medis ir išvedama naujo kliento prognozė.
   - NN: rodomi mokymo grafikai (loss / accuracy), testavimo accuracy ir naujo kliento prognozė procentais.

Visas paleidimas trunka apie 1–3 minutes.

## Įvesties duomenys

Duomenys atsisiunčiami automatiškai iš [Kaggle: Telco Customer Churn](https://www.kaggle.com/datasets/blastchar/telco-customer-churn) (~7 000 klientų, 21 stulpelis).

| Grupė | Stulpeliai |
|---|---|
| Kategoriniai | `gender`, `SeniorCitizen`, `Partner`, `Dependents`, `PhoneService`, `MultipleLines`, `InternetService`, `OnlineSecurity`, `OnlineBackup`, `DeviceProtection`, `TechSupport`, `StreamingTV`, `StreamingMovies`, `Contract`, `PaperlessBilling`, `PaymentMethod` |
| Skaitiniai | `tenure`, `MonthlyCharges`, `TotalCharges` |
| Tikslas (y) | `Churn` → pervadinamas į `target` (Yes → 1, No → 0) |
| Pašalinama | `customerID` |

Valymo žingsnyje (ląstelė **1.3**) taip pat: `TotalCharges` paverčiamas skaičiumi (kelios eilutės turi tuščią reikšmę ir pašalinamos), `SeniorCitizen` 0/1 paverčiamas į `No`/`Yes`.

## Ką daro užrašinės

| Žingsnis | Decision Forests | Neural Network |
|---|---|---|
| 1. Duomenys | Įkėlimas ir valymas (1.1–1.4) | Tas pats |
| 2. Skaidymas | train / test | train / validation / test (80/10/10) |
| 3. Paruošimas | **Nereikia** – medžiai patys supranta tekstą ir skaičius | Kategorijos užkoduojamos (`StringLookup` + `CategoryEncoding`), skaičiai normalizuojami (`Normalization`) |
| 4. Modelis | `RandomForestModel` | `Dense(64)` → `Dropout` → `Dense(32)` → `Dropout` → `Dense(1)` |
| 5. Mokymas | Sekundės, vienas `fit` | 30 epochų |
| 6. Vizualizacija | Vieno medžio brėžinys (`plot_model_in_colab`, `dtreeviz`) | Mokymo kreivės, TensorBoard |
| 7. Prognozė | Naujo kliento `sample` | Naujo kliento `sample`, rezultatas procentais |

## Orientaciniai rezultatai

Abu modeliai pasiekia maždaug **78–80 %** testavimo tikslumo. Tikslus skaičius skiriasi kiekvieną kartą, nes duomenys maišomi atsitiktinai.

## Dažniausios problemos

| Klaida | Priežastis ir sprendimas |
|---|---|
| `ModuleNotFoundError: tensorflow_decision_forests` | Diegimas nepavyko. Pasirinkite **Runtime version 2025.07** (žr. paleidimo 2 žingsnį), perkraukite sesiją ir paleiskite diegimo ląstelę iš naujo |
| `No matching distribution found for tensorflow~=2.15.0` | Colab turi per naują Python (3.13). Sprendimas tas pats: **Runtime version 2025.07** |
| `KeyError: 'customerID'` | Valymo ląstelė (1.3) paleista du kartus. Paleiskite dokumentą iš naujo nuo 1.1 |
| `ValueError: could not convert string to float` | Nepaleista valymo ląstelė (1.3), todėl `TotalCharges` vis dar tekstas |
| `NameError: name 'viz_cmodel' is not defined` | Ląstelė su `dtreeviz` buvo užkomentuota (`'''`). Ištrinkite `'''` ir paleiskite ją prieš paskutines dvi ląsteles |
| `NameError: name 'train_ds' is not defined` | Praleista „Step 2" ląstelė. Paleiskite ląsteles paeiliui |
| Kaggle atsisiuntimas nepavyksta | Atsisiųskite `WA_Fn-UseC_-Telco-Customer-Churn.csv` ranka ir įkelkite: `from google.colab import files; files.upload()` |

---

[← Grįžti į projekto aprašymą](../README.md)
