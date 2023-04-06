# RLC circuit analysis: nonlinear dynamics
Here's a part of the analysis done for the laboratory class in 2022. It deals with: quantifying errors and uncertainty in physical measurements, nonlinear regressions with residual analysis, chi-square and its relationship with probability, and more statistical tools.
I apologize, but the following text will be in Italian. I will update and translate it soon.

L'apparato strumentale consiste in un generatore di funzioni *Tektronix AFG1022*, un oscilloscopio digitale *Tektronix TBS 1102B* e una breadboard con annesse resistenze, capacità e induttanze. L'oscilloscopio è collegato al circuito attraverso delle sonde di compensazione $10X$, dotate di impedenza $\times 10$ e attenuazione di $1/10$. Le componenti sono state scelte affinchè il fenomeno fisico da studiare fosse prevalente rispetto a rumore e aspetti secondari.

## RLC nel tempo: oscillatore armonico smorzato
In questa sezione analizziamo il circuito attraverso lo strumento della regressione non lineare, dopo aver preventivamente scaricato i dati dall'oscilloscopio. Il dataset utilizzato consta di $2423$ triple che descrivono il segnale del canale $1$ e $2$ al tempo $t$. In particolare sono maggiormente significativi i dati relativi al secondo canale, quello in uscita. Le misure sono state prese ai capi dell'induttanza.

L'incertezza associata ai valori è la stessa utilizzata per le misure tramite cursori.
É stato utilizzato un fondoscala di $500$ per entrambi i canali e di $25.0\mu s$ per la scala dei tempi.

Incertezza sulle misure di tensione come somma quadratica del termine di termine casuale e di quello di scala:
$\sigma_V = 0.04 \text{div} \oplus 1.5\%k$.

Incertezza sui tempi come solo termine casuale:
$\sigma_t = 0.04\text{div}$.

<p align="center">
  <img src="images\image01.png" width="60%">
  <img src="images\image02_1.png" width="30%">
</p>

### Fit da modello
Dall'analisi del circuito si ricava un'espressione analatica per il voltaggio atteso ai capi dell'induttanza che risulta essere

$V(t) = V_0\frac{e^{-\delta t}}{\sqrt{\Omega^2-\delta^2}}\left(\sqrt{\Omega^2-\delta^2}\cos(t\sqrt{\Omega^2-\delta^2})-\delta\sin(t\sqrt{\Omega^2-\delta^2})\right)$

dove $V_0=2$V è il valore iniziale della tensione (doppio rispetto a quanto scelto dal generatore di funzioni), $\delta$ il fattore di smorzamento e $\Omega$ la pulsazione caratteristica.

L'analisi procede realizzando la regressione a due parametri dei dati secondo l'espressione data che restituisce i seguenti valori come stima dei parametri ignoti e delle loro incertezze.
Per approfondire la bontà del fit si graficano gli scarti e la loro distribuzione. 

<p align="center">
  <img src="images\image03.png" width="60%">
  <img src="images\image04.png" width="33%">
</p>

Dall'andamento degli scarti ci si accorge innanzitutto che i dati sono altamente correlati, in quanto presentano delle regolarità che paiono seguire l'andamento ondulatorio della curva. Inoltre i primi dati sono notevolmente peggio rappresentati dalla curva di fit. 
Questo secondo aspetto può essere dovuto in prima analisi da due fattori. In primis i dati di testa sono quelli con un'incertezza maggiore, data dal fattore di scala nella stima dell'errore, essendo quelli a voltaggio maggiore. In secondo luogo il segnale del canale in entrata non è costante (come invece era modellizzato essere) per la resistenza interna del generatore di funzioni. 

La distribuzione degli scarti è fortemente assimetrica perchè risente dei dati iniziali e in generale il confronto tra questi e gli errori a priori è indice di una regressione tutt'altro che priva di limiti.
Queste ipotesi vengono confermate dal calcolo del chi quadro, atteso essere circa pari alla cardinalità del dataset, che risulta invece essere di un'ordine di grandezza maggiore ( $\chi^2 = 22436$, $\chi^2_{\text{normalizzato}} = 9.27$). 

