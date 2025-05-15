# Tesi


# Abstract

Il mio lavoro di tesi triennale è incentrato su una particolare classe di grafi, ovvero i **grafi temporali**.<br/>
Un grafo temporale $G=(V,E)$ è un grafo dove ad ogni arco $(u,v)\in E$ viene associata una lista di **timestamp**, che vanno ad indicare gli istanti di tempo in cui l'arco risulta essere attivo.<br>
Il mio lavoro è stato quello di trovare un algoritmo **efficiente** che risolva il problema della **Temporal Connectivity**, ovvero determinare se un dato grafo temporale con topologia ad albero risulta essere **temporalemente connesso** oppure no.<br>
*Con temporalmente connesso si intende che per ogni coppia di nodi, deve esistere un percorso temporale valido*.<br>
L'obiettivo di questa tesi è stato quello di trovare un algoritmo molto più efficiente del Näive, che risulta avere una complessità $$O(nM^2)$$ con $M$=numero totale di timestamp, in modo da risolvere questo problema.<br>
Il mio algoritmo sfrutta le idee di *Earliest-Arrival* e *Latest-Departure*, e la sua complessità risulta essere $$O(n\log(L))$$ con $L=\max_{v}|L_v|$ e $L_v$=timestamp sull'arco $(v,p(v))$
# Codice

Di seguito viene riportato il codice del mio algoritmo, che ha una complessità **poly-log**.<br>
```python
def visit_dfs(tree, node, EA_max, LD_max):
    """
    Visita DFS unificata che, per ogni nodo v:
      - Se v è foglia, imposta EA_max[v] = min(L_v) e LD_max[v] = max(L_v)
      - Altrimenti, visita ricorsivamente i figli, raccoglie le coppie (EA_max, LD_max)
        e controlla la consistenza degli intervalli secondo lo pseudocodice.
      - Infine, calcola EA = max_{u in child(v)} EA_max[u] e LD = min_{u in child(v)} LD_max[u],
        e determina NextEA e NextLD in L_v (lista dei pesi associati a v) tramite funzioni di
        ricerca (binary_search e binary_search_leq).

    Se per qualche ragione il calcolo fallisce (NextEA o NextLD == -1), imposta EA_max[v] e
    LD_max[v] a infinito e ritorna False, altrimenti ritorna True.

    Parametri:
      - tree: grafo orientato rappresentante l'albero.
      - node: nodo corrente.
      - EA_max: dizionario per memorizzare i valori EA_max per ciascun nodo.
      - LD_max: dizionario per memorizzare i valori LD_max per ciascun nodo.

    Ritorna:
      - True se la connettività temporale risulta consistente lungo il ramo, False altrimenti.
    """
    # L_v: vettore dei pesi associato al nodo corrente
    weights = tree.nodes[node].get("weight", [])
    children = list(tree.successors(node))

    # Caso base: foglia
    if not children:
        EA_max[node] = min(weights)
        LD_max[node] = max(weights)
        return True

    # Inizializza il vettore D_v per raccogliere le coppie dai figli
    D_v = []
    for child in children:
        if not visit_dfs(tree, child, EA_max, LD_max):
            return False
        # Se uno dei figli ha valori infiniti, la connettività non è rispettata
        if EA_max[child] == float("inf") or LD_max[child] == float("inf"):
            return False
        D_v.append((EA_max[child], LD_max[child]))

    # Se sono presenti almeno due figli, eseguo il controllo di consistenza
    if len(D_v) > 1:
        # Trovo i due minimi rispetto a EA_max:
        # (min1, ld1) è la coppia con EA_min più piccolo e (min2, ld2) la seconda
        sorted_intervals = sorted(D_v, key=lambda x: x[0])
        minEA, ld1 = sorted_intervals[0]
        secondEA, ld2 = sorted_intervals[1]
        # Per ogni figlio, effettuo il controllo:
        for child in children:
            if EA_max[child] == minEA:
                # Se il figlio con EA_min ha un EA_max maggiore di ld2, la condizione non è soddisfatta
                if EA_max[child] > ld2:
                    return False
            else:
                # Gli altri devono rispettare EA_max[u] <= ld1
                if EA_max[child] > ld1:
                    return False
    # Se c'è un solo figlio non serve ulteriore controllo

    if node == 1:
        return True

    # Calcolo EA e LD aggregati dai figli
    EA = max(EA_max[child] for child in children)
    LD = min(LD_max[child] for child in children)

    # Calcola NextEA e NextLD usando la lista dei pesi L_v e le funzioni di ricerca
    # Si assume che 'binary_search' trovi il successore di EA in weights,
    # e 'binary_search_leq' trovi il predecessore (o il più vicino minore uguale) di LD.
    NextEA = binary_search(weights, EA) if weights else -1
    NextLD = binary_search_leq(weights, LD) if weights else -1

    if (NextEA == -1 or NextLD == -1) and node != 1:
        EA_max[node] = float("inf")
        LD_max[node] = float("inf")
        return False
    elif NextEA != -1 and NextLD != -1:
        EA_max[node] = NextEA
        LD_max[node] = NextLD
        return True

def algoritmo_unificato(T):
    """
    Algoritmo unificato per la verifica della connettività temporale di un albero.

    T: grafo orientato rappresentante l'albero.
    """
    n = T.number_of_nodes()
    EA_max = [0] * (n + 1)
    LD_max = [0] * (n + 1)
    if visit_dfs(T, 1, EA_max, LD_max):
        return "L'albero è temporalmente connesso"
    else:
        return "L'albero non è temporalmente connesso"
```
Da come si può vedere, il mio algoritmo non è altro che una visita DFS modificata (possiamo chiamarla senza perdità di generalità **visita DFS Temporale**) che sfrutta i principi di EA e LD.<br>
Sfrutta anche un'euristica particolare, ovvero preso $N(v)=$vicinato di $v$ se $$EA_{max}[v]\leq\min_{w\in N(v)}LD_{max}[w]$$Allora posso affermare che il sottoalbero radicato in $v$ risulta essere temporalmente connesso.<br>
Questo controllo viene propagato verso l'alto fino ad arrivare alla radice.<br>È li che verrà ritornato **True** o **False**, a seconda del risultato del check visto precedentemente.
# Prove Sperimentali
Ecco alcuni test condotti su alberi generati casualmente.<br>
Essi confrontano i tempi medi di esecuzione del mio algoritmo e del Näive.<bt>
{{< figure src="/images/Thesis/Test_25_2000n.png" title="Test con 1000 nodi e $L_v = 25$" >}}<br/>
{{< figure src="/images/Thesis/Test_70_2000n.png" title="Test con 1000 nodi e $L_v = 70$" >}}<br/>
{{< figure src="/images/Thesis/Test_300_1000n.png" title="Test con 1000 nodi e $L_v = 300$" >}}<br/>
{{< figure src="/images/Thesis/Test_n2_2000n.png" title="Test con 1000 nodi e $L_v = n^2$" >}}<br/>
# Disclaimer

Chiunque fosse interessato a leggere per intero la mia tesi è pregato di inviarmi una mail all'indirizzo **franco.salvucci@alumni.uniroma2.eu**

