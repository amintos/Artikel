Moderne Verschl�sselungen
=========================

Die Kunst, eine Botschaft durch gezieltes �Verschleiern� f�r fremde unkenntlich zu machen, ist fast so alt wie Schrift selbst. Schon die alten �gypter setzten vor f�nf Jahrtausenden spezielle Hieroglyphen ein, die nur einem ausgew�hlten Zirkel geistlicher bekannt waren. 


![](http://bits.wikimedia.org/static-1.23wmf2/extensions/wikihiero/img/hiero_M2.png) ![ ](http://bits.wikimedia.org/static-1.23wmf2/extensions/wikihiero/img/hiero_L4.png) ![](https://bits.wikimedia.org/static-1.23wmf2/extensions/wikihiero/img/hiero_S28.png)

*Pflanze, Heuschrecke und Stoff sind Hieroglyphen, die in einigen religi�sen Inschriften auftraten und vereinzelt die Lesung f�r uneingeweihte erschweren sollten.*

Um die Vertraulichkeit milit�rischer Korrespondenz zu wahren, setzte der r�mische Feldherr Gaius Julius Caesar laut �berlieferung die sp�ter nach ihm benannte C�sar-Chiffre ein: Dabei wurden jeder Buchstabe des Alphabets durch einen um drei Positionen weiter hinten liegenden ersetzt. 

![](http://upload.wikimedia.org/wikipedia/commons/thumb/2/2b/Caesar3.svg/320px-Caesar3.svg.png)

Mit der Erfindung der Chiffrierscheibe im 15. Jahrhundert waren auch andere Verschiebungen als jene um drei Positionen trivial zu realisieren. Das Verfahren wurde verallgemeinert, Sender und Empf�nger mussten sich nunmehr nur noch auf die Weite der Verschiebung einigen - einen **Schl�ssel**. Das Prinzip der Trennung von Algorithmus und Schl�ssel wird bis heute praktiziert.

![](https://upload.wikimedia.org/wikipedia/commons/thumb/b/b5/CipherDisk2000.jpg/220px-CipherDisk2000.jpg)

*Chiffrierscheibe f�r beliebige Caesar-Chiffren*

Blockchiffren
-------------

### Caesar als erste Blockchiffre

Die klassische Caesar-Chiffre operiert nur �ber einzelnen Buchstaben als "Eingabe", was sie anf�llig f�r Muster im Text macht - tritt z.B. der Buchstabe E sehr h�ufig auf, wird in einem um drei Verschobenen Alphabet der Buchstabe H geh�uft auftreten. �ber diese Korrelation l�sst sich der Schl�ssel schnell ermitteln. 

Betrachtet man hingegen mehrere Buchstaben gleichzeitig - quasi ein Alphabet aus 26 x 26 Zeichen - und ersetzt sie durch die gleiche Anzahl Geheimbuchstaben, werden Korrelationen undeutlicher. Doch eine solche �bersetzungstabelle h�tte bereits 676 Eintr�ge.

    AA | AB | AC | ... | BA | BB | ... | ZY | ZZ
    RP | RQ | RR | ... | SP | SQ | ... | RN | RO 


F�r gr��ere "Bl�cke" zusammenh�ngender Daten ist also eher eine mathematische Beschreibug n�tzlich als eine Chiffrierscheibe oder Tabelle. Weisen wir also den Buchstaben A - Z die Zahlen 0 bis 25 zu. Die Caesar-Chiffe l�sst sich dann einfach als Addition mit einem Schl�ssel modulo 26 ausdr�cken: ```C = P + K (mod 26)```. C, der verschl�sselte Buchstabe, wird als ***Ciphertext*** bezeichnet, der Buchstabe der urspr�nglichen Nachricht P als ***Plaintext*** und die Verschiebung K als ***Schl�ssel (Key)***.

Was spricht also dagegen, 256 im Speicher liegende Bits auf einmal zu nehmen (das entspricht 32 Buchstaben in ASCII-Codierung) und eine gro�e Addition modulo 2^256 mit einem m�glichst zuf�lligen Schl�ssel auszuf�hren? Zun�chst nichts, alle ```2^256```  Schl�ssel auszuprobieren ist momentan technisch ausgeschlossen, ein Fortschritt gegen�ber den 26 Schl�sseln bei Caesar. 

Das Problem hierbei ist nur, dass bei Kenntniss von Plaintext und Ciphertext der Schl�ssel trivial via ```K = C - P (mod 2^256)``` gewonnen und zum Entschl�sseln anderer, eventuell unbekannter Bl�cke eingesetzt werden kann.

### High-Tech Blockchiffre Schritt f�r Schritt

M�chte man den Fallstricken der einfachen Addition entgehen, muss man sich weiteren Operationen bedienen. H�ufige Operationen sind folgende:

    Addition:        1100   +   0101  =  0001   (modulo 2^4)
    Subtraktion:     1100   -   0101  =  0111   (modulo 2^4)
    Exklusiv-Oder:   1100  XOR  0101  =  1001
    Linksrotation:   00001100  <<< 3  =  01100000
    Rechtsrotation:  00001100  >>> 3  =  10000001

Diese werden nicht nur zwischen Schl�ssel und Plaintext angewendet, sondern auch zwischen verschiedenen Teilen des Plaintexts selbst. Beispielsweise kann man einen 256-Bit-Block auch als vier einzelne 64-Bit-Bl�cke A, B, C und D betrachten und diese untereinander verkn�pfen, um die Ausgabebl�cke A', B', C' und D' zu erhalten:

    1.)  A' = A + B  (modulo 2^64)
    2.)  C' = C + D  (modulo 2^64)
    3.)  B' = (D <<< 37) XOR C'
    4.)  D' = (B <<<  5) XOR A'
    