Viene riportato il countor plot del $\chi^2$ che evidenzia l'atteso comportamento paraboloide. Si evidenzano le linee del livello del $\chi_{\text{min}}^2+1$, la cui proiezione sugli assi restituisce -come atteso- l'incertezza stimata dal programma, e del $\chi_{\text{min}}^2+2.3$ che è attesa raccogliere il $68\%$ della probabilità a due parametri. 

<p align="center">
  <img src="images\image05.png" width="40%">
</p>

Una possibile spiegazione del fenomeno è la sottostima dell'errore a priori. Questo era in media pari a $0.02V$ (e di simile mediana) mentre quello a posteriori è di $0.07V$.
Tale confronto è limitato dal fatto che l'errore a priori sia composto da una componente di scala e una di offset, mentre quello a posteriori perde informazione su questa differenza. 
Una prima ipotesi sul perchè di questo comportamento può essere data dalla scelta di utilizzare la stessa incertezza dei cursori ignorando possibili perdite di accuratezza date dal salvataggio automatico dei dati che potrebbe risentire di possibili mancanze di stabilità del segnale.

Si procede ora a valutare ulteriori aspetti conducendo ulteriori analisi sul dataset.

### Curva smooth
La sensibilità dell'oscilloscopio è tale per cui i dati siano discretizzati, nel senso che non è in grado di apprezzare le variazioni di segnale per ogni tempo e perciò la curva, analizzata in dettaglio, presenta in realtà un andamento a gradini. Questo comportamento è accentuato vicino a massimi e minimi, zone fondamentali per una buona regressione di un seno o di un coseno.

<p align="center">
  <img src="images\image06.png" width="40%">
</p>

Si è dunque provato ad usare uno stratagemma per ovviare a questo problema realizzando artificialmente una curva più regolare attraverso la funzione `scipy.signal.savgol_filter` (usando come parametri finestre di lunghezza $35$ e polinomi di ordine $2$).
Si riporta un dettaglio delle curve, nei pressi del primo minimo, nel quale si apprezza la discretizzazione del segnale, il processo di regolarizzazione e la totale ininfluenza nei risultati della regressione, confermata dalla stima dei parametri.

### Fit pentaparametrico
Con l'obiettivo di testare l'accuratezza del modello teorico si è provato a parametrizzare la curva sulla base della soluzione più generale possibile per il problema dell'oscillatore armonico smorzato, omettendo dunque le informazioni sul sistema fisico in questione. Si tratta dunque di una regressione a cinque parametri del tipo

$V(t) = \alpha e^{-\delta t}\sin(\beta t + \gamma) + ϵ$

del quale si riporta il grafico e la visualizzazione degli scarti.

<p align="center">
  <img src="images\image07_1.png" width="60%">
  <img src="images\image08.png" width="33%">
</p>

Il chi quadro di questa regressione è $\chi^2 = 241$, estremamente buono, come preventivato dall'osservazione degli scarti. É interessante come la maggior parte di questi siano maggior di zero e che questo fenomeno sia verificato lungo tutta la curva. Inoltre appaiono meno correlati. 

Una possibile spiegazione è che il modello, ricavato per il circuito ideale, non sia perfettamente generalizzabile al caso reale seppure quest'ultimo mantenga le caratteristiche di un oscillatore armonico smorzato. Al costo di complicare notevolmente lo schema del circuito è dunque atteso che si possa meglio modellizzare il fenomeno effettivo.

### Minuit
In breve si cita il fatto che i risultati della regressione a due parametri sopra descritti sono robusti rispetto al cambio di algoritmo verso `Minuit` utilizzato per minimizzare i minimi quadrati.

Si è utilizzata l'apposita libreria che simula il codice di Root.

