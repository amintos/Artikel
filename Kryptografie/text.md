Moderne Verschl�sselungen
=========================

Die Kunst, eine Botschaft durch gezieltes �Verschleiern� f�r Fremde unkenntlich zu machen,
ist fast so alt wie Schrift selbst. 
Schon die alten �gypter setzten vor f�nf Jahrtausenden spezielle Hieroglyphen ein, 
die nur einem ausgew�hlten Zirkel Geistlicher bekannt waren. 


![](http://bits.wikimedia.org/static-1.23wmf2/extensions/wikihiero/img/hiero_M2.png) ![ ](http://bits.wikimedia.org/static-1.23wmf2/extensions/wikihiero/img/hiero_L4.png) ![](https://bits.wikimedia.org/static-1.23wmf2/extensions/wikihiero/img/hiero_S28.png)

*Pflanze, Heuschrecke und Stoff sind Hieroglyphen, 
die in einigen religi�sen Inschriften auftraten und vereinzelt die Lesung f�r uneingeweihte erschweren sollten.*

Um die Vertraulichkeit milit�rischer Korrespondenz zu wahren, 
setzte der r�mische Feldherr Gaius Julius Caesar laut �berlieferung die sp�ter nach ihm benannte C�sar-Chiffre ein: 
Dabei wurde jeder Buchstabe des Alphabets durch einen um drei Positionen weiter hinten liegenden ersetzt. 

![](http://upload.wikimedia.org/wikipedia/commons/thumb/2/2b/Caesar3.svg/320px-Caesar3.svg.png)

Mit der Erfindung der Chiffrierscheibe im 15. Jahrhundert waren auch andere Verschiebungen als jene um drei Positionen trivial zu realisieren. 
Das Verfahren wurde verallgemeinert, 
Sender und Empf�nger mussten sich nunmehr nur noch auf die Weite der Verschiebung einigen - einen **Schl�ssel**. 
Das Prinzip der Trennung von Algorithmus und Schl�ssel wird bis heute praktiziert.

![](https://upload.wikimedia.org/wikipedia/commons/thumb/b/b5/CipherDisk2000.jpg/220px-CipherDisk2000.jpg)

*Chiffrierscheibe f�r beliebige Caesar-Chiffren*

Caesars Blockchiffre
--------------------

Die klassische Caesar-Chiffre operiert nur �ber einzelnen Buchstaben als "Eingabe", 
was sie anf�llig f�r Muster im Text macht - tritt z.B. der Buchstabe E sehr h�ufig auf,
wird in einem um drei verschobenen Alphabet der Buchstabe H geh�uft auftreten. 
�ber diese Korrelation l�sst sich der Schl�ssel schnell ermitteln. 

Betrachtet man hingegen mehrere Buchstaben gleichzeitig - 
quasi ein Alphabet aus 26 x 26 Zeichen - 
und ersetzt sie durch die gleiche Anzahl Geheimbuchstaben, 
werden Korrelationen undeutlicher. 
Doch eine solche �bersetzungstabelle h�tte bereits 676 Eintr�ge.


    AA | AB | AC | ... | BA | BB | ... | ZY | ZZ
    RP | RQ | RR | ... | SP | SQ | ... | RN | RO 


F�r gr��ere "Bl�cke" zusammenh�ngender Daten ist also eher eine mathematische Beschreibung n�tzlich als eine Chiffrierscheibe oder Tabelle. 
Weisen wir also den Buchstaben A - Z die Zahlen 0 bis 25 zu. 
Die Caesar-Chiffe l�sst sich dann einfach als Addition mit einem Schl�ssel modulo 26 ausdr�cken: 
```C = P + K (mod 26)```. 
C, der verschl�sselte Buchstabe, wird als ***Ciphertext*** bezeichnet, 
der Buchstabe der urspr�nglichen Nachricht P als ***Plaintext*** 
und die Verschiebung K als ***Schl�ssel (Key)***.

Was spricht also dagegen, 
256 im Speicher liegende Bits auf einmal zu nehmen 
(das entspricht 32 Zeichen in ASCII-Codierung) 
und eine gro�e Addition modulo 2^256 mit einem m�glichst zuf�lligen Schl�ssel auszuf�hren? Zun�chst nichts, alle ```2^256```  Schl�ssel auszuprobieren ist momentan technisch ausgeschlossen, 
das ist ein Fortschritt gegen�ber den 26 Schl�sseln bei Caesar. 

Das Problem hierbei ist nur, 
dass bei Kenntnis von Plaintext und Ciphertext 
der Schl�ssel trivial via ```K = C - P (mod 2^256)``` gewonnen und zum Entschl�sseln anderer, eventuell unbekannter Bl�cke eingesetzt werden kann.

Blockchiffren im 21. Jahrhundert
--------------------------------

M�chte man den Fallstricken der einfachen Addition entgehen, 
muss man sich weiteren Operationen bedienen. H�ufige Operationen sind folgende:


    Addition:        1100   +   0101  =  0001   (modulo 2^4)
    Subtraktion:     1100   -   0101  =  0111   (modulo 2^4)
    Exklusiv-Oder:   1100  XOR  0101  =  1001
    Linksrotation:   00001100  <<< 3  =  01100000
    Rechtsrotation:  00001100  >>> 3  =  10000001

Diese werden nicht nur zwischen Schl�ssel und Plaintext angewendet, 
sondern auch zwischen verschiedenen Teilen des Plaintexts selbst. 
Beispielsweise kann man einen 256-Bit-Block auch als vier einzelne 64-Bit-Bl�cke 
A, B, C und D betrachten und diese untereinander verkn�pfen, 
um die Ausgabebl�cke A', B', C' und D' zu erhalten:

    1.)  A' = A + B  (modulo 2^64)
    2.)  C' = C + D  (modulo 2^64)
    3.)  B' = (D <<< 37) XOR C'
    4.)  D' = (B <<<  5) XOR A'
    
