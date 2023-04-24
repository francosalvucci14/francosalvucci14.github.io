# WebScraper


# Idea
L'idea di questo progetto è venuta ad un mio compagno di corso, Simone Zheng.

L'idea è quella di creare uno scraper che vada ad analizzare e "studiare" i dati presenti sui siti di immobili, in modo da riuscire ad analizzare i vari annunci, selezionati per zona, e a calcolare quale tra le case/appartmenti in vendita conviene comprare e quali invece conviene non guardare proprio

Attualmente, tutte le funzioni che si interessano di calcolare quale tra gli annunci sia il migliore, basandosi su stipendio medio, IRPEF e tc , sono ancora in fase di sviluppo

Attualmente, lo scraper si occupa solo di prelevare i dati dai siti di immobili, e calcolare il costo medio per zona degli immobili.

# Codice
Il codice dello scraper è fatto in python.
Le librerie utilizzate sono state :

- BeautifulSoup
- Pandas
- Requests
- Sys
- Getopt

Sono state implementate anche le funzioni per prendere in input, da terminale, dei valori che andranno a settare la zona di interesse e il tipo di immobile che si vuol cercare

QUesto è stato realizzato in questo modo

```python
arg_zone = ""
    arg_type = ""
    arg_help = "Help: -z <zone> -t <type>".format(argv[0])
    
    try:
        opts, args = getopt.getopt(argv[1:], "hz:t:", ["help", "zone=", 
        "type="])
    except:
        print(arg_help)
        sys.exit(2)
    
    for opt, arg in opts:
        if opt in ("-h", "--help"):
            print(arg_help)  # print the help message
            sys.exit(2)
        elif opt in ("-z", "--zone"):
            arg_zone = arg
        elif opt in ("-t", "--type"):
            arg_type = arg

    if arg_type == "all":
        arg_type = "case"
```
All'interno troviamo anche una funzione che va a cercare quanti risulsati sono stati trovati sul sito, in modo da poter scorrere le varie pagine tramite un ciclo for, che incrementa il valore `page=` del sito.

Il codice finale risulta essere questo

```python
# Importare moduli
import requests
import pandas as pd
from bs4 import BeautifulSoup
import sys
import getopt
import math
# Indirizzo sito web
def FindNumber(string):
    empty_string = ""
    for m in string:
        if m.isdigit():
            empty_string = empty_string+m
    return int(empty_string)

def SearchNumberOfElements(type,zone):
    url = f"https://www.immobiliare.it/vendita-{type}/roma/{zone}/?criterio=rilevanza&noAste=1"
    response = requests.get(url)
        # Analizzare documento HTML del codice sorgente con BeautifulSoup
    html = BeautifulSoup(response.text, 'html.parser')
    number_of_el = html.find('div', class_="in-searchList__title")
    value = number_of_el.text
    split = value.split()
    number = split[0]
    return int(number)

def search(argv):
    # Import argument from terminal
    arg_zone = ""
    arg_type = ""
    arg_help = "Help: -z <zone> -t <type>".format(argv[0])
    
    try:
        opts, args = getopt.getopt(argv[1:], "hz:t:", ["help", "zone=", 
        "type="])
    except:
        print(arg_help)
        sys.exit(2)
    
    for opt, arg in opts:
        if opt in ("-h", "--help"):
            print(arg_help)  # print the help message
            sys.exit(2)
        elif opt in ("-z", "--zone"):
            arg_zone = arg
        elif opt in ("-t", "--type"):
            arg_type = arg

    if arg_type == "all":
        arg_type = "case"
    locali_tot = []
    price_tot = []
    cycle = SearchNumberOfElements(arg_type,arg_zone)
    print(cycle)
    pagine = math.ceil(cycle/25)
    if pagine  == 1:
        url = f"https://www.immobiliare.it/vendita-{arg_type}/roma/{arg_zone}/?criterio=rilevanza&noAste=1"
        print(url)
        # Eseguire richiesta GET
        response = requests.get(url)
        # Analizzare documento HTML del codice sorgente con BeautifulSoup
        html = BeautifulSoup(response.text, 'html.parser')
        # Estrarre tutte le citazioni e gli autori dal documento HTML
        locali_html = html.find_all('a', class_="in-card__title")
        price_html = html.find_all(
            'li', class_="nd-list__item in-feat__item in-feat__item--main in-realEstateListCard__features--main")
        # Raccogliere le citazioni in un elenco
        locali = list()
        for locale in locali_html:
            locali.append(locale.text)
        # Raccogliere gli autori in un elenco
        prices = list()
        for price in price_html:
            prices.append(price.text)

        locali_tot += locali
        price_tot += prices
    else:
        for i in range(1, pagine+1):
            url = f"https://www.immobiliare.it/vendita-{arg_type}/roma/{arg_zone}/?criterio=rilevanza&pag={i}&noAste=1"
            print(url)
            # Eseguire richiesta GET
            response = requests.get(url)
            # Analizzare documento HTML del codice sorgente con BeautifulSoup
            html = BeautifulSoup(response.text, 'html.parser')
            # Estrarre tutte le citazioni e gli autori dal documento HTML
            locali_html = html.find_all('a', class_="in-card__title")
            price_html = html.find_all(
                'li', class_="nd-list__item in-feat__item in-feat__item--main in-realEstateListCard__features--main")
            # Raccogliere le citazioni in un elenco
            locali = list()
            for locale in locali_html:
                locali.append(locale.text)
            # Raccogliere gli autori in un elenco
            prices = list()
            for price in price_html:
                prices.append(price.text)

            locali_tot += locali
            price_tot += prices

    return locali_tot, price_tot


locali, prezzi = search(sys.argv)
somma_prezzi, count_locali, media_prezzo = 0, 0, 0
# for i in range(len(prezzi)):
#     prezzi[i] = prezzi[i].replace("€","")
#     print(prezzi[i])
#     somma_prezzi+=float(prezzi[i])
#     count_locali+=1
# print(somma_prezzi)
# media_prezzo = somma_prezzi/count_locali
df = pd.DataFrame({'Locali': locali, 'Prezzi': prezzi,
                  'Media prezzo:': media_prezzo})
df.to_csv('Immobiliare.csv', index=False, encoding='utf-8')

```

# Video