## RLC in frequenza: curva di risonanza
In questa sezione ci occuperemo di analizzare il comportamento della funzione di trasferimento al variare della frequenza dell'onda in entrata. Il dataset consta di 31 triple di dati che rappresentano frequenza, tensione in ingresso e tensione in uscita dal circuito. Le misure sono state prese ai capi della resistenza.

Per la tensione in ingresso si è utilizzato un fondoscala di $500mV/div$ mentre per la tensione in uscita di $100mV/div$. Anche in questo caso si trascura l'errore sulla frequenza emessa dal generatore.

### Visualizzazione dei dati sperimentali e relative incertezze

### Fit da modello

Si definisce l'amplificazione, o funzione di trasferimento, del segnale come il rapporto tra la tensione in uscita e quella in ingresso, con le dovute correzioni di scala
$T_V(f) = \frac{k_{out}V_{out}}{k_{in}V_{in}} $ 
e si associa a tale grandezza un errore di
$\sigma_T = T\sqrt{\left(\frac{0.04V/div}{V_{in}}\right)^2+\left(\frac{0.04V/div}{V_{out}}\right)^2+2\left(\frac{\sigma_k}{k}\right)^2}$
assumendo $\sigma_k = 0.012$ e $k=1$.

Ai capi della resistenza la funzione di trasferimento è attesa essere:

$T_R(\omega) = \frac{A\omega}{\sqrt{\omega^4-2\omega^2(\Omega^2-2\delta^2)+\Omega^4}}$ con $\delta = \frac{R}{2L}$ e $\Omega = \frac{1}{\sqrt{LC}}$

Si effettua dunque una regressione a tre parametri per stimare i valori di $A$, $\Omega$ e $\delta$.

Si noti che, almeno per gli ultimi due parametri, si dispone di una stima data dalla conoscenza delle componenti del circuito. Questo è fondamentale per indirizzare, assegnando dei valori iniziali, l'algoritmo che effettua la minimizzazione della funzione di costo.

Dall'andamento e dalla distribuzione degli scarti si osserva come tutti i valori siano abbondantemente all'interno dell'errore a priori. Il $\chi^2$ a 28 gradi di libertà ($3$ i vincoli) è pari a $2.87$, sufficientemente basso da far sospettare una sovrastima dell'incertezza. Tale risultato potrebbe essere frutto delle diverse divisioni di $V_{in}$ e $V_{out}$ e all'errata semplificazione dei due fattori di scala. Per procedere in tal senso si potrebbe calcolare un errore a posteriori che porti il $\chi^2$ al valore atteso tramite un parametro moltiplicativo $k$.

### $\chi^2$ profilato
In analogia a quanto fatto per la regressione non lineare nel dominio del tempo si è graficato l'andamento del $\chi^2$ in prossimità del best fit.

Essendo $3$ i parametri è stato oppurtuno profilare il $\chi^2$ scegliendo per ogni valore delle variabili sugli assi il minimo $\chi^2$ che si ottiene facendo variare il terzo parametro.

Operativamente si è scelto di valutare la funzione di costo in un array equispaziato di $100$ elementi in un range di $\pm 2\sigma$ per ogni parametro. Questo vuol dire operare con un tensore $(100\times100\times100)$ e aumentare notevolmente il carico computazionale. 

Profilare il terzo parametro implica che il $68\%$ delle coppie degli altri parametri sia comuque atteso essere nell'area interna alla curva di livello $\chi^2_{\text{min}}+2.3$. Anche in questo caso invece la proiezione della curva $\chi^2_{\text{min}}+1$ sugli assi determina le incertezze associate dal programma ai parametri. Dai grafici riportati si verifica che (almeno qualitativamente) è proprio così. 

Il grafico in cui si confrontano $A$ e $\delta$ evidenzia una correlazione tra i due parametri.

### Dal $\chi^2$ alla densità di probabilità

Il $\chi^2$ è intrensicamente legato alla densità di probabilità associata ad ogni parametro. Il passaggio tra le due quantità è retto dalla relazione
$\text{Prob}(\Delta\chi^2, 2 dof) = e^{-\Delta\chi^2/2}$
in cui i gradi di libertà (con un leggero abuso di notazione, in passato ci si è riferito ai parametri come vincoli) sono due grazie alla profilazione del terzo parametro.

