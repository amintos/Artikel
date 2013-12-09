Moderne Verschlüsselungen
=========================

Die Kunst, eine Botschaft durch gezieltes „Verschleiern“ für fremde unkenntlich zu machen, ist fast so alt wie Schrift selbst. Schon die alten Ägypter setzten vor fünf Jahrtausenden spezielle Hieroglyphen ein, die nur einem ausgewählten Zirkel geistlicher bekannt waren. 


![](http://bits.wikimedia.org/static-1.23wmf2/extensions/wikihiero/img/hiero_M2.png) ![ ](http://bits.wikimedia.org/static-1.23wmf2/extensions/wikihiero/img/hiero_L4.png) ![](https://bits.wikimedia.org/static-1.23wmf2/extensions/wikihiero/img/hiero_S28.png)

*Pflanze, Heuschrecke und Stoff sind Hieroglyphen, die in einigen religiösen Inschriften auftraten und vereinzelt die Lesung für uneingeweihte erschweren sollten.*

Um die Vertraulichkeit militärischer Korrespondenz zu wahren, setzte der römische Feldherr Gaius Julius Caesar laut Überlieferung die später nach ihm benannte Cäsar-Chiffre ein: Dabei wurde jeder Buchstabe des Alphabets durch einen um drei Positionen weiter hinten liegenden ersetzt. 

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

Was spricht also dagegen, 256 im Speicher liegende Bits auf einmal zu nehmen (das entspricht 32 Zeichen in ASCII-Codierung) und eine große Addition modulo 2^256 mit einem möglichst zufälligen Schlüssel auszuführen? Zunächst nichts, alle ```2^256```  Schlüssel auszuprobieren ist momentan technisch ausgeschlossen, das ist ein Fortschritt gegenüber den 26 Schlüsseln bei Caesar. 

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

### Sicherheitsaspekte bei der Implementierung

Es gibt neben dieser Variante auch Verschlüsselungen, die mathematische Operationen und das Ersetzen mittels einer großen, relativ zufällig aussehenden Ersetzungstabelle (*Substitutions- oder S-Box*) abwechselnd ausführen (z.B. RC4). Tut man dies in einem modernen Rechner, kann es sein, dass Tabelleneinträge im Cache der CPU abgelegt werden und plötzlich schneller gelesen werden, wenn sie nochmal verwendet werden. Beobachtet nun ein anderes Programm diese *Cache-Timings*, kann es Rückschlüsse auf die gerade verschlüsselten Daten ziehen. Die Möglichkeit dieser ***Seitenkanal-Angriffe*** sollte Verschlüsselungssoftware vermeiden, was sich technisch meist als äußerst schwer herausstellt. Threefish schließt Angriffe mittels Cache-Timings aus, da sie keine S-Boxen verwendet. Dies macht eine sichere Implementierung einfacher.

Ein weiteres wichtiges Merkmal dieser Blockchiffre ist die Verwendung von **konstanten** Rotationen. Viele CPUs können Rotation und *Bit-Shift* nicht für alle Rotationsweiten gleichschnell ausführen. Würde man die Rotationsweite abhängig von Schlüssel oder Plaintext machen, kann eine Beobachtung der Ausführungszeiten ebenfalls Rückschlüsse auf die Daten erlauben. 

### Mehr als einen Block verschlüsseln

16 oder 32 Bytes an gleichzeitig verschlüsselbaren Daten sind nicht allzuviel, wenn man große Datenmengen betrachtet. Auch hier können durch Wiederholung von ganzen Blöcken (z.B. großen schwarzen und weißen Bereichen in Bildern) Muster entstehen:

![](https://upload.wikimedia.org/wikipedia/commons/5/56/Tux.jpg)
![](https://upload.wikimedia.org/wikipedia/commons/f/f0/Tux_ecb.jpg)
![](https://upload.wikimedia.org/wikipedia/commons/a/a0/Tux_secure.jpg)

Eine Technik, dies zu vermeiden, ist das Addieren des verschlüsselten vorherigen Blocks in den Plaintext des nächsten Blocks, das sog. Cipher-Block Chaining (CBC). Der erste Block wird mit einem zufälligen Wert (*Initialisierungsvektor*) versehen, welcher unverchlüsselt mit den Daten mitgeschickt wird. Dadurch ist der Empfänger zwar gezwungen, die Blöcke in der gleichen Reihenfolge zu entschlüsseln, in der sie verschlüsselt wurden, verräterische Muster werden allerdings versteckt.

![](https://upload.wikimedia.org/wikipedia/commons/thumb/8/80/CBC_encryption.svg/601px-CBC_encryption.svg.png)

Wenn das komplette Entschlüsseln von vorne keine Option ist, z.B. in Festplattenverschlüsselungen á la TrueCrypt, kombiniert man stattdessen den Originalschlüssel mit der Position des Blocks zu einem einzigartigen Block-Schlüssel, indem man die Block-Position z.B. mit dem Originalschlüssel verschlüsselt. Dies erlaubt es, jeden Block für sich genommen zu entschlüsseln.

Elliptische Kurven zum Schlüsselaustausch
-----------------------------------------

Moderne Blockchiffren lösen zwar das Problem der sicheren Aufbewahrung der Daten entlang ihrer Transportwege, sie sind jedoch ***symmetrisch***: Der Schlüssel zum Entschlüsseln ist der gleiche wie zum Verschlüsseln und muss daher selbst sicher transportiert werden.

Eine erste Möglichkeit, wie zwei Parteien sich ohne Kenntniserlangung dritter über einen Schlüssel verständigen können, wurde 1976 von Whitfield Diffie und Martin Hellman eingeführt. Eine elegante Weiterentwicklung ist heute als Elliptic Curve Diffie-Hellman (ECDH) Verfahren bekannt. Um diese zu verstehen, betrachten wir zunächst elliptische Kurven an sich.

### Elliptische Kurven

Eine Gleichung der Form ```y^2 = x^3 + px + q``` wird als elliptische Kurve bezeichnet. Für ```p = -3``` und ```q = 3``` ergibt sich die hier dargestellte Lösungsmenge:

![](https://raw.github.com/amintos/Artikel/master/Kryptografie/curve.png)

Die bemerkenswerte Eigenschaft dieser Kurven ist, dass man auf ihnen eine ***Addition auf Punkten*** definieren kann: Zieht man eine Gerade durch zwei Punkte auf der Kurve, schneidet sie diese stets in einem dritten Punkt oder im Unendlichen, was wir auch als gültigen Punkt auffassen. Spiegelt man den Schnittpunkt an der x-Achse, erhält man die Summe der beiden Punkte. Weiterhin sei die Punktnegation ```-P``` definiert als die Spiegelung von ```P``` an der x-Achse (Beachte,  dass der "Nullpunkt" ```0 = P + (-P)``` im unendlichen liegt.) Bei der Verdoppelung ```P + P = 2P``` nimmt man als Gerade einfach die Tangente durch ```P```.

Mit diesen Werkzeugen können wir uns eine Multiplikation  ```nP``` definieren als ```n```-maliges addieren. Das lässt sich durch mathematische Tricks enorm beschleunigen, z.B. ```15P = 16P - P = 2*2*2*2P + (-P)``` benötigt nur eine Negation, eine Addition und vier Verdoppelungen statt 15 Additionen. Diese mathematischen Abkürzungen heißen *Non-adjacent forms*.

In der Praxis kommen allerdings keine kontinuierlichen elliptischen Kurven zum Einsatz, sondern nur deren **ganzzahlige Lösungen**. Weiterhin werden alle mathematischen Operationen modulo einer Primzahl ```N``` ausgeführt - das bedeutet, dass jede X- oder Y-Koordinate "überläuft", sobald sie größer als ```N - 1``` wird. Gute ```N``` erlauben eine große Anzahl an ganzzahligen Lösungen, z.B. bietet sich die große Mersenne-Primzahl ```2^521 - 1``` an. Die Punktmultiplikation funktioniert hier noch immer.

### Schlüsselaustausch mit Punkten

Punktmultiplikation ist Assiziativ, d.h. ```a*(b*P) = b*(a*P)```. 



### (Die Rücktransformation in Threefish)
    1.)  B = (D' XOR A') >>> 5
    2.)  D = (B' XOR C') >>> 37
    3.)  C = C' - D  (modulo 2^64)
    4.)  A = A' - B  (modulo 2^64)