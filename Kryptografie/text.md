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

Moderne Blockchiffren
---------------------

Die klassische Caesar-Chiffre operiert nur über einzelnen Buchstaben als "Eingabe", was sie anfällig für Muster im Text macht - tritt z.B. der Buchstabe E sehr häufig auf, wird in einem um drei Verschobenen Alphabet der Buchstabe H gehäuft auftreten. Über diese Korrelation lässt sich der Schlüssel schnell ermitteln. 

Betrachtet man hingegen mehrere Buchstaben gleichzeitig - quasi ein Alphabet aus 26 x 26 Zeichen - und ersetzt sie durch die gleiche Anzahl Geheimbuchstaben, werden Korrelationen undeutlicher. Doch eine solche Übersetzungstabelle hätte bereits 676 Einträge.

    AA | AB | AC | ... | BA | BB | ... | ZY | ZZ
    RP | RQ | RR | ... | SP | SQ | ... | RN | RO 


Für größere "Blöcke" zusammenhängender Daten ist also eher eine mathematische Beschreibug nützlich als eine Chiffrierscheibe oder Tabelle. Daten werden fortan nur noch als Zahlen betrachtet, sei es der ASCII-Code eines Buchstaben oder eine andere Codierung.

    X' := X + k

Hier sei X' unser verschlüsselter Block (***Ciphertext***), X unser ***Klartext*** und k der Schlüssel. Die Addition wird in der Kryptografie meist ohne Berücksichtigung des Überlaufs betrachtet - gibt es 256 Zeichen, dann ist z.B. 255 + 1 = 0, man rechnet *modulo 256*.




