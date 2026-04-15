<table border="0">
 <tr>
    <td style="width:300px; vertical-align:middle; text-align:center;">
      <img src="https://upload.wikimedia.org/wikipedia/commons/e/e1/University_of_Prishtina_logo.svg" 
           alt="University Logo" 
           style="width:250px; height:auto;" />
    </td>
    <td style="vertical-align:middle; padding-left:20px;">
      <h2><strong>Universiteti i Prishtinës</strong></h2>
      <h3>Fakulteti i Inxhinierisë Elektrike dhe Kompjuterike</h3>
      <p>Inxhinieri Kompjuterike dhe Softuerike</p>
      <p><strong>Lënda:</strong> Machine Learning</p>
      <p><strong>Profesor:</strong> Dr. Lule Ahmedi dhe MSc. Mërgim H. HOTI</p>
    </td>
 </tr>
</table>

---

## Përshkrim i përgjithësuar i projektit
Ky projekt realizohet në kuadër të lëndës “Machine Learning” dhe fokusohet në analizën dhe përpunimin e të dhënave reale të energjisë elektrike në Kosovë. 

Qëllimi kryesor është përgatitja e dataset-it për ndërtimin e modeleve të Machine Learning, përmes hapave të strukturuar si pastrimi, transformimi dhe analiza e të dhënave.

Projekti përfshin:
- para-procesimin e të dhënave  
- analizën e variablave kryesorë  
- krijimin e feature-ve të reja  
- përgatitjen e dataset-it për modele parashikuese  

---

## Përzgjedhja e Dataset-it

Dataset-i i zgjedhur për këtë projekt është i lidhur me sistemin energjetik të Kosovës dhe përmban të dhëna reale kohore mbi prodhimin dhe ndikimin mjedisor të energjisë elektrike.

| Atributi | Detaji |
| :--- | :--- |
| **Emri i Dataset-it** | Electricity Data for Kosovo |
| **Burimi** | Electricity Maps |
| **Përmbledhje** | Dataset kohor (time series) që përmban të dhëna mbi prodhimin e energjisë, intensitetin e karbonit dhe përqindjen e energjisë së rinovueshme. |

---

## Qëllimi i Projektit

Qëllimi final i këtij projekti është ndërtimi i modeleve të Machine Learning për:

- parashikimin e konsumit të energjisë  
- parashikimin e carbon intensity  
- analizimin e trendeve në kohë  
- identifikimin e anomalive në sistemin energjetik  

---

## Faza e parë - Para-procesimi i të dhënave 

Në këtë fazë është realizuar përgatitja e dataset-it për analiza dhe modelim. Janë ndërmarrë hapa për pastrim, transformim dhe strukturim të të dhënave.

*Rezultati përfundimtar është një dataset i pastër dhe i gatshëm për përdorim në Machine Learning.*

---

### 1. Identifikimi i Tipeve të të Dhënave

U analizua struktura e dataset-it për të kuptuar tipin e secilës kolonë.

<img width="556" height="417" alt="image" src="https://github.com/user-attachments/assets/fe1401ad-f580-4301-903c-06628157c9c1" />


Rezultati:
U identifikua që kolona Datetime (UTC) nuk ishte në format të duhur dhe kërkonte konvertim.

### 2. Konvertimi i Kolonës së Kohës

Për të mundësuar analizë kohore, kolona e datës dhe kohës u konvertua në format datetime.

<pre> python df['Datetime (UTC)'] = pd.to_datetime(df['Datetime (UTC)']) </pre>

- Funksioni pd.to_datetime() konverton të dhënat në format të standardizuar të kohës
- Ky hap është i domosdoshëm për operacione si resampling dhe analiza kohore

Rezultati:
Dataset-i tani mund të përdoret për analiza time-series dhe grupime sipas kohës.

### 3. Zbulimi i Vlerave Mungese (NULL)

Për çdo kolonë u llogarit përqindja e vlerave mungese për të vendosur metodën e duhur të pastrimit.

<img width="720" height="412" alt="image" src="https://github.com/user-attachments/assets/4f9da2dc-b4e4-4b31-99b9-649a2b0ffa6d" />

- isnull().sum() tregon numrin e vlerave të mungura për çdo kolonë
- Llogaritja e përqindjes ndihmon në vendimmarrje për trajtim

