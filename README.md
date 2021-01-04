# Relazione

##  Architettura generale

### Unità di controllo

Nell'unità di controllo ci sono 17 stati. 
Uno stato si dedica al controllo dell'inserimento del bancomat, 5 stati sono dedicati al controllo del codice per ogni tentativo (i tentativi sono 3 quindi un totale di 15 stati utilizzati) e uno manda un segnale di start al datapath.
 

 1. **S0**
	In questo stato si controlla se il bancomat è inserito o no. Se non risulta inserito rimane in attesa altrimenti passiamo allo stato successivo.
	
 2. **ING1_0**

	Questo stato serve a controllare il primo numero che viene inserito. Il passaggio allo stato successivo dipende dagli ingressi, infatti se viene inserito il numero corretto (5 decimale quindi 0101 binario) si passa alla stato **ING1_1** altrimenti allo stato **ERR1_1**.

 3. **ING1_1**

	Lo stato **ING1_1** controlla il secondo numero in ingresso ed ha lo stesso comportamento di  **ING1_0**.
	Se il secondo è corretto (5 decimale quindi 0101 binario)  si passa al controllo dell'ultima cifra nello stato **ING1_2** altrimenti si passa allo stato di errore **ERR1_2**.

 4. **ERR1_1**

	Lo stato **ERR1_1** passa in qualsiasi caso in  **ERR1_2**.

 5. **ING1_2**

	L'ultima cifra viene controllata in **ING1_2** se è corretta (ovvero 0 decimale quindi 0000 binario) si passa allo stato **CORRETTO**, se invece risulta errata il prossimo stato è **ING2_0**.

 6. **ERR1_2**

	Lo stato **ERR1_2** andrà sempre in **ING2_0**.

 7. **CORRETTO**

	Questo stato manda il segnale di **CHECK_DISPONIBILITÀ** al datapath.
 
 8. **ING2_0** e **ING3_0**

 	Stati sono analoghi a **ING1_0** per il secondo e il terzo tentativo
 
 9. **ING2_1** e **ING3_1**

	Stati sono analoghi a **ING1_1** per il secondo e il terzo tentativo

 10. **ING2_2** e **ING3_2**

	Stati sono analoghi a **ING1_2** per il secondo e il terzo tentativo

 11. **ERR2_1** e **ERR3_1**

	Stati sono analoghi a **ERR1_1** per il secondo e il terzo tentativo

 12. **ERR2_2** e **ERR3_2**
 
	Stati sono analoghi a **ERR2_1** per il secondo e il terzo tentativo

### Immagine unità di controllo
![enter image description here](https://cdn.discordapp.com/attachments/791357643905171471/795579323615674398/STG.png)

### Datapath
Nel datapath viene controllato se il **CASH_RICHIESTO** è 1/4 del **CASH_DISPONIBILE**.
Il datapath è formato da 6 unità funzionali che servono per controllare che la specifica venga rispettata.

1. Sommatore 1°
	**INPUT**
	- **CASH_RICHIESTO**
	- **CASH_RICHIESTO**
		
	**OUTPUT**
	- Il doppio del **CASH_RICHIESTO** 
2. Sommatore 2°
	**INPUT**
	- L'uscita del sommatore precedente
	- **CASH_RICHIESTO**
		
	**OUTPUT**
	- Il triplo del **CASH_RICHIESTO** 
3. Sommatore 3°
	 **INPUT**
	 -  L'uscita del sommatore precedente  	
	 - **CASH_RICHIESTO**
		
	**OUTPUT**
	- Il quadruplo del **CASH_RICHIESTO** 
4. Maggiore 
 **INPUT**
	 - L'uscita dell'ultimo sommatore
	 - 	**CASH_DIPONIBILE**
	
	**OUTPUT**
	- 0 se l'uscita dell'ultimo multiplexer è maggiore
	- 1 se **CASH_DISPONIBILE** è maggiore
5. AND
	**INPUT**
	- **CHECK_DISPONIBILITÀ**
	- L'uscita dell'unità funzionale di controllo precedente

	**OUTPUT**
	Il nome dell'output diventa **CASH_OK**
	- 1 se sia **CHECK_DISPONIBILITÀ** e l'altra uscita sono 1
	- 0 se almeno uno dei 2 è 0

	
7. Multiplexer
	**INPUT** 
	-  **CASH_RICHIESTO**
	- 10 bit tutti a 0

	**SELECT**
	- Il segnale di **CASH_OK**

	**OUTPUT**
	- **SEL = 0**: La serie di bit posti a 0
	- **SEL = 1**: **CASH_DA_EROGARE** che assume il valore di **CASH_RICHIESTO**

### IMMAGINE DATAPATH 
![enter image description here](https://media.discordapp.net/attachments/791357643905171471/795322878836998194/Datapath.png?width=1063&height=671)
