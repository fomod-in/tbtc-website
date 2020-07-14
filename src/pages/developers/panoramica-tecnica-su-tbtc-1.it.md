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

The Keep network requires a trusted source of randomness to select tBTC signers. This takes the form of a BLS Threshold Relay.

When a request comes in to create a signing group, the tBTC system uses a random seed from a secure decentralized random beacon to randomly select signing group members from the eligible pool of signers. These signers coordinate a distributed key generation protocol that results in a public ECDSA key for the group, which is used to produce a wallet address that is then published to the host chain. This completes the signer selection phase.

## Threshold Signatures

tBTC uses threshold signatures for key generation. Threshold signatures allow for a group of signers to generate a single public key from a set of private key &quot;shares.&quot; This method enables a subset of the signers to create signatures on behalf of the larger group. Users can verify groups of signers through a single public key corresponding to multiple private keys. This provides security without the work requirements of standard multisig designs.

Threshold signatures provide a number of benefits:

* Relatively little coordination is needed to set up a group
* No single member of the group has to be trusted
* They are resilient to as much as half of the group being malicious or failing to generate

For tBTC v1, signing groups are 3-of-3, meaning they are groups of 3 signers that require all 3 signers to collaborate to create signatures on behalf of the group.

You can find additional information about threshold signatures [here](https://blog.keep.network/threshold-signatures-ff2c2b98d9c7).

Visit our [GitHub](https://github.com/keep-network/tbtc) for more information, tools, and documentation. [Join the tBTC mailing list](https://tbtc.network/#mailing-list) for updates, including information about tBTC&#39;s upcoming launch on Ethereum mainnet. To learn more about tBTC&#39;s technical design, read the [technical spec](http://docs.keep.network/tbtc/index.pdf). Join the [Keep #tbtc channel on Discord](https://chat.tbtc.network) for technical questions about tBTC and [tbtc.js](https://tbtc.network/news/2020-02-14-announcing-tbtc-js), and follow [along on Twitter](https://twitter.com/search?q=%22%23tbtc%22&src=typed_query) news and opportunities to participate.
