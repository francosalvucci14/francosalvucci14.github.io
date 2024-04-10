# Progetto DataBase


# Intro

Questo è il progetto che ho fatto, in collaborazione con i miei colleghi dell'Università, per l'esame di Basi di Dati e di Conoscenza (BDC)

Il progetto rappresenta la realtà di una società di taxi (VroomA), che opera nel territorio di Roma

Questo è solo il database, ovviamente sopra al database c'è un'interfaccia web per gli utenti e gli amministratori del sistema

# Obiettivi

L'obiettivo principale di questo sistema è permettere agli utenti di organizzare gli spostamenti tramite taxi a seconda delle proprie esigenze, del tipo di veicolo scelto e del costo della tratta scelta.

Da un punto di vista societario, gli obiettivi sono quelli di valutare la qualità del lavoro degli autisti tramite i feedback forniti dai clienti e migliorare dove possibile il servizio.

# Servizi

I **ruoli aziendali** sono i seguenti:

- Autisti
- Manutentori

Gli **autisti** potranno scegliere se accettare o rifiutare la corsa, specificando in questo caso la motivazione del rifiuto.
Inoltre potranno lasciare un **feedback** all'utente riguardo il comportamento prima e durante la corsa.
Ogni **autista** ha la propria macchina privata, e può contattare i manutentori aziendali in caso di guasto del veicolo.
Ad ogni **autista** sono assegnati uno o più **turni** di lavoro, con il vincolo che il singolo autista non può essere assegnato a due turni lavorativi che hanno orario inizio e orario fine uguali.

I **manutentori** possono ricevere richieste di assistenza da parte degli autisti e contattare le officine convenzionate per effettuare il lavoro di assistenza.
Le **officine** non fanno parte della società.

Le tipologie di **veicolo** disponibili sono le seguenti:
- Base: 4 posti disponibili
- Plus: 7 posti disponibili, adibito a trasporto di carrozzine per disabili
- Premium: 12 posti disponibili, adibito a trasporto di carrozzine per disabili

Ogni **veicolo**, identificato in modo univoco dalla targa, per poter circolare, deve essere assicurato.

Quando si prenota una **corsa** (**tratta**) si possono scegliere due punti:
- Partenza, ovvero la **via** da dove si vuole iniziare la corsa
- Arrivo, ovvero la **via** in cui termina la corsa

Ogni **prenotazione** può essere accettata o rifiutata in base a determinate esigenze dell'**utente** (Es: prenotazione effettuata per errore) e dell'**autista** (Es: indisponibilità al servizio).
Ad ogni tratta completata è associato un feedback che può essere lasciato sia dall'**utente** che dall'**autista**.

Ogni **utente** può effettuare illimitate richieste di **prenotazione**, in base alle necessità personali (numero di passeggeri, persone con disabilità, punto di ritiro, punto di rilascio), con il vincolo di una corsa per volta.
A corsa completata l'**utente** può lasciare un **feedback** con un numero di stelle (da 1 a 5) e un commento.
Ogni **utente** deve aggiungere una **carta** con cui effettuare il pagamento relativo alla tratta effettuata, in un secondo momento potrà aggiungere altri metodi di pagamento secondo le proprie esigenze.
Ogni **utente** può aggiungere alla lista dei preferiti ua qualunque delle tratte effettuate da lui, scegliendo se aggiungere solo la tratta o anche l'autista.
Ogni **utente** può accedere alla cronologia delle prenotazioni effettuate.

# Schemi

Qui possiamo vedere gli schemi della base di dati da noi fatta

### Schema Concettuale

{{< figure src="/images/DB/SchemaFinale.jpg" title="Schema Concettuale">}}<br/>

### Schema Logico

{{< figure src="/images/DB/SchemaLogico.jpg" title="Schema Logico">}}<br/>

# Codice

