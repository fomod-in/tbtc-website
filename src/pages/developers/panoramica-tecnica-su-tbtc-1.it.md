---
template: resource
title: Panoramica tecnica su tBTC
heading: Panoramica tecnica su tBTC
description: >-
  Dettagli sul token di deposito TDT , lotti multipli, Keep random beacon, e le
  threshold signatures.
date: 2020-01-04T12:07:14.261Z
tags:
  - developers
---
tBTC è stato pensato con una architettura che determina implicazioni importanti per gli utenti. Questo articolo descrive 4 capisaldi fondamentali: Token TDT, Lotti multipli, Keep Random Beacon e le threshold signatures.

## Token di deposito TBTC (TDT)

Il TDT e un token non fungibile che è minato quando un utente inoltra una richiesta di deposito. Un TDT è un ERC-721 che figura da controparte a TBTC. Rappresenta quindi una  "intenzione" di deposito che sottende quindi una UTXO sulla blockchain di Bitcoin.

I depositi di TBTC possono essere bloccati o sbloccati. Un deposito bloccato può essere riscattato (sbloccato) solo dal titolare del corrispondente TDT. Ogni TDT è unico e collegato ad un solo deposito, e procura un diritto esclusivo di riscatto fino a 6 mesi.

Una volta che il deposito è pienamente concluso attraverso la prova data dal trasferimento effettivo dei BTC, l'holder può richiederne il riscatto, e, dopo aver pagato una commissione di firma al signer, è assicurato sulla medesima UTXO che ha utilizzato per il deposito sulla rete Bitcoin.

Il TDT e il TBTC sono interscambiabili attraverso uno smart contract chiamato "il distributore" (the vending machine), che controlla gli scambi fra TDT e TBTC e viceversa.

* Dato un TDT, sarà minato un TBTC.
* Dato un TBTC, potrà essere burnato e restituire uno specifico TDT.

*Un TDT è necessario per riscattare un deposito bloccato di BTC*. Consideralo come fosse lo scontrino fiscale di una merce che puoi rendere al venditore; senza di esso, non puoi ottenere il reso ne la garanzia.

I TDT sono trasferibili. I detentori possono anche scegliere di venderli o di utilizzarli a collaterale su altre applicazioni.

In caso di evento fraudolento o problemi di collateralizzazione, il possessore di TDT è sempre garantito grazie al collaterale del gruppo di signers. Se il deposito viene riscattato da un altro utente, il TDT holder viene invece garantito direttamente attraverso il TBTC (pagando una commissione minore). Considera che un TDT holder può sempre riscattare il suo deposito BTC anche dopo la scadenza dei 6 mesi, se nessun altro utente ha già provveduto a riscattarli.

Dato che c'è più convenienza a rubare 1 BTC depositato piuttosto che 0.001 BTC, si è molto più suscettibili a attacchi come quelli di tipo re-orgs. Come token non fungibile, TDT consente che tale rischio sia prezzato, e ciò è una questione rilevante per le applicazioni che usano BTC come collaterale. Ogni detentore di TDT necessita di valutare il profilo di rischio di un determinato token. TDT è progettato per fornire una mitigazione del rischio, dato che gli attackers dei depositi garantiti da TDT impattano solo ed esclusivamente il possessore di TDT, e non tutta la catena blockchain a garanzia (BTC).

## Lotti e dimensioni

I depositi in TBTC sono gestiti in lotti. Per rendere il sistema razionale e sostenibile, i lotti rappresentano un set di una dimensione fissa, gestiti dal sistema. Se un utente vuole depositare un ampio numero di BTC che supera la dimensione dei lotti concessi, deve inoltrare molteplici richieste di deposito. Questo consente a ogni deposito di essere garantito da diversi gruppi di signers, e ciò permette non solo una più efficiente collateralizzazione ma anche la mitigazione del rischio di problemi legati ai gruppi di signers, comportamenti malevoli o altri fallimenti. Questa architettura ha importanti implicazioni a cui gli utenti devono prestare attenzione.

*Ogni deposito deve rispettare un lotto di dimensione fissa*

Il sistema etichetta le richieste sovradimensionate o sottodimensionate -- nelle quali l'utente inoltra richieste di deposito superiore o inferiore alla dimensione fissa del lotto -- come un errore. Queste richieste distorgono la logica di collateralizzazione progettata per i signers, quindi il sistema trasferisce i costi di queste richieste sull'utente finale.

