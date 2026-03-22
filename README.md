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

Rezultati:
Dataset-i u pasurua me feature të rëndësishme për analizë të avancuar.

## Authors
- *Anjeza Sfishta*
- *Erza Merovci*
- *Fortesa Cena*