Per vedere nel dettaglio il codice, compreso di file per la creazione delle tabelle in SQL, e file usato per la creazione di inserimenti automatici, rimando alla mia repository di Github [Progetto Database](https://github.com/francosalvucci14/Database_Project)

### Inserimenti Automatici in Python

```python
import random
from faker import Faker
import string
import decimal
import datetime
from geopy.geocoders import Nominatim

def getLatAndLong(posto):
    # calling the Nominatim tool and create Nominatim class
    loc = Nominatim(user_agent="Geopy Library")

    # entering the location name
    getLoc = loc.geocode(posto)

    return getLoc.latitude, getLoc.longitude

def prendi_due_elementi(array):
    # Scegli due indici casuali
    indice1, indice2 = random.sample(range(len(array)), 2)
    
    # Se gli elementi sono uguali, scegli un nuovo indice2
    while array[indice1] == array[indice2]:
        indice2 = random.randint(0, len(array) - 1)
    
    return array[indice1], array[indice2]


fake = Faker("it_IT")

#Funzione Rand.DDN
def genRandomDate():
    start_date = datetime.date(1975, 1, 1)
    end_date = datetime.date(2001, 12, 30)
    num_days = (end_date - start_date).days
    rand_days = random.randint(1, num_days)
    random_date = start_date + datetime.timedelta(days=rand_days)

    return random_date

#Funzione Rand.DA
def genRandomInsuranceDate():
    start_date = datetime.date(2023, 1, 1)
    end_date = datetime.date(2025, 1, 1)
    num_days = (end_date - start_date).days
    rand_days = random.randint(1, num_days)
    random_date = start_date + datetime.timedelta(days=rand_days)

    return random_date

#Funzione Rand.CD
def genRandomCardDate():
    start_date = datetime.date(2027, 1, 1)
    end_date = datetime.date(2034, 12, 30)
    num_days = (end_date - start_date).days
    rand_days = random.randint(1, num_days)
    random_date = start_date + datetime.timedelta(days=rand_days)

    return random_date

#Funzione Rand.PD
def genRandomLicenceDate():
    start_date = datetime.date(2025, 1, 1)
    end_date = datetime.date(2035, 12, 30)
    num_days = (end_date - start_date).days
    rand_days = random.randint(1, num_days)
    random_date = start_date + datetime.timedelta(days=rand_days)

    return random_date

#Funzione Rand.DDR
def genRandomRequestDate():
    start_date = datetime.date(2020, 1, 1)
    end_date = datetime.date(2024, 2, 16)
    num_days = (end_date - start_date).days
    rand_days = random.randint(1, num_days)
    random_date = start_date + datetime.timedelta(days=rand_days)

    return random_date

#Funzione Rand.Mail
def generateEmail(name, surname):
    
    domain = fake.domain_name()

    return f"{name}.{surname}@{domain}"

#Funzione Rand.T
def generateTarga():
    SYMBOLS = "ABCDEFG"
    SYMBOLS_END = "HIJKLMNOPQR"
    NUMBERS = "0123456789"
    start = "".join(random.choice(SYMBOLS) for i in range(1))
    start_2 = "".join(random.choice(SYMBOLS_END) for i in range(1))
    mezzo = "".join(random.choice(NUMBERS) for i in range(3))
    fine = "".join(random.choice(SYMBOLS) for i in range(2))

    return start+start_2+mezzo+fine

#Funzione Rand.PSW
def generatePsw():
    ALL = "0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz"
    psw = "".join(random.choice(ALL) for i in range(9))
    return psw
#Funzione Rand.CN
def generateCardNumber():
    NUMBERS = "0123456789"
    number = "".join(random.choice(NUMBERS) for i in range(16))
    return number
#Funzione Rand.Star
def checkStelleUtenti(stelle):
    if stelle == 1:
        return 1
    elif stelle == 2:
        return 2
    elif stelle == 3:
        return 3
    elif stelle == 4:
        return 4
    elif stelle == 5:
        return 5

print("Inizio esecuzione...")

print("L'ORDINE DI ESECUZIONE DEI FILE È 1.txt,2.txt,etc...")

print("Inizio Creazione 1.txt")

SYMBOLS = "ABCDEFGHIJKLMNOPQRSTUVWXYZ"
NUMBERS = "0123456789"
ALL_SYMBOLS = "0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ"
print("Inizio Creazione 2.txt")

f = open("2.txt","w+")

print("--------------- Inizio Inserimento Patente\n")

patenti = ["B","BE","B96"]
unique_Patente = []
values_patenti = []
for i in range(3000):
    data = genRandomLicenceDate()
    random_numpatente = "".join(random.choice(ALL_SYMBOLS) for i in range(9))
    categoria = "".join(random.choice(patenti) for i in range(1))
    unique_Patente.append(random_numpatente)
    
    query = "('"+ random_numpatente+ "','"+ str(data)+ "','"+ categoria+ "')"
    
    values_patenti.append(query)
f.write(
    "INSERT INTO Patenti (NumeroPatente,DDS,Categoria) VALUES "+",\n".join(values_patenti)+";"
)
f.write("\n")
print("--------------- Fine Inserimento Patente\n")
f.write("\n")
print("--------------- Inizio Inserimento Turni\n")

unique_Turno = []
ora_inizio = ['9','10','11','14']
ora_fine = ['17','20','21','22']
values_turni = []
for i in range(5):
    
    #random_turno = "".join(random.choice(NUMBERS) for i in range(1))
    inizio = "".join(random.choice(ora_inizio))
    fine = "".join(random.choice(ora_fine))
    unique_Turno.append((inizio,fine))
    query = "('"+ inizio+ "','"+ fine+ "')"
    
    values_turni.append(query)
f.write(
    "INSERT INTO Turni (OrarioInizio,OrarioFine) VALUES "+",\n".join(values_turni)+";"
)
f.write("\n")
print("--------------- Fine Inserimento Turni\n")
print("--------------- Inizio Inserimento Autisti\n")

unique_Autisti = []
values_autisti = []
stipendio = ["1200","1100","900","800"]

for i in range(3000):
    matricola = "".join(random.choice(NUMBERS) for i in range(6))
    if matricola in unique_Autisti:
        matricola = "".join(random.choice(NUMBERS) for i in range(6))
    random_patente = unique_Patente[i]
    #random_Turno = random.choice(unique_Turno[1:])
    #random_targa = random.choice(unique_Veicolo)
    nome = fake.first_name()
    cognome = fake.last_name()
    email = generateEmail(nome,cognome)
    ddn = genRandomDate()
    num_telefono = fake.phone_number()
    query = "('"+ matricola+ "','"+ str(nome)+ "','"+ str(cognome)+ "','"+ str(email)+ "','"+ str(ddn)+ "','"+ num_telefono+ "','"+ random_patente+ "','"+random.choice(stipendio)+"')"
    unique_Autisti.append(matricola)
    values_autisti.append(query)
f.write(
    "INSERT INTO Autisti (Matricola,Nome,Cognome,Email,DDN,NumeroTelefono,NumeroPatente,Stipendio) VALUES "+",\n".join(values_autisti)+";"
)
f.write("\n")
print("--------------- Fine Inserimento Autisti\n")
f.write("\n")
print("--------------- Inizio Inserimento Veicoli\n")
unique_Veicolo = []
values_veicolo = []
dict_veicoli={
    "Fiat":["Punto","Panda","Tipo"],
    "BMW":["X3","X8","X1","Gran Coupè"],
    "Audi":["RS7", "A1", "Q3", "Q8"],
    "Range Rover":["Hybrid","Defender","Sport"],
    "Alfa Romeo":["Giulia", "Giulietta"],
    "Renault":["Clio", "Captur", "Arkana"]
}

for i in range(5000):
    random_targa = generateTarga()
    #random_assicurazione = unique_Assicurazione[i]
    marca_random = random.choice(list(dict_veicoli.keys()))
    modello_random = str(random.choice(dict_veicoli[marca_random]))
    autista = random.choice(unique_Autisti)
    query = "('"+ str(random_targa)+ "','"+ str(marca_random)+ "','"+ str(modello_random)+ "','"+str(random.randint(3,12))+"','"+autista+"')"
    unique_Veicolo.append(random_targa)
    values_veicolo.append(query)

f.write(
    "INSERT INTO Veicoli (Targa,Marca,Modello,NumPosti,Matricola) VALUES "+",\n".join(values_veicolo)+";"
)
f.write("\n")
print("--------------- Fine Inserimento Veicoli\n")
print("--------------- Inizio Inserimento Assicurazione\n")

unique_Assicurazione = []
values_assicurazione = []
tipo_assicurazione=["Kasko","Furto","Incendio","Base","Polizza cristalli"]

for i in range(5000):
    random_id = str(i)
    targa = unique_Veicolo[i]
    data = genRandomInsuranceDate()
    tipo = random.choice(tipo_assicurazione)
    if str(data) < "2024-02-16":
        stato = "Scaduta"
    else:
        stato = "Valida"
    query = "('"+ str(random_id)+ "','"+ str(data)+ "','"+ str(tipo)+ "','"+ str(stato)+"','"+str(targa)+"')"
    unique_Assicurazione.append(random_id)
    values_assicurazione.append(query)
f.write(
    "INSERT INTO Assicurazioni (Numero,DDS,Tipo,Stato,Targa) VALUES "+",\n".join(values_assicurazione)+";"
)
f.write("\n")
print("--------------- Fine Inserimento Assicurazione\n")
f.write("\n")
f.write("\n")
f.write("\n")
print("--------------- Inizio Inserimento TabellaOrarioLavorativo\n")

values_tabella = []
unique_TabellaOrario = []
for i in range(15000):
    matricola = random.choice(unique_Autisti)
    turno = random.choice(unique_Turno)
    turno_inizio = turno[0]
    turno_fine = turno[1]
    data = genRandomRequestDate()
    query = "('"+ matricola+ "','"+ turno_inizio+ "','"+turno_fine+"','"+str(data)+"')"
    unique_TabellaOrario.append((matricola,turno_inizio,turno_fine,data))
    values_tabella.append(query)
f.write(
    "INSERT INTO TabellaOrarioLavorativo (Matricola,OraInizio,OraFine,Data) VALUES "+",\n".join(values_tabella)+";"
)
f.write("\n")
print("--------------- Fine Inserimento TabellaOrarioLavorativo\n")
f.write("\n")
print("--------------- Inizio Inserimento Manutentori\n")

unique_Manutentori = []
values_manutentori = []
qualifica = ["Gommista","Elettrauto","Meccanico","Carrozziere"]
for i in range(200):
    random_id = str(i)
    nome = fake.first_name()
    cognome = fake.last_name()
    email = generateEmail(nome,cognome)
    ddn = genRandomDate()
    query = "('"+ random_id+ "','"+ random.choice(qualifica)+ "')"
    telefono = fake.phone_number()
    query = "('"+ random_id+ "','"+ str(nome)+ "','"+ str(cognome)+ "','"+ str(email)+ "','"+ str(ddn)+ "','"+ str(telefono)+ "','"+random.choice(qualifica)+"')"
    unique_Manutentori.append(random_id)
    values_manutentori.append(query)
f.write(
    "INSERT INTO Manutentori (ID_Manutentore,Nome,Cognome,Email,DDN,NumeroTelefono,Qualifica) VALUES "+",\n".join(values_manutentori)+";"
)
f.write("\n")
print("--------------- Fine Inserimento Manutentori\n")
f.write("\n")
print("--------------- Inizio Inserimento ContattaPerGuasto\n")

unique_Contatto = []
values_contatto = []
motivi = ["Gomma Bucata","Spia dell motore accesa","Radiatore bucato","Batteria scarica","Problema con il FAP","Errore centralina","Specchietto rotto","Guarnizione della testata bruciata","Rottura degli ammortizzatori","Semiasse distrutto","Differenziale rotto","La macchina non parte","Cambio pasticche dei freni"]

for i in range(1500):
    random_manutentore = random.choice(unique_Manutentori)
    random_autista = random.choice(unique_Autisti[0:200])
    data = genRandomInsuranceDate()
    query = "('"+ random_manutentore+ "','"+ random_autista+ "','"+random.choice(motivi)+"','"+str(data)+"')"
    unique_Contatto.append((random_manutentore,random_autista))
    values_contatto.append(query)
f.write(
    "INSERT INTO ContattaPerGuasto (ID_Manutentore,Matricola,Motivo,Data) VALUES "+",\n".join(values_contatto)+";"
)
f.write("\n")
print("--------------- Fine Inserimento ContattaPerGuasto\n")

print("2.txt Done")
f.close()

print("Inizio Creazione 3.txt")
f = open("3.txt","w+")

f.write("\n")
print("--------------- Inizio Inserimento Utenti\n")

unique_Utenti = []
values_utenti = []

for i in range(10000):
    random_id = str(i)
    surname = fake.last_name()
    name = fake.first_name()
    email = str(generateEmail(name, surname))
    psw = generatePsw()
    data = genRandomDate()
    query = "('"+ random_id+ "','"+ name+ "','"+ surname+ "','"+email+"','"+psw+"','"+str(data)+"')"
    unique_Utenti.append(random_id)
    values_utenti.append(query)
f.write(
    "INSERT INTO Utenti (ID_Utente,Nome,Cognome,Email,Password,DDN) VALUES "+",\n".join(values_utenti)+";"
)
f.write("\n")
print("--------------- Fine Inserimento Utenti\n")
f.write("\n")
print("--------------- Inizio Inserimento Carte\n")

unique_Carta = []
values_carta = []

utente_carta = []
for i in range(20000):
    
    numero_Carta = str(random.randint(4,5))+"".join(str(random.randint(0,9)) for i in range(3))+" "+"".join(str(random.randint(0,9)) for i in range(4))+" "+"".join(str(random.randint(0,9)) for i in range(4))+" "+"".join(str(random.randint(0,9)) for i in range(4))
    data_scadenza = genRandomCardDate()
    cvv = "".join(str(random.randint(0,9)) for i in range(3))
    utente = random.choice(unique_Utenti)
    query = "('"+ numero_Carta+ "','"+ str(data_scadenza)+ "','"+ cvv+ "','"+utente+"')"
    unique_Carta.append(numero_Carta)
    values_carta.append(query)
    
    utente_carta.append((utente,numero_Carta))
f.write(
    "INSERT INTO Carta (NumeroCarta,DataScadenza,CVV,ID_Utente) VALUES "+",\n".join(values_carta)+";"
)
print("--------------- Fine Inserimento Carte\n")


print("3.txt Done")
f.close()
print("Inizio creazione 4.txt")
f = open("4.txt","w+")
f.write("\n")
print("--------------- Inizio Inserimento Fermate\n")

unique_Fermata = []
values_fermata = []
fermate = ["Anagnina","Termini","Giardinetti","Lucio Sestio","Porta Furba","Tor Bella Monaca","Campo de Fiori","Trastevere","Tufello","Pigneto","Palmiro Togliatti","Salaria","Verano","Prima Porta","Colosseo","Prenestina"]

for i in range(16):
    
    fermata_choice = fermate[i]
    latitudine,longitudine = getLatAndLong(fermata_choice)
    query = "('"+ fermata_choice+ "','"+ str(latitudine)+ "','"+ str(longitudine)+ "')"
    unique_Fermata.append(fermata_choice)
    values_fermata.append(query)

f.write(
    "INSERT INTO Fermate (NomeFermata,Latitudine,Longitudine) VALUES "+",\n".join(values_fermata)+";"
)
f.write("\n")
print("--------------- Fine Inserimento Fermate\n")
f.write("\n")
print("--------------- Inizio Inserimento RichiestaPrenotazioni\n")

unique_RichPren = []
values_ricpren = []
date = []
ora = ['9','10','11','14','15','16','20','21','22']
id_carta_utente = []
for i in range(20000):
    #random_id = str(i)
    passeggeri = str(random.randint(1,12))
    
    utente = random.choice(unique_Utenti)
    #autista = random.choice(unique_Autisti)
    data = genRandomRequestDate()
    orario = random.choice(ora)
    partenza,arrivo = prendi_due_elementi(unique_Fermata)
    
    query = "('"+str(utente)+"','"+ str(partenza)+ "','"+ str(arrivo)+ "','"+str(data)+"','"+str(orario)+"','"+str(passeggeri)+"')"
    unique_RichPren.append((utente,partenza,arrivo,data,orario))
    values_ricpren.append(query)
    date.append(data)
f.write(
    "INSERT INTO RichiestePrenotazioni (ID_Utente,Partenza,Arrivo,DataRichiesta,OrarioRichiesta,NumeroPasseggeri) VALUES "+",\n".join(values_ricpren)+";"
)
f.write("\n")
print("--------------- Fine Inserimento RichiestaPrenotazioni\n")
f.write("\n")
print("--------------- Inizio Inserimento TratteCompletate\n")

unique_TrattaC = []
values_trattac = []
costo = ["25","65","115","35","50"]
pagamento = ["Carta di credito","Paypal","Contanti","Satispay","Carta di debito","CashUp","Postepay"]
for i in range(15000):
    pk = unique_RichPren[i]
    costi = random.choice(costo)
    orario_pagamento = random.choice(ora)
    if orario_pagamento < pk[4]:
        orario_pagamento = "23"
    metodo = random.choice(pagamento)
    autista = random.choice(unique_Autisti)
    query = "('"+ str(pk[0])+ "','"+ str(pk[1])+ "','"+ str(pk[2])+ "','"+ str(pk[3])+ "','"+ str(pk[4])+ "','"+ str(costi)+ "','"+ str(metodo)+ "','"+ str(pk[3])+ "','"+ str(orario_pagamento)+ "','"+str(autista)+"')"
    
    unique_TrattaC.append((pk[0],pk[1],pk[2],pk[3],pk[4]))
    values_trattac.append(query)
    
f.write(
    "INSERT INTO TratteCompletate (ID_Utente,Partenza,Arrivo,DataRichiesta,OrarioRichiesta,Costo,MetodoDiPagamento,DataPagamento,OraPagamento,Autista) VALUES "+",\n".join(values_trattac)+";"
)
f.write("\n")
print("--------------- Fine Inserimento TratteCompletate\n")
f.write("\n")
print("--------------- Inizio Inserimento Feedback\n")

unique_Feed = []
values_feed = []
feedback_utente = {
                    1: ["Non lo prenderò mai più!","Esperienza orribile","Guidava in stato di ebrezza"],
                    2: ["Non mi è piaciuto lo stile di guida","La prossima volta preferirei un altro autista","Non guidava in modo sicuro"],
                    3: ["Nulla di particolare","Tutto nella norma"],
                    4: ["Veicolo molto pulito e comodo.","Esperienza normale"],
                    5: ["Autista veramente cordiale","Ottima esperienza, lo dirò a tutti"],
                   }

feedback_autisti = {
                    1: ["Utente scortese!","L utente offende","L utente insisteva nel cambiare strada"],
                    2: ["Utente ritardatario","Non rispetta l autista","Stava fumando in macchina"],
                    3: ["Nulla di particolare","Utente ok"],
                    4: ["Utente rispettoso.","Utente gentile"],
                    5: ["Utente veramente genuino","Molto bravo e cortese"],
                   }

for i in range(15000):
    random_id = str(i)
    
    stelle_random_ut = random.choice(list(feedback_utente.keys()))
    
    commento_ut = str(random.choice(feedback_utente[stelle_random_ut]))

    #stelle_random_aut = checkStelleUtenti(stelle_random_ut)
    stelle_random_aut = random.choice(list(feedback_autisti.keys()))
    
    commento_aut = str(random.choice(feedback_autisti[stelle_random_aut]))
    fk_trattac = random.choice(unique_TrattaC)
    query = "('"+ random_id+ "','"+ str(stelle_random_ut)+ "','"+ str(commento_ut)+ "','"+str(stelle_random_aut)+"','"+str(commento_aut)+"','"+str(fk_trattac[0])+"','"+str(fk_trattac[1])+"','"+str(fk_trattac[2])+"','"+str(fk_trattac[3])+"','"+str(fk_trattac[4])+"')"

    unique_Feed.append(random_id)
    values_feed.append(query)
    unique_TrattaC.remove(fk_trattac)

f.write(
    "INSERT INTO Feedback (ID_Feedback,StelleUtente,CommentoUtente,StelleAutista,CommentoAutista,ID_Utente,Partenza,Arrivo,DataRichiesta,OrarioRichiesta) VALUES "+",\n".join(values_feed)+";"
)
f.write("\n")
print("--------------- Fine Inserimento Feedback\n")
f.write("\n")
print("--------------- Inizio Inserimento TratteRifiutate\n")

unique_TrattaR = []
values_trattar = []
motivi = ["Problema generale","Indisponibilità al servizio","Troppo lontano","Fuori dal mio orario lavorativo","Utente con recensioni troppo negative"]
for i in range(5000):
    fk_prenotazione = unique_RichPren[15000+i]
    motivo = random.choice(motivi)
    autista = random.choice(unique_Autisti)
    query = "('"+ str(fk_prenotazione[0])+ "','"+ str(fk_prenotazione[1])+ "','"+ str(fk_prenotazione[2])+ "','"+ str(fk_prenotazione[3])+ "','"+ str(fk_prenotazione[4])+ "','"+ str(motivo)+ "','"+str(autista)+"')"
    unique_TrattaR.append(fk_prenotazione)
    values_trattar.append(query)
f.write(
    "INSERT INTO TratteRifiutate (ID_Utente,Partenza,Arrivo,DataRichiesta,OrarioRichiesta,Motivazione,Autista) VALUES "+",\n".join(values_trattar)+";"
)
f.write("\n")
print("--------------- Fine Inserimento TratteRifiutate\n")
print("4.txt Done")
f.close()
```
<br/>
Questo file genera 10K inserimenti per ogni tabella (alcune tabelle hanno anche 20K o più inserimenti)

# Considerazioni Finali

In questo post ho riassunto molto brevemente tutto quello che abbiamo fatto per questo progetto.

Se volete più informazioni, o una relazione più dettagliata, vi rimando a questo file qui [RelazioneFinale](https://github.com/francosalvucci14/Database_Project/blob/main/documentazione/FaseTreCorretta.md)