In caso di deposito sottodimensionato, il sistema non crea una "proof" necessaria al riscatto dei TBTC. L'utente quindi rinuncia a bloccare i BTC in un deposito che può essere suddiviso fra diversi signers.

*L'utente deve tenere a mente questa fattispecie. In una situazione in cui l'unico lotto disponibile è ad esempio di 1 BTC, è semplice immaginare che l'utente possa provare a richiedere 1 TBTC attraverso 2 depositi da 0,5 BTC. In tal caso, l'utente perde i suoi BTC in quanto il sistema semplicemente riconosce 2 richieste di deposito sottodimensionate rispetto al lotto standard disponibile (1 BTC). Pertanto, il lotto ha una dimensione fissa e quel lotto deve essere riempito con quello stesso identico ammontare.*

In caso di deposito sovradimensionato, il sistema genera una "proof" necessaria al riscatto, ma solo pari all'ammontare del lotto disponibile. In un mercato pareto-efficiente, ci si aspetta che tale deposito venga immediatamente riscattato per opportunità di arbitraggio. Finchè il deposito non viene riscattato per il suo ammontare, l'utente perde la quota in eccesso (sovradimensionata) rispetto al lotto disponibile.

*Ad esempio, se è disponibile un lotto da 1 BTC e un utente deposita 1,4 BTC, riceverà una "proof" atta a consentirgi il riscatto di 1 solo BTC (pari alla dimensione del lotto). In quel momento però viene a crearsi un deposito sovradimensionato nel sistema, che si presterebbe ad un opportunità di arbitraggio per i detentori di 1 TBTC (che riscatterebbero 1,4 BTC). A quel punto, l'utente vedrebbe persi gli extra 0,4 BTC.*

Il sistema accetta solo la prima UTXO maggiore della dimensione del lotto. Tutti gli altri BTC inviati sono da considerarsi persi. Quindi è imperativo per l'utente utilizzare una singola UTXO. Accettare UTXO multipli da parte degli utenti imporrebbe un aumento della complessità e del gas cost in quanto ogni UTXO genera una ed una sola firma. I signers si vedrebbero costretti a firmare ogni transazione anche se non riescono a riconoscere a quanto ammonta il totale delle UTXO.

## Random Beacon per la selezione dei signers

Il Keep Network si basa su un meccanismo certificato di selezione casuale del signers in tBTC. Questo meccanismo prende il nome di "BLS Threshold Relay".

Quando arriva una richiesta di creazione di gruppo di signers, il sistema tBTC usa un seed casuale e decentralizzato per identificare il gruppo all'interno di una lista di candidati. Questi signers coordinano un protocollo di generazione di chiavi distribuite che producono una chiave ECDSA pubblica per lo stesso gruppo, usata per generare un wallet address che poi viene pubblicato sulla blockchain. Questo completa la fase di selezione randomica dei signers.

## Threshold Signatures

tBTC usa le threshold signatures per la generazione di chiavi. La Threshold signatures consente per ogni gruppo di signers, di generare una singola chiave pubblica da un set di chiavi private. Questo metodo abilita un sottogruppo di signers a creare una nuova firma per conto di un gruppo di signers più grande. Gli utenti possono verificare i gruppi di signers attraverso un unica chiave pubblica che corrisponde a molteplici chiavi private. Questo fornisce la sicurezza senza il bisogno di ricorrere allo standard multisig.

Threshold signatures benefits:

* Coordinazione semplice per costituire un gruppo di signers
* Non richiesto il trust su singoli membri del gruppo 
* Resilienza a comportamenti malevoli

Nella v1 di tBTC v1, i gruppi di signers sono 3-di-3, e ciò significa che bastano 3 signers in coordinazione per generare firme per nome e conto di un gruppo di signers più ampio.

Puoi trovare maggiori informazioni [qui](https://blog.keep.network/threshold-signatures-ff2c2b98d9c7).

Visita il nostro [GitHub](https://github.com/keep-network/tbtc) per altri documenti, tools e informazioni. Entra nella [tBTC mailing list](https://tbtc.network/#mailing-list) per aggiornamenti, inclusi altri dettagli su tBTC e sulla rete Ethereum. Per risorse addizionali, leggi il [documento tecnico](http://docs.keep.network/tbtc/index.pdf). Entra nel [canale #tbtc su Discord](https://chat.tbtc.network) per discussioni tecniche su tBTC e [tbtc.js](https://tbtc.network/news/2020-02-14-announcing-tbtc-js), e seguici su [Twitter](https://twitter.com/search?q=%22%23tbtc%22&src=typed_query) per ulteriori opportunità.
