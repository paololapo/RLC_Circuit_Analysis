# RLC circuit analysis: nonlinear dynamics
Here's a part of the analysis done for the laboratory class in 2022. It deals with: quantifying errors and uncertainty in physical measurements, nonlinear regressions with residual analysis, chi-square and its relationship with probability, and more statistical tools.
I apologize, but the following text will be in Italian. I will update and translate it soon.

L'apparato strumentale consiste in un generatore di funzioni *Tektronix AFG1022*, un oscilloscopio digitale *Tektronix TBS 1102B* e una breadboard con annesse resistenze, capacità e induttanze. L'oscilloscopio è collegato al circuito attraverso delle sonde di compensazione $10X$, dotate di impedenza $\times 10$ e attenuazione di $1/10$. Le componenti sono state scelte affinchè il fenomeno fisico da studiare fosse prevalente rispetto a rumore e aspetti secondari.

## RLC nel tempo: oscillatore armonico smorzato
In questa sezione analizziamo il circuito attraverso lo strumento della regressione non lineare, dopo aver preventivamente scaricato i dati dall'oscilloscopio. Il dataset utilizzato consta di $2423$ triple che descrivono il segnale del canale $1$ e $2$ al tempo $t$. In particolare sono maggiormente significativi i dati relativi al secondo canale, quello in uscita. Le misure sono state prese ai capi dell'induttanza.

L'incertezza associata ai valori è la stessa utilizzata per le misure tramite cursori.
É stato utilizzato un fondoscala di $500mV$ per entrambi i canali e di $25.0\mu s$ per la scala dei tempi.

Incertezza sulle misure di tensione come somma quadratica del termine di termine casuale e di quello di scala:
$\sigma_V = 0.04 \text{div} \oplus 1.5\%k $

Incertezza sui tempi come solo termine casuale:
$\sigma_t = 0.04\text{div}$
