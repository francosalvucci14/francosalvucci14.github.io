# WebScraper


# Idea
The idea for this project came from a classmate of mine, Simone Zheng.

The idea is to create a scraper that goes to analyze and “study” the data on real estate sites, so as to be able to analyze the various listings, selected by area, and calculate which among the houses/apartments for sale is worth buying and which ones are better not to look at at all

Currently, all the functions that are concerned with calculating which among the listings is the best, based on average salary, IRPEF and tc , are still under development

Currently, the scraper is only concerned with taking data from real estate sites, and calculating the average cost per area of properties.

# Code
The scraper code is done in python.
The libraries used were :

- BeautifulSoup
- Pandas
- Requests
- Sys
- Getopt

Functions have also been implemented to take in input, from the terminal, values that will go to set the area of interest and the type of property you want to search for

This was implemented in this way

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
Inside we also find a function that goes to look up how many results have been found on the site, so we can scroll through the various pages via a for loop, which increments the `page=` value of the site.

The final code turns out to be this

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
    print(f"Numeri di elementi trovati : {cycle}")
    pagine = math.ceil(cycle/25)
    print("Inizio scansione e raccolta dati")
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
            locali = list()
            for locale in locali_html:
                locali.append(locale.text)
            prices = list()
            for price in price_html:
                prices.append(price.text)

            locali_tot += locali
            price_tot += prices

    return locali_tot, price_tot,arg_type


locali, prezzi,tipo_immobile = search(sys.argv)
somma_prezzi, count_locali, media_prezzo = 0, 0, 0

print("Elaboro csv personalizzato")
df = pd.DataFrame({'Locali': locali, 'Prezzi': prezzi,
                  'Media prezzo:': media_prezzo})
df.to_csv(f'Immobiliare_{tipo_immobile}.csv', index=False, encoding='utf-8')
print("Fine elaborazione.")
```

### P. S 
Small reminder:
Please remember that scraping data online is completely LEGAL, the imporatate thing is that you have to stay enter a certain range of scans per day, but otherwise it is all legal since since a certain data is posted on a website, any person can access it without any problem.

Thank you :smile:

### P. P. S

The scraper is not even at its beta version, I dare say it's in the gamma version yet :smile:, so any criticism/modification/advice is welcome