Rezultati:
U identifikuan kolona 'Data estimation method' me përqindje të lartë të vlerave mungese.

### 4. Kontrolli për Duplikate

Ky hap siguron që dataset-i nuk përmban rreshta të përsëritur.

<img width="297" height="122" alt="image" src="https://github.com/user-attachments/assets/748f0d89-e920-4836-ab36-825cb961a34e" />

- duplicated() identifikon rreshtat që janë të njëjtë
- .sum() jep numrin total të tyre

Rezultati:
U konfirmua se nuk ka duplicate.

### 5. Heqja e Kolonave të Panevojshme

Janë larguar kolonat që nuk kontribuojnë në analizë ose modelim.

<pre> df = df.drop(columns=[
    'Data source',
    'Data estimated',
    'Data estimation method'
]) </pre>

Këto kolona përmbajnë metadata që nuk përdoren në analizë.                                                                                                                                   
Reduktohet kompleksiteti i dataset-it

Rezultati:
Dataset-i u thjeshtua dhe u fokusua në variabla relevante.

### 6. Analiza e Variablave Kryesorë

Fokus në kolonat që lidhen me energjinë dhe ndikimin mjedisor.

<pre>
important_cols = [
    'Carbon intensity gCO₂eq/kWh (direct)',
    'Carbon intensity gCO₂eq/kWh (Life cycle)',
    'Carbon-free energy percentage (CFE%)',
    'Renewable energy percentage (RE%)'
]

print(df[important_cols].describe()) </pre>


- 'describe()' jep statistika bazike si mesatarja, minimumi dhe maksimumi.
- Ndihmon në kuptimin e shpërndarjes së të dhënave.

Rezultati:
U fitua një pasqyrë e qartë mbi sjelljen e variablave kryesorë.

### 7. Agregimi i të Dhënave (Resampling)

Të dhënat u grupuan në nivel ditor për analizë më të thjeshtë.

<pre> daily = df.resample('D', on='Datetime (UTC)').mean(numeric_only=True) </pre>

- resample('D') - grupizon të dhënat sipas ditëve
- mean() - llogarit mesataren për çdo ditë

Rezultati:
U krijua një dataset i ri me vlera ditore, më i përshtatshëm për analizë dhe modelim.

### 8. Vizualizimi i të Dhënave

Vizualizim i trendit të carbon intensity.

<pre>
import matplotlib.pyplot as plt

daily['Carbon intensity gCO₂eq/kWh (direct)'].plot()
plt.title("Intensiteti ditor i karbonit")
plt.xlabel("Data")
plt.ylabel("gCO₂eq/kWh")
plt.show()
</pre>

<img width="569" height="490" alt="image" src="https://github.com/user-attachments/assets/9bb1e95d-1ccd-4179-b238-2dc30f4b648f" />


- Krijohet një grafik kohor (time series)
- Ndihmon në identifikimin e trendeve dhe anomalive

Rezultati:
U identifikuan variacionet dhe trendet në kohë.

### 9. Feature Engineering

Krijimi i variablave të reja nga data dhe koha.

<pre>
df['hour'] = df['Datetime (UTC)'].dt.hour
df['day'] = df['Datetime (UTC)'].dt.day
df['month'] = df['Datetime (UTC)'].dt.month
df['weekday'] = df['Datetime (UTC)'].dt.weekday
</pre>

- Nxirren komponentët e kohës nga kolona datetime
- Këto përdoren si input për modele Machine Learning

### 9. Zbulimi dhe heqja e outliers

Në këtë pjesë fokusi është në identifikimin dhe trajtimin e vlerave ekstreme (anomali), të cilat mund të ndikojnë negativisht në performancën dhe saktësinë e modeleve të Machine Learning.

#### 9.1. Vizualizimi i Shpërndarjes (Boxplot)

Para identifikimit të anomalive, u realizua një analizë vizuale për të kuptuar shpërndarjen e të dhënave.

![alt text](image.png)

Ky vizualizim tregon:
- Medianën e të dhënave
- Shpërndarjen (range)
- Vlerat ekstreme (potential outliers)

Boxplot-i është një mënyrë e shpejtë dhe efektive për të identifikuar anomalitë në mënyrë vizuale.

#### 9.2. Zbulimi i Anomalive me Metodën IQR

