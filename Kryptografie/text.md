Moderne Verschlüsselungen
=========================

Die Kunst, eine Botschaft durch gezieltes „Verschleiern“ für fremde unkenntlich zu machen, ist fast so alt wie Schrift selbst. Schon die alten Ägypter setzten vor fünf Jahrtausenden spezielle Hieroglyphen ein, die nur einem ausgewählten Zirkel geistlicher bekannt waren. 


![](http://bits.wikimedia.org/static-1.23wmf2/extensions/wikihiero/img/hiero_M2.png) ![ ](http://bits.wikimedia.org/static-1.23wmf2/extensions/wikihiero/img/hiero_L4.png) ![](https://bits.wikimedia.org/static-1.23wmf2/extensions/wikihiero/img/hiero_S28.png)

*Pflanze, Heuschrecke und Stoff sind Hieroglyphen, die in einigen religiösen Inschriften auftraten und vereinzelt die Lesung für uneingeweihte erschweren sollten.*

Um die Vertraulichkeit militärischer Korrespondenz zu wahren, setzte der römische Feldherr Gaius Julius Caesar laut Überlieferung die später nach ihm benannte Cäsar-Chiffre ein: Dabei wurden jeder Buchstabe des Alphabets durch einen um drei Positionen weiter hinten liegenden ersetzt. 

![](http://upload.wikimedia.org/wikipedia/commons/thumb/2/2b/Caesar3.svg/320px-Caesar3.svg.png)

Mit der Erfindung der Chiffrierscheibe im 15. Jahrhundert waren auch andere Verschiebungen als jene um drei Positionen trivial zu realisieren. Das Verfahren wurde verallgemeinert, Sender und Empfänger mussten sich nunmehr nur noch auf die Weite der Verschiebung einigen - einen **Schlüssel**. Das Prinzip der Trennung von Algorithmus und Schlüssel wird bis heute praktiziert.

![](https://upload.wikimedia.org/wikipedia/commons/thumb/b/b5/CipherDisk2000.jpg/220px-CipherDisk2000.jpg)

*Chiffrierscheibe für beliebige Caesar-Chiffren*

Blockchiffren
-------------

### Caesar als erste Blockchiffre

Die klassische Caesar-Chiffre operiert nur über einzelnen Buchstaben als "Eingabe", was sie anfällig für Muster im Text macht - tritt z.B. der Buchstabe E sehr häufig auf, wird in einem um drei Verschobenen Alphabet der Buchstabe H gehäuft auftreten. Über diese Korrelation lässt sich der Schlüssel schnell ermitteln. 

Betrachtet man hingegen mehrere Buchstaben gleichzeitig - quasi ein Alphabet aus 26 x 26 Zeichen - und ersetzt sie durch die gleiche Anzahl Geheimbuchstaben, werden Korrelationen undeutlicher. Doch eine solche Übersetzungstabelle hätte bereits 676 Einträge.

    AA | AB | AC | ... | BA | BB | ... | ZY | ZZ
    RP | RQ | RR | ... | SP | SQ | ... | RN | RO 


Für größere "Blöcke" zusammenhängender Daten ist also eher eine mathematische Beschreibug nützlich als eine Chiffrierscheibe oder Tabelle. Weisen wir also den Buchstaben A - Z die Zahlen 0 bis 25 zu. Die Caesar-Chiffe lässt sich dann einfach als Addition mit einem Schlüssel modulo 26 ausdrücken: ```C = P + K (mod 26)```. C, der verschlüsselte Buchstabe, wird als ***Ciphertext*** bezeichnet, der Buchstabe der ursprünglichen Nachricht P als ***Plaintext*** und die Verschiebung K als ***Schlüssel (Key)***.

Was spricht also dagegen, 256 im Speicher liegende Bits auf einmal zu nehmen (das entspricht 32 Buchstaben in ASCII-Codierung) und eine große Addition modulo 2^256 mit einem möglichst zufälligen Schlüssel auszuführen? Zunächst nichts, alle ```2^256```  Schlüssel auszuprobieren ist momentan technisch ausgeschlossen, ein Fortschritt gegenüber den 26 Schlüsseln bei Caesar. 

Das Problem hierbei ist nur, dass bei Kenntniss von Plaintext und Ciphertext der Schlüssel trivial via ```K = C - P (mod 2^256)``` gewonnen und zum Entschlüsseln anderer, eventuell unbekannter Blöcke eingesetzt werden kann.

### High-Tech Blockchiffre Schritt für Schritt

Möchte man den Fallstricken der einfachen Addition entgehen, muss man sich weiteren Operationen bedienen. Häufige Operationen sind folgende:

    Addition:        1100   +   0101  =  0001   (modulo 2^4)
    Subtraktion:     1100   -   0101  =  0111   (modulo 2^4)
    Exklusiv-Oder:   1100  XOR  0101  =  1001
    Linksrotation:   00001100  <<< 3  =  01100000
    Rechtsrotation:  00001100  >>> 3  =  10000001

Diese werden nicht nur zwischen Schlüssel und Plaintext angewendet, sondern auch zwischen verschiedenen Teilen des Plaintexts selbst. Beispielsweise kann man einen 256-Bit-Block auch als vier einzelne 64-Bit-Blöcke A, B, C und D betrachten und diese untereinander verknüpfen, um die Ausgabeblöcke A', B', C' und D' zu erhalten:

    1.)  A' = A + B  (modulo 2^64)
    2.)  C' = C + D  (modulo 2^64)
    3.)  B' = (D <<< 37) XOR C'
    4.)  D' = (B <<<  5) XOR A'
    
Im ***Threefish-Algorithmus*** wird diese mathematische Transformation 72-mal hintereinander ausgeführt. Dabei werden stets andere Rotationsweiten als nur 37 und 5 verwendet. Alle vier Durchläufe wird ein sog. ***Rundenschlüssel*** auf den gesamten 256-Bit-Block addiert. Man erkennt, dass man 72 / 4 = 18 Rundenschlüssel benötigt. Diese müssen daher durch mathematische Tricks aus einem einzigen 256-Bit breiten Schlüsselblock gewonnen werden - dieses Verfahren wird als ***Schlüsselexpansion*** bezeichnet und kann bisweilen komplizierter sein als die eigentliche Verschlüsselung.

Ziel dieses aufwändigen Durchwürfeln von Bits ist es, jeglichen Zusammenhang zwischen Plaintext, Schlüssel und Ciphertext zu verschleiern. Würde man auch nur ein einziges Bit im Plaintext oder im Schlüssel verändern, würde ein völlig anderer, unvorhersagbarer Ciphertext entstehen. Dies nennt man ***Diffusion***. Und doch ist all dies umkehrbar, wenn man die Rundenschlüssel kennt: Aus Addition wird Subtraktion, Linksrotation wird zu Rechtsrotation, XOR bleibt bestehen und alle Operationen werden in umgekehrter Reihenfolge angewendet, die Rundenschlüssel in umgekehrter Reihenfolge alle vier Runden subtrahiert - die Umkehroperation der oben dargestellten Transformation sei dem Leser überlassen.



### Die Rücktransformation in Threefish:
    1.)  B = (D' XOR A') >>> 5
    2.)  D = (B' XOR C') >>> 37
    3.)  C = C' - D  (modulo 2^64)
    4.)  A = A' - B  (modulo 2^64)





![](https://upload.wikimedia.org/wikipedia/commons/thumb/c/c1/ECClines.svg/680px-ECClines.svg.png)