Im ***Threefish-Algorithmus*** wird diese mathematische Transformation 72-mal hintereinander ausgef�hrt. 
Dabei werden stets andere Rotationsweiten als nur 37 und 5 verwendet. 
Alle vier Durchl�ufe wird ein sog. ***Rundenschl�ssel*** auf den gesamten 256-Bit-Block addiert. 
Man erkennt, dass man 72 / 4 = 18 Rundenschl�ssel ben�tigt. Diese m�ssen daher durch mathematische Tricks aus einem einzigen 256-Bit breiten Schl�sselblock gewonnen werden - 
dieses Verfahren wird als ***Schl�sselexpansion*** bezeichnet 
und kann bisweilen komplizierter sein als die eigentliche Verschl�sselung.

Ziel dieses aufw�ndigen Durchw�rfeln von Bits ist es, 
jeglichen Zusammenhang zwischen Plaintext, Schl�ssel und Ciphertext zu verschleiern. 
W�rde man auch nur ein einziges Bit im Plaintext oder im Schl�ssel ver�ndern, 
w�rde ein v�llig anderer, unvorhersagbarer Ciphertext entstehen. 
Dies nennt man ***Diffusion***. 
Und doch ist all dies umkehrbar, wenn man die Rundenschl�ssel kennt: 
Aus Addition wird Subtraktion, Linksrotation wird zu Rechtsrotation, 
XOR bleibt bestehen und alle Operationen werden in umgekehrter Reihenfolge angewendet, 
die Rundenschl�ssel in umgekehrter Reihenfolge alle vier Runden subtrahiert � 
die Umkehroperation der oben dargestellten Transformation sei dem Leser �berlassen.

### Sicherheitsaspekte bei der Implementierung

Es gibt neben dieser Variante auch Verschl�sselungen, 
die mathematische Operationen und das Ersetzen mittels einer gro�en, 
relativ zuf�llig aussehenden Ersetzungstabelle (*Substitutions- oder S-Box*) abwechselnd ausf�hren (z.B. RC4). 
Tut man dies in einem modernen Rechner, 
kann es sein, dass Tabelleneintr�ge im Cache der CPU abgelegt werden 
und pl�tzlich schneller gelesen werden, wenn sie nochmal verwendet werden. 
Beobachtet nun ein anderes Programm diese *Cache-Timings*, 
kann es R�ckschl�sse auf die gerade verschl�sselten Daten ziehen. 
Die M�glichkeit dieser ***Seitenkanal-Angriffe*** sollte Verschl�sselungssoftware vermeiden, was sich technisch meist als �u�erst schwer herausstellt. 
Threefish schlie�t Angriffe mittels Cache-Timings aus, da sie keine S-Boxen verwendet. Dies macht eine sichere Implementierung einfacher.

