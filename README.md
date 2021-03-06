# SmartHome: Casa domotica per ESP8266 basato sul protocollo MQTT

Il progetto SmartHome ha lo scopo di realizzare dei moduli domotici a basso costo per rendere "intelligente" la casa.  
Si divide in moduli realizzati con il microcontrollore ESP8266: modulo tapparella, modulo temperatura, modulo interruttore e modulo cancello.  
Ogni modulo, ha una propria logica di funzionamento e gestisce i GPIO (ingressi ed uscite) ad esso collegati.  
Ogni modulo comunica attraverso il protocollo MQTT con il broker, che puo' essere locale (LAN) o remoto (internet).  
Per interagire con i singoli moduli bisogna mandare ad esso specifici comandi (contraddistinto da un topic MQTT univoco). Si possono avere piu' moduli dello stesso tipo, basta che abbiano tutti un topic diverso (ad esempio: "tapparella_sala", "tapparella_cucina", "tapparella_bagno", ecc.) .  
Inviando i comandi al modulo (topic), si interagisce con esso, facendogli fare delle operazioni o interrogandolo. Il modulo risponderà sul topic "ack".  
### Protocollo MQTT (Message Queuing Telemetry Transport) 
e' un protocollo di comunicazione machine-to-machine (M2M) progettato come un protocollo estremamente leggero che implementa un architettura publish/subscribe.

Nella sua essenza un architettura Publish/Subscribe prevede 4 elementi principali:
* Un agent con il ruolo di Publisher: che invia messaggi al broker indirizzati ad uno specifico Topic
* Un agent con il ruolo di Subscriber: che riceve messaggi dal broker provenienti da uno specifico Topic
* Un Broker di comunicazione: un elemento centrale che orchestra tutti i processi di comunicazione e gestisce il ciclo di vita dei topic e dei messaggi
* Un Topic: un canale di comunicazione dedicato. 


