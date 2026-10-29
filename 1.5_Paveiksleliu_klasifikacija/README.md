# 1.5 – Paveikslėlių atpažinimas su paruoštais modeliais

| Užrašinė | Paskirtis | Atidaryti |
|---|---|---|
| `cnn_One_Image.ipynb` | Viena nuotrauka: klasifikacija, objektų aptikimas, segmentacija | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/ruta-stunzenaite/ReviewInsight-AI/blob/main/1.5_Paveiksleliu_klasifikacija/cnn_One_Image.ipynb) |
| `cnn_Transfer_learning.ipynb` | Transfer learning: paruoštas modelis pritaikomas savo duomenims | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/ruta-stunzenaite/ReviewInsight-AI/blob/main/1.5_Paveiksleliu_klasifikacija/cnn_Transfer_learning.ipynb) |

Abi užrašinės naudoja **jau išmokytus modelius iš TensorFlow Hub** (EfficientNetV2 ir kt.), o ne mokosi nuo nulio.

## Užrašinių skirtumas

| | `cnn_One_Image` | `cnn_Transfer_learning` |
|---|---|---|
| Ar modelis mokomas | **Ne**, tik naudojamas | **Taip**, pritaikomas savo klasėms |
| Duomenys | 1 nuotrauka (Kaggle arba sava) | Intel Image Classification (6 klasės) |
| Užduotys | Klasifikacija, detekcija, segmentacija | Klasifikacija |
| GPU | Nebūtinas | **Rekomenduojamas** (be jo mokymas labai lėtas) |
| Trukmė | ~1–3 min | ~5–10 min su GPU (epocha ~35–60 s) |

## Reikalavimai

- Google paskyra (Colab)
- Interneto prieiga (modeliai iš TF Hub, duomenys iš Kaggle per `kagglehub`)
- `cnn_Transfer_learning`: **Runtime → Change runtime type → T4 GPU**
- Papildomai nieko diegti nereikia – užrašinės pačios įdiegia `kagglehub`, `tf_keras`

## Paleidimas

### `cnn_One_Image.ipynb`

1. **Atidarykite užrašinę** (**Open in Colab**).
2. **Pasirinkite nuotrauką** ląstelėje „Select your own input image":
   - **A variantas:** nuotrauka iš Kaggle – keiskite `image_index`.
   - **B variantas:** sava nuotrauka (parduotuvė, sandėlis, gatvė) – atkomentuokite `files.upload()` eilutes.
3. **Runtime → Run all.**
4. **Patikrinkite rezultatą:**
   - **Classification:** 2 modelių (EfficientNetV2-S, įvestis 384×384) top 4 klasės su tikimybėmis, tada tas pats iškirptam nuotraukos gabalui.
   - **Object detection:** SSD MobileNet v2 (Open Images) rėmeliai su pavadinimu ir %, iki 20 rėmelių su tikimybe ≥ 0,1.
   - **Segmentation:** HRNet (ADE20K) pikselių klasių kaukės: pirma rodomos visos 151 kaukė, paskui 4 pasirinktos. Paskutinėje ląstelėje `chosen = [...]` įrašykite savo nuotraukoje esančių klasių numerius.

### `cnn_Transfer_learning.ipynb`

1. **Atidarykite užrašinę** ir įjunkite **GPU** (žr. reikalavimus).
2. **Chapter 1:** paleiskite ląsteles paeiliui. Modelis mokomas 3 epochas, išsaugomas į `saved_model_CIFAR10.zip`.
3. **Chapter 2:** modelis įkeliamas iš zip (po įkėlimo zip ir aplankas **ištrinami**, todėl Chapter 2 antrą kartą paleisti reikia pakartojus Chapter 1), atšaldomas ir toliau mokomas su data augmentation (3 iteracijos).
4. **Patikrinkite rezultatą:** po Chapter 1 rodomas 100 testavimo nuotraukų grafikas, kuriame **žalias** užrašas = teisinga prognozė, **raudonas** = klaida.