Për identifikimin e anomalive u përdor metoda IQR (Interquartile Range).

![alt text](image.png)

Shpjegim i parametrave:
- Q1 (30%) → kufiri i poshtëm i shpërndarjes
- Q3 (70%) → kufiri i sipërm i shpërndarjes
- IQR → diferenca midis Q3 dhe Q1
- Lower Bound → kufiri minimal i pranueshëm
- Upper Bound → kufiri maksimal i pranueshëm

Çdo vlerë jashtë intervalit [lower_bound, upper_bound] konsiderohet anomali.

#### 9.3. Analiza e Përqindjes së Anomalive

Për të kuptuar ndikimin e anomalive në dataset:

<pre>
outlier_percentage = df['is_outlier'].mean() * 100
print(f"Outliers: {outlier_percentage:.2f}%")
</pre>

Rezultat:

🔹 0.81% e të dhënave janë anomali

Dataset-i është kryesisht i pastër
Heqja e anomalive nuk ndikon ndjeshëm në humbjen e informacionit

#### 9.4. Krijimi i Dataset-it pa Anomali

Hiqen rreshtat që përmbajnë vlera ekstreme:

<pre>
df_no_outliers = df[df['is_outlier'] == False].copy()
df_no_outliers.to_csv("df_no_outliers.csv", index=False)
</pre>

Procesi:
- Filtrim i të dhënave normale
- Krijimi i dataset-it të ri
- Ruajtja për përdorim të mëtejshëm

👉 Dataset-i i ri është më i pastër dhe më i përshtatshëm për modelim.

Krahasimi: Para vs Pas Heqjes së Anomalive

#### 9.5. Vizualizimi i ndikimit të pastrimit të të dhënave:

import matplotlib.pyplot as plt

- Para heqjes
 FOTOO

- Pas heqjes
 fOTOO

Analiza e rezultateve:

- Reduktohet “noise”
- Trendet reale bëhen më të dukshme
- Përmirësohet cilësia e input-it për modele

Rezultati:
Dataset-i u pasurua me feature të rëndësishme për analizë të avancuar.


## Faza e dytë - Trajnimi i modelit

Kjo faze fokusohet ne analizen dhe modelimin e të dhënave te energjisë elektrike në Kosovë, me fokus në parashikimin e intensitetit të karbonit, i cili shërben si indikator i rëndësishëm për modelet e konsumit dhe prodhimit të energjisë elektrike.

Faza e dyte përfshin përgatitjen e të dhënave, ndërtimin i modeleve, vlerësimin e performancës dhe interpretimin e rezultateve.

### Supervised Learning – Klasifikimi i Intensitetit të Karbonit

Problemi është trajtuar si klasifikim binar, ku intensiteti i karbonit ndahet në “High” dhe “Low” duke përdorur medianën si prag. Kjo zgjedhje siguron një dataset të balancuar dhe ndihmon modelet të performojnë në mënyrë stabile.

Dataset-i përmban 8,689 mostra dhe është ndarë në trajnim dhe testim me raport 80/20, duke ruajtur balancën e klasave. Përpara modelimit, të dhënat janë standardizuar për të përmirësuar performancën e algoritmeve.

Një pjesë e procesit të përgatitjes paraqitet më poshtë:
<pre>
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import StandardScaler

X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42, stratify=y
)

scaler = StandardScaler()
X_train_scaled = scaler.fit_transform(X_train)
X_test_scaled = scaler.transform(X_test)
</pre>

Janë përdorur dy modele kryesore: Random Forest dhe Gradient Boosting. Random Forest është zgjedhur për stabilitet dhe interpretueshmëri, ndërsa Gradient Boosting për performancë më të lartë dhe aftësi për të kapur marrëdhënie komplekse.

Trajnimi i modelit Random Forest është bërë si më poshtë:
<pre>
from sklearn.ensemble import RandomForestClassifier

rf_model = RandomForestClassifier(n_estimators=100, max_depth=15, random_state=42)
rf_model.fit(X_train_scaled, y_train)
</pre>

Rezultatet tregojnë performancë jashtëzakonisht të lartë për të dy modelet. Accuracy është mbi 99.7%, ndërsa metrikat e tjera si precision, recall dhe F1-score janë pothuajse perfekte. Gradient Boosting arrin një AUC prej 1.0, që nënkupton ndarje ideale të klasave.

