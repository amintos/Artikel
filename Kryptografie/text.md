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

Moderne Blockchiffren
---------------------

Die klassische Caesar-Chiffre operiert nur �ber einzelnen Buchstaben als "Eingabe", was sie anf�llig f�r Muster im Text macht - tritt z.B. der Buchstabe E sehr h�ufig auf, wird in einem um drei Verschobenen Alphabet der Buchstabe H geh�uft auftreten. �ber diese Korrelation l�sst sich der Schl�ssel schnell ermitteln. 

Betrachtet man hingegen mehrere Buchstaben gleichzeitig - quasi ein Alphabet aus 26 x 26 Zeichen - und ersetzt sie durch die gleiche Anzahl Geheimbuchstaben, werden Korrelationen undeutlicher. Doch eine solche �bersetzungstabelle h�tte bereits 676 Eintr�ge.

    AA | AB | AC | ... | BA | BB | ... | ZY | ZZ
    RP | RQ | RR | ... | SP | SQ | ... | RN | RO 


F�r gr��ere "Bl�cke" zusammenh�ngender Daten ist also eher eine mathematische Beschreibug n�tzlich als eine Chiffrierscheibe oder Tabelle. Daten werden fortan nur noch als Zahlen betrachtet, sei es der ASCII-Code eines Buchstaben oder eine andere Codierung.

    X' := X + k

Hier sei X' unser verschl�sselter Block (***Ciphertext***), X unser ***Klartext*** und k der Schl�ssel. Die Addition wird in der Kryptografie meist ohne Ber�cksichtigung des �berlaufs betrachtet - gibt es 256 Zeichen, dann ist z.B. 255 + 1 = 0, man rechnet *modulo 256*.