## Kaip veikia

| Sąvoka | Paaiškinimas |
|---|---|
| **Klasifikacija** | Kas yra nuotraukoje? Viena klasė visai nuotraukai |
| **Detekcija** | Kur yra objektai? Rėmeliai aplink juos |
| **Segmentacija** | Kuris pikselis kam priklauso? Kiekvienas pikselis turi klasę |
| **Feature extraction** | Paruoštas modelis užšaldomas (`trainable=False`), mokomas tik naujas `Dense` sluoksnis |
| **Fine-tuning** | Modelis atšaldomas ir mokomas su 10× mažesniu mokymosi greičiu |
| **Data augmentation** | Atsitiktinis apvertimas, pasukimas, ryškumas ir pastūmimas didina duomenų įvairovę |

## Įvesties duomenys

[Kaggle: Intel Image Classification](https://www.kaggle.com/datasets/puneet6060/intel-image-classification) – 6 klasės (`buildings`, `forest`, `glacier`, `mountain`, `sea`, `street`). `cnn_One_Image` naudoja `seg_pred` nuotraukas, `cnn_Transfer_learning` – `seg_train` ir `seg_test`, sumažintas iki 32×32 (modelis jas padidina iki 240×240).

## Orientaciniai rezultatai

Rezultatai iš išsaugoto `cnn_Transfer_learning` paleidimo (3 000 testavimo nuotraukų; kiekvieną kartą šiek tiek skiriasi):

| Etapas | Accuracy |
|---|---|
| Chapter 1 (užšaldytas modelis, 3 epochos) | **~89,3 %** (validacija po 1 / 2 / 3 epochos: 88,2 / 89,1 / 89,3 %) |
| Chapter 2 (atšaldytas, augmentation, 3 iteracijos) | **~88,6 %** |

Tai gerokai daugiau nei paprastas CNN iš [1.4](../1.4_Paveiksleliu_klasifikacija/README.md) (~75 %), nors nuotraukos sumažintos iki 32×32. Chapter 2 tikslumo nepagerino (šiame paleidime jis liko panašus arba šiek tiek mažesnis), nes mokoma tik 3 trumpas iteracijas su 10× mažesniu mokymosi greičiu.

## Dažniausios problemos

| Klaida | Priežastis ir sprendimas |
|---|---|
| `NameError: name 'ds' is not defined` | Liko sena ląstelė su `ds["train"]["img"]`. Ištrinkite ją, nes normalizavimą jau atlieka duomenų įkėlimo ląstelė |
| `NameError: name 'class_names' is not defined` | Nepaleista duomenų įkėlimo ląstelė. Paleiskite ląsteles paeiliui |
| `replace saved_model_CIFAR10/...? [y]es, [n]o, [A]ll` | Ne klaida. Įrašykite **`A`** ir Enter, arba naudokite `!unzip -o -q saved_model_CIFAR10.zip` |
| `saved_model_CIFAR10.zip: cannot find` | Sesija perkrauta ir zip dingo. Paleiskite Chapter 1 iš naujo |
| Mokymas labai lėtas | Neįjungtas GPU: **Runtime → Change runtime type → T4 GPU** |
| `ModuleNotFoundError: tf_keras` | Paleiskite `!pip install -q tf_keras` ir perkraukite sesiją |
| `IndexError` ties `all_imgs[image_index]` | Nerasta `seg_pred` nuotraukų. Patikrinkite aplankus: `print(os.listdir(path))` |
| Tuščias arba netinkamas iškirpimas | `cut_size_in_pixels` didesnis už nuotrauką. Intel nuotraukoms naudokite ~70 |
| Baigėsi RAM | Paliktas mažesnis modelis (`..._s`, `input_size = 384`); perkraukite sesiją |
| Kaggle atsisiuntimas nepavyksta | Atsisiųskite dataset ranka, įkelkite į Colab ir nurodykite aplankų kelią |

---

[← Grįžti į projekto aprašymą](../README.md)
