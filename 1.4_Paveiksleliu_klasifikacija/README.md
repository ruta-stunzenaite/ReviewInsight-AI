# 1.4 – Paveikslėlių klasifikacija su CNN (Intel Image Classification)

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/ruta-stunzenaite/ReviewInsight-AI/blob/main/1.4_Paveikseliu_klasifikacija/cnn.ipynb)

Užrašinė [`cnn.ipynb`](cnn.ipynb) apmoko **konvoliucinį neuroninį tinklą (CNN) nuo nulio**: modelis kiekvienai nuotraukai priskiria vieną iš 6 klasių (pastatai, miškas, ledynas, kalnai, jūra, gatvė). Pabaigoje CNN palyginamas su paprastu `Dense` tinklu (MLP), kad matytųsi konvoliucijų nauda.

## Reikalavimai

- Google paskyra (Colab)
- Interneto prieiga (duomenys atsisiunčiami iš Kaggle per `kagglehub`, Kaggle paskyros nereikia)
- GPU nebūtinas, bet pagreitina mokymą (**Runtime → Change runtime type → T4 GPU**)
- Papildomai nieko diegti nereikia – užrašinė pati įdiegia `kagglehub`

## Paleidimas

1. **Atidarykite užrašinę.** Spauskite **Open in Colab** ženkliuką viršuje.

2. **Paleiskite.** **Runtime → Run all** arba ląsteles paeiliui (**Shift + Enter**). Antra ląstelė atsisiunčia ir sumažina ~17 000 nuotraukų, todėl užtrunka **1–2 minutes**.

3. **Patikrinkite rezultatą.**
   - Ląstelė su 100 nuotraukų parodo pavyzdžius su klasių pavadinimais.
   - Po mokymo (20 epochų) rodomas testavimo **accuracy** ir mokymo grafikas.
   - Part 2 parodo išmoktus filtrus ir feature maps.
   - Part 3 parodo MLP tikslumą palyginimui.

## Įvesties duomenys

Duomenys atsisiunčiami automatiškai iš [Kaggle: Intel Image Classification](https://www.kaggle.com/datasets/puneet6060/intel-image-classification).

| Aplankas | Turinys |
|---|---|
| `seg_train` | ~14 000 mokymo nuotraukų, išdėliotų po klasių aplankus |
| `seg_test` | ~3 000 testavimo nuotraukų |

Klasės: `buildings`, `forest`, `glacier`, `mountain`, `sea`, `street`. Originalios nuotraukos yra 150×150 px, bet užrašinėje sumažinamos iki **32×32**, kad modelis mokytųsi greitai. Klasių pavadinimai nuskaitomi automatiškai iš aplankų.

## Ką daro užrašinė

| Dalis | Aprašymas |
|---|---|
| **Part 1. CNN** | Nuotraukų įkėlimas, normalizavimas (÷ 255), 3 × `Conv2D` su `MaxPooling`, `Flatten`, `Dense(6)`. Mokymas 20 epochų |
| **Part 2. Filtrai** | Pirmojo sluoksnio filtrų ir feature maps vizualizacija, konvoliucijos skaičiavimas rankiniu būdu su `numpy` |
| **Part 3. MLP** | Ta pati užduotis su `Dense` tinklu be konvoliucijų |

## Orientaciniai rezultatai

CNN pasiekia maždaug **65–75 %**, o MLP gerokai mažiau (**~45–55 %**). Skirtumas parodo, kad konvoliuciniai sluoksniai nuotraukoms tinka daug geriau. Tikslūs skaičiai kiekvieną kartą skiriasi.

## Dažniausios problemos

| Klaida | Priežastis ir sprendimas |
|---|---|
| `NameError: name 'class_names' is not defined` | Nepaleista duomenų įkėlimo ląstelė (antra). Paleiskite ląsteles paeiliui |
| `IndexError: list index out of range` ties `seg_train` | Kitokia aplankų struktūra. Paleiskite `print(os.listdir(path))` ir patikrinkite kelią |
| Mokymo klaida apie netinkamą klasę arba keistas tikslumas | Paskutinis sluoksnis liko `Dense(10)`. Pakeiskite į `Dense(len(class_names))` abiejuose modeliuose (CNN ir MLP) |
| Įkėlimas labai ilgas | Normalu pirmą kartą (1–2 min). Pakartotinai paleidus Kaggle failai jau būna atsisiųsti |
| `ResourceExhaustedError` / baigėsi RAM | **Runtime → Restart session** ir paleiskite iš naujo |
| Kaggle atsisiuntimas nepavyksta | Atsisiųskite dataset ranka iš Kaggle, įkelkite į Colab ir nurodykite aplankų kelią `train_dir` / `test_dir` |

---

[← Grįžti į projekto aprašymą](../README.md)