Piccolo video di esempio [QUI](https://www.youtube.com/watch?v=1_ZGrr2Y1Ds)  
A breve sarà disponibile nel Google Play un [applicazione](https://play.google.com/store/apps/details?id=roncoa.SmartHome) Android dalla quale è possibile gestire tutti i vari moduli.  
Se vuoi diventare un beta tester clicca [QUI](https://play.google.com/apps/testing/roncoa.SmartHome)  

## Modulo "tapparella"
Il modulo "tapparella" serve per comandare tapparelle o serrande.  
Ad esso sono collegati:
* 2 GPIO usati per comandare 2 relè per il movimento della tapparella (SU/GIU).  
* 2 GPIO usati come ingressi fisici da pulsanti per comandare direttamente il movimento della tapparella(SU/GIU).  

Nello sketch vanno configurati i seguenti parametri:
```
Tapparella_Topic        -> indica il topic MQTT al quale inviare i comandi per muovere/gestire il modulo  
RELE_tapparella_SU      -> GPIO usato per comandare il RELE tapparella SU
RELE_tapparella_GIU     -> GPIO usato per comandare il RELE tapparella GIU
BOTTONE_tapparella_SU   -> GPIO usato per leggere lo stato del pulsante che alza la tapparella
BOTTONE_tapparella_GIU  -> GPIO usato per leggere lo stato del pulsante che abbassa la tapparella
```
Comandi da inviare al topic MQTT "**Tapparella_Topic**":
```
su            -> comando SU  
giu           -> comando GIU  
stop          -> comando STOP  
t=XX o T=XX   -> XX indica il tempo massimo di movimento della tapparella (es. T=30 -> setta il tempo massimo a 30 sec.)  
stato         -> restituisce sul topic ACK lo stato dei relè e per quanto tempo la tapparella può restare in azione (in sec.)  
stato2        -> restituisce sul topic ACK2 lo stato dei relè  
reset         -> pulisce la memoria EEPROM e resetta l'ESP
+XX           -> alza la tapparella della percentuale XX (es. +50 -> alza la tapparella del 50% del tempo massimo impostato)
-XX           -> abbassa la tapparella della percentuale XX (es. +30 -> abbassa la tapparella del 30% del tempo massimo impostato)
```

## Modulo "temperatura"
Il modulo "temperatura" è un termostato, serve per comandare apparecchiature per il riscaldamento.  
Ad esso sono collegati:
* 1 GPIO viene usato per la sonda di temperatura e umidità (DHT22).  
* 1 GPIO viene usato per comandare il relè termostato (se impostato in AUTO, funziona come un normale termostato, se impostato in MAN, lo si può commutare a piacere).  
* 1 GPIO viene usato per comandare un relè liberamente gestibile dall'utente.  
* 2 GPIO vengono usati per interfacciare un display I2C (SSD1306).  

Nello sketch vanno configurati i seguenti parametri:
```
Temperatura_Topic         -> indica il topic MQTT al quale inviare i comandi per muovere/gestire il modulo  
RELE_A_temperatura        -> GPIO usato per comandare il RELE del termostato
RELE_B_temperatura        -> GPIO usato per comandare il RELE AUX
DHTPIN_temperatura        -> GPIO usato per il sensore di temperatura DHT22
I2C_DISPLAY_ADDRESS_temperatura      -> indirizzo I2C Display
SDA_PIN_temperatura       -> GPIO usato per comandare il pin SDA del display
SDC_PIN_temperatura       -> GPIO usato per comandare il pin SDC del display
```
Comandi da inviare al topic MQTT "**Temperatura_Topic**":
```
man           -> Imposta il termostato in "manuale"
auto          -> Imposta il termostato in "automatico"
t=XX o T=XX   -> Imposta il termostato alla temperatura XX
1on           -> Imposta il relè termostato a ON
1off          -> Imposta il relè termostato a OFF
2on           -> Imposta il relè AUX a ON
2off          -> Imposta il relè AUX a OFF
stato         -> restituisce sul topic ACK lo stato dei relè la temperatura impostata e lo stato del termostato
stato2        -> restituisce sul topic ACK2 lo stato dei relè
read          -> legge la temperatura
reset         -> pulisce la memoria EEPROM e resetta l'ESP
```

## Modulo "interruttore"
Il modulo "interruttore" serve per comandare luci o prese. 
Ad esso sono collegati:
* 2 GPIO vengono usati per comandare 2 relè liberamente gestibili dall'utente.  
* 2 GPIO vengono usati come ingressi fisici da pulsanti per comandare direttamente i 2 relè.

Nello sketch vanno configurati i seguenti parametri:
```
Interruttore_Topic        -> indica il topic MQTT al quale inviare i comandi per muovere/gestire il modulo  
RELE_A_interruttore       -> GPIO usato per comandare il RELE A
RELE_B_interruttore       -> GPIO usato per comandare il RELE B
BOTTONE_A_interruttore    -> GPIO usato per leggere lo stato del pulsante che comanda il RELE A
BOTTONE_B_interruttore    -> GPIO usato per leggere lo stato del pulsante che comanda il RELE B
TIPO_BOTTONE_A_interruttore -> tipo di bottone A "interruttore" o "pulsante"
TIPO_BOTTONE_B_interruttore -> tipo di bottone B "interruttore" o "pulsante"
```
Comandi da inviare al topic "Interruttore_Topic":
```
1on           -> Imposta il relè A a ON  
1off          -> Imposta il relè A a OFF  
2on           -> Imposta il relè B a ON  
2off          -> Imposta il relè B a OFF  
stato         -> restituisce sul topic ACK lo stato dei relè  
stato2        -> restituisce sul topic ACK2 lo stato dei relè  
reset         -> pulisce la memoria EEPROM e resetta l'ESP  
```

## Modulo "cancello"
Il modulo "cancello" serve per comandare cancelli o portoni. 
Ad esso sono collegati:
* 2 GPIO vengono usati per comandare 2 relè temporizzati liberamente gestibili dall'utente.  

Nello sketch vanno configurati i seguenti parametri:
```
Cancello_Topic         -> indica il topic MQTT al quale inviare i comandi per muovere/gestire il modulo
RELE_A_cancello        -> GPIO usato per comandare il RELE A
RELE_B_cancello        -> GPIO usato per comandare il RELE B
BOTTONE_A_cancello     -> GPIO usato per leggere lo stato del pulsante che comanda il RELE A
BOTTONE_B_cancello     -> GPIO usato per leggere lo stato del pulsante che comanda il RELE B
TEMPO_IMPULSO_cancello -> indica la durata dell'impulso al rele' (in millisecondi)
```
Comandi da inviare al topic "Interruttore_Topic":
```
1on           -> Da un impulso ON al relè A per un determinato tempo gestibile dall'utente
2on           -> Da un impulso ON al relè B per un determinato tempo gestibile dall'utente
stato         -> restituisce sul topic ACK lo stato dei relè  
stato2        -> restituisce sul topic ACK2 lo stato dei relè  
reset         -> pulisce la memoria EEPROM e resetta l'ESP  
```

# SmartHome v4  
Utilizzare l'app dalla versione (16) 0.4β in poi.  
Changelog versione v4:  
* Raggruppato tutti i moduli in un unico file sorgente, basta configurare il file "SmartHome_config.h" .
```
NODI_TAPPARELLA     X                       // Max 2 nodi tapparella (consigliato usarne max 1)
NODI_INTERRUTTORE   X                       // Max 4 nodi interruttore
NODI_CANCELLO       X                       // Max 2 nodi cancello
NODI_TEMPERATURA    X                       // Max 1 nodo temperatura
```
* Aggiunto comando per alzare/abbassare tapparelle in percentuale (es. +50 alza del 50% , -30 abbassa del 30%).
```
+XX            -> comando SU in percentuale (alza la tapparella del XX% es. +50 alza la tapparella del 50%)  
-XX            -> comando GIU in percentuale (abbassa la tapparella del XX% es. -30 abbassa la tapparella del 30%)  
```
* Bug fix.  
  
# SmartHome v3  
Utilizzare l'app dalla versione (13) 0.3.3β in poi.  
Changelog versione v3:  
* Miglioramenti per la stabilità.  
* Introdotto aggiornamento OTA da Arduino IDE.  
* Miglioramenti vari.  
* Bug fix.
    


