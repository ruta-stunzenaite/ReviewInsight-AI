# 1.1 – ReviewInsight AI sistemos projektavimas (PlantUML → Mermaid)

**ReviewInsight AI** – klientų atsiliepimų analizės sistema e. parduotuvei, naudojanti generatyvųjį dirbtinį intelektą (Google Gemini API).

Šioje dalyje su Claude pagalba sistema suprojektuota UML diagramomis: pirmiausia sukurtos 5 **PlantUML** diagramos, vėliau jos konvertuotos į **Mermaid** sintaksę, kurią GitHub atvaizduoja tiesiogiai.

> **Naudotas įrankis:** Claude (_Opus 5.5_)

## Turinys

1. [Užduoties formuluotė](#užduoties-formuluotė)
2. [Pirmas prompt'as – PlantUML](#pirmas-promptas--plantuml)
3. [Claude atsakymas – architektūros aprašas](#claude-atsakymas--architektūros-aprašas)
4. [PlantUML diagramos](#plantuml-diagramos)
5. [Antras prompt'as – Mermaid](#antras-promptas--mermaid)
6. [Mermaid diagramos](#mermaid-diagramos)

---

## Užduoties formuluotė

> Dviejų (arba vieno) studentų komanda pasirenka temą. Tos temos rėmuose turi būti sukurta informacinė sistema, kuri naudoja generatyvųjį dirbtinį intelektą.
> Parašomas prompt'as AI modeliui, kuriame paprašoma tokią sistemą sukurti pradžioje naudojant PlantUML diagramas, o po to – Mermaid.

---

## Pirmas prompt'as – PlantUML

````text
Tu esi patyręs programų sistemų architektas ir UML ekspertas. Padėk man suprojektuoti informacinę sistemą, kuri naudoja generatyvųjį dirbtinį intelektą.

SISTEMOS PAVADINIMAS: ReviewInsight AI – klientų atsiliepimų analizės sistema e. parduotuvei.

SISTEMOS TIKSLAS:
E. parduotuvė kasdien gauna daug klientų atsiliepimų apie prekes. Sistema turi automatiškai juos analizuoti naudodama generatyvųjį DI (Google Gemini API) ir padėti parduotuvės darbuotojams greičiau reaguoti į klientų problemas.

FUNKCINIAI REIKALAVIMAI:
1. Atsiliepimų priėmimas: vadybininkas įkelia iš e. parduotuvės platformos eksportuotą CSV/JSON failą su atsiliepimais (tekstas, įvertinimas 1–5, prekės ID, data).
2. Teksto paruošimas: valymas, kalbos nustatymas, per trumpų atsiliepimų atmetimas.
3. DI analizė per Gemini API, grąžinanti JSON: nuotaika su pasitikėjimo balu, problemų kategorijos, santrauka, skubumo lygis ir atsakymo klientui juodraštis.
4. Atsakymo validavimas: JSON struktūros patikrinimas, pakartotinė užklausa klaidos atveju.
5. Vadybininko peržiūra: DI atsakymo patvirtinimas, redagavimas arba atmetimas.
6. Suvestinės: nuotaikų ir problemų kategorijų pasiskirstymas.

AKTORIAI:
- Klientas (palieka atsiliepimą e. parduotuvėje ir gauna patvirtintą atsakymą; su sistema tiesiogiai nebendrauja);
- Parduotuvės vadybininkas (peržiūri analizę ir atsakymus);
- Administratorius (konfigūruoja sistemą, prompt'us, kategorijas);
- Išorinė sistema: Google Gemini API;
- Išorinė sistema: e. parduotuvės platforma (atsiliepimų šaltinis).

TECHNOLOGIJOS: Python, Gemini API (google-genai biblioteka), pandas, matplotlib, SQLite duomenų saugojimui, prototipas veikia Google Colab aplinkoje.

UŽDUOTIS:
Pirmiausia trumpai (iki 10 sakinių) aprašyk sistemos architektūrą. Tada sukurk šias PlantUML diagramas:
1. Užduočių diagrama (Use Case) – visi aktoriai ir jų panaudos atvejai, su <<include>> / <<extend>> ryšiais, kur tinka.
2. Komponentų (architektūros) diagrama – pagrindiniai sistemos moduliai ir jų ryšiai su išorinėmis sistemomis.
3. Klasių diagrama – pagrindinės klasės (pvz., Review, AnalysisResult, ReviewLoader, ReviewPreprocessor, GeminiAnalyzer, ResponseValidator, ReportGenerator) su atributais, metodais ir ryšiais.
4. Sekų diagrama (Sequence) – atsiliepimų apdorojimas nuo failo įkėlimo iki vadybininko patvirtinto atsakymo, įskaitant Gemini API iškvietimą ir klaidos atvejį (alt blokas).
5. Veiklos diagrama (Activity) – visas duomenų apdorojimo srautas su sprendimo taškais (pvz., ar atsiliepimas tinkamas, ar JSON validus).

REIKALAVIMAI ATSAKYMUI:
- Kiekviena diagrama atskirame kodo bloke, prasidedanti @startuml ir besibaigianti @enduml.
- Kiekviena diagrama turi turėti title.
- Sintaksė turi būti validi ir sugeneruojama per plantuml.com arba PlantText be klaidų.
- Po kiekviena diagrama – 2–3 sakinių paaiškinimas lietuvių kalba.
- Elementų pavadinimai diagramose – lietuvių kalba, klasių ir metodų pavadinimai – anglų kalba (kaip programiniame kode).
````

---

## Claude atsakymas – architektūros aprašas

ReviewInsight AI remiasi trijų sluoksnių architektūra, o jos centre yra duomenų apdorojimo konvejeris (angl. pipeline). Pateikimo sluoksnį sudaro Google Colab užrašinė su ipywidgets sąsajomis vadybininkui ir administratoriui. Taikymo sluoksnyje veikia orkestratorius ReviewPipeline, kuris paeiliui kviečia kelis modulius: failų įkėlimo (ReviewLoader, pandas), teksto paruošimo (ReviewPreprocessor), DI analizės (GeminiAnalyzer) ir atsakymų validavimo (ResponseValidator). GeminiAnalyzer jungiasi prie Gemini API per google-genai biblioteką. Užklausoje nurodomi response_mime_type="application/json" ir response_schema, todėl modelis grąžina struktūrizuotą JSON. Validatorius tikrina schemą ir reikšmių ribas. Jei atsakymas nevalidus arba įvyksta API klaida, užklausa kartojama su eksponentiniu laukimu (exponential backoff), bet ne daugiau kaip nustatytą kartų skaičių. Duomenų sluoksnį sudaro ReviewRepository ir SQLite duomenų bazė, kurioje saugomi atsiliepimai, analizės rezultatai, vadybininkų sprendimai ir versijuojami prompt'ų šablonai. Sistema veikia pagal human-in-the-loop principą: jokio DI atsakymo klientas negauna, kol jo nepatvirtina vadybininkas. ReportGenerator su pandas ir matplotlib kuria nuotaikų ir kategorijų suvestines, o patvirtinti atsakymai eksportuojami atgal į e. parduotuvės platformą.

---

## PlantUML diagramos

### 1. Užduočių (panaudos atvejų) diagrama

```plantuml
@startuml
title ReviewInsight AI – užduočių (panaudos atvejų) diagrama
left to right direction
skinparam packageStyle rectangle
skinparam actorStyle awesome
skinparam usecase {
BackgroundColor #F5F9FF
BorderColor #3A6EA5
}
actor "Parduotuvės\nvadybininkas" as Vad
actor "Administratorius" as Adm
actor "Klientas" as Kl
actor "E. parduotuvės\nplatforma" as Plat <<išorinė sistema>>
actor "Google Gemini API" as Gem <<išorinė sistema>>
rectangle "ReviewInsight AI" {
usecase "Įkelti atsiliepimų failą\n(CSV/JSON)" as UC1
usecase "Patikrinti failo\nformatą" as UC2
usecase "Paruošti atsiliepimų\ntekstą" as UC3
usecase "Analizuoti atsiliepimus\nsu DI" as UC4
usecase "Validuoti DI\natsakymą" as UC5
usecase "Pakartoti užklausą\nGemini API" as UC6
usecase "Peržiūrėti DI analizę" as UC7
usecase "Patvirtinti atsakymą" as UC8
usecase "Redaguoti atsakymo\njuodraštį" as UC9
usecase "Atmesti DI analizę" as UC10
usecase "Filtruoti pagal skubumą\nir kategoriją" as UC11
usecase "Peržiūrėti suvestines" as UC12
usecase "Generuoti\npasiskirstymo diagramas" as UC13
usecase "Eksportuoti patvirtintus\natsakymus" as UC14
usecase "Konfigūruoti prompt\nšablonus" as UC15
usecase "Valdyti problemų\nkategorijas" as UC16
usecase "Konfigūruoti Gemini API\nparametrus" as UC17
usecase "Peržiūrėti klaidų\nžurnalą" as UC18
}
Adm --|> Vad
Vad --> UC1
Vad --> UC4
Vad --> UC7
Vad --> UC12
Vad --> UC14
Adm --> UC15
Adm --> UC16
Adm --> UC17
Adm --> UC18
UC1 -- Plat
UC14 -- Plat
UC4 -- Gem
UC6 -- Gem
UC1 ..> UC2 : <<include>>
UC1 ..> UC3 : <<include>>
UC4 ..> UC5 : <<include>>
UC6 ..> UC5 : <<extend>>
UC8 ..> UC7 : <<extend>>
UC9 ..> UC7 : <<extend>>
UC10 ..> UC7 : <<extend>>
UC11 ..> UC7 : <<extend>>
UC12 ..> UC13 : <<include>>
Kl ..> Plat : palieka atsiliepimą,\ngauna atsakymą
note right of Kl
Klientas su ReviewInsight AI
tiesiogiai nebendrauja –
tik per e. parduotuvės platformą.
end note
@enduml
```

![Užduočių (panaudos atvejų) diagrama (PlantUML)](images/01_use_case_plantuml.png)

**Paaiškinimas:** Pagrindinis aktorius yra vadybininkas. Administratorius paveldi visas jo teises (generalizacija) ir papildomai konfigūruoja prompt'us, kategorijas bei API parametrus. `<<include>>` žymi privalomus žingsnius, pavyzdžiui, kiekviena DI analizė visada validuojama. `<<extend>>` žymi sąlyginius veiksmus: pakartotinė užklausa vyksta tik validacijai nepavykus, o patvirtinimas, redagavimas ir atmetimas yra alternatyvios peržiūros baigtys. Klientas pavaizduotas už sistemos ribų, nes su sistema jis sąveikauja tik netiesiogiai, per platformą.

### 2. Komponentų (architektūros) diagrama

```plantuml
@startuml
title ReviewInsight AI – komponentų (architektūros) diagrama
actor "Vadybininkas" as Vad
actor "Administratorius" as Adm
node "E. parduotuvės platforma" as SHOP
artifact "CSV / JSON\neksporto failas" as FILE
node "Google Colab aplinka" {
package "Pateikimo sluoksnis" {
[Vadybininko sąsaja\n(ipywidgets)] as UI
[Administravimo sąsaja\n(ipywidgets)] as AUI
}
package "Taikymo (verslo logikos) sluoksnis" {
[Apdorojimo orkestratorius\n(ReviewPipeline)] as PIPE
[Atsiliepimų įkėlimo modulis\n(ReviewLoader)] as LOAD
[Teksto paruošimo modulis\n(ReviewPreprocessor)] as PREP
[DI analizės modulis\n(GeminiAnalyzer)] as AN
[Atsakymų validavimo modulis\n(ResponseValidator)] as VAL
[Peržiūros modulis\n(ReviewApprovalService)] as APPR
[Ataskaitų modulis\n(ReportGenerator)] as REP
[Konfigūracijos modulis\n(ConfigManager)] as CFG
}
package "Duomenų sluoksnis" {
[Duomenų saugykla\n(ReviewRepository)] as REPO
database "SQLite DB" as DB
}
}
cloud "Google Cloud" {
[Google Gemini API] as GEM
}
interface "HTTPS / REST (JSON)" as GAPI
GEM - GAPI
Vad --> UI
Adm --> AUI
SHOP ..> FILE : eksportuoja
UI ..> FILE : įkelia
UI --> PIPE : paleidžia apdorojimą
UI --> APPR : peržiūra ir sprendimai
UI --> REP : suvestinės
AUI --> CFG : nustatymai
PIPE --> LOAD
PIPE --> PREP
PIPE --> AN
AN --> VAL : validuoja atsakymą
AN --> CFG : gauna prompt šabloną
AN ..> GAPI : google-genai SDK
LOAD --> REPO
PIPE --> REPO
APPR --> REPO
REP --> REPO
CFG --> REPO
REPO --> DB : SQL
APPR ..> SHOP : patvirtinti atsakymai\n(eksportas)
@enduml
```

![Komponentų (architektūros) diagrama (PlantUML)](images/02_component_plantuml.png)

**Paaiškinimas:** Diagramoje matyti sluoksninė architektūra. Visi taikymo moduliai prie duomenų kreipiasi tik per ReviewRepository, todėl vėliau SQLite būtų galima pakeisti, pavyzdžiui, PostgreSQL, nekeičiant verslo logikos. Su Gemini API tiesiogiai bendrauja vienintelis modulis, GeminiAnalyzer, veikiantis kaip adapteris, tad keičiant DI tiekėją reikėtų keisti tik jį. Su e. parduotuvės platforma integruojamasi per failų eksportą ir importą, nes tai paprasčiausias ir prototipui tinkamiausias būdas.

### 3. Klasių diagrama

```plantuml
@startuml
title ReviewInsight AI – klasių diagrama
skinparam classAttributeIconSize 0
skinparam packageStyle rectangle
package "Domeno modelis" {
enum ReviewStatus {
NEW
PREPROCESSED
REJECTED
ANALYZED
ANALYSIS_FAILED
APPROVED
DISMISSED
}
enum Sentiment {
POSITIVE
NEUTRAL
NEGATIVE
MIXED
}
enum UrgencyLevel {
LOW
MEDIUM
HIGH
CRITICAL
}
enum DecisionType {
APPROVED
EDITED
REJECTED
}
class Review {
- id: int
- product_id: str
- text: str
- clean_text: str
- rating: int
- created_at: datetime
- language: str
- status: ReviewStatus
+ is_valid(min_length: int): bool
+ mark_rejected(reason: str): None
+ to_dict(): dict
}
class AnalysisResult {
- id: int
- review_id: int
- sentiment: Sentiment
- confidence: float
- categories: List[str]
- summary: str
- urgency: UrgencyLevel
- reply_draft: str
- model_name: str
- prompt_version: int
- attempts: int
- created_at: datetime
+ {static} from_json(data: dict): AnalysisResult
+ to_dict(): dict
+ needs_attention(): bool
}
class ManagerDecision {
- id: int
- analysis_id: int
- decision: DecisionType
- final_reply: str
- manager_name: str
- comment: str
- decided_at: datetime
}
class Category {
- id: int
- name: str
- description: str
- is_active: bool
}
class PromptTemplate {
- id: int
- version: int
- template_text: str
- is_active: bool
+ render(review: Review, categories: List[str]): str
}
}
package "Apdorojimo logika" {
class ReviewPipeline {
- loader: ReviewLoader
- preprocessor: ReviewPreprocessor
- analyzer: GeminiAnalyzer
- repository: ReviewRepository
+ run(file_path: str): dict
}
class ReviewLoader {
- required_columns: List[str]
+ load_file(file_path: str): DataFrame
- parse_csv(file_path: str): DataFrame
- parse_json(file_path: str): DataFrame
- validate_columns(df: DataFrame): bool
+ to_reviews(df: DataFrame): List[Review]
}
class ReviewPreprocessor {
- min_length: int
- supported_languages: List[str]
+ process(reviews: List[Review]): List[Review]
+ clean_text(text: str): str
+ detect_language(text: str): str
+ is_too_short(text: str): bool
}
class GeminiAnalyzer {
- client: genai.Client
- model_name: str
- temperature: float
- max_retries: int
- validator: ResponseValidator
- config: ConfigManager
+ analyze(review: Review): AnalysisResult
- build_prompt(review: Review): str
- call_api(prompt: str): str
- backoff(attempt: int): None
}
class ResponseValidator {
- schema: dict
- allowed_categories: List[str]
- errors: List[str]
+ validate(raw_json: str): bool
+ parse(raw_json: str): dict
+ get_errors(): List[str]
}
class ReviewApprovalService {
- repository: ReviewRepository
+ get_pending(sort_by: str): List[AnalysisResult]
+ approve(analysis_id: int): ManagerDecision
+ edit(analysis_id: int, new_text: str): ManagerDecision
+ reject(analysis_id: int, reason: str): ManagerDecision
+ export_approved(file_path: str): None
}
class ReportGenerator {
- repository: ReviewRepository
+ sentiment_distribution(): DataFrame
+ category_distribution(): DataFrame
+ plot_sentiment(): Figure
+ plot_categories(): Figure
+ export_report(file_path: str): None
}
class ConfigManager {
- repository: ReviewRepository
+ get_active_prompt(): PromptTemplate
+ update_prompt(text: str): PromptTemplate
+ get_categories(): List[Category]
+ add_category(name: str, description: str): Category
}
class AnalysisError {
- review_id: int
- attempts: int
- last_error: str
}
}
package "Duomenų prieiga" {
class ReviewRepository {
- db_path: str
- connection: Connection
+ save_reviews(reviews: List[Review]): None
+ update_status(review_id: int, status: ReviewStatus): None
+ save_analysis(result: AnalysisResult): int
+ save_decision(decision: ManagerDecision): int
+ get_pending_reviews(): List[AnalysisResult]
+ get_statistics(): DataFrame
}
}
Review "1" *-- "0..1" AnalysisResult : turi
AnalysisResult "1" *-- "0..1" ManagerDecision : peržiūrima
AnalysisResult "*" --> "1..*" Category : priskiriama
AnalysisResult --> PromptTemplate : sukurta pagal
ReviewPipeline o-- ReviewLoader
ReviewPipeline o-- ReviewPreprocessor
ReviewPipeline o-- GeminiAnalyzer
ReviewPipeline o-- ReviewRepository
GeminiAnalyzer --> ResponseValidator : naudoja
GeminiAnalyzer --> ConfigManager : gauna šabloną
ConfigManager "1" --> "*" PromptTemplate : valdo
ConfigManager "1" --> "*" Category : valdo
ReviewLoader ..> Review : <<create>>
ReviewPreprocessor ..> Review : apdoroja
GeminiAnalyzer ..> AnalysisResult : <<create>>
GeminiAnalyzer ..> AnalysisError : <<throw>>
ReviewApprovalService ..> ManagerDecision : <<create>>
ReviewApprovalService --> ReviewRepository
ReportGenerator --> ReviewRepository
ConfigManager --> ReviewRepository
@enduml
```

![Klasių diagrama (PlantUML)](images/03_class_plantuml.png)

**Paaiškinimas:** Klasės suskirstytos į tris paketus: domeno modelį (duomenų objektai ir enum tipai), apdorojimo logiką (servisai) ir duomenų prieigą. Kompozicija Review → AnalysisResult → ManagerDecision atspindi atsiliepimo gyvavimo ciklą: analizė ir sprendimas be atsiliepimo neegzistuoja. AnalysisResult saugo model_name ir prompt_version, todėl visada galima atsekti, kuriuo modeliu ir kuria prompt'o versija buvo gautas rezultatas. Tai svarbu kokybės auditui.

### 4. Sekų diagrama

```plantuml
@startuml
title ReviewInsight AI – atsiliepimų apdorojimo sekų diagrama
autonumber
actor "Vadybininkas" as Vad
boundary "Vadybininko\nsąsaja" as UI
control "ReviewPipeline" as PIPE
participant "ReviewLoader" as LOAD
participant "ReviewPreprocessor" as PREP
participant "GeminiAnalyzer" as AN
participant "ResponseValidator" as VAL
participant "ReviewApprovalService" as APPR
database "SQLite DB" as DB
participant "Google Gemini API" as GEM <<išorinė sistema>>
== Failo įkėlimas ir teksto paruošimas ==
Vad -> UI : įkelia CSV/JSON failą
UI -> PIPE : run(file_path)
PIPE -> LOAD : load_file(file_path)
LOAD -> LOAD : validate_columns(df)
LOAD --> PIPE : List[Review]
PIPE -> DB : save_reviews(reviews)
PIPE -> PREP : process(reviews)
loop kiekvienam atsiliepimui
PREP -> PREP : clean_text(), detect_language()
alt tekstas per trumpas arba kalba nepalaikoma
PREP -> PREP : status = REJECTED
else atsiliepimas tinkamas
PREP -> PREP : status = PREPROCESSED
end
end
PREP --> PIPE : paruošti atsiliepimai
== DI analizė ==
loop kiekvienam paruoštam atsiliepimui
PIPE -> AN : analyze(review)
AN -> AN : build_prompt(review)
loop kol atsakymas nevalidus ir bandymų < max_retries
AN -> GEM : generate_content(prompt, response_schema)
alt sėkmingas API atsakymas
GEM --> AN : JSON tekstas
AN -> VAL : validate(raw_json)
alt JSON validus
VAL --> AN : true
else JSON nevalidus
VAL --> AN : false, get_errors()
AN -> AN : papildyti prompt klaidų aprašu
end
else API klaida (429, 5xx, timeout)
GEM --> AN : klaidos pranešimas
AN -> AN : backoff(attempt)
end
end
alt analizė sėkminga
AN --> PIPE : AnalysisResult
PIPE -> DB : save_analysis(result)
else viršytas bandymų skaičius
AN --> PIPE : AnalysisError
PIPE -> DB : update_status(review_id, ANALYSIS_FAILED)
end
end
PIPE --> UI : apdorojimo santrauka
UI --> Vad : rodo statistiką (apdorota / atmesta / klaidos)
== Vadybininko peržiūra ==
Vad -> UI : atidaro peržiūros sąrašą
UI -> APPR : get_pending(sort_by="urgency")
APPR -> DB : get_pending_reviews()
DB --> APPR : analizių sąrašas
APPR --> UI : analizės pagal skubumą
UI --> Vad : rodo analizę ir atsakymo juodraštį
alt vadybininkas patvirtina
Vad -> UI : patvirtinti
UI -> APPR : approve(analysis_id)
else vadybininkas redaguoja
Vad -> UI : pataiso juodraštį
UI -> APPR : edit(analysis_id, new_text)
else vadybininkas atmeta
Vad -> UI : atmesti
UI -> APPR : reject(analysis_id, reason)
end
APPR -> DB : save_decision(decision)
UI --> Vad : sprendimas išsaugotas
@enduml
```

![Sekų diagrama (PlantUML)](images/04_sequence_plantuml.png)

**Paaiškinimas:** Diagrama suskirstyta į tris fazes: įkėlimą ir paruošimą, DI analizę ir vadybininko peržiūrą. Vidinis loop su įdėtais alt blokais aprašo patikimumo mechanizmą. Kai JSON nevalidus, į prompt'ą įtraukiamas klaidų aprašas, kad modelis galėtų pasitaisyti. Kai įvyksta API klaida (pvz., 429 dėl užklausų limito), taikomas eksponentinis laukimas. Viršijus bandymų limitą, atsiliepimas pažymimas ANALYSIS_FAILED ir neblokuoja likusių atsiliepimų apdorojimo.

### 5. Veiklos diagrama

```plantuml
@startuml
title ReviewInsight AI – duomenų apdorojimo veiklos diagrama
|Vadybininkas|
start
:Eksportuoti atsiliepimus iš e. parduotuvės\nir įkelti CSV/JSON failą;
|Sistema|
:Nuskaityti failą (pandas);
if (Failo formatas ir privalomi stulpeliai teisingi?) then (taip)
:Išsaugoti atsiliepimus DB\n(būsena NEW);
else (ne)
:Parodyti klaidos pranešimą;
stop
endif
while (Yra neapdorotų atsiliepimų?) is (taip)
:Išvalyti tekstą\n(HTML žymės, tarpai, dublikatai);
:Nustatyti kalbą;
if (Tekstas pakankamo ilgio\nir kalba palaikoma?) then (taip)
:Sudaryti užklausą pagal\naktyvų prompt šabloną;
repeat
|Gemini API|
:Sugeneruoti analizę\nJSON formatu;
|Sistema|
if (Gauta API klaida?) then (taip)
:Palaukti (exponential backoff);
else (ne)
:Validuoti JSON struktūrą\nir reikšmių ribas;
endif
repeat while (Rezultatas nevalidus\nir bandymų < 3?) is (taip) not (ne)
if (JSON validus?) then (taip)
:Išsaugoti AnalysisResult\n(būsena ANALYZED);
if (Skubumas HIGH/CRITICAL arba\npasitikėjimo balas < 0,6?) then (taip)
:Pažymėti kaip prioritetinę;
endif
else (ne)
:Pažymėti ANALYSIS_FAILED\nir įrašyti į klaidų žurnalą;
endif
else (ne)
:Atmesti atsiliepimą\n(būsena REJECTED);
endif
endwhile (ne)
:Surikiuoti analizes pagal skubumą;
|Vadybininkas|
while (Yra neperžiūrėtų analizių?) is (taip)
:Peržiūrėti nuotaiką, kategorijas,\nsantrauką ir atsakymo juodraštį;
if (Analizė ir juodraštis tinkami?) then (taip)
:Patvirtinti atsakymą;
elseif (Juodraštį galima pataisyti?) then (taip)
:Redaguoti ir patvirtinti atsakymą;
else (ne)
:Atmesti DI analizę;
endif
|Sistema|
:Išsaugoti vadybininko sprendimą DB;
endwhile (ne)
|Sistema|
fork
:Sugeneruoti nuotaikų\npasiskirstymo diagramą;
fork again
:Sugeneruoti problemų kategorijų\npasiskirstymo diagramą;
end fork
:Eksportuoti patvirtintus atsakymus\ne. parduotuvės platformai;
stop
@enduml
```

![Veiklos diagrama (PlantUML)](images/05_activity_plantuml.png)

**Paaiškinimas:** Takeliai (swimlanes) parodo atsakomybių pasiskirstymą tarp vadybininko, sistemos ir išorinio Gemini API. Pagrindiniai sprendimo taškai yra failo formato tikrinimas, atsiliepimo tinkamumas (ilgis ir kalba) ir JSON validumas. Pastarasis įgyvendintas repeat while ciklu su trijų bandymų limitu. Papildomas prioriteto žymėjimas pagal skubumą ir žemą pasitikėjimo balą padeda vadybininkui pirmiausia reaguoti į svarbiausias problemas, o fork blokas parodo, kad abi suvestinės gali būti generuojamos lygiagrečiai.

---

## Antras prompt'as – Mermaid

````text
Dabar konvertuok visas 5 aukščiau sukurtas PlantUML diagramas į Mermaid sintaksę, išlaikydamas tą patį turinį, elementus, pavadinimus ir ryšius.

KONVERTAVIMO TAISYKLĖS:
1. Užduočių diagrama → flowchart LR. Mermaid neturi atskiro use case diagramos tipo, todėl:
- aktorius vaizduok kaip ((apskritimus)), išorines sistemas – kaip [[dvigubus stačiakampius]];
- panaudos atvejus vaizduok kaip ([suapvalintus mazgus]);
- sistemos ribas „ReviewInsight AI“ vaizduok kaip subgraph;
- <<include>> ir <<extend>> ryšius vaizduok punktyrinėmis rodyklėmis su tekstu, pvz. A -.->|"«include»"| B.
2. Komponentų diagrama → flowchart TB su subgraph blokais sistemos sluoksniams (pvz., duomenų įkėlimas, apdorojimas, DI analizė, saugojimas, ataskaitos) ir atskirais mazgais išorinėms sistemoms (Gemini API, e. parduotuvės platforma).
3. Klasių diagrama → classDiagram. Išlaikyk atributų ir metodų tipus, matomumą (+, -), ryšių tipus (asociacija, kompozicija, priklausomybė) ir kardinalumą.
4. Sekų diagrama → sequenceDiagram. Išlaikyk visus dalyvius, alt/else blokus klaidos atvejui, loop blokus (jei yra) ir aktyvacijas.
5. Veiklos diagrama → flowchart TD. Sprendimo taškus vaizduok kaip {rombus}, pradžią ir pabaigą kaip ((apskritimus)), o šakas žymėk tekstu ant rodyklių (pvz., |Taip| / |Ne|).

REIKALAVIMAI ATSAKYMUI:
- Kiekviena diagrama atskirame kodo bloke, pažymėtame ```mermaid, kad ją būtų galima tiesiogiai įklijuoti į GitHub README.md failą.
- Kiekviena diagrama turi turėti pavadinimą (naudok Mermaid frontmatter: ---\ntitle: ...\n---).
- Sintaksė turi veikti mermaid.live redaktoriuje ir GitHub be klaidų: mazgų tekstus su lietuviškomis raidėmis, skliaustais, dvitaškiais ar kitais specialiaisiais simboliais rašyk kabutėse, o mazgų ID naudok tik lotyniškas raides be tarpų (pvz., UC1, ReviewLoader).
- Išlaikyk tą pačią kalbų taisyklę: elementų pavadinimai lietuvių kalba, klasės ir metodai – anglų kalba.
- Po kiekviena diagrama 1–2 sakiniais nurodyk, ar konvertuojant kas nors buvo prarasta ar pakeista, palyginti su PlantUML versija.
- Pabaigoje pateik lentelę: Diagramos tipas | PlantUML sintaksė | Mermaid atitikmuo | Pastabos.
````

---

## Mermaid diagramos

GitHub šias diagramas atvaizduoja automatiškai iš ` ```mermaid ` kodo blokų.

### 1. Užduočių (panaudos atvejų) diagrama

```mermaid
---
title: ReviewInsight AI – užduočių (panaudos atvejų) diagrama
---
flowchart LR
Vad(("Parduotuvės<br/>vadybininkas"))
Adm(("Administratorius"))
Kl(("Klientas"))
Plat[["«išorinė sistema»<br/>E. parduotuvės platforma"]]
Gem[["«išorinė sistema»<br/>Google Gemini API"]]
NKl["Klientas su ReviewInsight AI<br/>tiesiogiai nebendrauja –<br/>tik per e. parduotuvės platformą."]
subgraph SYS["ReviewInsight AI"]
UC1(["Įkelti atsiliepimų failą<br/>(CSV/JSON)"])
UC2(["Patikrinti failo<br/>formatą"])
UC3(["Paruošti atsiliepimų<br/>tekstą"])
UC4(["Analizuoti atsiliepimus<br/>su DI"])
UC5(["Validuoti DI<br/>atsakymą"])
UC6(["Pakartoti užklausą<br/>Gemini API"])
UC7(["Peržiūrėti DI analizę"])
UC8(["Patvirtinti atsakymą"])
UC9(["Redaguoti atsakymo<br/>juodraštį"])
UC10(["Atmesti DI analizę"])
UC11(["Filtruoti pagal skubumą<br/>ir kategoriją"])
UC12(["Peržiūrėti suvestines"])
UC13(["Generuoti<br/>pasiskirstymo diagramas"])
UC14(["Eksportuoti patvirtintus<br/>atsakymus"])
UC15(["Konfigūruoti prompt<br/>šablonus"])
UC16(["Valdyti problemų<br/>kategorijas"])
UC17(["Konfigūruoti Gemini API<br/>parametrus"])
UC18(["Peržiūrėti klaidų<br/>žurnalą"])
end
Adm ==>|"«generalizacija»"| Vad
Vad --> UC1
Vad --> UC4
Vad --> UC7
Vad --> UC12
Vad --> UC14
Adm --> UC15
Adm --> UC16
Adm --> UC17
Adm --> UC18
UC1 --- Plat
UC14 --- Plat
UC4 --- Gem
UC6 --- Gem
UC1 -.->|"«include»"| UC2
UC1 -.->|"«include»"| UC3
UC4 -.->|"«include»"| UC5
UC6 -.->|"«extend»"| UC5
UC8 -.->|"«extend»"| UC7
UC9 -.->|"«extend»"| UC7
UC10 -.->|"«extend»"| UC7
UC11 -.->|"«extend»"| UC7
UC12 -.->|"«include»"| UC13
Kl -.->|"palieka atsiliepimą,<br/>gauna atsakymą"| Plat
Kl -.- NKl
classDef aktorius fill:#FFFFFF,stroke:#333333,stroke-width:2px
classDef isorine fill:#EEEEEE,stroke:#555555,stroke-width:2px
classDef uc fill:#F5F9FF,stroke:#3A6EA5
classDef pastaba fill:#FFF8DC,stroke:#C9A227
class Vad,Adm,Kl aktorius
class Plat,Gem isorine
class UC1,UC2,UC3,UC4,UC5,UC6,UC7,UC8,UC9,UC10,UC11,UC12,UC13,UC14,UC15,UC16,UC17,UC18 uc
class NKl pastaba
style SYS fill:#FAFCFF,stroke:#3A6EA5,stroke-width:2px
```

**Pokyčiai lyginant su PlantUML:** Mermaid neturi generalizacijos rodyklės su tuščiavidure trikampe galvute, todėl Administratorius → Vadybininkas pavaizduota stora rodykle su užrašu «generalizacija». PlantUML pastaba (note) pakeista geltonu mazgu, sujungtu punktyrine linija, o aktoriai rodomi apskritimais vietoj žmogeliukų.

### 2. Komponentų (architektūros) diagrama

```mermaid
---
title: ReviewInsight AI – komponentų (architektūros) diagrama
---
flowchart TB
Vad(("Vadybininkas"))
Adm(("Administratorius"))
SHOP[["«išorinė sistema»<br/>E. parduotuvės platforma"]]
FILE[/"«artifact»<br/>CSV / JSON eksporto failas"/]
subgraph COLAB["Google Colab aplinka"]
direction TB
subgraph PRES["Pateikimo sluoksnis"]
UI["Vadybininko sąsaja<br/>(ipywidgets)"]
AUI["Administravimo sąsaja<br/>(ipywidgets)"]
end
subgraph APP["Taikymo (verslo logikos) sluoksnis"]
PIPE["Apdorojimo orkestratorius<br/>(ReviewPipeline)"]
LOAD["Atsiliepimų įkėlimo modulis<br/>(ReviewLoader)"]
PREP["Teksto paruošimo modulis<br/>(ReviewPreprocessor)"]
AN["DI analizės modulis<br/>(GeminiAnalyzer)"]
VAL["Atsakymų validavimo modulis<br/>(ResponseValidator)"]
APPR["Peržiūros modulis<br/>(ReviewApprovalService)"]
REP["Ataskaitų modulis<br/>(ReportGenerator)"]
CFG["Konfigūracijos modulis<br/>(ConfigManager)"]
end
subgraph DATA["Duomenų sluoksnis"]
REPO["Duomenų saugykla<br/>(ReviewRepository)"]
DB[("SQLite DB")]
end
end
subgraph GCLOUD["Google Cloud"]
GEM[["«išorinė sistema»<br/>Google Gemini API"]]
GAPI(("HTTPS / REST<br/>(JSON)"))
end
Vad --> UI
Adm --> AUI
SHOP -.->|"eksportuoja"| FILE
UI -.->|"įkelia"| FILE
UI -->|"paleidžia apdorojimą"| PIPE
UI -->|"peržiūra ir sprendimai"| APPR
UI -->|"suvestinės"| REP
AUI -->|"nustatymai"| CFG
PIPE --> LOAD
PIPE --> PREP
PIPE --> AN
AN -->|"validuoja atsakymą"| VAL
AN -->|"gauna prompt šabloną"| CFG
GEM --- GAPI
AN -.->|"google-genai SDK"| GAPI
LOAD --> REPO
PIPE --> REPO
APPR --> REPO
REP --> REPO
CFG --> REPO
REPO -->|"SQL"| DB
APPR -.->|"patvirtinti atsakymai<br/>(eksportas)"| SHOP
classDef aktorius fill:#FFFFFF,stroke:#333333,stroke-width:2px
classDef isorine fill:#EEEEEE,stroke:#555555,stroke-width:2px
classDef komponentas fill:#F5F9FF,stroke:#3A6EA5
classDef sasaja fill:#FFFFFF,stroke:#3A6EA5,stroke-width:2px
class Vad,Adm aktorius
class SHOP,GEM,FILE isorine
class UI,AUI,PIPE,LOAD,PREP,AN,VAL,APPR,REP,CFG,REPO komponentas
class GAPI sasaja
```

**Pokyčiai lyginant su PlantUML:** UML komponento ženkliukas ir sąsajos „ledinuko“ (lollipop) notacija neturi atitikmenų. Komponentai rodomi stačiakampiais, sąsaja apskritimu, artefaktas lygiagretainiu, o node ir cloud elementai virto subgraph blokais. Sluoksnių struktūra, visi moduliai ir ryšiai liko tokie patys.

### 3. Klasių diagrama

```mermaid
---
title: ReviewInsight AI – klasių diagrama
---
classDiagram
namespace Domeno_modelis {
class ReviewStatus {
<<enumeration>>
NEW
PREPROCESSED
REJECTED
ANALYZED
ANALYSIS_FAILED
APPROVED
DISMISSED
}
class Sentiment {
<<enumeration>>
POSITIVE
NEUTRAL
NEGATIVE
MIXED
}
class UrgencyLevel {
<<enumeration>>
LOW
MEDIUM
HIGH
CRITICAL
}
class DecisionType {
<<enumeration>>
APPROVED
EDITED
REJECTED
}
class Review {
-id: int
-product_id: str
-text: str
-clean_text: str
-rating: int
-created_at: datetime
-language: str
-status: ReviewStatus
+is_valid(min_length: int) bool
+mark_rejected(reason: str) None
+to_dict() dict
}
class AnalysisResult {
-id: int
-review_id: int
-sentiment: Sentiment
-confidence: float
-categories: List~str~
-summary: str
-urgency: UrgencyLevel
-reply_draft: str
-model_name: str
-prompt_version: int
-attempts: int
-created_at: datetime
+from_json(data: dict) AnalysisResult$
+to_dict() dict
+needs_attention() bool
}
class ManagerDecision {
-id: int
-analysis_id: int
-decision: DecisionType
-final_reply: str
-manager_name: str
-comment: str
-decided_at: datetime
}
class Category {
-id: int
-name: str
-description: str
-is_active: bool
}
class PromptTemplate {
-id: int
-version: int
-template_text: str
-is_active: bool
+render(review: Review, categories: List~str~) str
}
}
namespace Apdorojimo_logika {
class ReviewPipeline {
-loader: ReviewLoader
-preprocessor: ReviewPreprocessor
-analyzer: GeminiAnalyzer
-repository: ReviewRepository
+run(file_path: str) dict
}
class ReviewLoader {
-required_columns: List~str~
+load_file(file_path: str) DataFrame
-parse_csv(file_path: str) DataFrame
-parse_json(file_path: str) DataFrame
-validate_columns(df: DataFrame) bool
+to_reviews(df: DataFrame) List~Review~
}
class ReviewPreprocessor {
-min_length: int
-supported_languages: List~str~
+process(reviews: List~Review~) List~Review~
+clean_text(text: str) str
+detect_language(text: str) str
+is_too_short(text: str) bool
}
class GeminiAnalyzer {
-client: genai.Client
-model_name: str
-temperature: float
-max_retries: int
-validator: ResponseValidator
-config: ConfigManager
+analyze(review: Review) AnalysisResult
-build_prompt(review: Review) str
-call_api(prompt: str) str
-backoff(attempt: int) None
}
class ResponseValidator {
-schema: dict
-allowed_categories: List~str~
-errors: List~str~
+validate(raw_json: str) bool
+parse(raw_json: str) dict
+get_errors() List~str~
}
class ReviewApprovalService {
-repository: ReviewRepository
+get_pending(sort_by: str) List~AnalysisResult~
+approve(analysis_id: int) ManagerDecision
+edit(analysis_id: int, new_text: str) ManagerDecision
+reject(analysis_id: int, reason: str) ManagerDecision
+export_approved(file_path: str) None
}
class ReportGenerator {
-repository: ReviewRepository
+sentiment_distribution() DataFrame
+category_distribution() DataFrame
+plot_sentiment() Figure
+plot_categories() Figure
+export_report(file_path: str) None
}
class ConfigManager {
-repository: ReviewRepository
+get_active_prompt() PromptTemplate
+update_prompt(text: str) PromptTemplate
+get_categories() List~Category~
+add_category(name: str, description: str) Category
}
class AnalysisError {
-review_id: int
-attempts: int
-last_error: str
}
}
namespace Duomenu_prieiga {
class ReviewRepository {
-db_path: str
-connection: Connection
+save_reviews(reviews: List~Review~) None
+update_status(review_id: int, status: ReviewStatus) None
+save_analysis(result: AnalysisResult) int
+save_decision(decision: ManagerDecision) int
+get_pending_reviews() List~AnalysisResult~
+get_statistics() DataFrame
}
}
Review "1" *-- "0..1" AnalysisResult : turi
AnalysisResult "1" *-- "0..1" ManagerDecision : peržiūrima
AnalysisResult "*" --> "1..*" Category : priskiriama
AnalysisResult --> PromptTemplate : sukurta pagal
ReviewPipeline o-- ReviewLoader
ReviewPipeline o-- ReviewPreprocessor
ReviewPipeline o-- GeminiAnalyzer
ReviewPipeline o-- ReviewRepository
GeminiAnalyzer --> ResponseValidator : naudoja
GeminiAnalyzer --> ConfigManager : gauna šabloną
ConfigManager "1" --> "*" PromptTemplate : valdo
ConfigManager "1" --> "*" Category : valdo
ReviewLoader ..> Review : «create»
ReviewPreprocessor ..> Review : apdoroja
GeminiAnalyzer ..> AnalysisResult : «create»
GeminiAnalyzer ..> AnalysisError : «throw»
ReviewApprovalService ..> ManagerDecision : «create»
ReviewApprovalService --> ReviewRepository
ReportGenerator --> ReviewRepository
ConfigManager --> ReviewRepository
```

**Pokyčiai lyginant su PlantUML:** Paketai virto Mermaid namespace blokais. Jų pavadinimai negali turėti tarpų ir lietuviškų raidžių, todėl rašoma Domeno_modelis, Apdorojimo_logika, Duomenu_prieiga. Generiniai tipai užrašyti Mermaid sintakse List~str~, kuri atvaizduojama kaip `List<str>`. Statinis metodas pažymėtas $ (pabrauktas šriftas), o visi atributai, metodai, matomumas, ryšių tipai ir kardinalumai išlaikyti.

### 4. Sekų diagrama

```mermaid
---
title: ReviewInsight AI – atsiliepimų apdorojimo sekų diagrama
---
sequenceDiagram
autonumber
actor Vad as Vadybininkas
participant UI as «boundary»<br/>Vadybininko sąsaja
participant PIPE as «control»<br/>ReviewPipeline
participant LOAD as ReviewLoader
participant PREP as ReviewPreprocessor
participant AN as GeminiAnalyzer
participant VAL as ResponseValidator
participant APPR as ReviewApprovalService
participant DB as «database»<br/>SQLite DB
participant GEM as «išorinė sistema»<br/>Google Gemini API
rect rgb(235, 245, 255)
Note over Vad,GEM: Failo įkėlimas ir teksto paruošimas
Vad->>+UI: įkelia CSV/JSON failą
UI->>+PIPE: run(file_path)
PIPE->>+LOAD: load_file(file_path)
LOAD->>LOAD: validate_columns(df)
LOAD-->>-PIPE: List[Review]
PIPE->>DB: save_reviews(reviews)
PIPE->>+PREP: process(reviews)
loop kiekvienam atsiliepimui
PREP->>PREP: clean_text(), detect_language()
alt tekstas per trumpas arba kalba nepalaikoma
PREP->>PREP: status = REJECTED
else atsiliepimas tinkamas
PREP->>PREP: status = PREPROCESSED
end
end
PREP-->>-PIPE: paruošti atsiliepimai
end
rect rgb(255, 245, 230)
Note over Vad,GEM: DI analizė
loop kiekvienam paruoštam atsiliepimui
PIPE->>+AN: analyze(review)
AN->>AN: build_prompt(review)
loop kol atsakymas nevalidus ir bandymų mažiau nei max_retries
AN->>GEM: generate_content(prompt, response_schema)
activate GEM
alt sėkmingas API atsakymas
GEM-->>AN: JSON tekstas
AN->>VAL: validate(raw_json)
activate VAL
alt JSON validus
VAL-->>AN: true
else JSON nevalidus
VAL-->>AN: false, get_errors()
AN->>AN: papildyti prompt klaidų aprašu
end
deactivate VAL
else API klaida (429, 5xx, timeout)
GEM-->>AN: klaidos pranešimas
AN->>AN: backoff(attempt)
end
deactivate GEM
end
alt analizė sėkminga
AN-->>PIPE: AnalysisResult
PIPE->>DB: save_analysis(result)
else viršytas bandymų skaičius
AN-->>PIPE: AnalysisError
PIPE->>DB: update_status(review_id, ANALYSIS_FAILED)
end
deactivate AN
end
PIPE-->>-UI: apdorojimo santrauka
UI-->>-Vad: rodo statistiką (apdorota / atmesta / klaidos)
end
rect rgb(235, 250, 235)
Note over Vad,GEM: Vadybininko peržiūra
Vad->>+UI: atidaro peržiūros sąrašą
UI->>+APPR: get_pending(sort_by="urgency")
APPR->>+DB: get_pending_reviews()
DB-->>-APPR: analizių sąrašas
APPR-->>-UI: analizės pagal skubumą
UI-->>Vad: rodo analizę ir atsakymo juodraštį
alt vadybininkas patvirtina
Vad->>UI: patvirtinti
UI->>APPR: approve(analysis_id)
else vadybininkas redaguoja
Vad->>UI: pataiso juodraštį
UI->>APPR: edit(analysis_id, new_text)
else vadybininkas atmeta
Vad->>UI: atmesti
UI->>APPR: reject(analysis_id, reason)
end
APPR->>DB: save_decision(decision)
UI-->>-Vad: sprendimas išsaugotas
end
```

**Pokyčiai lyginant su PlantUML:** PlantUML fazių skirtukai `(== ... ==)` pakeisti spalvotais rect blokais su pastabomis, o dalyvių tipai (boundary, control, database) rodomi stereotipais pavadinimuose, nes Mermaid tokių formų neturi. Aktyvacijos juostos PlantUML versijoje nebuvo nurodytos, bet čia pridėtos pagal jūsų prašymą. `<` simbolis ciklo sąlygoje pakeistas žodžiais „mažiau nei“, kad sintaksė nesulūžtų.

### 5. Veiklos diagrama

```mermaid
---
title: ReviewInsight AI – duomenų apdorojimo veiklos diagrama
---
flowchart TD
S(("Pradžia"))
A1["Eksportuoti atsiliepimus iš e. parduotuvės<br/>ir įkelti CSV/JSON failą"]
A2["Nuskaityti failą (pandas)"]
D1{"Failo formatas ir privalomi<br/>stulpeliai teisingi?"}
A3["Parodyti klaidos pranešimą"]
F1(("Pabaiga"))
A4["Išsaugoti atsiliepimus DB<br/>(būsena NEW)"]
D2{"Yra neapdorotų<br/>atsiliepimų?"}
A5["Išvalyti tekstą<br/>(HTML žymės, tarpai, dublikatai)"]
A6["Nustatyti kalbą"]
D3{"Tekstas pakankamo ilgio<br/>ir kalba palaikoma?"}
A7["Atmesti atsiliepimą<br/>(būsena REJECTED)"]
A8["Sudaryti užklausą pagal<br/>aktyvų prompt šabloną"]
A9["Sugeneruoti analizę<br/>JSON formatu"]
D4{"Gauta API klaida?"}
A10["Palaukti<br/>(exponential backoff)"]
A11["Validuoti JSON struktūrą<br/>ir reikšmių ribas"]
D5{"Rezultatas nevalidus<br/>ir bandymų #lt; 3?"}
D6{"JSON validus?"}
A12["Išsaugoti AnalysisResult<br/>(būsena ANALYZED)"]
D7{"Skubumas HIGH/CRITICAL arba<br/>pasitikėjimo balas #lt; 0,6?"}
A13["Pažymėti kaip prioritetinę"]
A14["Pažymėti ANALYSIS_FAILED<br/>ir įrašyti į klaidų žurnalą"]
A15["Surikiuoti analizes<br/>pagal skubumą"]
D8{"Yra neperžiūrėtų<br/>analizių?"}
A16["Peržiūrėti nuotaiką, kategorijas,<br/>santrauką ir atsakymo juodraštį"]
D9{"Analizė ir juodraštis<br/>tinkami?"}
A17["Patvirtinti atsakymą"]
D10{"Juodraštį galima<br/>pataisyti?"}
A18["Redaguoti ir patvirtinti<br/>atsakymą"]
A19["Atmesti DI analizę"]
A20["Išsaugoti vadybininko<br/>sprendimą DB"]
FORK["lygiagretus vykdymas (fork)"]
A21["Sugeneruoti nuotaikų<br/>pasiskirstymo diagramą"]
A22["Sugeneruoti problemų kategorijų<br/>pasiskirstymo diagramą"]
JOIN["sujungimas (join)"]
A23["Eksportuoti patvirtintus atsakymus<br/>e. parduotuvės platformai"]
F2(("Pabaiga"))
S --> A1 --> A2 --> D1
D1 -->|Ne| A3 --> F1
D1 -->|Taip| A4 --> D2
D2 -->|Taip| A5 --> A6 --> D3
D3 -->|Ne| A7 --> D2
D3 -->|Taip| A8 --> A9 --> D4
D4 -->|Taip| A10 --> D5
D4 -->|Ne| A11 --> D5
D5 -->|Taip| A9
D5 -->|Ne| D6
D6 -->|Taip| A12 --> D7
D7 -->|Taip| A13 --> D2
D7 -->|Ne| D2
D6 -->|Ne| A14 --> D2
D2 -->|Ne| A15 --> D8
D8 -->|Taip| A16 --> D9
D9 -->|Taip| A17 --> A20
D9 -->|Ne| D10
D10 -->|Taip| A18 --> A20
D10 -->|Ne| A19 --> A20
A20 --> D8
D8 -->|Ne| FORK
FORK --> A21 --> JOIN
FORK --> A22 --> JOIN
JOIN --> A23 --> F2
subgraph LEG["Takeliai (spalvų legenda)"]
L1["Vadybininkas"]
L2["Sistema"]
L3["Gemini API"]
end
classDef vad fill:#E8F5E9,stroke:#2E7D32
classDef sis fill:#E3F2FD,stroke:#1565C0
classDef gem fill:#FFF3E0,stroke:#E65100
classDef ribos fill:#FFFFFF,stroke:#333333,stroke-width:2px
classDef sinch fill:#333333,stroke:#333333,color:#FFFFFF
class A1,D8,A16,D9,A17,D10,A18,A19,L1 vad
class A2,D1,A3,A4,D2,A5,A6,D3,A7,A8,D4,A10,A11,D5,D6,A12,D7,A13,A14,A15,A20,A21,A22,A23,L2 sis
class A9,L3 gem
class S,F1,F2 ribos
class FORK,JOIN sinch
```

**Pokyčiai lyginant su PlantUML:** Mermaid flowchart nepalaiko takelių (swimlanes), todėl atsakomybės pažymėtos spalvomis su legenda. fork/join sinchronizacijos juostos pakeistos tamsiais mazgais, o repeat while ciklas pavaizduotas grįžtamąja rodykle nuo D5 į A9. Simbolis `<` užrašytas Mermaid esybės kodu `#lt;`, kad jis nebūtų interpretuojamas kaip HTML.

---

[← Grįžti į projekto aprašymą](../README.md)