Ein weiteres wichtiges Merkmal dieser Blockchiffre ist die Verwendung von **konstanten** Rotationen. 
Viele CPUs k�nnen Rotation und *Bit-Shift* nicht f�r alle Rotationsweiten gleichschnell ausf�hren. 
W�rde man die Rotationsweite abh�ngig von Schl�ssel oder Plaintext machen, 
kann eine Beobachtung der Ausf�hrungszeiten ebenfalls R�ckschl�sse auf die Daten erlauben. 

### Mehr verschl�sseln

16 oder 32 Bytes an gleichzeitig verschl�sselbaren Daten sind nicht allzuviel, 
wenn man gro�e Datenmengen betrachtet. 
Auch hier k�nnen durch Wiederholung von ganzen Bl�cken 
(z.B. gro�en schwarzen und wei�en Bereichen in Bildern) Muster entstehen:

![](https://upload.wikimedia.org/wikipedia/commons/5/56/Tux.jpg)
![](https://upload.wikimedia.org/wikipedia/commons/f/f0/Tux_ecb.jpg)
![](https://upload.wikimedia.org/wikipedia/commons/a/a0/Tux_secure.jpg)

Eine Technik, dies zu vermeiden, 
ist das Addieren des verschl�sselten vorherigen Blocks in den Plaintext des n�chsten Blocks, 
das sog. Cipher-Block Chaining (CBC). 
Der erste Block wird mit einem zuf�lligen Wert (*Initialisierungsvektor*) versehen, welcher unverschl�sselt mit den Daten mitgeschickt wird. 
Dadurch ist der Empf�nger zwar gezwungen, die Bl�cke in der gleichen Reihenfolge zu entschl�sseln, 
in der sie verschl�sselt wurden, verr�terische Muster werden allerdings versteckt.

![](https://upload.wikimedia.org/wikipedia/commons/thumb/8/80/CBC_encryption.svg/601px-CBC_encryption.svg.png)

Wenn das komplette Entschl�sseln von vorne keine Option ist, 
z.B. in Festplattenverschl�sselungen � la TrueCrypt, 
kombiniert man stattdessen den Originalschl�ssel mit der Position des Blocks zu einem einzigartigen Block-Schl�ssel, 
indem man die Block-Position zun�chst mit dem Originalschl�ssel verschl�sselt. 
Dies erlaubt es, jeden Block f�r sich genommen zu entschl�sseln.

Elliptische Kurven zum Schl�sselaustausch
-----------------------------------------

Moderne Blockchiffren l�sen zwar das Problem der sicheren Aufbewahrung und �bermittlung der Daten, sie sind jedoch *symmetrisch*: Der Schl�ssel zum Entschl�sseln ist der gleiche wie zum Verschl�sseln und muss daher selbst sicher transportiert werden.

Eine erste M�glichkeit, wie zwei Parteien sich ohne Kenntniserlangung dritter *asymmetrisch* �ber einen Schl�ssel verst�ndigen k�nnen,
 wurde 1976 von Whitfield Diffie und Martin Hellman eingef�hrt. 
Eine moderne Weiterentwicklung ist heute als Elliptic Curve Diffie-Hellman (ECDH) Verfahren bekannt. Um diese zu verstehen, 
betrachten wir zun�chst elliptische Kurven an sich.

### Elliptische Kurven

Eine Gleichung der Form ```y^2 = x^3 + px + q``` 
wird als elliptische Kurve bezeichnet. 
F�r ```p = -3``` und ```q = 3``` ergibt sich die hier dargestellte L�sungsmenge:

![](https://raw.github.com/amintos/Artikel/master/Kryptografie/curve.png)

Die bemerkenswerte Eigenschaft dieser Kurven ist, 
dass man auf ihnen eine ***Addition auf Punkten*** definieren kann: 
Zieht man eine Gerade durch zwei Punkte auf der Kurve, 
schneidet sie diese stets in einem dritten Punkt 
(oder im Unendlichen, was wir auch als g�ltigen Punkt auffassen). 
Spiegelt man diesen Schnittpunkt an der x-Achse, 
erh�lt man die Summe der beiden Punkte. 
Weiterhin sei die Punktnegation ```-P``` definiert 
als die Spiegelung von ```P``` an der x-Achse 
(Hier wird sichtbar, dass der "Nullpunkt" ```0 = P + (-P)``` jener Punkt im Unendlichen ist). 
Bei der Verdoppelung ```P + P = 2P``` nimmt man als Gerade einfach die Tangente durch ```P```.

Mit diesen Werkzeugen k�nnen wir uns eine Multiplikation  ```n*P``` definieren als ```n```-maliges addieren. 
Das l�sst sich durch mathematische Abk�rzungen allerdings beschleunigen, 
z.B. ist ```15*P = 16*P - P = 2*2*2*2*P + (-P)``` und ben�tigt nur eine Negation, eine Addition und vier Verdoppelungen statt 15 Additionen. Diese Darstellung des Koeffizienten ```n``` hei�t *Non-adjacent form*.

In der Praxis kommen allerdings keine kontinuierlichen elliptischen Kurven zum Einsatz,
 sondern nur deren **ganzzahlige L�sungen**. 
Weiterhin werden alle mathematischen Operationen modulo einer Primzahl ```N``` 
ausgef�hrt - das bedeutet, dass jede X- oder Y-Koordinate "�berl�uft", 
sobald sie gr��er als ```N - 1``` wird und es eine endliche Anzahl an Punkten gibt. 
Gute ```N``` erlauben eine gro�e Anzahl an ganzzahligen L�sungen, 
z.B. bietet sich die Mersenne-Primzahl ```2^521 - 1``` an. 
Die Punktmultiplikation funktioniert hier noch immer.

### Schl�sselaustausch mit Punkten

Punktmultiplikation ist assoziativ, d.h. ```a*(b*P) = b*(a*P)```. 
Beim ECDH-Schl�sselaustausch wird diese Eigenschaft genutzt. 
Angenommen, Alice und Bob m�chten sich auf einen Schl�ssel einigen, 
dann verfahren sie wie folgt:

1. Alice und Bob einigen sich auf eine elliptische Kurve (inklusive der Primzahl ```N```) und einen ganzzahligen Punkt ```P```
2. Alice erzeugt eine Zufallszahl ```a``` zwischen 1 und ```N - 1```. Dies ist ihr privater Schl�ssel. 
Sie ver�ffentlicht ```a*P``` als �ffentlichen Schl�ssel. 
Es ist technisch nicht m�glich, aus zwei Punkten ```a*P``` und ```P``` den Koeffizienten ```a``` direkt zu berechnen, 
wenn ```N``` und ```a``` sehr gro� sind.
3. Bob erzeugt seine private Zufallszahl ```b``` und ver�ffentlicht seinen �ffentlichen Schl�ssel ```b*P```
4. Alice kann nun Bobs �ffentlichen Schl�ssel ```b*P``` beziehen und ihn mit ihrem privaten Schl�ssel multiplizieren. 
Sie erh�lt ```a*(b*P)```. 
Bob kann seinerseits Alices �ffentlichen Schl�ssel mit seinem privaten multiplizieren 
und erh�lt ```b*(a*P)``` und somit den gleichen Punkt. 

Beide k�nnten nun beispielsweise die X-Koordinate des gemeinsamen Punktes 
als Schl�ssel f�r ihre symmetrische Blockchiffre verwenden und so sicher kommunizieren. 
Sie sollten sich allerdings sicher sein, 
dass sie jeweils den �ffentlichen Schl�ssel des anderen und nicht den eines Angreifers erhalten haben, 
beispielsweise mittels einer digitalen Signatur.


Zusammenfassung
---------------

Moderne Kryptografie ist also keine schwarze Magie, 
sie bedient sich einfachen, pragmatischen Konzepten, 
die sich leicht auf modernen CPUs implementieren lassen (z.B. Addition modulo ````2^32``` oder ```2^64```, Rotation und XOR) oder geometrischen Konzepten, 
wie der Punktaddition auf elliptischen Kurven. 
Die eigentliche Schwierigkeit liegt einerseits darin, 
die Sicherheit mathematisch zu beweisen, 
und andererseits in den bereits angedeuteten Fallstricken beim Absichern der Implementierung. 
Bereits ein "zu wenig zuf�lliger" Zufallszahlengenerator kann jedes Verfahren zu Fall bringen.
Dies ist auch ein Grund, weshalb es sehr wenige, 
wirklich sichere Implementierungen der jeweiligen Verfahren gibt. 
Der bedeutende Kryptologe Bruce Schneier schrieb: �Vertraue der Mathematik, Verschl�sselung ist dein Freund�, 
doch das wahre Sicherheitsrisiko steckt im Detail der Implementierung, 
ob gewollt beeinflusst durch Firmen, Regierungen oder Geheimdienste oder unabsichtlich durch Programmierer.