### Rezultatet e Modeleve

| Model              | Accuracy | F1-Score | Recall  | Precision | AUC    |
|--------------------|----------|----------|---------|-----------|--------|
| Random Forest      | 99.77%   | 0.9977   | 99.77%  | 99.77%    | 0.9993 |
| Gradient Boosting  | 99.71%   | 0.9971   | 99.88%  | 99.54%    | 1.0000 |

Rezultati: Të dy modelet janë pothuajse perfekte, me Gradient Boosting që performon pak më mirë në identifikimin e rasteve “High”.

### Vizualizimet
Më poshtë paraqiten disa nga vizualizimet kryesore që ndihmojnë në interpretimin e modeleve:

#### Model Performance Comparison
Ky vizualizim paraqet një krahasim të drejtpërdrejtë të performancës së dy modeleve, Random Forest dhe Gradient Boosting, duke përdorur metrika kryesore si Accuracy, F1-score, Recall dhe Precision.

FOTOO

Në këtë grafik, boshti horizontal përfaqëson modelet, ndërsa boshti vertikal tregon vlerat e metrikave (nga 0 në 1). Secila metrikë është e paraqitur me një bar të veçantë, duke lejuar një krahasim të qartë vizual midis modeleve.

Rezultatet tregojnë se të dy modelet kanë performancë shumë të lartë, me vlera pothuajse identike. Random Forest paraqet një balancë të plotë midis metrikave, ndërsa Gradient Boosting ka një avantazh të vogël në Recall, që nënkupton se është më efektiv në identifikimin e rasteve me intensitet të lartë të karbonit.

#### Confusion Matrix (Heatmap)
Confusion Matrix është një nga vizualizimet më të rëndësishme për klasifikim, pasi tregon në mënyrë të detajuar se si modeli ka bërë parashikimet.

FOTOO

Grafiku është një matricë 2x2 ku:

- Boshti horizontal përfaqëson klasat e parashikuara
- Boshti vertikal përfaqëson klasat reale

Katër komponentët kryesorë janë:

- True Positives (rastet High të parashikuara saktë)
- True Negatives (rastet Low të parashikuara saktë)
- False Positives (rastet Low të klasifikuara gabimisht si High)
- False Negatives (rastet High të klasifikuara gabimisht si Low)

Në këtë projekt, vlerat në diagonale janë shumë të larta, ndërsa gabimet janë minimale (vetëm disa raste nga mbi 1700). Kjo tregon që modeli ka performancë pothuajse perfekte.

#### ROC Curve
ROC Curve (Receiver Operating Characteristic) tregon aftësinë e modelit për të dalluar midis klasave në nivele të ndryshme të pragut të vendimmarrjes.

FOTOO

Boshti horizontal paraqet False Positive Rate, ndërsa boshti vertikal paraqet True Positive Rate. Një model i mirë do të ketë kurbën sa më afër këndit të sipërm të majtë.

Në këtë projekt, të dy modelet kanë kurba shumë të ngritura, me AUC shumë afër 1. Gradient Boosting arrin AUC = 1.0, që tregon ndarje perfekte të klasave.

#### Actual vs Predicted (Time Series Plot)
Ky grafik paraqet krahasimin midis vlerave reale dhe atyre të parashikuara në një periudhë kohore (zakonisht një pjesë e dataset-it testues).

FOTOO

Boshti horizontal përfaqëson kohën (indeksin e mostrave), ndërsa boshti vertikal klasën (0 ose 1). Dy linja paraqesin:

- vlerat reale
- vlerat e parashikuara nga modeli

Ketu linjat janë pothuajse të mbivendosura, që tregon përputhje shumë të lartë midis realitetit dhe parashikimeve.

### Rezultati
Analiza e këtyre vizualizimeve tregon se modelet jo vetëm që janë shumë të sakta, por edhe shumë të sigurta në vendimet e tyre. Probabilitetet janë të qarta (afër 0 ose 1), ndërsa gabimet janë minimale.

Një insight shumë i rëndësishëm është se veçoria Carbon intensity (Life cycle) dominon në parashikim, duke kontribuar në pjesën më të madhe të vendimeve të modelit.

### Unsupervised Learning – Clustering

## Authors
- *Anjeza Sfishta*
- *Erza Merovci*
- *Fortesa Cena*