Operativamente si tratta di una griglia $(100\times100)$ che va normalizzata dividendo ogni elemento per la somma totale in modo tale che l'integrale dell'istogramma 2d che si sta discretizzando sia unitario. 

Si procede valutando se all'interno dell'area definita dal $\chi^2_{\text{min}}+2.3$ la probabilità sia effettivamente quella attesa del $68\%$. In realtà questo non avviene ma si sono ottenuti valori attorno al $74\%$.

Il fatto che vi sia una differenza rispetto a quanto atteso può essere dovuto al range scelto per la mappa del $\chi^2$. Si era optato per valutare la funzione di costo nell'intervallo $(\pm2\sigma, \pm2\sigma, \pm2\sigma)$ e questo potrebbe aver portato ad un'incorretta normalizzazione della distribuzione di probabilità, mancando le code. Tale effetto è di difficile valutazione in quanto si sta considerando una gaussiana a due parametri costretti in un quadrato, ma è di più facile stima rispetto a quanto fatto in seguito.

 ### Pdf di un parametro
 
Semplicemente sommando i valori della griglia rispetto ad un asse si ottiene la proiezione della curva rispetto ad un parametro. Qualitativamente si ritrova l'andamento gaussiano atteso.

Per valutare se all'interno del $\pm \sigma$ dato dalla regressione vi sia la probabilità attesa non si è effettuato un ulteriore fit ma si si sono semplicemente sommati i bin nel range in analisi. Tale procedimento non è forse perfettamente rigoroso (seppur dotato di senso data la normalizzazione) ma fornisce una buona prima stima di quanto cercato. In questo contesto è chiaro cosa fare per corregge il range dei $2\sigma$ che contiene (in teoria) solo il $95\%$ della probabilità (la proporzione la si è fatta a posteriori, i grafici mostrano la proiezione grezza della pdf della sottosezione procedente sugli assi).

### Toy set

Come ultima verifica per il coverage del $68\%$ dell'ellissse definita dal $\Delta\chi^2<2.3$ si è effettuato un processo di bagging. Nel caso specifico sono stati generati $10^5$ pseudo-dataset (*toy*) rigenerando i valori per il fit in modo tale che ogni punto avesse una distribuzione gaussiana centrata sul dato sperimentale e con una deviazione standard uguale a quella assegnata a priori. Assegnando a questi punti gli stessi errori usati in precedenza si ripete per ogni toy set il fit e si osserva la distribuzione dei parametri ottenuti. 

Qualitativamente si osserva come gli istogrammi 2d plottati non siano troppo dissimili a quanto fosse atteso. Resta da valutare la frazione dei set appena generati che danno parametri contenuti nell'ellisse. 

Tale operazione, seppure concettualmente chiara, non è elementare perchè passa per la parametrizzazione di ellissi roto-traslate. Innanzitutto non sono noti i semiassi dell'ellisse, nè la loro proiezione sugli assi (lo sono invece nel caso del $\Delta\chi^2<1$, le incertezze $\sigma$ dei parametri). Assunta la regolarità del paraboloide del $\chi^2$ nei pressi del minimo i semiassi cercati devono essere pari a $\alpha\sigma$ per qualche parametro di scala $\alpha$. Facendo variare tale parametro sul grafico del $\chi^2$ si è giunti alla stima ragionevole di $\alpha=1.55$, senza però riuscire ad associarvi un significato statistico che invece sicuramente ha. \\
Dato $\alpha$ le ellissi si parametrizzano sulla base di relazioni trigonometriche e geometria di base.

La frazione di parametri, intepretata probabilisticamente, contenuta nelle aree in questione è di poco inferiore al $70\%$, superiore a quella attesa. Questo potrebbe confermare la presunta sovrastima degli errori ipotizzata all'inizio di questa analisi. 