Im ***Threefish-Algorithmus*** wird diese mathematische Transformation 72-mal hintereinander ausgef�hrt. Dabei werden stets andere Rotationsweiten als nur 37 und 5 verwendet. Alle vier Durchl�ufe wird ein sog. ***Rundenschl�ssel*** auf den gesamten 256-Bit-Block addiert. Man erkennt, dass man 72 / 4 = 18 Rundenschl�ssel ben�tigt. Diese m�ssen daher durch mathematische Tricks aus einem einzigen 256-Bit breiten Schl�sselblock gewonnen werden - dieses Verfahren wird als ***Schl�sselexpansion*** bezeichnet und kann bisweilen komplizierter sein als die eigentliche Verschl�sselung.

Ziel dieses aufw�ndigen Durchw�rfeln von Bits ist es, jeglichen Zusammenhang zwischen Plaintext, Schl�ssel und Ciphertext zu verschleiern. W�rde man auch nur ein einziges Bit im Plaintext oder im Schl�ssel ver�ndern, w�rde ein v�llig anderer, unvorhersagbarer Ciphertext entstehen. Dies nennt man ***Diffusion***. Und doch ist all dies umkehrbar, wenn man die Rundenschl�ssel kennt: Aus Addition wird Subtraktion, Linksrotation wird zu Rechtsrotation, XOR bleibt bestehen und alle Operationen werden in umgekehrter Reihenfolge angewendet, die Rundenschl�ssel in umgekehrter Reihenfolge alle vier Runden subtrahiert - die Umkehroperation der oben dargestellten Transformation sei dem Leser �berlassen.



### Die R�cktransformation in Threefish:
    1.)  B = (D' XOR A') >>> 5
    2.)  D = (B' XOR C') >>> 37
    3.)  C = C' - D  (modulo 2^64)
    4.)  A = A' - B  (modulo 2^64)





![](https://upload.wikimedia.org/wikipedia/commons/thumb/c/c1/ECClines.svg/680px-ECClines.svg.png)