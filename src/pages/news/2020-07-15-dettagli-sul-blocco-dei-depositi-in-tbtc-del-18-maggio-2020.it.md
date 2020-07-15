---
template: news-item
title: '  # Dettagli sul Blocco dei Depositi in tBTC del 18 Maggio 2020'
description: '  # Dettagli sul Blocco dei Depositi in tBTC del 18 Maggio 2020'
date: 2020-05-21T07:35:34.246Z
---
2020-05-21

La mattina del 18 Maggio 2020 il [è stato attivato il blocco di emergenza per 10 giorni sui depositi](https://etherscan.io/tx/0xc5c08e982cfdd9d11b01c32bcb16b1597aa792c0bdf0f65d0a21386dd1edc846#eventlog). Questa decisione è stata intrapresa dopo aver osservato un problema al flusso di riscatto dei depositi che, in alcune situazioni, avrebbe potuto rappresentare un rischio per gli asset collateralizzati dai gruppi di signers.

Subito dopo è iniziato un processo di riconversione da BTC per TBTC che ha portato ad un recupero del 99,83% del circolante a [questo indirizzo](https://etherscan.io/token/0x1bBE271d15Bb64dF0bc6CD28Df9Ff322F2eBD847?a=0xe52e028f0d8f2e7a9d78e48199234b1231774e6a). La rimozione di ETH residuali inutilizzati nel sistema è ancora sotto lavorazione.

# Descrizione dell'evento

In tBTC, un utente che detiene BTC, chiamato depositante, può aprire un deposito tBTC sulla chain di Ethereum. Il deposito è costituito da uno smart contract che interagisce con un gruppo di 3 signers in un network separato, che controlla e genera un wallet Bitcoin in modo tale che nessuno dei 3 signers, preso singolarmente, può prenderne l'esclusivo controllo. Quando il deposito è aperto, il depositante seleziona la dimensione del lotto in BTC e, una volta ottenuto l'indirizzo BTC (generato ad-hoc dal gruppo di singers), invia l'ammontare desiderato a tale address. Il depositante poi invia la prova di avvenuto deposito (proof) ad un smart contract e ottiene in cambio l'equivalente ammontare di TBTC appena minati. Questo consente agli holder di BTC di entrare nel sistema tBTC e usare i token TBTC per interagire con altri smart contract che lo supportano.

Per dimostrare appropriatamente la transazione da Bitcoin sulla rete di Ethereum, viene stabilito un collegamento che comunica i dati rilevati nei blocchi della rete Bitcoin su uno smart contract Ethereum, allo scopo di calcolare quante conferme ha ricevuto una determinata transazione. Questo meccanismo è escogitato per esser sicuri che una determinata transazione (a) esiste sulla blockchain, e (b), che essa abbia ricevuto sufficienti conferme in modo tale che un eventuale fork non sia in grado di cancellarla o rimuoverla.

Per assicurare che il gruppo di singers non collida e abbia comportamenti malevoli sull'address BTC generato per il depositante, essi sono obbligati a porre a garanzia un collaterale pari al 150% del valore dei BTC in ETH. Questo deposito è bloccato in uno smart contract fin quando il depositante non decide di riscattare i propri BTC.

Il riscatto funziona come segue: quando un TBTC holder su Ethereum è interessato a riprendersi i BTC, può avviare l'operazione inversa. Il riscatto pertanto consente all'utente di Ethereum di restituire il lotto di TBTC più una piccola commissione, indicare un address BTC e autorizzare i signers a produrre una firma che completa il trasferimento dell'equivalente valore in BTC sull'address fornito dall'utente. Questo gli consente quindi di uscire da tBTC e tornare alla chain di Bitcoin. D'altra parte, a operazione conclusa, il collaterale dei signers che erano a protezione dei BTC viene rimesso a disposizione di altri utenti e di altre transazioni. I signers guadagnano la commissione pagata dall'utente che ha riscattato i propri BTC.

## Cronologia dell'evento

L'evento avverso ha inizio alle 15:52 del 15 Marzo 2020 UTC con la creazione del sistema di sorteggio di tBTC. Il sistema di sorteggio non pone alcun rischio sui fondi depositati o in collaterale. Tuttavia, essendo il sistema di sorteggio un meccanismo di selezione casuale dei gruppi di signers, ad essi è richiesto di mantenere degli ETH a collaterale bloccati in uno smart contract. Questo contratto richiede ai signers una ulteriore autorizzazzione all'utilizzo di questi fondi a collaterale, affinchè il meccanismo di sorteggio sia finalizzato. Infine, il signer deve registrarsi all'interno dello stesso strumento di selezione per partecipare alla lista dei potenziali signers pescati allo scopo di consentire il deposito.

Nel giro di 3 giorni, molti signers si sono registrati per partecipare al meccanismo di selezione, ponendo a collaterale diversi fondi, ma solo 3 signers si sono registrati come candidati per una specifica richiesta di deposito. Questi 3 signers erano controllati da un singolo soggetto in contatto col team di sviluppo di tBTC.

I depositi erano disponibili attraverso una versione alpha della dAPP su <https://dapp.tbtc.network/>, limitati ad un lotto di dimensione di 0.001 BTC. Gli ETH a collaterale erano correttamente depositati, per un valore 1,5 volte superiore. La sera del 15 Maggio, la dAPP viene messa temporanemente offline per indagare su un piccolo errore di codice, poi corretto, e rimessa online il 16 Maggio di mattina. Inoltre, nel frattempo molti utenti della community testavano la dAPP aprendo richieste di deposito superiori alla dimensione del lotto di 0.001 BTC.

Alle 2:29 del 18 Maggio UTC, lo smart contract definito "operator" correlato ad un gruppo di 3 signers non era in grado di consentire il riscatto di un deposito. Questo potrebbe esser successo a causa di un elevato gas price sulla rete di Ethereum che ha prodotto un rallentamento della lettura dei blocchi sulla blockchain di BTC. La sincronizzazione era stata ristabilita intorno alle 3:07 UTC.

Alle 3:13 UTC, lo stesso "operator" ha nuovamente difficoltà a riscattare. A questo punto si è osservato un errore anche sullo smart contract del deposito, "Tx sends value to wrong pubkeyhash". Questo errore indica che la "proof" generata dalla dAPP necessaria per consentire il riscatto dei BTC era completamente scorretta; specificatamente, la "proof" non era in grado di certificare che la transazione dall'indirizzo di deposito all'indirizzo del riscatto fosse avvenuta correttamente.

Dopo una approfondita analisi, si è ritenuto che ciò poneva ad elevato rischio i fondi depositati dai signers. Dato che il collaterale dei signers può restare bloccato solo dopo 6 ore in assenza di una "proof" di riscatto, l'indagine è continuata.

Alle 5:02 UTC, le indagini furono supportate da alcuni sostenitori chiave di tBTC, e si decise di reindirizzare l'homepage della dAPP di tBTC in modo da impedire ulteriori richieste di deposito.

Alle 5:18 UTC, è stato attivato il blocco di emergenza dei depositi per 10 giorni. Questa funzione previene l'apertura di nuovi depositi per 10 giorni, ma non influenza il funzionamento di quelli già attivi. Per sicurezza, qualsiasi update al sistema di tBTC deve essere autorizzato da 2 di 3 firme appartenenti al team di sviluppo tecnico. Questo processo si è chiuso alle 5:45 UTC.

## Descrizione tecnica del problema

Il problema è stato circoscritto nel processo che certifica una transazione di riscatto sulla chain di Bitcoin. In normali circostanze, il signer che fornisce una firma valida per una transazione di riscatto Bitcoin, si vedrebbe immediatamente rilasciato il proprio collaterale messo a garanzia. Se il sistema tBTC tuttavia in un momento del genere, lasciasse cadere ogni obbligo in capo al signer, quest'ultimo potrebbe sfruttare l'opportunità di generare una firma differente, inviare i fondi ad un indirizzo diverso specificato dall'utente che sta riscattando i propri BTC e diffonderla prima che l'utente possa trasmettere la sua richiesta di riscatto. In tal senso, tBTC deve mantenere quindi a collaterale i fondi del signer almeno fin quando il signer non abbia prodotto non solo una firma valida ma anche una prova che tale transazione sia stata confermata sulla chain di Bitcoin.

La prova che tale transazione sia confermata sulla rete Bitcoin include anche ulteriori controlli; uno di questi è generato dalla funzione [`redemptionTransactionChecks`](https://github.com/keep-network/tbtc/blob/9651d53a443b3d2470e13ee1db0ecae60be8b246/solidity/contracts/deposit/DepositRedemption.sol#L326-L355):

```sol

```

L'errore viene identificato mediante il messaggio “Tx sends value to wrong pubkeyhash”:

```sol

```

Bitcoin ha diverse tipologie di scripts di output. Gli standard di output sono "pay to pubkeyhash" (p2pkh), "pay to scripthash" (p2sh), "pay to witness pubkeyhash" (p2wpkh) e "pay to witness scripthash" (p2wsh). L'indirizzo è costituito da un hash a 20 o 32 byte, da un checksum e da informazioni relative al tipo di output script. Queste ultime sono usate per inserire l'hash in uno schema standard. Questo crea il corrispondente script di output:

| Standard Type | Prefix | Hash | Postfix | Total length |
| ------------- | ------ | ---- | ------- | ------------ |
| p2pkh         | 3      | 20   | 2       | 25           |
| p2sh          | 2      | 20   | 1       | 23           |
| p2wpkh        | 2      | 20   | \-      | 22           |
| p2wsh         | 2      | 32   | \-      | 34           |

Gli script di aoutput presentanto diverse lunghezze. Output scripts vary in length. As a result all output scripts are prefixed with 1 byte that encodes the length of the script in bytes. The value of an output is represented as an 8-byte little-endian integer, serialized immediately before the output script. A standard output is between (8 + 1 + 22 =) 31 and (8 + 1 + 34 =) 43 bytes long.

`BTCUtils.extractHash()`extracts the hash from a standard output. It does this by inspecting the output script prefix and suffix to determine the location of the hash. If the output script is non-standard, it returns an empty byte array.

Some patterns are identifiable. Output bytes 0-7 are always the value. All legacy types have postfixes, while all witness types do not. All types except p2pkh will begin the hash on the (8 + 1 + 2 =) 10th byte of the output, which is at index

```sol

```

This expression takes bytes 8, 9, and 10, and concatenates the hash. For witness types, byte 8 is the length-prefix, while bytes 9 and 10 are the template prefix, so it is easy to see that concatenating them to the hash will produce the (length-prefixed) output script. However, for p2sh addresses, this expression will not append the template postfix. For p2pkh addresses, it will extract only 2 bytes of the prefix, and will (again) not append the postfix. This means that the expression modifies legacy output scripts, and will never output a valid legacy output script.

```sol

```

This code is equivalent to the deployed code. After accidentally modifying legacy scripts, it then compares them to unmodified legacy output scripts. Whenever`_d.redeemerOutputScript`is a legacy script, this equality will always fail, and the transaction will always be reverted.

Neither the redeemer nor depositor is harmed by this bug—that is, the user’s funds are safe. Because this code validates the redemption proof, it is only run after the redeemer has received BTC.

However, because the system cannot verify that redemption succeeded, the signer bonds become available for seizure as if redemption had failed. In particular, if a redemption transaction has not been proven to have occurred to the deposit contract 6 hours after the signers have provided the signature for that transaction, the redeemer can notify the contract that the redemption proof has timed out.

Notification of redemption proof timeout is treated as a signer abort, in which signers fail to fulfill the system’s requirements, but which the system does not treat as malicious intent. During redemption, this means the system cannot verify that the redeemer received their funds. In this scenario, the system seizes the signer’s bonds, and gives the redeemer the full bond value as compensation. This recourse is taken to prevent a scenario where the signers produce a signature over the requested redemption transaction, but also collude to produce a signature over a different transaction, and then race to confirm their transaction before the correct one is confirmed.

In normal cases, a signer producing a bad transaction can be punished by proving fraud, showing that they signed data that they were not authorized to sign. The effect of this proof is the same, seizing signer bonds and giving the full amount to the redeemer. However, unless the system validates the redemption transaction’s output script, the signers could simply prove their malicious transaction instead. As such, the redemption proof has to check the output script, requiring both validation at redemption request time and an adjustment at redemption proof time.

## How The Code Landed

The initial design of tBTC restricted redemptions to p2wpkh addresses, and enforced this restriction in the redemption process. In early February, a[change](https://github.com/keep-network/tbtc/commit/71361a51c220536d82681f1ab77ed640836329ce)was committed that loosened redemption transactions to allow not only p2wpkh output scripts, but also any other output scripts. During redemption, this was intended to allow deposits to accept an arbitrary Bitcoin output script, to give redeemers the flexibility to accept funds in whatever wallet they prefer to use. The code in question is a remnant of the previous validation system. Other artifacts of the p2wpkh verification code (e.g. the no-longer-necessary`abi.encodePacked`) are present.

The commit message above notes that ‘The result passes all current tests, though there are no tests for non-p2wpkh output scripts in the repo yet.’ This did not change over the ensuing months, though Bitcoin testnet addresses generated by Green wallet, which arepay-to-scripthash(p2sh) addresses, were tested repeatedly on testnets. But due to a bug in the redemption dApp in use at the time, the proof step of the redemption flow never occurred. These p2sh addresses would have failed validation had the proof step occurred, but reliance on the dApp’s display of a completed state meant the redemption was incorrectly assumed to have completed successfully.

## Additional Observations

The issue explained here was not the only problem with the redemption code. Even if the proof code had been correct, a malicious redeemer could potentially still have specified an output script that yielded an invalid Bitcoin transaction due to the consensus rules regarding`OP_VERIF`and`OP_VERNOTIF`. This would force a situation where the transaction would never be included in a Bitcoin block. In such a scenario, being able to confirm the transaction’s output script is irrelevant, and the redeemer would be able to guarantee receipt of the bond (while leaving the BTC with the signers). As such, in addition to the incorrect validation*once redemption had occurred*, there was missing validation*when redemption was requested*. Because of this, future versions will support only standard address types.

If a redeemer specified an output script resulting in an invalid Bitcoin transaction, although they would be able to seize the signer bonds, the signers would remain in control of the BTC deposit. Thus, the overall value loss would be less than a valid transaction that can’t be proven to the deposit contract. The overcollateralization of signer bonds nonetheless means that the malicious redeemer would still experience a net gain from this scenario.

The bugged verification code was also present in a dead codepath that has been removed[in the bugfix PR](https://github.com/keep-network/tbtc/pull/636).

## Analysis

Several factors contributed to the redemption issue:

* Failure to capture an action item to add further test vectors outside of the original commit message, resulting in failure to identify the issue during development.
* Failure to verify that a successful redemption in the UI resulted in a closed deposit on-chain during manual dApp-based QA, which could have identified the issue during manual QA.
* Incomplete consideration of input validation at the entry point to redemption. As one of relatively few completely user-controlled pieces of data in the system, this should be a high priority for input validation.
* Insufficient time spent generating Bitcoin test vectors for unit tests. In particular, fuzz testing of the redemption flow could have caught both the requested output script validation and the transaction proof issues.

# Next Steps

In addition to the existing actions to redeem outstanding deposits in the running system and ensure the operator who was bonding ETH at the time receives their bonds back, a number of additional actions will be undertaken ahead of a relaunch of the tBTC system.

## Steps Taken To Date

* [A PR with a proposed fix has been opened on GitHub](https://github.com/keep-network/tbtc/pull/636). Test vectors will be adjusted before merging over the coming days.
* The scope of the already-planned Trail of Bits audit, which started on Monday the 18th, has been adjusted from an aggressive focus on the Go clients in the network to instead split time between the Go clients and the smart contracts in the system.
* Both the issue and the fix have been communicated to tBTC’s previous auditors, ConsenSys Diligence, as well as its current auditors, Trail of Bits, for confirmation and further feedback.
* [99.83% of the TBTC supply](https://etherscan.io/token/0x1bBE271d15Bb64dF0bc6CD28Df9Ff322F2eBD847#balances)has been retrieved by offering a 1.005 BTC-to-1 TBTC purchase of outstanding TBTC. The secured TBTC will be used to redeem open deposits and free up bonded ETH.

## Further Actions

* Clarification and improvement of processes for capturing follow-up work in cases where pull requests are merged with future changes still pending.
* Improvement of processes for blocking pull request merges for cases where user-controlled input is used in the system and sufficient testing is not present.
* Importation of the failed redemptions as test vectors into tBTC’s test suite.
* Creation of additional test vectors based on various known address types for our test suite.
* Exploration of the feasibility of finding or building a Bitcoin transaction fuzzing tool.
* Collaboration with Trail of Bits to expand and automate more integration and system tests for tBTC, as well as to add fuzz and property testing to various components of the system that currently rely on simpler unit tests.
* Collaboration with Trail of Bits to identify any additional areas of the system that may merit additional scrutiny.
