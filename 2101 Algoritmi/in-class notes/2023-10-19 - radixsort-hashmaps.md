---
type: university
date: 2023-10-19
subjectId: 2101
year: 2
semester: 1
---
#university #in-class #subject-2101
### 2023-10-19
> [!summary] Algoritmi

## Counting & Radix Sort

Per ordinare un array sulla base di più chiavi è sufficiente ordinare per ogni chiave dalla meno importante alla più importante. È necessario che gli algoritmi di ordinamento utilizzati siano stabili, altrimenti il metodo non funziona.
*L'ordinamento, anche se non è stabile, se utilizzato solo sulla prima iterazione, rende comunque l'algoritmo funzionante, ma non stabile.*

Per ordinare numeri grossi con il counting sort si può utilizzare il metodo precedente, ordinando per ogni cifra separatamente.
Si può utilizzare `n%10` per ottenere l'ultima cifra e utilizzare $\frac{n}{10^{i-1}}\mod10$ per ottenere l'`i`-esima cifra da destra.
Questo algoritmo è detto **Radix Sort**.
```cpp
/*
se A=[8532, 2553, 2634] e i=2 il counting sort vede [5, 5, 6]
*/
void radixSort(int* A, int n) {
	int c = getMaxNumberOfDigits(A, n); // se max(A)=1'000'000   =>   c=7
	for (int i = 0; i < c; i++) {
		countingSort(A, n, i); // dove i è la posizione (da destra) della cifra su cui ordinare
	}
}
```

## Dizionario
È importante avere una ricerca veloce, a costo di sacrificare tempi di inserimento e cancellazione.

### Tabella a indirizzamento diretto
Si utilizza una tabella a indici da $0$ a $n$ dove il numero $a$ (compreso tra $0$ e $n$) si inserisce nella posizione $a$.
```cpp
void insert(int* A, int x) {
	A[x] = x;
}
void remove(int* A, int x) {
	A[x] = NULL; // pseudocodice
}
bool search(int* A, int x) {
	return A[x] != NULL;
}
```
Si può ottimizzare sostituendo il tipo dell'array da `int` in `bool` (mettendo `1` se l'elemento è presente, altrimenti `0`).
> [!warning] Nota
> Questa implementazione non funziona quando bisogna conservare numeri molto grandi (servirebbe un array di uguale dimensione).

### Tabella Hash
Si consideri $U$ l'insieme delle chiavi da rappresentare.
Si utilizza una tabella (chiamata $T$) a indici da $0$ a $m-1$.
La tabella contiene meno celle delle chiavi da rappresentare: $|T|=m<|U|$

Si utilizza un *oracolo*: è una funzione, detta **funzione hash**, che prende in input un elemento di $U$ e restituisce un numero compreso tra $0$ e $m-1$.
$$ h: U \to \{0, 1, \dots,m-1\}$$

È importante che la funzione hash ritorni sempre lo stesso valore per lo stesso numero dato in input.

Essendo $|T|<|U|$ esistono delle collisioni: $\exists\, k_{1},k_{2} \in U:\,h(k_{1})=h(k_{2})$.
Esistono molti metodi per risolvere il problema e conservare $k_{1}$ e $k_{2}$.
##### Metodo concatenazione
Si utilizzano le liste.
Ogni elemento di $T$ è in realtà una lista a cui vanno concatenati gli elementi man mano che vengono inseriti.
Con questo metodo bisogna effettuare la ricerca all'interno di una lista in tempo lineare (e non più costante).
Consideriamo $\alpha = \frac{n}{m}$ chiamato **fattore di carico**, dove $n$ è il numero di elementi.
Ogni operazione di ricerca impiega in media $O(\alpha)$, adattando $m$ si può considerare un tempo costante.
```cpp
void insert(list* T, int k) {
	T[h(k)].insert(k);
}
void remove(list* T, int k) {
	T[h(k)].remove(k);
}
bool search(list* T, int k) {
	return T[h(k)].search(k);
}
```
##### Metodo indirizzamento aperto
*prossima lezione*

---
#### Proprietà della Hash Function
L'**hashing uniforme semplice** è una proprietà che indica la probabilità che la funzione di hash distribuisca in modo uguale gli elementi nelle celle.
$$ \forall \,k \in U \to \Pr\{h(k)=i\}=\frac{1}{m}$$
#### Implementazione
Ci sono vari metodi per implementare la funzione hash.
##### Metodo divisione
$$ h(k) = k \mod{m}$$
Se consideriamo $m=2^p$ (con $p \in \mathbb{N}$) il metodo di divisione restituisce gli ultimi $p$ bit (meno significativi), aumentando il rischio di collisione. Per questo si consiglia di evitare potenze di due come valore di $m$.
##### Metodo moltiplicazione
Si consideri $A \in \mathbb{R}: 0 \leq A < 1$.
$$ h(k) = \lfloor m\cdot [(kA)\mod 1] \rfloor$$
Si prende $k$, si moltiplica per $A$ e si ottiene un numero arbitrariamente grande ($0 \leq kA < k$) decimale. Con il $\mod 1$ si prende la parte decimale di quest'ultimo e si moltiplica per $m$, ottenendo un numero compreso tra $0$ e $m-1$.

A differenza del caso precedente, la scelta di $m$ non influenza l'ipotesi dell'hashing uniforme semplice. Si può quindi considerare $m=2^{p}$ e adattare l'algoritmo per ottimizzarlo.
Considerando $w$ come il numero di bit della parola della macchina, l'algoritmo diventa:
$$h(k)=[(S\cdot k)\gg(w-p)] \,\&\, (m-1)$$
