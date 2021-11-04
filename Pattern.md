Title:     Entwurfsmuster-Buch-Teaser  
Date:      2021-06-16  
Author:    Matthias Geirhos  
Keywords:  Entwurfsmuster, Design-Pattern, Entscheidungshilfe, Buchteaser   
Buchlink:  https://www.rheinwerk-verlag.de/entwurfsmuster-das-umfassende-handbuch/   
ISBN:      978-3-8362-2762-9  
Info:      Es wurde Beschreibung und Alternativen der Muster aus dem Buch entnommen, um relativ schnell einen groben Überblick zu bekommen. Für Details bitte das Buch dazu kaufen oder ausleihen!  


# Erzeugungsmuster

## Factory

### Anwendungsfälle

An vielen Stellen gibt es abstrakte Klassen mit jeweils einer variablen Anzahl von
Implementierungen. Vor allem Bibliotheken und Frameworks arbeiten ausgiebig
damit.
Als Entwickler programmieren wir nun gern gegen die Basisklasse und überlassen
»die Details« den untergeordneten Klassen, also beispielsweise die Implementierung
einer Stored Procedure für den SQL-Server und Oracle.
Nun wird die Entscheidung über die konkret zu implementierende Klasse erst sehr
spät, zur Laufzeit, getroffen, zum Beispiel durch die Konfiguration in der Anwendungskonfigurationsdatei,
ob als SQL-Server Oracle oder der Microsoft SQL-Server
zum Einsatz kommt.
Betrachten wir das Beispiel von oben weiter: die Belegverarbeitung. Nehmen wir einmal
an, fleißige Menschen im Kundenservice erfassen Tag für Tag Geschäftsvorfälle:
Kündigungen, Bestellungen, Gutschriften, Auslieferungen und vieles mehr. Die fakturiereDokumente-
Methode kann nun unmöglich wissen, welche konkreten Objekte
benötigt werden (soll heißen, welche Belege fakturiert werden sollen), aber sie weiß,
dass für jeden Geschäftsvorfall ein konkretes Belegobjekt benötigt wird und wann es
im Prozess zu erzeugen ist. Daher wird lediglich die Fabrikmethode aufgerufen; die
eigentliche Objekterzeugung findet aber erst in den konkreten, abgeleiteten Klassen
statt, wie in der Klasse GutschriftFakturierer.
Diese Implementierung ist nun also ausgelagert, vermutlich in ganz eigenen Bibliotheken.
Sie ist auch erweiterbar, und in der Praxis werden Implementierungen häufig
sogar zur Laufzeit bei Bedarf dazu geladen. Wir sind damit in der Lage, das
Framework, also die Klassen zur Steuerung des Prozesses (der Belegverarbeitung),
von der Implementierung zu trennen.

Damit ergeben sich die wichtigsten Anwendungsfälle:
* Die konkret zu erzeugenden Objekte sollen bequem erweiterbar sein.
* Sie sind im Framework zur Entwicklungszeit nicht bekannt, und dennoch soll
damit gearbeitet werden.

Auf der anderen Seite muss es auch möglich sein, im Framework-Code etwas Sinnvolles
zu leisten, also das Objekt zu erzeugen und damit zu arbeiten, egal welches
konkrete Objekt zur Laufzeit dann tatsächlich verwendet wird. Hätten Rechnungen
und Gutschriften wenig oder nichts miteinander zu tun, dann wäre dieses Muster
nicht von Nutzen.

### Alternativen

Der Grundgedanke dieses Musters ist es, Spezifika – also zum Beispiel die Details
einer Gutschrift – aus dem Framework herauszuhalten, damit die Klasse Fakturierer
weitgehend unabhängig davon entwickelt werden kann.
Allerdings hängt an diesem Muster ein Preisschild: Es werden zwei Vererbungshierarchien
benötigt, eine für die zu erzeugenden Produkte und eine weitere für die
Erzeuger, die Hand in Hand entwickelt werden müssen. Ein Client, also der Code, der
die Erzeugerklassen verwendet, muss sich darauf einstellen, indem er immer die
richtige Erzeugerklasse verwendet. Man könnte argumentieren, das Ableiten der
Erzeugerklasse sei eine Doppelung der ohnehin schon vorhandenen Produkthierarchie
– und hätte damit recht.


Weitere Hierarchieebenen, noch mehr Erzeuger oder die Alternative:
Fabrikmethoden mit Parametern


## Singleton

Das Singleton-Muster hat vielleicht nicht den besten Ruf, was daran liegt, dass in Zeiten
von Multicore-Systemen Parallelisierung der Trend ist, während das Singleton-
Muster sicherstellt, dass es von einer Klasse jeweils nur genau ein Objekt gibt. Das ist
das genaue Gegenteil, denn wenn es nur ein Objekt gibt, kann auch zu einer Zeit nur
jeweils ein Thread Änderungen am Objekt vornehmen.


### Anwendungsfälle

Das Singleton-Muster ist überall dort sinnvoll anzuwenden, wo es naturgemäß nur
eine einzige Ressource gibt oder wo wir aus anderen Gründen den Zugriff zentralisieren
wollen. Einige Beispiele:

* Zugriff auf zentrale (Hardware)-Ressourcen: So kann beispielsweise nur ein Softwaresystem
zu einer bestimmten Zeit auf einen installierten Scanner zugreifen.
* Bereitstellung eines Verteilmechanismus, zum Beispiel eines Druckerpools:
Während die Abarbeitung selbst durch mehrere Objekte stattfinden kann (häufig
außerdem multithreaded), ist die Queue selbst als Singleton implementiert.
* Zur fachlichen Serialisierung: Denken Sie an ein System zur Faktur von Rechnungen.
Dort könnte die Klasse zur Vergabe der Belegnummer als Singleton ausgeführt
sein. Damit ist sichergestellt, dass zwei (gleichzeitig) anfragende Clients
dennoch zwei aufeinander folgende Belegnummern erhalten.
* Bei Klassen, deren Instanziierung großen Aufwand kostet: Nehmen wir eine
Konfigurationsklasse, die selbst eine große Konfigurationsdatei lädt. Sie wollen
vermutlich nicht, dass bei jedem der häufigen Zugriffe auf die Konfiguration stets
ein neues Objekt erzeugt wird, das dann immer wieder diese Datei laden muss.

Wie gesagt: Das Singleton-Muster implementiert diese Anforderungen dadurch, dass
es von einer (Singleton-)Klasse immer nur ein konkretes Objekt gibt. Das ist keine
bloße Konvention, sondern wird durch die Implementierung technisch sichergestellt.
Allerdings muss diese Klasse dann auch folgerichtig einen globalen Zugriff
ermöglichen.
In der Praxis gibt es viel mehr zentrale Ressourcen, die jeweils nur von einem Client
genutzt werden können, als es den Anschein hat, wenn man die Bibliotheken von
Java, .NET, C++ & Co. sichtet. So kann eine Festplatte nur eine einzige Schreib- oder
Leseoperation gleichzeitig bedienen, wenn die Anfrage nicht vom internen Cache
beantwortet werden kann, und es gibt auch ungleich mehr gleichzeitig laufende
Threads, als physikalische Prozessoren (bzw. Kerne) vorhanden sind. Meist werden
diese Ressourcen »weiter hinten« im gesamten Prozess fair aufgeteilt. Üblicherweise
geschieht dies durch Warteschlangen, in die die Anfragen in ihrer Reihenfolge und
Priorität eingereiht werden. Häufig kommen zudem Zeitscheibenverfahren zum Einsatz,
wie bei der Zuteilung von Rechenzeit an laufende Threads, um eine Gleichzeitigkeit
zu simulieren, wo in Wirklichkeit ein echter »Singleton« am Werke ist.
Wie auch immer: Hier geht es um das Singleton-Muster, und dessen Reichweite ist
begrenzt. In .NET und in Java wird ein Singleton üblicherweise über statische Methoden
implementiert, und die stellen eine Eindeutigkeit, vereinfacht gesagt, nur in dem
aktuell laufenden Prozess sicher.

### Alternativen

Das Singleton-Muster hat eine lange Geschichte und zählt daher zu den bekanntesten
und am besten verstandenen Mustern überhaupt. Dennoch (oder gerade deshalb)
gibt es einige Variationen.

#### Globale Variablen
In der Praxis sieht man anstelle des Singleton-Musters häufig globale Variablen. Das
Singleton-Muster hat allerdings einige Vorteile, denn der Charme des Singletons
besteht darin, dass der Zugriff strikt kontrolliert ist, während eine globale Variable
von überall aus zugreifbar ist. Außerdem kann das Konfiguration-Objekt in einem
eigenen Namensraum leben und bequem in ein eigenes Package bzw. eine eigene
DLL ausgelagert werden. Und die Konfiguration-Klasse wird erst dann instanziiert,
wenn sie auch wirklich zum ersten Mal benötigt wird. Nicht umsonst hat es sich
inzwischen herumgesprochen, dass globale Variablen, in aller Regel jedenfalls, vermieden
werden sollten.

#### Statische Klasse
Manchmal wird die Konfiguration-Klasse auch vollständig zur statischen Klasse
umfunktioniert. Allerdings sind Sie dann hinsichtlich der Implementierung eingeschränkt,
denn eine gewöhnliche Klasse kann ihren eigenen Zustand einfach viel
bequemer in privaten Variablen speichern und auch nichtstatische Methoden für
den Client anbieten.

#### Vererbung
Grundsätzlich verträgt sich das Singleton-Muster auch mit Vererbung, allerdings
darf die Sichtbarkeit des Konstruktors dann nicht mehr private sein, sondern protected,
sodass die abgeleitete Klasse auf den Konstruktor der Singleton-Basisklasse
zugreifen kann. Dennoch rate ich davon ab, schon allein deshalb, weil ein Anwender
der Singleton-Klasse dies im Regelfall nicht erwarten würde. Um diesen Gedanken
explizit auszudrücken, bieten die meisten Sprachen eine Versiegelung der Klasse an.


## Multiton

Das Multiton-Muster ist das natürliche Geschwister des Singleton, das im vorherigen
Abschnitt behandelt wurde. Bitte lesen Sie die dortigen Ausführungen, denn ich fasse
mich hier kurz und gehe (im Wesentlichen) nur auf die Unterschiede ein.
Eine Multiton-Klasse arbeitet genauso wie eine Singleton-Klasse, allerdings mit dem
wichtigen Unterschied, dass es nicht genau ein Objekt davon gibt, sondern mehrere
(aber nicht beliebig viele).
Man kann sich darüber streiten, ob es das Multiton-Muster wirklich gibt oder ob
nicht das Anlegen mehrerer Objekte eine Eigenschaft des Singletons ist; daher findet
man in der Wissenschaft mal die eine, mal die andere Sichtweise. Wie auch immer:
Wenn es Ihnen – wie mir – merkwürdig vorkommt, dass ein Singleton eben nicht
mehr singulär ist, dann sprechen Sie lieber vom Multiton-Muster; wenn nicht, dann
betrachten Sie diesen Abschnitt einfach als Erweiterung des vorherigen.


### Anwendungsfälle
Dieses Muster eignet sich für dieselben Szenarien wie das Singleton-Muster, sofern
ein einzelnes Objekt nicht ausreicht. Einige Beispiele:
* Vielleicht stehen in einem Rechner mehrere Hardwareressourcen des gleichen
Typs zur Verfügung. Dann können sie durch jeweils eine eigene Instanz der Multiton-
Klasse abgebildet werden.
* Häufig gibt es softwaretechnisch »teure« Ressourcen, Datenbankverbindungen
sind ein Beispiel dafür. Eine Verbindung zu einem Datenbankserver aufzubauen,
nebst Authentifizierung, kann schon einmal eine Sekunde in Anspruch nehmen.
Eine solche Ressource lässt sich mit einem Multiton bei Bedarf erstellen und für
andere Clients wiederverwenden. Man spricht dann auch von Object Pooling.


### Alternativen
Es gelten die Ausführungen im Abschnitt zum Singleton (siehe Abschnitt 2.2.5). Die
Threadsicherheit wird im Beispiel durch eine einfache Sperre erreicht; auch hier
sind elegantere Versionen denkbar. Allerdings ist der kritische Code sehr kurz,
jedenfalls wenn keine Datenbankverbindung erstellt werden muss. Daher ist die
Lösung hier vertretbar.


## Abstract Factory

Manchmal geht es bei der Erzeugung von Objekten nicht nur um ein Objekt, sondern
sogar um eine ganze Familie von Objekten. Nehmen wir einmal an, Sie erstellen eine
Software für die Hausautomation. Dazu gehören ganz verschiedene Objekte, wie Sensoren
(zum Beispiel für die Messung der Temperatur) und Aktoren (wie für das Ausund
Einfahren der Jalousien). Das sind in der OO-Welt ganz typische Objekte.
Nun haben es solche Technologien so an sich, dass es zumeist verschiedene miteinander
konkurrierende Systeme gibt. Und genau da liegt die Hauptanwendung dieses
Musters: einen Klienten (in unserem Beispiel wäre das die Software zur Steuerung
dieser Geräte) von einer konkreten Objektfamilie unabhängig zu machen.
Ein »Klassiker«, der bei diesem Muster häufig ins Spiel gebracht wird, sind verschiedene
Oberflächenelemente, wie Listen, Comboboxen oder Eingabefelder, die es auch
in verschiedenen Varianten gibt. Diese Varianten sind zwar in ihrer Grundfunktion
gleich (ein Eingabefeld ist nun einmal ein Eingabefeld), unterscheiden sich aber in
ihrem Aussehen und teilweise auch in ihrem Verhalten.


### Anwendungsfälle
Ein typischer Einsatzzweck sind Klassenbibliotheken. Nehmen Sie dazu einmal an,
dass eine der beiden Firmen, sagen wir PROBUS, eine Klassenbibliothek zur Steuerung
des Eigenheims entwirft, mit dieser Bibliothek aber einen De-facto-Standard
schaffen möchte. Damit das klappt, Firmen wie HOMEBUS also darauf anspringen,
muss die Bibliothek generisch sein und darf keine konkreten Objekte der Firma
PROBUS enthalten.

Ganz konkret könnte die Bibliothek also einen Klienten (das Steuerungsmodul) enthalten
und die jeweils abstrakten Klassen für die Produkte (also Sensoren und Aktoren)
und die dazugehörigen Fabriken. Fabriken und Produkte sind zwar abstrakt,
können aber dennoch bereits Funktionen enthalten, die über alle Produktfamilien
einheitlich sind – wir können schließlich sicher davon ausgehen, dass ein Temperatursensor
die aktuelle Temperatur dem Homecontroller melden wird. Dieser Homecontroller
wird dann zum Beispiel bereits Funktionen für das Abrufen der
Temperatur und das Logging derselben mitbringen – nebst schicken Funktionen für
die Darstellung versteht sich.
Die konkreten Fabriken und Produkte verpackt PROBUS hingegen in einer eigenen
auslieferbaren DLL, die ein Kunde, der sich für das System dieser Firma entscheidet,
dann mitgeliefert bekommt, während die Kunden der Firma HOMEBUS eben deren
DLL erhalten.
Was nun konkrete Produkte sind, das ist freilich Sache des jeweiligen fachlichen Problems.
Zusammengefasst lässt sich sagen, dass sich das Muster also eignet, wenn
* es mehrere Objekte gibt, die jeweils zu einer Produktfamilie gehören.
* bereits heute oder in naher Zukunft weitere Produktfamilien dazukommen.
 der Klient von den Spezifika der Objekterzeugung befreit werden soll (wobei das
praktisch für alle Muster dieses Kapitels zutrifft).
* die Objekte untereinander Abhängigkeiten besitzen, sodass immer die gesamte
Produktfamilie ergänzt oder ausgetauscht wird und nicht nur einzelne Objekte.
In unserem Beispiel ist klar: Controller, Sensoren und Aktoren gehören zusammen,
schließlich sollen die Jalousien bei Frostgefahr automatisch eingefahren
werden: Temperatursensor und Jalousieaktor wirken also über den Heimcontroller
zusammen.
* Sie vermeiden wollen, dass Objekte verschiedener Familien gemischt werden.
Auch das ist einleuchtend, wenn wir annehmen, dass die Systeme der Firma PROBUS
und die von HOMEBUS nicht kompatibel sind.
Gerade die letzten beiden Punkte der Aufzählung haben es in sich, denn realisiert
werden sie gerade dadurch, dass die Fabrikklassen für jedes zu erzeugende Objekt
eine eigene Methode mitbringen. Die Art und Menge der zu erzeugenden Produkte
ist also in der Schnittstelle festgelegt.
Und, wie immer bei Schnittstellen, erfordert das Erweitern einer Schnittstelle auch
automatisch, dass alle konkreten Fabrikklassen erweitert werden müssen. Wenn also
PROBUS sich dazu entschließt, ein neues Produkt auf den Markt zu bringen (sagen
wir, ein Telefongateway zur Meldung von Einbrüchen an den Hausbesitzer), dann


### Alternativen

Das Abstrakte-Fabrik-Muster ähnelt den Fabrikmethoden, die bereits beschrieben
wurden. Eigentlich sind die Erzeuger-Methoden ja nichts anderes. Was dieses Muster
unterscheidet, sind die verschiedenartigen Objekte, die eben alle einer Produktfamilie
angehören und die auch untereinander Beziehungen aufweisen können.
Dabei unterstützt dieses Muster einerseits die enge Beziehung der Produkte einer
Familie, und es stellt andererseits sicher, dass sich die Produkte verschiedener Familien
nichts ins Gehege kommen – schließlich verwendet der Klient zu einer Zeit nur
eine konkrete Fabrik – und damit eine Produktfamilie.

## Builder

Dieses Pattern scheint, begrifflich gesehen, der Inbegriff aller Erzeugungsmuster zu
sein, wird aber in der Praxis eher seltener verwendet. Es kommt immer dann zum
Zuge, wenn es gilt, die Erzeugung eines Objekts von dessen Darstellung (Repräsentation)
zu trennen.
Am Ende des Prozesses entsteht immer ein konkretes Objekt, das selbst aus verschiedenen
Teilen besteht. Welche das sind und welche Schritte für deren Erstellung nötig
sind, soll der Client nicht wissen, und derselbe Erstellungsprozess soll Objekte mit
verschiedenen Teilen liefern können, ohne angepasst werden zu müssen.
Wenn Ihnen das jetzt nicht so recht klar ist: Werfen Sie einen Blick auf die Anwendungsfälle
und die Implementierung, denn dieses Muster ist schon recht abstrakt.

### Anwendungsfälle
Der Klassiker für dieses Muster ist die Konvertierung in verschiedene Zielformate.
Der Direktor ist dann eine Klasse, die beispielsweise eine Datei einliest und für jede
Zeile, die zu konvertieren ist, einen Erbauer bemüht – zum Beispiel eben Produkte
aus einer ERP-Software in verschiedene Austauschformate.
Solche Konvertierungsaufgaben erfordern es natürlich, dass für jedes Element der zu
konvertierenden Menge ein Erbauer beauftragt wird. Bei einer Konvertierung von
Texten in verschiedene Zielformate, sagen wir UTF-8 oder Plain ASCII, muss vielleicht
jeder Token einzeln konvertiert werden, beim ERP-Beispiel von oben ist es eine Liste
von Produkten, die einzeln konvertiert werden. Die Unterklassen der abstrakten
Erbauer-Klasse sind dann Klassen für die verschiedenen Zielformate, wie eben UTF-8
oder ASCII.
Ein anderer Paradefall für dieses Beispiel sind Objekte, die aus verschiedenen Teilen
zusammengesetzt sind bzw. die in verschiedenen Varianten vorkommen können.
Solche Objekte werden auch Komposita genannt. Eine CAD-Anwendung könnte zum
Beispiel verschiedene Fenster zur Auswahl anbieten, die einer Zeichenfläche hinzugefügt
werden können.

Der Client wäre übrigens die CAD-Anwendung selbst. Ganz praktisch gesehen würde
es vermutlich einen Eventhandler geben, wenn der Anwender auf ein Toolbar-Symbol
klickt, zum Beispiel das Symbol zur Erstellung von Dachfenstern.
Der Charme des Erbauer-Musters besteht nun darin, dass die Komplexität zur Erstellung
von Fenstern und die Erzeugung entsprechender Produkt-Objekte in den konkreten
Erbauerklassen versteckt sind. Die Vorteile:
* Es können jederzeit neue Fenstertypen hinzugefügt werden, ohne an der Direktor-
Klasse (der CAD-Arbeitsfläche in unserem Beispiel) Änderungen vornehmen zu
müssen. Wir müssen lediglich eine neue konkrete Erbauer-Klasse erzeugen (z. B.
zur Erstellung von Schiffsfenstern) und den Direktor aus dem CAD-Client heraus
damit aufrufen.
* Die Software ist auch besser wartbar, weil die Komplexität dort versteckt wird, wo
sie hingehört – in der konkreten Implementierung.


### Weitere Überlegungen und Alternativen
Das Erbauer-Muster unterscheidet sich von der abstrakten Fabrik, die ebenfalls in
diesem Kapitel zu finden ist. Auch deren Hauptaufgabe ist die Erzeugung eines komplexen
Objekts. Das Erbauer-Muster erschafft das Objekt allerdings Schritt für
Schritt über die baueTeil-Methoden (addWidget-Methoden im obigen Beispiel). Erst
am Ende wird das Produkt (die fertige HTML-Seite) zurückgegeben, und zwar über
die Methode gibErgebnis (Methode render im Beispiel). Wenn das Erstellen also ein
mehrteiliger Prozess ist, dann ist das Erbauer-Muster geeignet, während die
abstrakte Fabrik dafür auch abgeleitete Produkt-Objekte erstellen und zurückgeben
kann.
An die Stelle einer abstrakten Erbauer-Klasse kann grundsätzlich auch eine Schnittstelle
treten. Allerdings kommt bei diesem Muster schnell der Wunsch auf, doch
noch die eine oder andere Aufgabe in dieser Klasse zu übernehmen, wie das Erstellen
der HtmlPage im Beispiel. Daher bevorzuge ich tatsächlich die abstrakte Klasse, die
aber dafür nicht unbedingt abstrakt sein muss. Die baueTeil-Methode kann auch virtuell
sein und in den abgeleiteten Erbauer-Klassen überschrieben werden.
Als Alternative werden häufig auch Produkt-Klassen mit mehreren überladenen
Konstruktoren genannt, in denen die Klasse dann entsprechend den Wünschen konfiguriert
wird. Das ist allerdings keine echte Alternative, weil ihr praktisch alle Vorteile
des Erbauer-Musters fehlen – vor allem die Entkopplung des Clients von der
Erzeugung des Produkts.



## Prototype

In der Softwareentwicklung verstehen wir unter einem Prototyp häufig einen ersten
Entwurf, der meist noch über keine oder sehr wenige Funktionen verfügt und zur
Anschauung und Diskussion dient. Im ursprünglichen Wortsinn jedoch ist ein Prototyp
ein einzelnes Exemplar, das als Vorlage zur späteren Serienfertigung verwendet
wird.
Und genau darum geht es in diesem Muster. Es wird ein prototypisches Exemplar
erzeugt, von dem später beliebig viele identische (oder wenigstens ähnliche) Kopien
erzeugt werden.
Wenn Sie mit Vorlagen arbeiten, beispielsweise unter Microsoft Word, dann ähnelt
dieses Verfahren diesem Muster. Denn das Erstellen eines neuen Dokuments aus
einer Vorlage bedeutet im Wesentlichen, eine Kopie dieser Vorlage anzufertigen, die
Sie anschließend bearbeiten können.

### Anwendungsfälle
Kurz gesagt spielt dieses Muster vor allem dann seine Stärken aus, wenn die Erzeugung
von Objekten durch Klonen einfacher und schneller ist als das erstmalige
Erzeugen von Objekten.
Natürlich ist in jedem Fall erst einmal das erste Objekt zu erzeugen. Dafür können
Sie die hier vorgestellten Muster verwenden, die auch über eine Erzeugerhierarchie
verfügen. Sie könnten beispielsweise das Abstrakte-Fabrik-Muster verwenden, um
damit die Sensoren und Aktoren aus dem dort vorgestellten Anwendungsfall zu
erzeugen, und anschließend diese Objekte nur noch mit dem Prototyp-Muster
kopieren.
Damit ist auch schon ein Paradefall beschrieben: das Laden von Objektzuständen aus
Dateien. Denn natürlich ist das Dateisystem um viele Größenordnungen langsamer
als der Arbeitsspeicher, sodass Sie ein Objekt vermutlich nur einmal beim Programmstart
vor der ersten Verwendung erstellen und dabei aus der Konfigurationsdatei
konfigurieren wollen. Im Anschluss hilft Ihnen das Prototyp-Muster, dieses
Objekt immer wieder aufs Neue zu vervielfältigen.
Was Sie mit diesem prototypischen Objekt machen, ist dabei eigentlich zweitrangig.
Sie könnten es zum Beispiel nehmen und damit eine GUI-Werkzeugleiste füllen oder
es sich in einer internen Struktur für das spätere Klonen merken – das Prototyp-Muster
macht keine Vorhersagen darüber, wie ein Klient an die zu klonenden Objekte
kommt.


### Alternativen
Ich habe schon erwähnt, dass es dem Muster egal ist, woher der Klient die Prototyp-
Objekte erhält, die er im späteren Verlauf kopieren kann. In unserem Dienstplan werden
die Objekte durch das Einlesen von einer XML-Datei erzeugt und in einer Hash-
Map, also einer Key-Value-Datenstruktur, gespeichert. In der Praxis ist das ein großer
Vorteil, denn auch andere Klassen könnten nun ihrerseits wiederum neue Prototyp-
»Kopiervorlagen« zum System hinzufügen oder bestehende entfernen – und das zur
Laufzeit. Durch die Konfiguration, also den Aufbau der XML-Datei, wird der Funktionsumfang
der Dienstplansoftware vorgegeben und kann auf diese Weise auch recht
leicht erweitert werden. Das Anlegen neuer Dienstplan-Objekt-Typen wird zu einer
Konfigurationsangelegenheit, zumindest wenn keine grundlegend neuen Funktionen
erwartet werden.
Der Klient muss die jeweiligen Besonderheiten der zu klonenden Klassen zudem
nicht kennen. Er ruft einfach die klone-Funktion auf, wenn er ein neues Objekt eines
bestimmten Typs benötigt.

# Strukturmuster


## Adapter

Schon wieder so ein Begriff, der im Grunde so spezifisch ist wie ein Politiker im Wahlkampf.
Im Falle des Adapter-Musters ist er aber gut gewählt, denn so, wie ein Stromadapter
das Stromnetz und den Verbraucher zusammenkommen lässt, so hilft das
Adapter-Muster dabei, zwei an sich inkompatible Schnittstellen miteinander zu verbinden.

Das Adapter-Muster gibt es in zwei Varianten:
* Der Klassenadapter bedient sich der Mehrfachvererbung und erbt sowohl von der
Schnittstelle des Zielsystems (also der Klasse, die ein Klient verwenden möchte)
als auch von der zu implementierenden Klasse.
* Der Objektadapter kommt ohne Mehrfachvererbung aus, indem er eine Operation
(in der Regel also einen Methodenaufruf) auf ein Objekt der adaptierten Klasse
abbildet.


### Anwendungsfälle
Es geht darum, einen Klienten über die eigene Schnittstelle des Klienten mit einer
fremden Klasse bzw. einem fremden Objekt zu vereinen, so viel ist klar. Das ist meist
dann wichtig, wenn Sie ohnehin schon Klassen haben, die ein bestimmtes Interface
vorgeben und eine andere Klasse dazu passend machen müssen.
Das kommt recht häufig vor, jedenfalls häufiger, als man dieses Muster in der Praxis
vorfindet. Ein Klient, also Ihre eigene Anwendung, wird ja bereits über eigene Schnittstellen
bzw. Basisklassen verfügen. Und es gehört nicht viel negatives Denken dazu,
sich vorzustellen, dass eine Klassenbibliothek eine andere, wahrscheinlich inkompatible
Schnittstelle anbietet, Sie deren Funktionalität aber in Ihrer Anwendung nutzen
möchten.
Entscheidend dabei ist die Ziel-Schnittstelle des Klienten, denn natürlich könnte ein
Klient die Sache trivial lösen, indem er die adaptierte Klasse instanziiert und einfach
alle Methoden aufruft, Events abonniert etc., die er gerade benötigt. Die Zielschnittstelle
aber soll verhindern, dass ein Klient die konkrete Klasse AdaptierteKlasse kennen
muss. Klient und Funktionalität werden über die Schnittstelle also voneinander
abstrahiert, was ein hohes Gut darstellt.

Das hat natürlich seine Grenzen, denn ein Adapter muss erst einmal in der Lage sein,
die adaptierte Klasse mit der Ziel-Schnittstelle kompatibel zu machen. Im UML-Beispiel
wird dazu einfach die Operation der adaptierten Klasse in der Operation der
Ziel-Schnittstelle aufgerufen; ein Schelm wer dabei denkt, dass die Praxis vielleicht
ein Jota vertrackter sein könnte.
Idealerweise fügt der Adapter sich dabei nahtlos in die Vererbungshierarchie des Klienten
ein, und in der Praxis werden Sie ihn vermutlich nicht einmal Adapter nennen
oder ihm das Wort »Adapter« anhängen, wie im Praxisbeispiel weiter hinten.
In gewissen Grenzen kann ein Adapter auch zusätzliche Funktionalität nachrüsten,
die die adaptierte Klasse so gar nicht bereitstellt. Oft ist dies schon allein deshalb
nötig, weil die Zielschnittstelle diese Funktionalität einfach fordert.


| Spricht eher für den Klassenadapter | Spricht eher für den Objektadapter |
| :------ | :------ |
| Die Anzahl der Klassen ist gering. | Die Anzahl der Klassen ist hoch. |
| Die Klassen sind bereits zur Entwurfszeit vollständig bekannt. | Es finden häufig Erweiterungen statt, und die konkreten Objekte sind erst zur Laufzeit bekannt. |
| Es wird eine tiefe Integration benötigt, z. B. wenn der Adapter auch auf Interna der adaptierten Klasse zugreifen soll. | Die Tiefe der Integration ist nicht sehr hoch, und die nach außen hin sichtbare Schnittstelle der adaptierten Klasse genügt, um den Adapter zu bauen. |
| Sie nutzen z. B. C++, das echte Mehrfachvererbung bietet. | Sie nutzen eine OO-Sprache ohne Mehrfachvererbung. |
| Sie wollen Funktionen der adaptierten Klasse überschreiben. | Sie kommen mit der Funktion der adaptierten Klasse grundsätzlich zurecht. |
| Die Objekterstellung soll außerhalb des Adapters stattfinden | Es ist in Ordnung, wenn der Adapter die adaptierte Klasse selbst instanziiert und Operationen darauf ausführt. |
| Es gibt keine oder keine tiefere Vererbungshierarchie bei den adaptierten Klassen. | Die Vererbungshierarchie ist umfangreicher, und ein Adapter soll sowohl mit der Basisklasse der adaptierten Klasse als auch mit deren Unterklassen zurechtkommen. |

### Alternativen
Im vorherigen Beispiel haben wir einen Objektadapter implementiert. Ein solcher
verwendet Objektkomposition, unterhält also eine Instanz auf die zu adaptierende
Klasse und delegiert die Anforderung zur Laufzeit an diese Instanz. Man bezeichnet
solche Klassen auch als Wrapperklassen oder Hüllenklassen.
Auf solche Hüllen trifft man sowohl in Java als auch in der .NET-Welt, beispielsweise
bei der Verwendung primitiver Datentypen. Dort gibt es für jeden primitiven Datentyp
eine entsprechende Wrapper-Klasse. Die Klasse Integer in Java legt sich also um
den Datentyp int. Der eigentliche Wert wird in einem privaten Feld gespeichert.

Ein als Boxing und Unboxing bekannter Mechanismus lässt uns diese Klassen
anstelle der primitiven Datentypen verwenden, wobei Java (und .NET) die primitiven
Typen dabei automatisch in Objekte verpackt (Boxing) und wieder auspackt
(Unboxing).
Die vielleicht wichtigste Frage, die man sich bei diesem Muster stellen sollte, liegt
nahe: Ist die zu adaptierende Klasse wirklich inhaltlich kompatibel? Vermittelt der
Adapter also nur zwischen zwei verschiedenen Implementierungswelten (im Beispiel
sind es verschiedene Rückgabetypen), oder greift er auch inhaltlich mehr oder
minder stark in die zu adaptierende Klasse ein (im Beispiel betrachteten wir die
Fähigkeit, dass ein Workflow-Objekt Unterobjekte beinhalten kann). Im letzten Fall
gibt es eine Grenze, die sich nur schwer in Worte fassen lässt. Wenn Sie bei der
Umsetzung merken, dass Sie immer mehr Klimmzüge machen müssen, um die zu
adaptierende Klasse kompatibel zum Ziel zu machen, dann sollten Sie irgendwann
die Reißleine ziehen und ein anderes Muster wählen. Oder anders gesagt: Das Erstellen
des Adapters sollte wesentlich weniger Aufwand verursachen, als die Erstellung
der zu adaptierenden Klasse versursacht hat.

## Bridge

Für gewöhnlich unterscheiden wir in der OO-Welt zwischen einer Schnittstelle und
ihrer Implementierung. Die Schnittstelle kann eine (häufig) abstrakte Basisklasse
oder eine Schnittstelle im Sprachsinne sein. Implementierungen entstehen dann
durch Vererbung bzw. Implementierung dieser Schnittstelle.
Diese Vorgehensweise bringt Implementierung und Schnittstelle (die sogenannte
Abstraktion) recht eng zusammen, was in den meisten Fällen erwünscht ist oder
jedenfalls kein echtes Problem darstellt.
Dennoch gibt es Fälle, in denen man die Schnittstellen sauber von den Implementierungen
trennen möchte, zum Beispiel wenn man beides weitgehend getrennt
voneinander entwickeln will. Durch die Vermischung von Schnittstelle und Implementierung
leidet nämlich die Übersichtlichkeit, und nicht selten kommt es vor,
dass in einem Projekt Schnittstellenklassen, Implementierungsklassen und viele
weitere Schnittstellen bzw. abstrakte Klassen vermischt sind. Als Verwender stellt man sich da manchmal die Frage, was da noch Schnittstelle ist und wo schon die
Implementierung beginnt, und wünscht sich eine saubere Trennung.
Das Brückenmuster erlaubt es, genau das zu tun. Durch die Trennung der Implementierung
von den Schnittstellen ist es möglich, die Implementierung auf
einfache Art und Weise zur Laufzeit auszutauschen oder auch die Schnittstelle
wiederzuverwenden.

### Anwendungsfälle
Das Brückenmuster ist ein wenig abstrakt, und seine Vorteile erschließen sich erst
einmal nicht auf den ersten Blick. Die Verwendung von zwei verschiedenen Hierarchien
– eine für die Abstraktion und eine weitere für die Implementierung – scheint
erst einmal unnötig kompliziert zu sein, und tatsächlich ist die höhere Komplexität
ein Nachteil dieses Musters.
Damit Sie die Vorteile sehen, folgt hier ein Beispiel aus meiner eigenen Praxis. In
(praktisch) jeder Geschäftsanwendung gibt es die Notwendigkeit, Dateien zu verwalten.
In meinem Fall sind das dröge Ausgangsrechnungen, Lieferscheine, Eingangsrechnungen,
Bestellbestätigungen und weitere Belege.
Diese Dateien liegen natürlich irgendwo, häufig im Dateisystem. Das ist aber häufig
nicht optimal, denn natürlich sind diese Belege mit Datenbankinhalten verknüpft.
Eine Rechnung ist sowohl in Datenbanktabellen vorhanden als auch als gerenderter
Beleg, und es wäre schön, wenn beides untrennbar verbunden wäre. Denn dann könnten beides – Datenbanktabellen und Dateien – in derselben Datensicherung
gespeichert werden und beim Löschen der Rechnung in den Datenbanktabellen
würde auch gleich der zugehörige Beleg gelöscht werden.
Dafür gibt es Technologien, beispielsweise bietet Microsofts SQL-Server Filestream
an, was genau das tut – die transaktionale Welt der Datenbanken mit der Welt der
Dateien zu vereinen.

### Weitere Überlegungen und Alternativen
Nun noch einmal im Detail: Wann genau eignet sich das Brückenmuster?
* wenn die Erweiterung einer Klassenhierarchie viele weitere Klassen mit sich bringen
würde, weil verschiedene Aspekte einer Klasse eine Differenzierung notwendig
machen (wie im Beispiel mit den Controls das Zielsystem)
* wenn während der Kompilierzeit die Implementierer nicht bekannt sein sollen –
eine unumgängliche Maßnahme, wenn Sie die klassische Vererbung einsetzen.
Das kann zum Beispiel dann der Fall sein, wenn zur Laufzeit die Implementierung
ausgetauscht werden soll.
Die direkte Folge der fehlenden Kompilierzeit-Abhängigkeit zwischen Implementierer
und Abstraktion ist, dass ein Klient nicht neu kompiliert werden muss,
wenn die Implementierung sich ändert, sodass die Implementierung auch
bequem eigenständig ausgerollt werden kann.
* wenn die Definition von etwas (z. B. die Controls) weitgehend unabhängig von seiner
Verwendung ist (z. B. dem Zeichnen) und daher auch unabhängig voneinander
entwickelt werden kann
* wenn eine oder mehrere Implementierungsklassen von einer oder mehreren
Abstraktionsklassen wiederverwendet werden können. Im Falle der Controls gibt
es bestimmt noch weitere Controls, die für ihre Darstellung einen Pfeil nach unten
zeichnen müssen.
So weit, so gut, aber woher hat dieses Muster seinen Namen? Wo ist die Brücke? Als
Brücke bezeichnet dieses Muster eben genau die Verbindung zwischen Abstraktion
und Implementierer, denn die Abstraktion gelangt ja über die Referenz zum Implementierer,
und damit sind die beiden Hierarchiewelten überbrückt.

#### Ähnliche Muster
Ein Adapter arbeitet ähnlich, hat aber als Hauptzweck die Kompatibilität zwischen
zwei Schnittstellen, während die Motivation der Brücke die Trennung zwischen zwei
Hierarchien zum Zwecke der getrennten Weiterentwicklung ist.
Die abstrakte Fabrik kann eine Brücke erstellen, auch wenn dieser Fall nicht sehr häufig
vorkommen sollte.

### Wann eignet sich dieses Muster nicht?
Der Natur nach eignet sich das Brückenmuster weniger, wenn die Voraussetzungen
nicht vorliegen, also wenn
* es nur eine Implementiererklasse gibt und keine weitere in Sicht ist, obwohl die
Trennung auch in diesem Fall einen Vorteil bringen kann – denken Sie nur an das
getrennte Ausrollen des Klienten und des Implementierers.
* ein Implementierer fachlich oder technisch so eng mit einer Abstraktionsklasse
verbunden ist, dass die direkte Ableitung einfacher und sinniger ist.
* eine Implementiererklasse nur für eine Abstraktion zuständig ist, eine Wiederverwendung
also ausgeschlossen ist.

## Composite

Das wesentliche Merkmal komplexerer Objekte ist es, dass sie aus einfacheren Objekten
zusammengesetzt sind, die häufig aus noch einfacheren Objekten bestehen –
und seien es nur Strings oder Arrays.
Auf diese Weise entstehen Baumstrukturen oder, vornehmer ausgedrückt, es entstehen
Teil-Ganzes-Hierarchien. Solcherlei Objekte zu erstellen ist einfach (dem new-Operator
sei gedankt), sie aber in der eigenen Anwendung so zu behandeln wie einfache
Objekte ist eine Kunst, die dieses Muster beherrscht.

### Anwendungsfälle
Inzwischen ist klar: Es geht um Objektbäume. Wie alle anderen Bäume auch gibt es
dort Blattobjekte und Komposita (man könnte auch Container dazu sagen).

#### Eignung
Damit eignet sich dieses Muster, wenn
* erst einmal überhaupt solche komplexeren Strukturen in Ihrer Anwendung vorkommen
* die Komplexität nicht immer gleich ist, die Objekte also zur Laufzeit aus verschiedenen
Elementen zusammengebaut werden können, wie im Beispiel des BPMEditors.
Solche Objekte sind also rekursiv aus immer weiteren Objekten zusammengesetzt.
* ein Klient mit Objekten ganz unterschiedlicher Komplexität in derselben Art und
Weise arbeiten muss.
* die Komposita und Blattknoten erweitert werden sollen, zum Beispiel um neue
BPM-Objekte. Die zentrale Schnittstelle Komponente macht dann keine Änderung
im Klient erforderlich.
Damit eignet sich dieses Muster für ein weites Feld von Aufgabenstellungen. Häufig
sind diese Aufgabenstellungen dergestalt, dass wirklich verschiedene Typen zusammengesetzt
werden können, wenn sie nur von der zentralen Schnittstelle Komponente
abgeleitet wurden. Es gibt aber auch die Fälle, in denen ein Kompositum-Objekt nur
gewisse andere Objekte beinhalten darf, sei es aus fachlichen oder aus technischen
Gründen. Auch dann lässt sich dieses Muster anwenden. Sie müssen aber dann beim
Hinzufügen von neuen Objekten zur Laufzeit prüfen, ob das Objekt, das hinzugefügt
werden soll, in diesem Kontext erlaubt ist. Ab einem gewissen Grad an Abhängigkeiten
verliert dieses Muster aber an Charme und Sie wollen vielleicht lieber ganz einfache
Ableitungen verwenden.

#### Vorsicht, Rekursion!
Dieses Muster verwendet Rekursion, eine Technik, die für den einen oder anderen
Entwickler so verwirrend ist wie Zeitreisen oder das Umsatzsteuergesetz. Sie sollten
sich also einigermaßen sicher in Bäumen bewegen können und den Aufruf einer
Methode aus sich selbst heraus für etwas ganz Normales halten.
Bei dieser Gelegenheit präsentiere ich Ihnen zwei Begriffsdefinitionen, sodass wir
dasselbe unter den Begriffen verstehen:
* Unter Rekursion verstehen wir den Aufruf einer Methode aus sich selbst heraus.
Rekursion ist das Mittel der Wahl, um Bäume zu traversieren.
* Traversieren bedeutet, Bäume Knoten für Knoten zu durchlaufen. Eine andere
Möglichkeit ist es, die Rekursion aufzubrechen, indem ein Objekt »flachgeklopft«
wird.

### Weitere Überlegungen und Alternativen
Das Kompositum-Muster ist einfach, hat es aber in sich. Und so gibt es viele Varianten
und Ergänzungen für dieses Muster. Eine erste wurde schon beschrieben: die Entscheidung
für die Deklaration der Verwaltungsmethoden für Kindobjekte in der
Klasse Komponente oder Kompositum.

#### Alternativen
Vom Durchlaufen des Baums war schon die Rede. Hierfür können Sie auch das Iterator-
Muster verwenden.
Kompatibel zu diesem Muster ist auch der Dekorierer.

## Decorator

Auch beim Dekorierer ist es ein Ziel, einem Objekt neue Funktionalitäten beizubringen,
und zwar auch hier ohne Klassenvererbung, also ohne Erweiterung durch Vererbung.
Ein Objekt wird also zur Laufzeit dynamisch mit neuen Zuständigkeiten bzw.
Funktionen »dekoriert«.

### Anwendungsfälle
Wie auch schon bei anderen Mustern ist einer der wichtigsten Gründe dafür, dieses
Muster anzuwenden, die Vermeidung von ausufernden Klassenhierarchien. Das
geschieht immer dann, wenn eine Klasse in verschiede Richtungen entwickelt werden
soll. 
Nehmen wir einmal an, Sie hätten eine Klassenhierarchie mit, sagen wir, zehn ganz
unterschiedlichen Klassen. Sie entscheiden sich nun, diesen Klassen neue Funktionen
zu spendieren, zum Beispiel die Fähigkeit, sich auf einem Datenträger zu speichern
(man nennt das dann Serialisierung bzw. Deserialisierung). An sich ist das eine
legitime Forderung, aber schon werden aus zehn Klassen zwanzig Klassen, jeweils
eine mit und eine ohne Serialisierungsfunktion. Bei der nächsten Unterscheidung
werden daraus schon vierzig Klassen, und irgendwann reicht auch der Datenbereich
eines Integers nicht mehr aus, um die Klassen zu zählen (okay, das war übertrieben).
Wie auch immer, das Dekorierermuster eignet sich, wenn
* neue Funktionen zu bestehenden Klassen hinzugefügt werden sollen, ohne für
jede Funktion neue Unterklassen bilden zu müssen
* Sie zur Laufzeit den Dekorierer einsetzen bzw. austauschen möchten und nicht
statisch, zur Kompilierzeit. Allein das ist schon ein riesiger Vorteil.
* es sich eher um »Aspekte« einer Sache handelt, sodass eine Vererbung ohnehin
nicht ihre volle Stärke ausspielen könnte
* Sie mehrere Dekorierer an ein Objekt anhängen wollen



Besonders der zweite Punkt verdient Beachtung. In vielen Softwaresystemen ist man
es heute gewöhnt, dass Anwender zur Laufzeit Entscheidungen treffen können und
nicht bereits zum Zeitpunkt der Erstellung der Software.
Manche dieser Entscheidungen sind teuer. Denken Sie zum Beispiel an die Kommunikation
über ein Netzwerk. Im Intranet soll diese möglichst einfach stattfinden, die
Sicherheit ist hier vielleicht nicht besonders wichtig. Jeder Overhead, der sich durch
Verschlüsselung und Authentifizierung zwangsläufig ergibt, ist daher nicht willkommen
– und in Hinblick auf die Sicherheit sind die Kosten bisweilen immens, gemessen
in CPU-Zeit und Traffic. Im Internet sieht die Sache natürlich anders aus, und so
könnte man ein Objekt, sagen wir einen Proxy, mit diesen Fähigkeiten auszeichnen,
also dekorieren.
There is no such thing as a free lunch heißt es, und das gilt auch für den Dekorierer. Ein
Dekorierer ist ein Wrapper, und jeder Aufruf einer Basisfunktionalität muss zweimal
stattfinden: einmal an den Dekorierer und ein zweites Mal vom Dekorierer zum
dekorierenden Objekt, das ja weiterhin die Basisfunktionalitäten abbildet. Das gilt es
also zu bedenken – nicht umsonst spricht man auch von einem Wrapper.
Das Dekorierermuster findet man in der Praxis recht häufig, vor allem die gängigen
APIs verwenden das Muster für ihre Zwecke. Ein schönes Beispiel findet man in der
Java-API. Dort gibt es für Eingabeströme diverse Klassen.


### Alternativen
Das Strategie-Muster verfolgt ähnliche Ziele und ist vor allem dann besser geeignet,
wenn die Komponentenklasse eben nicht mehr überschaubar und leichtfüßig daher kommt, sondern – im Gegenteil – vom Dekorierer einiges an Implementierungsarbeit
abverlangt. Während ein Dekorierer von außen wirkt, ein Objekt also umhüllt
(daher ja der Begriff Wrapper), wirkt das Strategie-Muster von innen.
Das Adapter-Muster lässt sich von einem Klienten ebenso transparent verwenden
wie ein Dekorierer und kann obendrein noch die Schnittstelle des adaptierten Systems
ändern, also zu einem Ziel kompatibel machen. Der Dekorierer lässt die Schnittstelle
hingegen unverändert.

## Facade

Ich kann mir nicht helfen, aber seit Bully Herbigs Film »Der Schuh des Manitu« hat
dieses Muster bei mir an Ansehen verloren. Dort will der listige Schurke Santa Maria
den Häuptling der Apachen, Abahachi, mit einer Fassade eines Salons über den Tisch
ziehen, die alsbald in sich zusammenfällt, weil außer der Fassade nichts da ist. Solche
leeren Fassaden, hinter denen rein gar nichts ist, gibt es in der Praxis auch dann und
wann, die Regel ist das aber nicht.
Eine Fassade bietet eine einheitliche und meist deutlich einfachere Schnittstelle für
eines oder mehrere Systeme dahinter. Der Name ist also gut gewählt: Wie Abahachi
sieht ein (Software-)Klient nur die Fassade, nicht aber die dahinterliegenden Systeme;
er kann nur hoffen, dass da wirklich etwas ist …


### Anwendungsfälle
Fassaden sind in der Praxis ziemlich häufig anzutreffen, weil die Probleme, die sie
lösen möchten, sich mit der Zeit in vielen Systemen ganz automatisch einstellen.
Die meisten Systeme entwickeln sich organisch weiter. Am Anfang steht häufig ein
System überschaubarer Komplexität. Mit der Zeit kommen weitere Klassen, Ableitungen
und Beziehungen dazu, das System wächst – und mit ihm auch die Komplexität.
An einem gewissen Punkt teilt man ein System in Subsysteme auf, bildet also
einen neuen Layer und damit eine weitere Ebene der Komplexität. Wiederum später
treten völlig neue Systeme auf, die sich dann auf dieselbe Art und Weise entwickeln.
Denn merke: Komplexität ist ein Eichhörnchen. Bis man die Komplexität wahrnimmt,
hat es schon viele Haselnüsse versteckt.
Oder es sollen zwei Systeme integriert werden, die im Grunde dasselbe machen, so
wie im Praxisbeispiel weiter hinten. Eine Fassade bietet dann eine einheitliche
Schnittstelle zu beiden Systemen, sodass die vielleicht vielen verschiedenen Anwendungen
nur mit der einheitlichen Fassade kommunizieren und die Spezifika der
unterschiedlichen Subsysteme vor ihnen verborgen bleiben.
Die wichtigsten Gründe für das Fassadenmuster:
* Ein Klient soll mit einer einfacheren Schnittstelle kommunizieren und sich nicht
um die Komplexität der Welt hinter der Fassade kümmern müssen. Damit kann
der Klient einfacher erstellt, getestet und gewartet werden.
* Durch die einheitliche Schnittstelle der Fassade können die Systeme dahinter,
jedenfalls bis zu einem gewissen Grad, auch weiterentwickelt werden, ohne dass
die Klienten davon etwas merken würden.
* Fassaden können die Sicherheit erhöhen: einerseits, weil Fehlanwendungen vermieden
werden, und andererseits, weil das dahinterliegende System einfacher
geschützt werden kann, wenn nur die Fassade darauf zugreift. Die Fassade erfüllt
dann zusätzlich zur Vereinfachung die Rolle einer Trust Boundary.
* Eine Fassade begünstigt die lose Kopplung, ein Prinzip in der Softwareentwicklung,
das die Abhängigkeit verschiedener Systeme voneinander verringert. Das ist
meist eine gute Idee, weil die Systeme dann weniger verzahnt sind und Änderungen
an einem System dann nur lokale Auswirkungen haben.
* Die lose Kopplung erleichtert auch die Portierung, denn die Systeme hinter der
Fassade können ihren Host und ihre Implementierung ändern, ohne dass die Klienten
davon betroffen wären.
* Manchmal gibt es auch verschiedene Anwendergruppen, die voneinander isoliert
werden sollen. Zum Beispiel soll das interne ERP-System eines Unternehmens vollen
Zugriff auf alle Funktionen der Middleware erhalten, die Kunden aber nur
Zugriff auf zum Beispiel den Lieferstatus oder das automatische Bestellwesen. Eine Fassade verhindert dann, dass Kunden auf dumme Ideen kommen, und sorgt
dafür, dass sie nur das sehen, was sie auch tatsächlich nutzen können.
* In seltenen Fällen sollen Funktionen »ausgebaut« werden. Der Klient soll davon
aber nichts mitbekommen, weil vielleicht ein Rollout in Dutzenden von Zweigstellen
dafür notwendig wäre. Dann kann eine Fassade Anfragen ins Leere laufen lassen
oder Standardantworten liefern.
* Dann gibt es noch die spezielleren Fassadentypen, von denen in Abschnitt 3.5.5 die
Rede ist.


### Alternativen
Gewisse Ähnlichkeiten bestehen zum Proxy. Ein Proxy ist ja ein Stellvertreterobjekt,
und auch die Fassade übernimmt eine solche Aufgabe – ein Klient benutzt die
Fassade stellvertretend für die dahinter verborgenen Subsysteme. Allerdings ist der
Fokus bei einer Fassade häufig die Vereinfachung, während Proxys meist nur für einzelne
Klassen eingesetzt werden – und eben nicht für mehrere Subsysteme.
Der Adapter ist auch eine Art Fassade, adaptiert dabei aber eine Schnittstelle auf eine
andere, eigene Schnittstelle.
Natürlich kann das Fassadenobjekt selbst wiederum mit den Erzeugungsklassen
erzeugt werden.

## Flyweight

Das Fliegengewicht-Entwurfsmuster könnte gleich zwei Preise abräumen: einen für
den schönsten Namen für ein Entwurfsmuster und einen für den geringsten
Bekanntheitsgrad. Lassen Sie uns daran arbeiten …

### Beschreibung
Manchmal, nein – eigentlich recht häufig – gibt es in Anwendungen viele oder sogar
sehr viele Objekte, die zudem noch sehr fein granular sind. Wenn man nicht aufpasst,
entsteht daraus schnell ein objektorientiertes Kaffeekränzchen mit negativen Auswirkungen
auf die Performance und den Speicherverbrauch.
Denn auch, wenn heutige Laufzeitumgebungen sich große Mühe um die Effizienz
geben, hat doch jedes Objekt eine Mindestgröße, die durch das Layout im Speicher
bestimmt wird, und manche Werte müssen zudem noch im Speicher ausgerichtet
werden, was zusätzlichen Platz kostet.
Häufig ist dabei aber gar nicht die Abwesenheit dieses Musters schuld, sondern einfach
der Irrglaube, man müsse heutzutage alles in ein Objekt verpacken. Aber es ist
auch unbestreitbar, dass Objekte Vorteile bringen. Zur Illustration greife ich daher
ein wenig vor und stelle kurz das Praxisbeispiel vor, das wir später umsetzen werden.
Es geht dabei um eine Umweltmessstation, die ihre Werte in einer CSV-Datei auf
einem wechselbaren Datenträger bereitstellt. Sagen wir, sie ermittelt zwei verschiedene
Temperaturen, den Luftdruck und die Windgeschwindigkeit. Eine dem Produkt
beigelegte Desktopanwendung kann diese Dateien nun auslesen, umrechnen und in
eine Datenbank schreiben und hübsch in Grafiken verpacken.
Der einfache, naive Ansatz wäre nun, für jede einzulesende Zeile ein Objekt zu erzeugen
– abhängig vom konkreten Typ, also davon, um welche Art der Messung es sich
handelt.
Bei 10 Millionen Datensätzen werden so 10 Millionen Objekte benötigt, die alle
erstellt, verwaltet und irgendwann auch wieder bereinigt werden müssen. Dennoch:
Die Messwerte als Objekt zu haben brächte Vorteile, denn die einzelnen Sensoren
bringen natürlich Fähigkeiten mit, die wir gern in einem Objekt gekapselt hätten –
beispielsweise die Kalibrierung oder die Fähigkeit, die Objekte in verschiedenen Einheiten
speichern zu können.

Das Fliegengewicht-Muster bietet eine Alternative, indem es wiederverwendbare
Objekte bereitstellt, sodass diese zwar feingranular verwendet werden können, aber
eben ohne die Nachteile – schließlich gibt es nur wenige davon. Im Beispiel brauchen
wir also nur noch vier Objekte, nämlich ein Objekt für jeden Sensor. Diese Objekte
sind leichtgewichtig, daher der Name: Sie speichern nur noch das, was sie von den
anderen unterscheidet – also das, was den Sensor ausmacht – zum Beispiel den schon
erwähnten Korrekturwert für die Kalibrierung des Sensors. Man nennt das den intrinsischen
Zustand des Objekts.
Alle anderen Informationen, also beispielsweise das Datum, die Uhrzeit und der gemessene
Wert an sich, werden außerhalb gespeichert. Folgerichtig spricht man hier
vom extrinsischen Zustand. Und natürlich kann dieser Zustand in effizienten Datenstrukturen
gespeichert werden – in Listen, Arrays oder was auch immer die Aufgabenstellung
verlangt. Dieser extrinsische Zustand ist also der Kontext, in dem das
Fliegengewicht-Objekt eingesetzt wird. Das Objekt muss also jeweils diesen Kontext
von außen erhalten, bevor man im konkreten Fall damit arbeiten kann.

### Anwendungsfälle
Es eignet sich dann, wenn:
* viele – oder noch besser – sehr viele Objekte benötigt werden.
* eine einfache Datenstruktur, sagen wir eine Liste, nicht ausreichend ist, Sie also
nicht auf die Vorteile von Objekten verzichten möchten.
* der Speicherplatz knapp wird, einfach weil es zu viele Objekte sind oder weil die
einzelnen Objekte besonders groß werden können.
* die Objekte, die Sie benötigen, wirklich Fliegengewichte sind, die intrinsischen
Zustände also relativ klein sind, dafür die extrinsischen Zustände aber sehr
umfangreich.
* es auch wirklich möglich ist, die Aufgabe mit relativ wenigen Objektinstanzen zu
erledigen.

## Proxy

Neulich habe ich gelesen, Informatiker wären Menschen, die glauben, dass sich alle
Probleme dieser Welt mit einer zusätzlichen Indirektionsebene lösen lassen - ein
Zitat, das übrigens gleich mehreren Urhebern zugeschrieben wird. Und auch das
nächste Muster wirkt erst einmal unnötig umständlich, denn ein Proxy hat nur den
einen Zweck, dass ein Klient nicht direkt mit dem Zielobjekt kommuniziert, sondern
mit dem Proxy, der dann auf das Zielobjekt zugreift. Dafür kann es aber gute Gründe
geben, und Frameworks und das Betriebssystem nutzen dieses Muster weidlich.

### Beschreibung
Ein Proxy ist erst einmal eine ganz gewöhnliche Klasse. Es dient als Stellvertreter
für eine andere Klasse und muss folglich dieselbe Schnittstelle bedienen wie diese,
erbt also üblicherweise von deren Basisklasse bzw. implementiert dieselben
Schnittstellen.
Der Nachteil liegt auf der Hand: Anstatt eine Operation direkt auf einem Zielobjekt
auszuführen, wird sie auf dem Proxy ausgeführt, der sie anstelle des Klienten auf
dem Zielobjekt ausführt. Das verursacht zusätzlichen Code und damit zusätzlichen
Entwicklungs- und Testaufwand. Zudem erhöht es die Komplexität, und natürlich
wird auf diese Weise die Operation nicht gerade schneller ausgeführt.

Aber es hat auch einen Vorteil: Die Verlagerung des Proxyobjekts vor das Zielobjekt
verschafft eine zusätzliche Ebene der Kontrolle, denn die Proxyklasse ruft die Methoden
des Zielobjekts nicht einfach nur auf, sondern kann zusätzliche Prüfungen einbauen
oder Funktionen ausführen.


### Anwendungsfälle
Ein Proxy ist immer dann eine gute Wahl, wenn Sie den Zugriff auf ein Objekt kontrollieren
möchten oder müssen. Dafür gibt es zahlreiche Gründe:
#### Schutz-Proxy (Protection Proxy)
Ein Proxy fängt den Zugriff auf das echte Subjekt ab und kann infolgedessen auch
den Zugriff darauf kontrollieren. Ein Proxy kann eine bequeme Möglichkeit zur
Sicherung des Zugriffs darstellen, wenn sich diese Anforderung im abzusichernden
Objekt selbst nicht umsetzen lässt. Das ist der Fall, wenn der Quellcode dafür nicht
vorliegt oder wenn kein Änderungszugriff besteht, weil das Objekt in einem anderen
Prozess oder auf einem anderen Server lebt und der Betrieb ausgelagert ist.
Wir dürfen den Begriff Objekt nicht allzu wörtlich nehmen. Denn auch wenn dieses
Muster – wie alle GoF-Muster – ein Muster der objektorientierten Softwareentwicklung
ist, ist das Konzept des Proxy doch universeller anwendbar. Und so kommt es
durchaus vor, dass ein bestimmter Webservice von einem anderen Unternehmen
betrieben wird und sich daher nicht direkt ändern lässt. Ein Proxy-Webservice kann
dann dieselbe Schnittstelle implementieren, also dieselben Methoden mit denselben
Signaturen anbieten, mit dem einen Unterschied, dass er vor der Weiterleitung der
Anfragen an den »echten Webservice« eine Überprüfung der Identität und der
Zugriffsrechte vornimmt.
#### Remote-Proxy (Remote Proxy)
Im vorigen Beispiel wurden schon Webservices angesprochen. Der schlagende
Grund dafür, warum ein Stellvertreterobjekt benötigt wird, ist dort der, dass einfach
kein Zugriff auf ein Objekt möglich ist, weil dieses Objekt in einem völlig anderen
Adressraum, in einem anderen Prozess oder gar auf einer anderen Maschine ausgeführt
wird.
Ein Remote-Proxy übernimmt dann beispielsweise das Verpacken der Anforderung
in ein Serialisierungsformat und kümmert sich – zusammen mit Frameworks und
dem Betriebssystem – auch um die Kommunikation über das Netzwerk. Auf der
anderen Seite des digitalen Teichs kann auch wieder ein Proxy warten, und das
Objekt, für das der eigentliche Aufruf gedacht war, erledigt seine Arbeit, und eine
etwaige Rückmeldung an den Klient erfolgt auf demselben Kanal.
Remote-Proxys gibt es, seit es Netzwerkkommunikation gibt. Beispiele dafür sind
DCOM oder WCF in der Microsoft- bzw. .NET-Welt. In WCF, einem Framework für die
Entwicklung serviceorientierter Architekturen (SOA) werden diese Remote-Proxys
sogar automatisch generiert, und zwar aus den Metadaten, die ein veröffentlichter
Webservice öffentlich zur Verfügung stellt.

Remote-Proxys sind nicht zwingend notwendig. Natürlich könnte eine Anwendung
auch eine SOAP-Nachricht (das Austauschformat für Webservices) von Hand erstellen,
eine TCP/IP-Socketverbindung aufbauen, die Netzwerkkommunikation durchführen,
sich um Serialisierung, Transaktionen, Sicherheit und all die anderen Dinge
kümmern – ganz ohne Proxy. Ein Proxy macht die Sache aber um Längen komfortabler.

#### Virtueller Proxy (Virtual Proxy)
Es gibt Fälle, in denen ist die Erzeugung eines Objekts teuer oder sogar richtig teuer.
Ein virtueller Proxy stellt dann einem Klient zwar den vollständigen Leistungsumfang
des Objekts zur Verfügung (er implementiert ja dieselbe Schnittstelle), behält
aber die Kontrolle, wann welche Teile des Objekts tatsächlich erzeugt werden. Das
könnte beim ersten Aufruf der Fall sein oder vielleicht zeitverzögert.

Virtuelle Proxys eignen sich häufig dann, wenn seltene oder langsame Ressourcen
im Spiel sind – Datenbankverbindungen zum Beispiel oder zentral genutzte Hardwareressourcen.
Auch der Zugriff auf einen Mainframe oder auf einen entfernten
Standort über eine schmalbrüstige Leitung kann davon profitieren.

#### Dynamischer Proxy (Dynamic Proxy)
Einfach gesagt, ist ein dynamischer Proxy ein Proxy, der den echten Proxy zur Laufzeit
erzeugt. Ein Nachteil des Proxy-Musters ist es, dass Proxys die gesamte Schnittstelle
des Subjekts implementieren müssen, auch wenn der Proxy nur für wenige
Operationen benötigt wird. Das automatische Generieren von Proxys zur Laufzeit
kann dann eine Alternative sein.
Das schon erwähnte WCF ermöglicht die statische Generierung von Proxys aus den
Metadaten eines Service. Das ist zwar keine große Arbeit für den Entwickler, aber die
Proxys können groß werden, und die Generierung kann bei umfangreichen Contracts
lange dauern.

Ein weiteres Beispiel für den Einsatz von dynamischen Proxys sind Interceptor-
Objekte, also Objekte, die eine Operation abfangen und entweder davor oder danach
weitere Funktionalitäten ausführen, die auch dynamisch – also zur Laufzeit, dem
Proxy hinzugefügt werden können. WCF ist auch hier wieder ein gutes Beispiel. Dort
ist ein Zugriff auf den Ausführungsstack an beinahe jeder Stelle möglich. Aber auch
in der Java-Welt ist dies gang und gäbe: JBoss ist so ein Applikationsserver, der einen
umfangreichen Interceptor-Mechanismus bietet.


### Weitere Anwendungsfälle
Die weiteren Anwendungsfälle ergeben sich aus der Frage, was ein Proxy neben der
Weiterleitung an das »echte Subjekt« natürlich noch zusätzlich Sinnvolles erledigen
kann. Die folgende Liste ist eine kleine Aufzählung praktischer Aufgaben, die ein
Proxy übernehmen kann:
* Das Zwischenspeichern von Informationen: Beispielsweise könnte ein Proxy sich
den Rückgabewert der Methode gibUmsatzProArtikel merken, sodass weitere
Anfragen keine (teure) Datenbankabfrage mehr auslösen, sondern aus dem lokalen
Cache des Proxys beantwortet werden können.
* Proxys können den Zugriff auf das Objekt dahinter reglementieren, zum Beispiel
indem sie die Anzahl der Zugriffe zählen.
* Ein Sonderfall davon ist das Zählen der gerade benötigten Referenzen, sodass das
echte Subjekt freigegeben werden kann, wenn kein Zugriff mehr darauf besteht.
* Auch das Serialisieren ist möglich, indem ein Proxy einen Synchronisierungsmechanismus
implementiert und so einen seriellen Zugriff auf das echte Objekt
erzwingt.
* Denkbar ist auch, dass ein Proxy nicht immer auf dasselbe echte Subjekt zugreift,
sondern dass dieses zur Laufzeit austauschbar ist, was die Wartung vereinfacht.
Der Proxy kann dann Zugriff von Klienten bis zum erfolgten Austausch abfedern,
bevor ein Fehler auftritt.
* Proxys können auch veritabel sogenannte »Aspekte« abbilden, also Funktionalitäten,
die eigentlich nichts mit der Geschäftslogik des echten Subjekts zu tun haben
(wie Logging oder die Bereitstellung von Daten zum Monitoring einer Anwendung)
und die Sie eben deshalb nicht in das echte Subjekt einbauen wollen.
* Ein Proxy kann, wenn auch in Grenzen, Datenformate konvertieren oder auch
Funktionalitäten verändern oder hinzufügen, zum Beispiel indem ein Standardwert
verwendet wird, wenn ein Klient für eine Operation einen Übergabeparameter
leer lässt. Ein Beispiel für die Konvertierung wäre die Umsetzung von ANSI
nach Unicode bei String-Parametern.

### Nachteile
Zugegeben, das sind recht viele Vorteile. Der vielleicht größte Vorteil ist der, dass die
Proxyklasse über dieselbe Schnittstelle verfügt wie die Klasse dahinter, das echte
Subjekt. Man kann Proxy und echtes Subjekt also einfach gegeneinander austauschen.
Das aber spricht nicht für den schnellen Einsatz, sondern dagegen, denn denken Sie
an YAGNI (You ain’t gonna need it) – Sie können den Proxy später, wenn Sie sicher
wissen, dass Sie ihn brauchen, ja relativ unkompliziert zwischen Klient und Objekt
schieben.

Die Nachteile des Proxy-Musters sind:
* natürlich der zusätzliche Aufwand und die zusätzliche Komplexität
* die Notwendigkeit, die Subjekt-Schnittstelle vollständig im Proxy implementieren
und auch im Laufe der Zeit pflegen zu müssen, wenn auch dynamische Proxys
(s.o.) eine Alternative sein können
* eventuelle Geschwindigkeitseinbußen aufgrund des zusätzlichen Aufrufs


### Alternativen
Der Dekorierer ist dem Proxy ähnlich. Mit ihm kann man aber zusätzliche Funktionalitäten
bereitstellen, das Objekt also um Zuständigkeiten erweitern.
Der Adapter funktioniert ebenfalls ähnlich, erlaubt es aber, die Schnittstelle zu verändern,
während ein Proxy immer die vollständige Schnittstelle zur Verfügung stellt.



# Verhaltensmuster

Dieses Kapitel ist das umfangreichste dieses Buches. Kein Wunder, geht es doch hier
um die Beziehung, die Interaktion und das Verhalten von Objekten untereinander
und darum, welches Objekt welche Zuständigkeiten abbildet.

## Chain of Responsibility
Für gewöhnlich gibt es in der objektorientierten Kommunikation immer einen Sender
(die sendende Klasse) und einen Empfänger (die empfangende Klasse), die eine
Operation durchführen. Nicht so bei der Zuständigkeitskette. Dort richtet ein Objekt
eine Anfrage an eine Kette von Objekten, und die Anfrage wird entlang dieser Kette
weitergeleitet, bis ein Objekt die Anfrage beantwortet.

### Beschreibung
Die Zuständigkeitskette entkoppelt das Objekt, das eine Anfrage startet, von dem
Objekt, das die Anfrage bearbeitet. Ja, das anfragende Objekt kennt noch nicht einmal
das Objekt, das schlussendlich zum Zuge kommt.
Vom Standpunkt des Algorithmus betrachtet, handelt es sich bei der Struktur um
eine einfache verkettete Liste, die nur in eine Richtung durchlaufen wird. 
Diese Art der Entkopplung geht schon sehr weit und setzt voraus, dass das anfragende
Objekt weder wissen kann (noch wissen muss), wer die Anfrage beantwortet.
Es kann aber auch sein, dass kein Objekt entlang der Kette die Anfrage entgegennimmt,
was im Programmfluss entsprechend berücksichtigt werden muss. Manchmal
bezeichnet man das bearbeitende Objekt auch als impliziten Empfänger, weil es
eben vorher nicht bekannt ist. Im Gegensatz dazu gibt es den expliziten Empfänger,
wenn das bearbeitende Objekt bereits bekannt ist.

### Alternativen
Obwohl dieses Muster nützlich ist, ist der Feind dieses Musters die »nächstbeste
Alternative«. So wurde es in Java 1.0 AWT eingesetzt, ab Version 1.1 aber durch das
Beobachter-Muster ersetzt.

Im Falle von AWT – und in vielen weiteren Beispielen, wenn GUIs im Spiel sind – definiert
die Vererbungshierarchie den jeweils nächsten Aufruf, von unten nach oben.
Das Prinzip ist aber dasselbe.
Man kann sich leicht vorstellen, dass bei der Vielzahl der erzeugten Events eine saubere
Programmierung vonnöten ist, will man nicht, dass alle Events immer bis zur
Wurzelklasse durchgereicht werden – das war häufig genug nicht der Fall, was eben
Auswirkungen auf die gefühlte Geschwindigkeit der Anwendung hatte.
Das Problem bei verketteten Listen ist, dass die Geschwindigkeit sehr von der Anzahl
ihrer Elemente abhängt (linear, wenn jedes Element in der Kette dieselbe Ausführungszeit
in Anspruch nimmt), sowie von der durchschnittlichen Position des Suchergebnisses.
Daher werden verkettete Listen (und damit auch dieses Muster) in der
Praxis eher seltener eingesetzt und vor allem dann, wenn die Listen kurz sind.
In unserem Praxisbeispiel wäre vermutlich eine Art Registratur sinnvoller, in der
(z. B. in einem assoziativen Speicher wie einer HashMap) alle Kommandos mit ihren
Objektreferenzen hinterlegt sind.

## Command
Kommen wir nun zu einem Klassiker und zu einem der nützlichsten Muster aus der
Kategorie Verhaltensmuster – dem Befehlsmuster (Command).

### Beschreibung
Das Befehlsmuster löst ein wichtiges Problem in der Entwicklung von Software: Wie
kann man einen Befehl über eine Leitung transportieren, auf einer Festplatte speichern
oder in einer Liste ablegen? Die Antwort des Befehlsmusters: Man verpackt ihn
in ein Objekt. Genauer gesagt verpackt man den Befehl selbst und etwaige Parameter,
die der Befehl erhalten soll.
Wie bei der Zuständigkeitskette muss der Sender (derjenige, der das Befehlsobjekt
also erzeugt und versendet) nicht notwendigerweise wissen, welcher Empfänger den
Befehl ausführt. Er braucht nicht einmal zu wissen, ob und wann dies geschieht.
Befehle in Objekten zu speichern hat viele Vorteile. Die vielleicht wichtigsten Vorteile
sind:
* Man kann auf diese Weise Befehle in Listen einreihen und damit sowohl in der Reihenfolge
der Ausführung verändern als auch in der Ausführungszeit.
* Objekte lassen sich serialisieren und später wieder deserialisieren. Das ist nötig,
wenn ein Befehlsobjekt über eine Netzwerkleitung transportiert werden oder auf
einem Datenträger gespeichert werden soll.
* Befehle lassen sich auf diese Weise komfortabel parametrisieren, einfach indem
eine Befehlsklasse weitere Attribute erhält, die den Parametern entsprechen.
* Außerdem kann man Befehlsobjekte klonen, Befehle damit also auf einfache
Weise duplizieren.
* Wie andere Objekte auch, können Befehlsobjekte innerhalb einer Anwendung in
gewöhnlichen Objektreferenzen gespeichert werden.
* Die Objekte können auch herumgereicht werden, zum Beispiel entlang einer Pipeline,
was der Zuständigkeitskette nicht ganz unähnlich ist.


Wie andere Muster auch dient das Befehlsmuster im Wesentlichen dazu, den Sender
(d. h. denjenigen, der einen Befehl auslöst) vom Empfänger (also demjenigen, der
einen Befehl ausführt) zu entkoppeln. Das reduziert Abhängigkeiten, weil sich zur
Kompilierzeit Sender und Empfänger nicht kennen müssen. Das wiederum eröffnet
ein weites Feld für Plug-ins und andere dynamische Konstrukte, bei denen gerade
diese Abhängigkeit nicht erwünscht ist.
Das sind gewichtige Gründe für dieses Muster, und entsprechend häufig werden Sie
diesem Muster in der Praxis begegnen.

### Anwendungsfälle
Ein Praxisbeispiel habe ich gerade erwähnt. Aber das heißt jetzt natürlich nicht, dass
gewöhnliche Methodenaufrufe zwischen zwei Objekten verboten wären und stattdessen
jeder Aufruf in ein Objekt verpackt werden müsste. In den folgenden Anwendungsfällen
sollten Sie jedoch darüber nachdenken.

#### Zeitliche Entkopplung von Aufruf und Ausführung
Wenn die Aktion nicht sofort ausgeführt werden soll, sondern später, dann ist die
Aufrufer-Klasse genau der Ort, an dem dies im Befehlsmuster möglich wird.
Beispiel: In einer Fabrik sollen in der Früh alle Lichter angehen. Dafür gibt es einen
Befehl (»Lampe an«), der für jede Lampe vom Aktor (Empfänger) in die Tat umgesetzt
wird – irgendwo klickt dann ein Relais, der Stromkreis ist geschlossen, und die Lampe
leuchtet.
Allerdings nicht lange, denn das parallele Anfahren von vielleicht 5000 Leuchtstofflampen
wird nicht gutgehen. Andererseits: Man kann die Lampen auch nicht einfach
alle nacheinander anschalten, das würde viel zu lange dauern.
Der Aufrufer (in diesem Fall der Steuerungscontroller) würde nun die »Lampe an«-
Befehle entgegennehmen und könnte selbstständig entscheiden, wie viele Lampen
er jeweils in einem Rutsch anschalten möchte. Er sammelt einfach beispielsweise
zehn Lampe-an-Befehle und führt sie dann erst aus. Nur der Vollständigkeit halber:
Der Klient wäre in unserem Fall der Sensor, also die Taste mit der Beschriftung »Alle
Lampen anschalten«.
Das wäre nun ein Fall, in dem ein Steuerungscontroller die Ausführung zeitlich
genau steuert. Weitere Möglichkeiten sind:
* Die Kommandos werden in eine Warteschlange eingereiht, von wo aus sie der
Reihe nach ausgeführt werden – zum Beispiel um Lastspitzen abzufedern, was
einer asynchronen Ausführung entspricht.
* Die Kommandos werden zwischengespeichert, weil eine entfernte Ressource
gerade nicht erreichbar ist, zum Beispiel weil das Internet im Moment nicht zur
Verfügung steht. Auch dafür kann eine Warteschlange eingesetzt werden, wenn
Warteschlange und Verarbeitung fehlertolerant angelegt wurden.

#### Räumliche Entkopplung von Aufruf und Ausführung
Was zeitlich geht, geht auch räumlich: von Befehlen, die über ein Netzwerk (zum Beispiel
das Internet) übertragen werden, bis hin zu Befehlen zur Lagekorrektur an einen
Marsrover. Wenn ein Befehl also den aktuellen Prozess verlässt, lässt er sich mit dem
Befehlsmuster bequem in ein serialisierbares Format, zum Beispiel XML, übersetzen
und auf diese Weise übertragen.
Ein weiterer Einsatz ist das Speichern von Befehlen, zum Beispiel um
* ihren Einsatz zu protokollieren. Beispiel: Die Kauf- und Verkaufsorders von Aktien
sollen in einer SQL-Datenbank mit allen Parametern gespeichert werden.
* sie später wieder zurücknehmen zu können, auch nach einem Neustart der
Anwendung. Ein Beispiel wäre die Implementierung eines Undo, der auch nach
einem Programmabbruch noch funktioniert.
* später wieder an einer abgebrochenen Stelle aufsetzen zu können. Ein Beispiel ist
das Nachfahren von Transaktionen.

#### Undo/Redo
Die Aufrufer-Klasse hat noch einen weiteren Nutzen: Sie kann einen Undo/Redo-
Mechanismus implementieren, was im obigen Beispiel mit der Textverarbeitung
natürlich gang und gäbe wäre. Ohne einen solchen Mechanismus würde der Aufrufer
nach erfolgreichem Ausführen des Befehls den Befehl einfach löschen. Mit dem
Mechanismus würde er alle Befehle (vielleicht nur bis zu einer gewissen Tiefe) in
einem Undo-Puffer speichern.
Natürlich müssen die Befehle selbst dann nicht nur eine fuehreAus-Methode implementieren,
sondern zusätzlich auch noch eine undo-Methode.

#### Umfangreiche Parametrisierungen
Natürlich können wir einer Methode auch eine stattliche Anzahl an Parametern
übergeben. Komfortabler (und erweiterbar) aber ist es, wenn wir für komplexere
Parametrisierungen ein Objekt verwenden. Dann ist der nächste Schritt, nicht nur
die Parametrisierung, sondern den ganzen Befehl in ein Objekt zu packen, nicht
mehr weit.
Einige Unittest-Frameworks machen aus Testfällen Klassen, wobei viele Testfälle wiederum
nichts anderes tun, als Methoden in festgelegten Parametern aufzurufen und
ein vordefiniertes Ergebnis zu überprüfen.


#### Callbacks
Die meisten OO-Sprachen bieten Callbacks, also Funktionen, die von einem anderen
Objekt zu einem späteren Zeitpunkt aufgerufen werden. Das kann (muss aber nicht
unbedingt) auf die Eignung dieses Musters für die konkrete Aufgabenstellung hindeuten.

### Weitere Überlegungen und Alternativen
Zunächst einige Worte zu Undo und Redo.

#### Undo/Redo
Das Befehlsmuster kümmert sich selbst nicht um ein Undo/Redo, es eignet sich aber
gut dafür, weil es
* über den Aufrufer das Ausführen steuert und damit auch das Gegenteil tun kann:
das Zurücknehmen von Befehlen.
* eine Befehlsklasse gibt, die Undo ebenfalls unterstützen kann, obwohl die Empfängerklasse
dies selbst nicht tun muss.


Beides ist im Praxisbeispiel so umgesetzt. Die Befehlsklasse beinhaltet zu diesem
Zweck den Zustand vor der Änderung, und der Aufrufer speichert alle ausgeführten
Befehle seit dem letzten Undo, die Undo-Historie. Vorgeschlagen wurde das schon
1977, und die spätere Umsetzung hat die Tastenkürzel (Strg)+(Z) für Undo (und noch
später (Strg)+(R) für Redo) ins kollektive IT-Gedächtnis befördert.
Wenn Sie selbst einen Undo-Mechanismus umsetzen wollen, empfehle ich Ihnen,
Folgendes zu berücksichtigen:
* Undo kostet natürlich Speicher, vielleicht zu viel Speicher, vor allem aber ist es
häufig aus Performancegründen nicht möglich, viele Schritte auf einmal zurückzunehmen.
Begrenzen Sie in einem solchen Fall die Undo-Historie, indem Sie die
Undo-Befehle zum Beispiel in einem Ringpuffer speichern.
* Es mag Befehle geben, die kann man nicht zurücknehmen – das Versenden einer
E-Mail zum Beispiel. Sie könnten für Befehle, die man zurücknehmen kann, eine
Schnittstelle implementieren. Der Aufrufer kann den Befehl daraufhin untersuchen
und ihn erst gar nicht in die Undo-Historie einreihen.
* Andere Befehle wiederum könnten zur Leerung der Undo-Historie führen, z. B. das
Speichern einer Datei auf der Festplatte. Auch für diese Aufgabe eignet sich eine
Schnittstelle oder ein Zweig in der Vererbungshierarchie der Befehle.
* Undo ist potenziell eine brandgefährliche Operation, und eine Anwendung sollte
sich nach einem Undo in demselben Zustand befinden wie vorher. Ist das nicht
möglich, weil das Undo selbst Fehler verursacht (erst recht, wenn der Anwender
Undo-Ping-Pong spielt, also ständig auf Undo und dann wieder auf Redo klickt),
dann sollten Sie die Reißleine ziehen. Sie könnten die Anwendung beenden oder
wenigstens die Historie löschen oder vielleicht von Zeit zu Zeit einen Snapshot des
inneren Zustands Ihrer Anwendung speichern, zu dem Sie jederzeit zurückkehren
können – das Memento-Muster bietet dafür eine gewisse Unterstützung.
* Nicht immer verlangen die Anforderungen, dass mehrere Stufen zurückgegangen
werden kann. Vielleicht genügt es im konkreten Fall, die letzte Operation zurückzunehmen
– dann wird vieles einfacher.
* Unter Umständen ist es einfacher, wenn statt des Originalbefehls eine Kopie in die
Undo-Historie eingefügt wird und diese Kopie »umgekehrte« Parameter beinhaltet. Im Beispiel gibt es eine Undo-Methode, die die neue und alte Position vertauscht.
Dieses Vertauschen hätte auch schon vorher stattfinden können, und der
Befehl mit den vertauschten Positionen könnte stattdessen in die Undo-Historie
gelangen.
* Auch das Einfügen des »gegenteiligen« Befehls kann sinnvoll sein. Beim Einfügen
eines Texts könnte statt des Einfügen-Befehls ein Löschen-Befehl in der Undo-Historie
gespeichert werden, der den eingefügten Text als zu löschenden Text als
Zustand erhält.
* Nicht immer weisen der Befehl und der Undo-Befehl dieselbe Granularität auf.
Manchmal ist es einfacher und bequemer, den Undo »gröber« zu gestalten. In
einer Textverarbeitung möchte man beispielsweise nur selten Zeichen für Zeichen
rückgängig machen, sondern stattdessen lieber ganze Wörter oder Textblöcke entfernen.
* Auch an die Transaktionen sollte man denken, zum Beispiel wenn der ursprüngliche
Befehl schon in einer Transaktion lief. Es kann auch von Vorteil sein, wenn die
gesamte Undo/Redo-Kette in einer einzigen Transaktion abläuft, vor allem, wenn
Datenbanken und andere persistente transaktionale Ressourcen im Spiel sind.


Es ist eine prima Sache, ein Undo/Redo zu verstehen und selbst implementieren zu
können, aber natürlich gibt es auch einige Undo/Redo-Frameworks, und viele von
ihnen lassen sich kostenfrei nutzen.
In unserem Schachspiel hat das Undo übrigens eine Schwachstelle: Wir können zwar
Figuren bewegen und diese Bewegung wieder zurücknehmen (auch über viele
Schritte), aber was ist, wenn durch die Bewegung eine Figur geschlagen wird, also aus
dem Spiel verschwindet? Dann ist das Zurücknehmen nicht mehr so einfach. Wie so
etwas dennoch gelingt, zeige ich Ihnen im Abschnitt zum Memento-Muster (siehe
Abschnitt 4.7.4), in dem dieses Praxisbeispiel weitergeführt wird.


#### Befehle entlang von Verarbeitungspipelines
Die Zuständigkeitskette nimmt eine Anfrage entgegen und leitet sie so lange weiter,
bis ein Objekt in der Kette sich darum kümmert oder die Kette zu Ende ist. Das lässt
sich mit dem Befehlsmuster natürlich gut kombinieren, wenn in der Anfrage das
Befehlsobjekt übergeben wird.
Außerdem muss ein Befehl nicht statisch sein. Er kann von anderen Objekten im
Laufe der Verarbeitung verändert werden. Ein Beispiel wäre ein Befehl zum Einkauf
einer Ware, der von einer Anwendung im Laufe der Verarbeitung mit einem Zertifikat
versehen und damit offiziell genehmigt wird.

#### Makroobjekte
Da Befehlsobjekte ganz gewöhnliche Objekte sind, kann man für sie sowohl die
Erzeugungs- als auch einige Strukturmuster gut anwenden. Für zusammengesetzte
Befehle eignet sich z. B. das Kompositionsmuster ganz gut, das größere Objekte aus
kleineren Objekten zusammenbaut.

#### Autonome Befehlsobjekte
Das klassische Muster sieht vor, dass ein Befehl letztendlich vom Empfänger ausgeführt
wird. Gibt es keinen solchen Empfänger, müsste man also einen Empfänger
künstlich entwerfen. Dann ist es auch möglich, dass ein Befehl sich selbst ausführt.
Ein Speichern-Befehl könnte so ein Befehl sein, der keinen natürlichen Empfänger
besitzt, oder der Befehl zum Beenden der Anwendung.
Überhaupt kann es ein Problem sein, den richtigen Empfänger zu finden. Im Beispiel
haben wir dem Befehl bereits eine Objektreferenz übergeben. Das war leicht, haben
wir die Spielfigur doch im Eventhandler des Schachspiels übergeben bekommen.
Andere Fälle sind da nicht so einfach, zum Beispiel beim Befehl zum Kursivsetzen
eines Texts (dort muss man den Text vielleicht erst mal finden, wenn in der Zwischenzeit
neuer Text eingefügt wurde).
Wie dem auch sei, ich empfehle Ihnen, die Extreme zu meiden, also weder Empfängerobjekte
als »Dummy-Objekte« zu erzeugen noch die Befehlsobjekte zu umfangreichen
Steuerungszentralen auszubauen.

### Ähnliche und ergänzende Muster
Das Memento-Muster kann ein Undo vereinfachen, indem es Zustandsinformationen
für die spätere Verwendung speichern kann. Das kann auch ein »Snapshot« sein,
also der vollständige Zustand eines Objekts oder eines Systems zu einem gewissen
Zeitpunkt. 
Das Kompositum hilft dabei, Befehle zu größeren Makrobefehlen zusammenzusetzen,
zum Beispiel um ein gröberes Undo zu realisieren.

## Interceptor

Das Interceptor-Muster ist der Abfangjäger unter den Entwurfsmustern und ein
Muster, das im ursprünglichen Katalog der GoF nicht enthalten ist.

### Beschreibung
Der Interceptor ist ein Kind moderner Middleware-Systeme und Frameworks. Solche
Systeme können unmöglich alle Funktionalität mitbringen. Sie benötigen einen
Mechanismus, um zur Laufzeit neue Funktionen in den Ablauf zu integrieren – eben
das Interceptor-Muster.

Ein Empfänger –
sagen wir, eine Komponente, die an einem TCP/IP-Port auf Nachrichten von außen
wartet – übermittelt die empfangene Nachricht an die Verarbeitungskomponente,
die dann einen Service ausführt oder sonst etwas mit der Nachricht macht.
Was geschieht aber nun, wenn die Nachricht über den Transportkanal verschlüsselt
wurde? Man könnte diese Funktionalität natürlich in die Komponente Verarbeiten
einbauen. Mit der Zeit würde diese Komponente aber schnell überfrachtet werden
und wäre praktisch nicht mehr zu warten: Schnell würden verschiedene Verschlüsselungen
implementiert werden, und neue Komponenten (wie Autorisierung, Authentifizierung,
Logging, Tracing oder Filtermodule) würden die Komponente aufblähen.
Eleganter ist da schon der Ansatz, solche Funktionen bei Bedarf in die Verarbeitungskette
einzuschleusen. Das kann statisch geschehen, einfach indem die Kette so konfiguriert wird, oder auch dynamisch – im Beispiel könnte die Nachricht einfach eine
Kennzeichnung besitzen, ob sie verschlüsselten Inhalt trägt oder einen Inhalt im
Klartext.
Interceptor-Module lassen sich aufgrund ihrer eher losen Kopplung auch dynamisch,
also zur Laufzeit, hinzuladen, was einen Markt für Plug-ins schafft. Dass es
sich um ein Verhaltensmuster handelt, ist bei diesem Muster augenscheinlich, wird
doch diesmal das Verhalten einer Anwendung selbst durch den Interceptor geändert.
Für die Entwickler solcher Interceptor-Module beschränkt sich die Welt auf deren
Schnittstellen. Sie müssen den Rest der Verarbeitungskette nicht kennen und diese
darf für die Ausführung des Interceptors auch keine Rolle spielen. Natürlich machen
nicht alle Kombinationen Sinn – eine bereits entschlüsselte Nachricht kann nicht
von einem zweiten Interceptor erneut entschlüsselt werden –, aber das zu verhindern
ist eine Aufgabe, die dem Administrator oder dem Architekten der Anwendung
zufällt bzw. demjenigen, der die Interceptor-Module konfiguriert.
Ein wenig geht das auch in die Richtung aspektorientierter Programmierung oder
wenigstens in Richtung solcher Systeme, die sich um Aspekte erweitern lassen. Als
Aspekt bezeichnet man in der Softwareentwicklung eine generische Funktion, die
von vielen Klassen verwendet werden kann. Typischerweise behandeln solche
Aspekte also »Querschnittsfunktionen« oder, wie man auch sagt, Cross-Cutting-
Concerns. Die Steuerung von Transaktionen, das Logging, Tracing und Monitoring
sowie die Instrumentierung sind typische Aspekte, die häufig von Interceptor-Modulen
umgesetzt werden.

### Anwendungsfälle
Das Interceptor-Muster kann immer dann gute Dienste leisten, wenn eine Anwendung
– möglichst zur Laufzeit – um neue Funktionen erweitert werden soll und wenn
es ein Framework gibt, das diese Erweiterung unterstützt.
Für den Hersteller des Frameworks hat dies den großen Vorteil, dass es Dritthersteller
(oder eine aktive Community) gibt, die fehlende Funktionen nachrüsten oder bessere
Lösungen für vorhandene Interceptor-Module bereitstellen. Das spart Kapazität
und damit Geld und erhöht die Akzeptanz mit jedem neuen Interceptor. Außerdem
kann auf diese Weise (leider, möchte man sagen) ein Framework zu einem sehr frühen
Zeitpunkt auf den Markt geworfen werden, zu dem eigentlich noch wichtige
Funktionen fehlen. Ein Beispiel dafür ist das ASP.NET Web API, das in der ersten Version
mehr Lücken als Inhalte hatte.
Für den Kunden bedeutet es, dass er jemanden beauftragen kann, einen Interceptor
zu schreiben, und dass er nicht völlig vom Hersteller des Frameworks abhängig ist.
Entwickler wiederum können mit der Entwicklung Geld verdienen oder einfach
Gutes tun – oder natürlich beides.
Diese Beschreibung ist natürlich immer noch zu allgemein. Ich muss sie daher einschränken:
Das Interceptor-Muster eignet sich weniger, wenn sich die zu implementierenden
Funktionen nicht in eine Verarbeitungskette einreihen lassen, wenn es um
GUI-Funktionen geht oder wenn ein Interceptor eine sehr tiefe Integration ins
Framework benötigen würde – dann ist das Framework selbst der Ort, an dem die
Funktion untergebracht werden sollte. Und ein Interceptor muss eigenständig arbeiten
können und nur von den definierten Schnittstellen abhängig sein. Wir haben
schon gesehen: Unter Umständen hat ein Interceptor zwar Zugriff auf Teile des
Frameworks, aber die Interceptor-Module müssen dennoch der Reihe nach ausgeführt
werden können und unabhängig voneinander sein.
Einige Beispiele für Interceptor-Module, wie man sie in der Praxis häufiger antrifft:
* Authentifizierung und Autorisierung: Hier könnten verschiedene Arten der
Authentifizierung wahlweise angeboten werden (z. B. über Active Directory, Facebook,
Google oder Microsoft Live).
* Caching: Über Caching-Provider lässt sich die Verarbeitungskette unter definierten
Umständen verkürzen.
* Logging, Tracing, Monitoring, Instrumentierung: Das eröffnet das weite Feld von
»DevOps«, also der Verbindung zwischen Entwickler und Administratoren. Ein
Logging-Interceptor könnte z. B. ETW (Event Tracing for Windows) nutzen, um
über wichtige Events im Eventlog von Windows zu berichten.
* Exception Handling: Definiert den Umgang mit auftretenden Fehlern, häufig
auch in Verbindung mit Logging.
* Validierung: Hier werden meist zusätzliche, kundenspezifische Validierungen in
die Verarbeitungskette eingebracht.
* Verschlüsselung: Ver- und Entschlüsselung der gesamten Nachricht oder von
Teilen.
* Encoding, Transcoding etc.: Das Verändern des Originalinhalts in Bezug auf Sprache,
Formate, Zeichensätze usw.
Beispiele für das Interceptor-Muster sind die Reader- und Writer-Interceptors in
JBoss oder auch die Verarbeitungspipeline des IIS im Allgemeinen und von ASP.NET
im Speziellen. Aber Achtung: Der Begriff Interceptor ist nicht geschützt, und nicht
immer sind solche Verarbeitungsketten auch nach dem hier beschriebenen Muster
implementiert worden.

### Weitere Überlegungen und Alternativen
Bei diesem Muster gilt: Die Implementierung des »reinen« Musters ist nicht allzu
schwer, das Muster in realen Anwendungen zuverlässig zum Laufen zu bringen ist
jedoch ungleich komplizierter. In den folgenden Abschnitten behandle ich einige
Dinge, die Sie dabei beachten sollten.

#### Sicherheit
Interceptors sind weder dem Dispatcher noch dem Framework bekannt. Sie können
ja erst zur Laufzeit dynamisch geladen werden. Das birgt potenzielle Sicherheitsrisiken,
denen Dispatcher und Framework begegnen müssen, vor allem dann, wenn das
Framework eine weitgehende Beeinflussung bzw. Steuerung durch die Interceptors
erlaubt.
Häufig werden dann Trust Boundaries eingeführt, oder der Interceptor wird in einer
Sandbox ausgeführt, also innerhalb einer Barriere, die ein Interceptor nicht verlassen
kann.
Eine andere Alternative ist es, Interceptors mithilfe von Zertifikaten zur Ausführung
überhaupt erst zuzulassen. Ebenfalls anzutreffen ist die Variante, in der nur zertifizierte
Interceptors vollen Zugriff auf das Laufzeitsystem haben, während nicht zertifizierten
Interceptors gewisse Restriktionen auferlegt werden.

#### Stabilität, Robustheit
Aus den gerade erwähnten Gründen kann man auch nicht davon ausgehen, dass ein
Interceptor sich immer korrekt verhält. Das betrifft im Wesentlichen zwei Aspekte:
* Ein Interceptor könnte instabil sein, also z. B. Eingaben unzureichend validieren
und auf diese Weise häufig Exceptions auslösen.
* Oder ein Interceptor ist schlicht zu leistungsschwach, die Ausführung der Handler
also zu langsam.


Oder vielleicht kommt auch beides vor. Im ersten Fall ist Exception Handling das
Mittel der Wahl. Dem zweiten Problem begegnet man am besten mit asynchroner
Ausführung der Interceptor-Methoden und ggf. mit der Einführung eines Timeouts,
nach dessen Ablauf ein Eintrag geloggt und die Ausführung des Interceptors unterbrochen
wird.
Die Schwierigkeit für den Hersteller einer Anwendung besteht dabei in der Unvorhersehbarkeit
des Laufzeitverhaltens, weiß man doch nicht, welche Interceptors in
der konkreten Installation verwendet werden und was der Kunde als Nächstes installiert.


### Abhängigkeiten, Ausführungsreihenfolge
Das Muster funktioniert am besten, wenn die einzelnen Interceptors voneinander
unabhängig sind. In der Praxis ist das nicht in jedem Fall zu erreichen. Manchmal
macht ein Interceptor eine Anfrage unbrauchbar für einen weiteren Interceptor oder
Abhängigkeiten bedingen die Reihenfolge der Ausführung. Beispielsweise kann der
Inhalt einer Nachricht erst dann validiert werden, wenn sie zuvor entschlüsselt
wurde.
Eine passable Möglichkeit, dies zu verhindern, besteht darin, verschiedene Dispatcher
zu verwenden. Dispatcher können durchaus in einer festgelegten Reihenfolge
ausgeführt werden und voneinander abhängig sein, wenn das auch hier nicht
wünschenswert ist. Innerhalb eines Dispatchers sollten die Interceptors dann aber
unabhängig ausgeführt werden können.
Die Reihenfolge der Ausführung der registrierten Interceptors kann aber auch noch
aus anderen Gründen geändert werden:
* Vielleicht möchte ein Anwender die Reihenfolge selbst festlegen.
* Auch vordefinierte Prioritäten könnten eine Rolle spielen.
* Die Reihenfolge könnte auch vom Inhalt der zu verarbeitenden Nachricht abhängen.


Es kann auch vorkommen, dass ein Interceptor die Ausführungsreihenfolge dergestalt
ändert, dass eine Nachricht in einer Schleife festhängt oder aufgrund der Ausführung
weitere Events erzeugt werden, die wiederum zu weiteren Dispatch-
Aufrufen führen, die wiederum weitere Interceptors aufrufen, die selbst wiederum
Events erzeugen …

Sie sehen, das macht eine Anwendung nicht gerade weniger komplex.


## Interpreter
Das nächste Muster, der Interpreter, ist zugegebenermaßen eine abstrakte Angelegenheit
und in der Praxis zudem reichlich unterrepräsentiert. Das kann auch daran
liegen, dass es im Vergleich zu den anderen Mustern kaum beschrieben ist. Dem
möchte ich mit einer ausführlichen Beschreibung gegensteuern. Denn es lohnt sich
auf jeden Fall, dieses Muster kennenzulernen. Sie sollten sich daher auch unbedingt
das Praxisbeispiel näher ansehen.

### Beschreibung
In der Softwareentwicklung gibt es immer wieder dieselbe Aufgabenstellung, die an
verschiedenen Stellen zu lösen ist. Der Klassiker, der immer wieder gern verwendet
wird, um dieses Muster zu beschreiben, ist die Suche nach Zeichenketten. Wenn Entwickler
auf solche Probleme stoßen, dann durchlaufen sie meist eine ganz
bestimmte Reihenfolge. Nehmen wir das Beispiel der Textsuche innerhalb eines langen
Strings.

#### Methode
Der erste Gedanke ist es häufig, den Code in eine Methode zu packen, um einen gewissen
Grad an Wiederverwendbarkeit zu gewährleisten. Das könnte eine Methode
sein wie:

    public boolean kommtTextVor(String suchText, String text);

Damit lässt sich noch keine Waschmaschine gewinnen, und schnell gibt es Methoden
mit vielen Parametern oder viele Methodenüberladungen wie:

    public boolan kommtTextVor(String suchText, String suchText2, String text,
      boolean isCaseSensitive, boolean suchTexteUndVerknuepft …)

Klassen
Das klappt also nicht, weil es zu komplex ist. Okay, die nächste Ebene ist dann die der
Klasse. Einer Klasse kann man viele Felder spendieren; der innere Zustand der Klasse
ist dann die Suchabfrage. Eine einfache Methode erledigt dann die Suche:

    SuchKlasse suchKlasse = new SuchKlasse();
    suchKlasse.SuchTexte.add("Mein Suchtext");
    suchKlasse.SuchTexte.add("Mein Suchtext2");
    suchKlasse.Verknuepfung = Verknuepfung.OR;
    boolean gefunden = suchKlasse.suchtrefferGefunden();

Das ist einfacher, hat aber auch so seine Probleme. Erst einmal kann man eine solche
Klasse nicht mit Konstruktoren initialisieren, weil die ähnlich umfangreich wären
wie die Suchmethode oben. Bleibt noch das Setzen der Attribute im Code, wie gezeigt.
Allerdings gibt es jetzt schnell Kombinationen, die sich ausschließen. Der Verwender
der Klasse muss also alle zu setzenden Felder samt ihren Auswirkungen kennen und
außerdem wissen, welche Kombinationen sich ausschließen und wie die Klasse bei
einer widersprüchlichen Initialisierung arbeitet. Im obigen Beispiel könnte die
Klasse die Verknüpfung der Suchtexte einfach ignorieren, wenn es nur einen Suchtext
gibt – oder aber eine Exception auslösen.

#### Vererbungshierarchie
Eine Möglichkeit, dieses Problem zu lösen, wäre mit Vererbung. Dann gibt es eben
eine Klassenhierarchie.
Das verhindert nun unsinnige Kombinationen, und jemand, der die Klasse verwenden
will, muss nur noch die richtige Klasse aussuchen und die (wenigen) Felder richtig
befüllen.

Aber leider rächt sich auch dieses Design schnell. Denn einmal differenziert, bringt
man Klassen leider nicht wieder zusammen, jedenfalls nicht in Sprachen, die keine
Mehrfachvererbung unterstützen – und das ist heute die Mehrzahl aller Sprachen.
Wollten wir noch eine Variante einführen, die Fuzzy-Suche bzw. phonetische Suche, dann muss an verschiedenen Objekten abgeleitet werden. 
Das führt nun schnell zu riesigen Vererbungshierarchien, wenn jeder Teilaspekt des
Problems in allen Varianten ausdifferenziert wird. Oder man zieht das Problem in
der Hierarchie nach oben, mit den oben beschriebenen Problemen.

### Weitere Ansätze
Natürlich endet das Instrumentarium der objektorientierten Sprachen hier nicht.
Man könnte beispielsweise den Aspekt der phonetischen Suche in eine eigene Klasse
auslagern (die selbst wiederum eine Vererbungshierarchie aufweisen kann) und dieses
Objekt dann dem Suchobjekt als Parameter übergeben. Oder man wendet eines
der hier beschriebenen Muster an: Der Dekorierer und weitere Muster eignen sich,
um einem Objekt zur Laufzeit neue Eigenschaften beizubringen.
Aber auch das bringt Probleme: Die Komplexität steigt schnell an, die Validierung
des gesamten Objekts wird schwierig bis unmöglich, und die Anzahl an Kombinationen
wird weder für einen Verwender dieser Klassen noch für den Ersteller ein Zuckerschlecken.
Das wäre jetzt eine prima Gelegenheit, sich nach Alternativen umzuschauen. In
unserem Beispiel ist das nicht schwer: Jede Sprache, die etwas auf sich hält, bietet
schließlich reguläre Ausdrücke. Aber warum reguläre Ausdrücke? Das bringt uns
zum Kern dieses Musters.

#### Interpreter
Wenn ein Problem nur oft genug zu lösen ist und dieses Problem einen gewissen
Variantenreichtum aufweist – beides trifft für die Suche nach Text sicherlich zu - 
dann besteht eine Alternative darin, sich eine Sprache zu überlegen. Und das Interpreter-
Muster interpretiert diese Sprache und löst das Problem für uns.
Reguläre Ausdrücke sind eine solche Sprache. Wie jede andere Sprache auch braucht
es vor allem drei Dinge:
* Ausdrücke und Token – Beispiel: Der Ausdruck \s steht für ein Whitespace, also
das Leerzeichen oder ein anderes Zeichen (z. B. für den Tabulator oder NewLine).
* Syntax – Beispiel: Wenn ein Set geöffnet wird (mit [), dann muss es auch wieder
geschlossen werden, und zwar mit ].
* Semantik – Beispiel: Ein Set von Zeichen bedeutet irgendeines dieser Zeichen, wie
in [abc].


Kurz gesagt: Man muss eine Grammatik für die eigene Sprache erfinden.
Alle Probleme löst das nicht: Ein Anwender muss zwar keine komplexen Klassen
mehr bändigen, aber dafür die Sprache erlernen. Und wer schon einmal durch einen
harmlos wirkenden regulären Ausdruck exponentielle Laufzeiten verursacht hat, der
weiß: So ganz ohne Kenntnis der inneren Arbeitsweise macht die Anwendung nur
halb so viel Spaß. Was übrigens für die meisten Sprachen gilt, ob sie nun SQL heißen
oder BPML.
Aber eines bleibt unbestreitbar: Ein Problem mithilfe einer eigenen Sprache zu lösen,
kann kurz, prägnant und ausdruckstark sein. Und wenn man es richtig macht, ist es
für den Verwender sehr effizient – und obendrein lässt sich die Sprache auch noch
erweitern. Das Interpreter-Muster macht’s möglich.

### Anwendungsfälle
Um das Muster sinnvoll anwenden zu können, sollten einige Bedingungen erfüllt
sein:
* Es soll sich lohnen. Das bedeutet: Das zu lösende Problem sollte hinreichend oft in
der Aufgabenstellung auftreten. Das ist bei regulären Ausdrücken sicherlich der
Fall.
* Oder aber: Die Aufgabenstellung ist ohne dieses Muster nur sehr umständlich zu
lösen, wie die Berechnung des Scores im Praxisbeispiel. Häufig ist das der Fall,
wenn es viele Varianten und Verschachtelungen gibt, die Grammatik also ein
wenig komplexer ist und häufige Änderungen anstehen.
* Andererseits sollte die Grammatik nicht zu komplex sein, weil für jeden Ausdruck
eine Klasse benötigt wird. Eine sehr komplexe Grammatik mündet dann in einem
umfangreichen Klassenbaum, den wir ja eigentlich vermeiden wollten.
* Die Performance darf bei diesem Muster keine zentrale Rolle spielen. Der Umgang
mit breiten und tiefen Syntaxbäumen kann unter Umständen von der Laufzeit her inakzeptabel sein. Viele Frameworks nutzen daher andere Technologien und
kompilieren beispielsweise einen regulären Ausdruck schon vor der Ausführung.
* Und natürlich muss sich das Problem erst einmal in einen Syntaxbaum übersetzen
lassen, was aber häufig der Fall ist.

Einige Beispiele für das Muster sind:
* Regel-Engines, zum Beispiel für Workflows. Regeln ist nur schwer beizukommen,
weil sie eben die gesamte Bandbreite an Operatoren, Feldern und anderen Ausdrücken
aufweisen können. Andererseits können solche Regeln nicht fest codiert
werden, weil Kunden heutzutage eben erwarten, dass sie ihre Geschäftsregeln
ändern können, ohne dafür einen Programmierauftrag vergeben zu müssen.
* Formelgeneratoren, wie im Score-Beispiel. Während bei Regel-Engines die Ausdrücke
häufig zu einem booleschen Wert ausgewertet werden, werden Formeln zu
Zahlen ausgewertet.
* Beliebige andere Generatoren, wie im Praxisbeispiel einer aufgeführt ist, bei denen
sich die zu generierenden Daten komfortabel über eine Definition beschreiben
lassen.
* Ein weiteres Beispiel ist die gute alte umgekehrte polnische Notation, eine spezielle
Schreibweise für numerische Ausdrücke, bei der die Operatoren hinter den Operanden
stehen. Dafür gibt es eine formale Grammatik, und daher lässt sie sich
leicht mit diesem Muster abbilden.
* Predicate Expressions: Auch Predicates sind Ausdrücke, die true oder false ergeben.
Man kann sie Funktionen übergeben, zum Beispiel mittels Lambda Expressions,
und sie werden dann in der Funktion ausgewertet – zum Beispiel mit dem
Interpreter-Muster. Sie werden häufig eingesetzt, um damit Filterausdrücke abzubilden.
Immer häufiger trifft man in diesem Zusammenhang auf Domain Specific Langues
(DSL). Solche DSLs sind Sprachen, die spezifisch für eine bestimmte Domäne sind,
also für eine bestimmte fachliche Aufgabenstellung. Der Vorteil des Interpreter-Musters
dabei ist, dass wir die Grammatik völlig nach eigenen Vorstellungen gestalten
können.

### Weitere Überlegungen und Alternativen
Die Limitationen des Musters wurden schon beleuchtet: Die Syntaxbäume können
komplex werden, wenn die formale Grammatik dazu viele Varianten und Verschachtelungen
enthält. Das allein wäre aber noch kein Problem. Schwierig wird es, wenn
ein umfangreicher Syntaxbaum noch dazu zu einer langen Laufzeit führt.
Dem gegenüber stehen der Komfort und die Erweiterbarkeit, denn das Muster verwendet
ja Klassen, um die Regeln der Grammatik abzubilden. Diese Klassenhierarchie
kann man verändern und auch relativ leicht erweitern – und damit zusätzliche
Funktionalität schnell und komfortabel nachrüsten. Und der unschlagbare Komfort
besteht darin, mittels einer eigenen Sprache auf kurze, prägnante Art das zu erwartende
Ergebnis beschreiben zu können.

#### BNF
Von der Backus-Naur-Form (BNF) war schon die Rede – ich habe sie weiter vorn verwendet,
um die Grammatik zu beschreiben. Wenn Sie eigene Sprachen entwerfen
und dafür eigene Grammatiken beschreiben wollen, dann lohnt es sich, die (wenigen)
Regeln dieser Sprache zu erlernen, um mit ihr zu einer logisch korrekten und
vollständigen Grammatik zu gelangen. 
Neben ihr gibt es noch die erweiterte Backus-Naur-Form (EBNF) und diverse Derivate,
die sich vor allem dann lohnen, wenn Sie Elemente in Ihrer Grammatik einsetzen
wollen, die sich wiederholen.

#### Tools
Für BNF/EBNF gibt es Tools, die aus einer Grammatik eine Klassenhierarchie erzeugen
können, die Sie dann vielleicht nur noch an Ihre eigenen Bedürfnisse anpassen
müssen.
Außerdem gibt es bereits fertige Parser, die einen Syntaxbaum aus einem String
generieren können. Allerdings bringen beide Tools wieder eine eigene Komplexität
ins Spiel und dürften sich vermutlich erst dann lohnen, wenn Sie mithilfe dieses
Musters und BNF komplexe DSLs aufbauen wollen.

#### Parser/Compiler
Ein Parser wird in jedem Fall benötigt, wenn aus einem Text ein Syntaxbaum werden
soll. Der bekannteste Fall sind Entwicklungsumgebungen, die auf eben diese Weise
vorgehen. Allerdings setzen sie Compiler ein, und das ist auch die bevorzugte Alternative,
wenn die Geschwindigkeit besonderes Augenmerk verlangt.

#### Kompositum
Das Interpreter-Muster ähnelt dem Kompositionsmuster, das ebenfalls komplexere
Objektstrukturen, sogenannte Teil-Ganzes-Hierarchien erzeugen kann. Die Blatt-
Klassen entsprechen dabei den terminalen Ausdrücken, wobei die Kompositum-
Klassen in den nichtterminalen Ausdrücken ihre Entsprechung finden.
Bei aller Gemeinsamkeit gibt es aber auch Unterschiede:
 Beim Kompositum benötigen man, in aller Regel jedenfalls, keinen Parser. Die
Struktur kann sich zur Laufzeit auf ganz unterschiedliche Art und Weise ergeben.
 Und natürlich gibt es beim Kompositum keine Interpretiere-Methode, sondern
ganz allgemein eine auszuführende Operation.



## Iterator
Beim Iterator geht es darum, die Elemente einer Menge zu durchlaufen, und zwar
mithilfe eines Zeigers auf das jeweils nächste Objekt in der Menge.

### Beschreibung
Es klingt zunächst einfach, das Navigieren durch eine Menge von Elementen. Und
tatsächlich wurde diese Aufgabe in der Frühzeit der Entwicklungssprachen (ja, so
lange ist das noch gar nicht her) ganz pragmatisch gelöst: Am Anfang waren das
Array und die for-Schleife.
Um die Grenzen des Array musste man sich als Entwickler peinlich genau kümmern,
und wehe ein Zeiger verirrte sich einmal über das Arrayende hinaus. Multithreading
war gar kein Thema, und schon allein der knappe Speicherplatz und die
beschränkte Verarbeitungsgeschwindigkeit hätten eine »elegantere« Alternative
ohnehin verhindert.
Genug der Nostalgie: Das hat sich natürlich geändert. Klassen nutzen heute ganz
unterschiedliche Datentypen zur Speicherung ihrer Elemente, also Objekte, primitive
Datentypen usw., und auch das Array ist natürlich noch da. Ein Klient möchte
heutzutage durch die Elemente iterieren, ohne die Details kennen zu müssen. Aus
Sicht des Klienten sollen eine LinkedList, eine ArrayList, ein Array und auch ein assoziativer
Speicher, wie die Klasse HashMap einen bereitstellt, auf dieselbe Art und Weise
zu benutzen sein. Warum auch nicht? Schließlich kann man durch alle diese Klassen
iterieren, wenn auch technisch gesehen jeweils auf eine andere Art und Weise.
Das genau leistet das Iterator-Muster. Und es kann noch mehr: Denn mehrere Klienten
können auch zur selben Zeit durch eine Menge iterieren und dabei zeitgleich
gerade verschiedene Elemente der Menge erreicht haben. Das Muster speichert also
den Status für jeden Klienten eigens.
Heutige Programmiersprachen bieten das Konzept des Iterators über festgelegte
Schnittstellen an. In Java funktionieren die Zeilen

    for (String s : list) {
    System.out.println(s);
    }

für alle Klassen, die Iterable\<T> implementieren, direkt oder indirekt. Nur für assoziative
Speicher muss man noch angeben, ob man durch die Werte oder die Schlüssel iterieren möchte. In .NET gibt es die Schnittstelle IEnumberable\<T>, die
vergleichbar ist:

    foreach(var item in list) //List implementiert IEnumerable<T> {
    Console.WriteLine(item);
    }

Diese Sprachen bringen das Iteratorobjekt also bereits im Framework mit: Das ist
komfortabel und einheitlich. Für die »Standards«, das Iterieren durch einfache Listen,
hat sich das Thema also erledigt. Wozu braucht man also noch dieses Muster?
Zwei Gründe dafür:
* Iterieren kann man nicht nur durch einfache Listen, sondern auch durch komplexere,
womöglich verteilte Datenlieferanten. Ein Beispiel wäre ein Cursor, der
durch die Ergebnismenge einer Datenbankabfrage iteriert; oder es soll durch ein
von einem Webservice bereitgestelltes Ergebnis iteriert werden.
* Das Iterieren selbst kann durchaus auch Geschäftslogik abbilden. Beispiel: Es soll
durch eine Dateiliste iteriert werden, aber nur durch die Dateien, auf die wir als
Anwender Schreibzugriff haben. Das Iterator-Muster kann solche Geschäftslogik
sauber gekapselt im Iterator verstecken.


Das erreicht dieses Muster, indem das Iterieren durch die Menge von einem eigenen
Iteratorobjekt erledigt wird. Dieses Objekt folgt einer einheitlichen Schnittstelle und
merkt sich die gerade aktuelle Position. Das Listenobjekt und das Iteratorobjekt sind
also voneinander getrennt; während sich das Listenobjekt um die Datenhaltung
kümmert, erledigt das Iteratorobjekt das Durchlaufen der Listeneinträge.
Allerdings: Das Iterator-Muster ist eines der Muster, bei denen es in der Implementierung
besonders viele Freiheiten gibt, und das macht es wiederum spannend. 

Die Bezeichnungen des Musters sind ein wenig gewöhnungsbedürftig. Aber wir müssen
uns klarmachen, dass die Bezeichnungen eben generisch sind. Ein Aggregat ist
eine Zusammenfassung mehrerer Objekte. Das trifft für Listen sicherlich zu, aber
auch für einen Datenbankcursor.
Nehmen wir noch einmal die ArrayList\<E> von Java und deren Implementierung für
das Iterator-Muster, dann wird das Ganze gleich geläufiger (jedenfalls für Java-Programmierer).

Die Implementierung entspricht nicht zu 100 % dem Iterator-Muster, wie es hier
beschrieben ist. Die wichtigsten Unterschiede und Besonderheiten sind:
 Der Iterator bzw. die Schnittstelle Iterator\<E> unterstützt die optionale Methode
remove zum Entfernen des letzten Elements, das vom Iterator zurückgegeben
wurde. Allerdings ist die Standardimplementierung einfach eine UnsupportedOperationException,
sofern der konkrete Iterator die Implementierung nicht ändert.
* Der Iterator kann nicht auf den Anfang gesetzt werden. Das ist aber kein praktisches
Problem; man kann sich ja zu jeder Zeit einen neuen Iterator erzeugen lassen,
der dann wieder an den Anfang gesetzt wird.
* Auch die AktuellesElement-Methode fehlt. Auch das ist kein Problem, weil die
next-Methode ja das nächste Element zurückliefert. Man kann es sich ja einfach
selbst merken.
* Der forEachRemaining-Methode kann man eine Operation übergeben, die dann für
alle restlichen Elemente in der Auflistung ausgeführt wird.
* Die konkrete Iterator-Klasse, die Klasse Itr, ist privat, von außen also nicht sichtbar
und instanziierbar. Das ist nur natürlich, weil der Iterator ja engen Zugriff auf
die ArrayList-Klasse und deren Elemente benötigt und andererseits der Iterator
daher auch nur für diese Klasse funktioniert.
* Es gibt noch eine zweite Schnittstelle ListIterator, die die Iterator-Schnittstelle
um einige Fähigkeiten erweitert. So kann die Auflistung auch rückwärts durchlaufen
werden und Elemente können ersetzt und hinzugefügt werden.


Bleibt eine Frage: Was haben das Entfernen, Hinzufügen und Ersetzen von Listeneinträgen
im Iterator zu suchen? Um diese Fragen zu beantworten, müssen wir uns klarmachen,
was ein Iterator tut. Er durchläuft eine Menge von Elementen und speichert
dazu die aktuelle Position. Da das Iteratorobjekt ein eigenes Objekt ist, muss es sich
auf die zugrunde liegende Datenquelle verlassen können. Wir können also nicht einfach
während einer Iteration die zugrunde liegende Datenquelle ändern, in die
ArrayList also neue Einträge hinzufügen oder welche löschen.

Wie auch immer, eines wird klar: Iteratoren sind einerseits sehr unterschiedlich, das
eine Muster gibt es nicht, und jede Sprache implementiert die Iteratoren unterschiedlich.

### Anwendungsfälle
Die Notwendigkeit, eine Liste von Elementen durchlaufen zu können, bedarf keiner
besonderen Rechtfertigung. Wie ich schon angedeutet habe, kann das Muster aber
auch angewendet werden, wenn komplexere Datenstrukturen im Spiel sind. Einige
Beispiele:
 Ein Iterator kann durch Bäume iterieren und direkt die Blattknoten zurückliefern
– die Knoten, die Kindknoten haben, also ignorieren. Dabei versteckt der Iterator
den Algorithmus vor einem Klienten.
 Services bzw. das Ergebnis von Anfragen an Webservices können durchlaufen werden.
Ein Klient kann so das Ergebnis von Webservices genauso bequem nutzen wie
eine einfache lokale Liste.
 Datenbanken bzw. Cursor auf Datenbanken sind ebenfalls Iteratoren. So gibt es
immer noch Cursor, die »forward only« sind, also ganz im Sinne dieses Musters
nur vorwärts durchlaufen werden können.
Ein Klient nutzt den Iterator und weiß nicht einmal, dass eine Datenstruktur vielleicht
überhaupt keine Liste ist, sondern ein Datenstrom vom Netzwerk.

#### Optimierte Iteratoren
Wie wir schon im Beispiel der Java-Klasse ArrayList gesehen haben, kann es auch
mehr als einen Iterator geben. Fachliche Erfordernisse können das verlangen, oder es
gibt einfache Iteratoren für einfachere Anforderungen, die dann auch schneller ausgeführt
werden. Die Forward Cursors bei Datenbanken gehören in diese Kategorie.
Man könnte aber auch einen Cursor öffnen, der beliebig in der Datenmenge navigieren
kann. Man muss dafür aber einen Preis bezahlen.
Nicht umsonst ist die konkrete Iterator-Klasse, die die ArrayList zurückgibt, eine private
Klasse. Der Iterator kann auf diese Weise auf die Besonderheiten der Datenhaltung
in der ArrayList Rücksicht nehmen und den Code für den Zugriff auf die Daten
hoch optimieren. Eine ArrayList heißt ja so, weil die Daten in einem Array gespeichert
sind. Der Iterator kann also die Indexposition des letzten zurückgegebenen Elements
speichern. Das wird bei einer verketteten Liste nicht funktionieren, weswegen
die LinkedList in Java den Iterator auch anders abbildet.
Für die Anwendungsfälle heißt dies: Wir können optimierte Implementierung wählen
für jeweils ganz spezielle Aufgabenstellungen.


#### Geschäftslogik im Iterator
Nehmen wir mal eine Liste von MP3-Musikstücken in einem portablen Gerät. Ein Iterator
würde dann einem Klient eine Möglichkeit bieten, das jeweils nächste Lied
abzurufen, um es abspielen zu können, bis das Ende der Liste gekommen ist.
Auf welche Weise das geschieht, ist Geschäftslogik und hängt von den Vorlieben des
Anwenders ab. Ein Anwender könnte »Zufällige Wiedergabe« eingestellt haben, oder
ein MP3-Player spielt die Titel in der Reihenfolge ihrer Bewertungen ab. In beiden Fällen
setzt die Weiter-Methode des Iterators den Zeiger auf den nächsten abzuspielenden
Titel; was das aber fachlich bedeutet, ist im Iterator codiert und damit von der
Benutzeroberfläche entkoppelt.
Das Iterator-Muster eignet sich also auch, um unterschiedliche Reihenfolgen in Listen
zu ermöglichen, ohne dass der Klient dafür modifiziert werden müsste.

### Weitere Überlegungen und Alternativen
Nun kommen wir zu einigen Varianten des Iterator-Musters. Einige davon finden
sich bereits in Standardimplementierungen, andere wiederum können für ganz spezielle
Anwendungsfälle nützlich sein.

#### Externe bzw. interne Iteratoren
Das Praxisbeispiel hat einen externen Iterator umgesetzt. »Extern« bedeutet, dass der
Klient die Iteration steuert, also die Weiter-Methode des Iterators aufruft, um zum
jeweils nächsten Element zu gelangen.
Im Gegensatz dazu steuert beim internen Iterator der Iterator selbst die Iteration.
Dazu muss er dann aber wissen, welche Operation er für jedes Element ausführen
soll, und diese Information erhält er vom Klient.
Die Standard-Java-Implementierung des Iterators bietet beide Möglichkeiten, also
Methoden wie next() und hasNext(), mit denen sich ein externer Iterator umsetzen
lässt, und eine Methode, die als Eingabeparameter eine auszuführende Operation
entgegennimmt:

    default void forEachRemaining(Consumer<? super E> action) {
        Objects.requireNonNull(action);
        while (hasNext())
            action.accept(next());
    }


Auf unser Beispiel übertragen, benötigen wir eine weitere Methode in der Schnittstelle
Iterator\<E>, die eine Default-Implementierung besitzt:

    void iteriere(Consumer<? super E> action);


Und wir brauchen die Implementierung des internen Iterators in der implementierenden
Klasse ZufallsIterator:

    public void iteriere(Consumer<? super E> action)
    {
        while (!istFertig()) {
            weiter();
            action.accept(aktuellesElement());
        }
    }

Die Anwendung ist dann einfach, erst recht in Java 8, in der Lambda Expressions
möglich sind:

    Iterator<MP3> iterator = bibliothek.erzeugeIterator();
    iterator.iteriere(mp3->bibliothek.play(mp3));

Der Vorteil zeigt sich schon an diesem Stück Code: Der Code ist kürzer, und der Klient
muss nicht wissen, wie der Iterator funktioniert, welche Methoden also aufzurufen
sind. Andererseits sind externe Iteratoren flexibler zu nutzen, weil der Klient eben
die volle Kontrolle über den Iterationsprozess besitzt.
Praktisch ist es auch, wenn ein interner Iterator nicht nur die auszuführende Operation,
sondern auch eine Predicate Expression entgegennimmt, also einen Filter für
die zu iterierenden Elemente.


#### Veränderungen während des Iterierens
Weiter vorn habe ich schon erwähnt, dass Java eine ConcurrentModificationException
auslöst, wenn wir die zugrunde liegende Datenstruktur verändern, also zum Beispiel
ein Element während des Iterierens löschen. Dort gibt es aber Methoden, um die Elemente
während des Iterierens verändern zu können.
Auch .NET verhindert Veränderungen an der zu iterierenden Datenstruktur (System.
InvalidOperationException – die Auflistung wurde geändert). Dabei werden die
Iteratoren in .NET häufig hinter den Kulissen verwendet. So lässt sich in C# die foreach-
Schleife nur dann anwenden, wenn die zu iterierende Klasse die Schnittstelle
IEnumerable implementiert, und dort gibt es wiederum die Methode GetEnumerator,
die einen Iterator zurückliefert, wenn das in .NET auch nicht exakt dasselbe ist.
Wenn Sie selbst Iteratoren entwickeln, dann fällt Ihnen diese Aufgabe natürlich zu.
Ignorieren wäre eine Möglichkeit, birgt aber die Gefahr von Laufzeitfehlern (ein Element
wird abgerufen, das überhaupt nicht mehr in der Liste enthalten ist) oder von
logischen Fehlern (es werden zu viele Elemente iteriert).
Die einfachste Möglichkeit besteht wohl darin, die aufzulistenden Elemente beim
Erstellen des Iterators einfach zu kopieren, was aber aus Gründen der Performance
und des benötigten Arbeitsspeichers für hoch frequentierte Iteratoren ausscheidet.
Aber dennoch: Nachdenken kann man darüber.
Die etwas aufwendigere Möglichkeit wäre es, im Iterator Methoden unterzubringen,
wie addElement, replaceElement und removeElement (wie in Java). Dann müssen aber
Aggregat und Iterator im Falle einer Modifikation miteinander reden. Denn es
könnte ja sein, dass verschiedene Klienten über die Elemente gerade iterieren – und
dabei gerade an verschiedenen Stellen angelangt sind. In Java wird dafür die Anzahl
der Modifikationen einfach um eins erhöht (modCount), wenn zum Beispiel ein Element
eingefügt wird. Der Iterator prüft bei jeder Iteration, ob modCount den zu erwartenden
Wert annimmt (Der Wert wird in der Variable expectedModCount abgelegt).
Wenn das nicht der Fall ist, dann wird die schon erwähnte Exception ausgelöst und
die Iteration ist damit zu Ende. Wird nun über den Iterator eine Änderung angestoßen,
dann werden modCount und expectedModCount jeweils um eins erhöht und die
Exception bleibt aus.

#### Cursor
Fassen wir den Begriff Iterator einmal weiter. In den Anwendungsfällen habe ich den
Zugriff auf eine Datenbank genannt. Dort ist das Konzept eines Cursors realisiert,
also eines Zeigers auf den jeweils aktuellen Datensatz in der Ergebnismenge einer
SQL-Abfrage. Der Unterschied zum klassischen Iterator ist dabei, dass nicht der Iterator
selbst den Iterationsvorgang durchführt, sondern die Datenbank, also in der Sprache
des Musters das Aggregat.
Anstelle des Iterators wird das Aggregat aufgefordert, das nächste Element zu liefern.
Das geschieht schon aus praktischen Gründen, wir können ja schlecht einer Datenbank
vorschreiben, wie sie an den nächsten auszuliefernden Datensatz gelangt. Der
aktuelle Cursor wird vom Aggregat verändert, zum Beispiel indem dessen weiter-
Methode einen Cursor als Eingabeparameter erhält, der innerhalb der Methode dann
auf den nächsten Datensatz gesetzt wird.

Das wären nun schon drei Möglichkeiten:

|Wer implementiert die Iteration?|Wer steuert die Iteration?|Typ|
|:-------|:--------|:-------|
|Iterator|Klient|Externer Iterator|
|Iterator|Iterator|Interner Iterator|
|Aggregat|Iterator|Cursor|


Iterationen bei komplexeren Datenstrukturen
Bisher waren die Datenstrukturen einfach – Listen, die man ohne großes Brimborium
sequenziell durchlaufen kann. Das muss aber nicht so sein, denn das Iterator-
Muster verlangt keine »einfachen« Datenstrukturen. Das Kompositum-Muster
erlaubt es, sehr komplexe baumartige Objektstrukturen zu erzeugen, für die ebenfalls
ein Iterator erzeugt werden kann.
Wie durch diesen Baum iteriert (oder sagen wir besser traversiert) wird, ist im Iterator
codiert. Auf jeden Fall muss der Iterator sich dafür nicht nur eine einfache Position
merken, sondern vielleicht den gesamten Pfad bis zum aktuellen Objekt, sodass ein
Bezugspunkt für das nächste Objekt vorhanden ist.
Etwas einfacher ist da die Implementierung eines internen Iterators, in dem das Iterieren
einfach durch Rekursion erfolgen kann. Die weiter-Methode kann sich dann
selbst aufrufen, mit der aktuellen Position als Übergabeparameter. Der Pfad muss
dann nicht explizit gespeichert werden, sondern ergibt sich aus den rekursiven Aufrufen,
also aus dem Zustand des Stacks.


#### Private Klassen, Nested Classes und »friend«
Das bisher Gesagte zeigt schon: Iteratoren und Aggregate müssen »miteinander können
«. Es ist daher in der praktischen Implementierung manchmal angezeigt, die beiden
Klassen auch technisch eng zu verbinden. In Java gibt es dafür die »private
classes«, wie es dort auch umgesetzt wurde. Je nach Sprache können solche Klassen
direkt auf die privaten/protected Variablen (wie modCount) des Aggregats und auch
ohne Umwege auf die Datenstrukturen zugreifen, die die zu iterierenden Elemente
enthalten – praktisch. In C++ gibt es dafür das Schlüsselwort friend, wenn es auch in
C++ andere Implementierungen gibt, die ohne friends auskommen.
Die Kehrseite der Medaille ist wiederum, dass es ja eigentlich die Absicht des Musters
war, Iterator und Aggregat sauber voneinander zu trennen und damit auch die zu
implementierenden Aspekte Datenhaltung und Iteration.

#### Iterator-Hierarchien und zusätzliche Operationen
Auch hier kann man wieder bei Java nachschlagen: Die Schnittstelle ListIterator\<E>
erweitert die Schnittstelle Iterator\<E> um einige interessante, nämlich um die Fähigkeit,
rückwärts durch die Auflistung zu navigieren und Elemente hinzuzufügen
und auszutauschen. Damit einher geht die Hierarchie in den eigentlichen Iterator-
Klassen.
Das ist eine Möglichkeit, um Iteratoren zu erweitern. Auch im Praxisbeispiel könnte
es verschiedene Iterationsverfahren geben. In dessen Iterator werden die Titel zufällig
abgespielt. Es wären auch andere Iterationen denkbar, die über eigene Iteratorklassen oder über neue Methoden in der bestehenden Iteratorklasse abgebildet
werden können.

#### Nulliteratoren
Eine weitere Spielart ist der Nulliterator, dessen istFertig-Methode immer true
zurückliefert. Man kann folglich auch nichts darüber iterieren. Sein Einsatz lohnt sich
dann, wenn es nichts zu iterieren gibt. Ein Beispiel dafür wären Bäume. Ein Knoten,
der weitere Elemente beinhaltet, würde dann einen »regulären« Iterator zurückliefern,
sodass darüber auf die Kindelemente zugegriffen werden kann. Ein Knoten, der
keine weiteren Elemente beinhaltet (ein Blattknoten) könnte einen Nulliterator
erzeugen und die Iteration an dieser Stelle bequem beenden. Der Klient muss dann
nicht zwischen regulären Knoten und Blattknoten unterscheiden, und der Nulliterator
ist performant, weil er keine Arbeit zu verrichten braucht, außer dem Klienten
das Ende der Auflistung mitzuteilen.

#### Ähnliche und ergänzende Muster
Vom Kompositum war schon die Rede und davon, dass ein Iterator auch durch komplexe
Objektbäume iterieren kann.
Für das Speichern der aktuellen Position, also des aktuellen Zustands des Iterators,
kann das Memento-Muster praktisch sein, wenn eine einfache Variable vom Typ int
dazu nicht mehr ausreicht.


## Mediator / Vermittler

Der Vermittler ist sozusagen der Mediator unter den Mustern, und im Englischen
heißt er ja auch so. Er vermittelt zwischen zwei oder mehreren Objekten, sodass diese
nicht direkt miteinander zu reden brauchen, und unterstützt daher die lose Kopplung.

### Beschreibung
Häufig müssen Objekte zwar miteinander kommunizieren, aber man möchte diese
enge Kopplung lieber vermeiden. Gründe dafür gibt es viele: Vielleicht möchte man
die hinter den Objekten stehenden Klassen von verschiedenen Teams unabhängig
weiterentwickeln lassen, oder die Objekte »leben« auf verschiedenen Sicherheitsebenen.
Um also das Gegenteil einer engen Kopplung – die lose Kopplung – zu erreichen,
kann ein Vermittler (selbst ein Objekt) dazwischen die Kommunikation der Objekte
untereinander übernehmen.
Wie auch im echten Leben wird eine Mediation umso schwerer, je mehr Beteiligte es
gibt. Je mehr Objekte also über einen Vermittler kommunizieren, desto mehr Besonderheiten
gilt es zu berücksichtigen. Andererseits liegt dort der große Vorteil: Bereits
5 Objekte, die alle miteinander in Beziehung stehen, unterhalten 20 enge Verbindungen.
Kommunizieren diese 5 Objekte nur mit dem Vermittler, reduziert sich die
Abhängigkeit auf 5 Verbindungen – oder 10 Verbindungen, wenn man den Rückweg
vom Vermittlerobjekt zu den Teilnehmerobjekten mitrechnet.


Kurz gesagt: Anstatt das Verhalten auf viele verschiedene Klassen zu verstreuen,
macht jede Klasse nur noch das, was nur sie kann, und überlässt die Komplexität, die
sich aus dem Zusammenspiel der Klassen untereinander ergibt, dem Vermittler.
Bitte nennen Sie mich keinen Spielverderber, wenn ich Ihnen nun auch noch die
Kehrseite des Musters, also seine möglichen Nachteile, aufzeige:
* Das Muster tendiert dazu, dass die Implementierung des Vermittlers komplexer
ist als die Summe der Änderungsaufwände in den Klassen, die ohne dieses Muster
notwendig wären. Oder anders gesagt: Die Kommunikation wird zwar einfacher,
also weniger komplex, dafür muss der Vermittler die Protokolle aller beteiligten
Objekte kennen, was eine gewisse Komplexität in der Implementierung mit sich
bringt. Die Komplexität wandert also von den Kollegenklassen in die Vermittlerklassen,
und es besteht die reelle Gefahr, dass 1+1+1+1 = 5 ergibt.
* Es besteht die Gefahr zu »überzentralisieren«, also statt »wendigen«, dezentralen
Systemen monolithische, zentral organisierte und gesteuerte Systeme zu bauen.
* Der Vermittler kann ein weiterer Single Point of Failure sein. Das ist ein wenig
widersprüchlich. Denn einerseits können zentral vermittelte System robuster,
weil einfacher und deterministischer sein; andererseits läuft gar nichts mehr,
wenn das Vermittlerobjekt eine unbehandelte Exception auslöst.


Kurzum, es läuft auf die alte Informatikerfrage hinaus: »Was ist besser: zentrale
Steuerung oder dezentrale Steuerung?« – eine Frage, die auch ein halbes Jahrhundert,
nachdem sie aufgeworfen wurde, noch einer Antwort harrt. Für Ihre eigenen Projekte
wird Ihnen nichts anders übrig bleiben, als die hier erwähnten Vorteile und Nachteile
gegeneinander abzuwägen und eine Entscheidung zu treffen.
Auf jeden Fall sollte die Reichweite des Vermittlers begrenzt sein. Man spricht auch
gern von einer Gruppe von Objekten, die von einem Vermittler betreut werden. Das
Ampelbeispiel liefert auch dafür eine Analogie: Schließlich geht es im Beispiel nur
um diese Kreuzung. Eine Kreuzung am anderen Ende der Stadt wird also ihren eigenen
Vermittler besitzen – was aber nicht heißt, dass nicht alle Vermittler selbst wiederum
durch einen Vermittler koordiniert werden können.

### Anwendungsfälle
Um den für Sie richtigen Anwendungsfall zu finden, liste ich hier einige Voraussetzungen
auf, die für dieses Muster sprechen:
* Es gibt eine mehr oder minder große Anzahl von Objekten, die in enger Verbindung
zueinander stehen.
* Die Objekte gehören einer bestimmten Anwendungsdomäne an, lassen sich also
fachlich gut einkreisen. Sie lassen sich technisch also einer Gruppe zuordnen.
* Die Art der Verbindung ist nachvollziehbar und gut dokumentiert. Sie wissen also
recht genau, wie die Kommunikation auszusehen hat.
* Dennoch fällt es Ihnen vielleicht schwer, den Kommunikationsfluss zu überschauen,
weil die Kommunikation eine gewisse Komplexität erreicht hat.
* Die Objekte sollen wiederverwendbar sein.
* Die Kommunikation in die Objekte einzubauen wäre vielleicht »too much«, weil
Sie vielleicht gar nicht wollen, dass dieses Kommunikationsverhalten die Objekte
aufbläht.
* Vielleicht sollen die Klassen weiterentwickelt werden, ohne dass immer garantiert
werden könnte, dass alle zusammenwirkenden Klassen dann noch kompatibel
zueinander sind.


Zugegeben, das trifft immer noch auf ziemlich viele Anwendungsfälle zu, daher folgen
jetzt einige konkrete Beispiele.


#### Wann wird der Vermittler tätig?
Im Praxisbeispiel wird er auf Wunsch der Teilnehmer tätig, die Endgeräte lösen ja die
»Geschäftsvorfälle« aus, z. B. indem ein Anwender auf der Tastatur des Telefons eine
Nummer wählt.
Es könnte aber genauso gut sein, dass ein externer Klient, zum Beispiel eine Anwendung,
ein Vermittlerobjekt erzeugt und dieses Vermittlerobjekt dann bei den Kollegenobjekten
Events registriert und das Auftreten solcher Events dann das
Vermittlerobjekt triggert. In diesem Fall kennt das Kollegenobjekt vielleicht gar nicht
seinen Vermittler, sondern die Beziehung wird umgekehrt, nach dem Motto Don’t
call us, we call you. Das Beobachter-Muster bietet dafür einen Mechanismus. Eventgesteuerte
Verarbeitung ist besonders dann sinnvoll, wenn wir die Kollegenklassen
überhaupt nicht ändern können, zum Beispiel weil sie zu einer Komponentenbibliothek
eines Drittherstellers gehören. Ein Beispiel dafür findet sich weiter vorn, die
Steuerung von UI-Controls bei Wechsel eines Mandanten.
Noch generischer wird der Schnittstellenansatz, wenn statt eines einfachen Beobachter-
Musters ein Nachrichtenbus zum Einsatz kommt. Der Vermittler lauscht dann an
diesem Bus auf bestimmte, für ihn relevante Nachrichten und schickt selbst Nachrichten
als Antwort darauf, die von den Kollegenobjekten wiederum empfangen und
verarbeitet werden.
Oder der Vermittler bringt selbst eine Steuerung mit, zum Beispiel eine Zeitsteuerung,
und steuert dann seine Kollegenobjekte nach einem zeitlichen Muster.


#### Über die Notwendigkeit der Schnittstellen bzw. abstrakten Klassen
Wie schon weiter oben kurz erwähnt: Wir hätten die Schnittstelle Switch auch weglassen
können. Das Muster funktioniert auch ohne die Vermittler-Schnittstellen, nur
mit einer konkreten Vermittlerklasse.
Die beiden Möglichkeiten sind:
* Es gibt nur einen Vermittler, dann kann auf die Schnittstelle verzichtet werden.
* Es gibt mehrere Vermittler, dann kann die Schnittstelle sinnvoll sein, und die Kollegen
nutzen unterschiedliche Vermittler für ihre Aufgaben.

#### Ähnliche und ergänzende Muster
Kollegen können mit ihrem Vermittler über das Beobachter-Muster kommunizieren,
wobei die Steuerung der Kommunikation in diesem Fall vom Vermittler ausgeht.


## Memento

Vom Memento-Muster war schon an verschiedenen Stellen die Rede. Seine Aufgabe
ist es, den inneren Zustand eines Objekts (oder eines Teils davon) festzuhalten,
sodass dasselbe Objekt (oder ein anderes Objekt) später wieder in genau denselben
Zustand gebracht werden kann.

### Beschreibung
Es kommt immer wieder einmal vor, dass man zu einem späteren Zeitpunkt zum
ursprünglichen Zustand eines Objekts zurückkehren möchte. Zwei Beispiele:
* Mit dem Befehlsmuster lässt sich ein Undo implementieren. Dazu ist es notwendig,
den Zustand vor einer Änderung zu speichern.
* Beim Iterator-Muster ist es notwendig, die aktuelle Position zu speichern, wofür
das Memento-Muster hilfreich ist, wenn diese aktuelle Position nicht nur eine einfache
Zahl ist, sondern zum Beispiel ein komplexer Pfad.
Der Zustand soll dabei »externalisiert« werden, heißt es im Muster, was nichts anderes
bedeutet, als dass er außerhalb des Objekts, zu dem der Zustand gehört, gespeichert
ist. Das kann im Arbeitsspeicher geschehen oder – in einem zweiten Schritt –
auch auf Festplatte, einem SQL-Server oder auf einem anderen persistenten Datenträger. Das Muster selbst speichert den externalisierten Zustand in einem eigenen
Objekt, dem Memento-Objekt.
Dieser innere Zustand ist immer ein Schnappschuss, der zu einem bestimmten Zeitpunkt
erstellt wurde. Daher ist es genauso wichtig, nicht nur den inneren Zustand
des Objekts zu kennen (also das Memento-Objekt), sondern auch den genauen Zeitpunkt,
den dieses Objekt repräsentiert.
Den inneren Zustand eines Objekts zu kennen ist immer eine heikle Angelegenheit.
Das ganze Prinzip der Kapselung wurde ja gerade dafür geschaffen, dass Außenstehende
diesen inneren Zustand nicht zu kennen brauchen – und ihn erst recht nicht
modifizieren können. Das ändert auch das Memento-Muster nicht, denn nur das
Objekt selbst, das seinen Zustand speichern möchte (man nennt es das Urheber-
Objekt), kann auf alle Daten des Memento-Objekts zugreifen und so seinen Zustand
von diesem Schnappschuss aus der Vergangenheit wiederherstellen.
Im Idealfall bietet die Memento-Klasse nach außen zwei unterschiedliche Schnittstellen:
eine »schmale« Schnittstelle, mit der ein Memento-Objekt nur gespeichert
und weitergegeben werden kann, und eine »breite« Schnittstelle, mit der der im
Objekt gespeicherte Zustand abgerufen werden kann. Die schmale Schnittstelle ist
für den Aufbewahrer gedacht, die breite Schnittstelle für den Urheber.

### Anwendungsfälle
Eines ist inzwischen klar geworden: Mithilfe des Memento-Musters kann der
Zustand eines Objekts später wiederhergestellt werden – entweder der gesamte
Zustand oder nur ein Teil davon, entweder in dasselbe Objekt oder ein anderes
Objekt derselben Klasse.
Um die Anwendungsfälle besser zu verstehen, kann man sich klarmachen, was denn
die Alternative zu diesem Muster wäre:
* Der Aufbewahrer könnte den Zustand selbst speichern. Dafür müsste der Urheber
den Zustand aber nach außen hin öffnen, was dem Prinzip des Information Hiding
zuwiderläuft.
* Man könnte mit den Mitteln des Frameworks das Urheber-Objekt serialisieren,
zum Beispiel in einen XML-String oder in ein Byte-Array, und der Urheber könnte
diese Daten dann speichern. Das Deserialisieren würde aber stets ein neues Objekt
erzeugen. Man müsste dann den Zustand also trotzdem danach auf das eigentliche
Zielobjekt übertragen. Außerdem ist der Vorgang sehr technisch und spezifisch
für eine Technologie – man muss dem Serialisierer ja mitteilen, welche Daten
gespeichert und welche ausgelassen werden sollen –, denn vielleicht sollen ja
einige Teile des Zustands nicht wiederhergestellt werden. Außerdem können in
der SetzeMemento-Methode auch Validierungen eingebaut werden.


Und auch hier gilt: Es verletzt die Kapselung, denn der Aufbewahrer könnte auf die
serialisierten Daten zugreifen und diese verändern.
Allerdings ist es durchaus praktisch, zwar einerseits das Memento-Muster einzusetzen,
den Zustand aber andererseits im Memento-Objekt mittels Serialisierung abzulegen.
Das kombiniert die Vorteile des Musters (Kapselung, klare Zuständigkeiten)
mit den Vorteilen der Technologie (einfache, bequeme, vollständige Speicherung des
Zustands).
In den folgenden Abschnitten zeige ich Ihnen drei typische Anwendungsfälle dieses
Musters: Undo, Savepoints und das Entladen bzw. Reaktivieren von Anwendungen.

#### Undo
Ich habe es schon erwähnt: Undo ist ein Kandidat für dieses Muster. Wenn Sie mal
kurz zum Befehlsmuster springen, dann haben wir dort ein
Undo implementiert. Dabei haben wir einfach die alte Position der Spielfigur
gemerkt und diese im Falle des Undos einfach wieder dorthin gesetzt. Andere einfache
Fälle sind Bewegungen. Ein Undo wäre dann einfach eine Bewegung in die entgegengesetzte
Richtung gleichen Betrags.
Die Praxis ist natürlich komplizierter. Denn bei einer Schachsoftware werden ja nicht
nur die Positionen der Spielfigur gespeichert, sondern auch noch die abgelaufene
Zeit pro Spieler und diverse statistische Daten. Und der Computer wird bei jedem Zug
eines Menschen das Spielgeschehen analysieren und die daraus gewonnene Bewertung
speichern, um mit ihr den nächsten Zug berechnen zu können.
Beim Zurücknehmen eines Zugs kann das Memento-Muster dann dafür eingesetzt
werden, diese Analyse (sagen wir, das wäre der innere Zustand des Objekts KI) wiederherzustellen.

#### Savepoints
Eine Spielart des Undo sind sogenannte Savepoints. Mithilfe von Savepoints lassen
sich Transaktionen unterteilen. Typische Datenbanksysteme bieten eine Implementierung an, aber Savepoints kann man auch in eigenen Anwendungen einsetzen. Und
eine Möglichkeit, dies umzusetzen, sind Mementos. Dabei kann für jeden Savepoint
ein Memento-Objekt erzeugt und gespeichert werden.
Ein Savepoint bietet eine Granularität zwischen einem einzelnen Geschäftsvorfall
und der gesamten Transaktion. Die einzelnen Geschäftsvorfälle zwischen zwei Savepoints
lassen sich zwar nicht rückgängig machen, wohl aber alle Geschäftsvorfälle
seit dem letzten Savepoint oder einem Savepoint davor.


#### Entladen bzw. Reaktivieren von Anwendungen
In der klassischen Desktopwelt kann man so viele Anwendungen parallel laden und
ausführen, wie es Arbeitsspeicher und CPU-Ressourcen erlauben. Das ist im Wandel,
denn Windows-8.1-Anwendungen (wenn diese WinRT nutzen) müssen eines beherrschen:
Ihren Zustand zu speichern. Windows 8.1 kann sie nämlich zu jedem beliebigen
Zeitpunkt entladen, sodass sie keine weiteren Ressourcen mehr verbrauchen.
Wechselt der Anwender wieder zu dieser Anwendung, so wird der gespeicherte Zustand
flugs wieder geladen, und für den Anwender sieht es so aus, als ob die Anwendung
niemals beendet worden wäre.
Auch wenn Windows 8.1 (und andere Systeme) natürlich fertige Mechanismen dafür
anbieten, kann man dieses Muster entweder damit kombinieren, oder man implementiert
ein ähnliches Verhalten in einer althergebrachten Desktopanwendung. Ein
Beispiel dafür wäre das Entladen von selten benötigten Plug-ins.

### Weitere Überlegungen und Alternativen
Es sind vor allem Fragen der Implementierung, die bei diesem Muster Fragen aufwerfen.

#### Schmale und breite Schnittstelle
Die zwei unterschiedlichen Schnittstellen zum Memento-Objekt für Aufbewahrer
und Urheber wurden im Beispiel so gelöst, dass die Memento-Klasse selbst privat ist,
wie auch ihr Konstruktor und alle Methoden. Sie implementiert jedoch eine öffentliche
Schnittstelle, sodass das Objekt über diese Schnittstelle gespeichert und weitergereicht
werden kann. Von außen ist also nur diese Schnittstelle sichtbar. Da sie
keine Operationen definiert, kann man mit dem Objekt auch nichts anfangen; noch
nicht einmal der innere Zustand lässt sich auf diese Weise ermitteln. In einigen Sprachen
geht das eleganter. In C++ kann der Urheber zum Beispiel ein friend der
Memento-Klasse sein und dadurch Zugriff auf sie erhalten.

#### Fragen zum Speicherplatz und zur Performance
Das Kopieren des Zustands kostet Ressourcen. Sowohl die CPU als auch der Arbeitsspeicher
werden dadurch belastet. Die Frage ist: Wie umfangreich sind die Zustandsdaten,
und wie oft wird ein neues Memento-Objekt erstellt?
Drei Optimierungen sind augenfällig:
* Anstatt den gesamten Zustand zu speichern, kann man sich auf einen Teil konzentrieren.
In aller Regel gibt es Zustandsinformationen, die flüchtig sind, also nicht
wiederhergestellt werden müssen (zum Beispiel GUIDs).
* Bei sehr umfangreichen Zuständen lohnt sich vielleicht der zusätzliche Aufwand,
die einzelnen Mementos nicht vollständig, sondern inkrementell zu erstellen. Bei
den schon erwähnten Savepoints ist das sogar gang und gäbe.
* Klassisches Undo und Memento lassen sich auch kombinieren. Bei Befehlen, die
durch einfache Umkehrung rückgängig gemacht werden, führt das Befehlsobjekt
die Umkehr aus, sonst weist es den Urheber an, den Zustand wiederherzustellen.

### Weitere Details in der Implementierung
Das Speichern von Zuständen und das Wiederherstellen münden oft in neue Objektreferenzen.
Im Beispiel täten wir gut daran, die equals-Methode so zu überschreiben,
dass zwei Spielfiguren nicht nur identisch sind, wenn sie auf dasselbe Objekt zeigen,
sondern auch, wenn es gleiche Typen (Turm = Turm) sind, die zum selben Spieler
gehören und die auf demselben Feld auf dem Schachbrett stehen.

## Observer

Das nächste Muster, das Beobachter-Muster, ist ebenso sehr ein Klassiker wie das
Länderspiel Deutschland gegen Argentinien und gehört zu den am meisten verwendeten
Mustern überhaupt. Es verkörpert die alte Hollywood-Regel: Don’t call us, we call you, indem es zuvor registrierte Objekte über Änderungen am eigenen Zustand
informiert, damit diese ihren eigenen Zustand daraufhin anpassen können. Das
Objekt »ruft also zurück«, und die interessierten Objekte müssen bis dahin nur
darauf warten.

### Beschreibung
Auf welche Weise können zwei (oder mehrere Objekte) miteinander so kommunizieren,
dass bei einer Änderung des Zustands eines Objekts andere Objekte wiederum
ihre Zustände ändern können?
Sagen wir, das eine Objekt wäre ein Datumsauswahl-Control, und zwei weitere
Objekte, ein Kalender und die Aufgabenliste, zeigen ihre Daten für das jeweils ausgewählte
Datum. Ändert sich das Datum (der Zustand des Objekts Datumsauswahl) so
sollen sich auch die Zustände der beiden anderen Objekte ändern (die von ihnen
angezeigten Daten).
Die Softwareentwicklung kennt vier Wege (bzw. vier übliche Wege), um die Abhängigkeit
von Objekten zu bewerkstelligen (siehe Abbildung 4.28).
1. Push: Das Objekt Datumsauswahl informiert direkt und ohne Aufforderung die
Objekte Aufgaben und Kalender. Das kann einfach dadurch geschehen, dass die
entsprechenden Methoden aufgerufen werden. Das ist einfach, schnell und effizient,
setzt aber voraus, dass die zu informierenden Objekte bekannt sind und sich
nicht mehr ändern – zwei gewichtige Annahmen.
2. Pull (Polling): Die beiden abhängigen Objekten fragen beim Objekt Datumsauswahl
das gerade eingestellte Datum ab und reagieren dann darauf. Beim Polling
geschieht das in (meist regelmäßigen) Intervallen. Beispiel: Polling des Lieferstatus
einer Bestellung alle vier Stunden über eine Webseite des Versenders. Der
Begriff Pull ist allgemeiner und legt das Vorgehen nicht weiter fest. Beispiel: Beim
Öffnen eines Formulars wird der zentral eingestellte Mandant abgefragt, und das
Formular zeigt dann dessen Daten an.
3. Publish/Subscribe: Bei diesem Kommunikationsmuster wird die Abhängigkeit
umgekehrt. Interessierte Objekte, also Aufgaben und Kalender, registrieren ihren
Wunsch, bei Änderungen benachrichtigt zu werden (Subscribe). Ändert sich dann
der Zustand (wird also ein anderes Datum ausgewählt), dann informiert das
Objekt Datumsauswahl alle registrierten Subscriber (Publish). Eingedeutscht kann
man auch von »Publizierer« und »Abonnent« sprechen.
4. Message Bus: Der Nachrichtenbus funktioniert auch nach dem Publish/Subscribe-
Verfahren, allerdings registrieren sich interessierte Objekte nicht direkt bei
einem Publisher, sondern beim Nachrichtenbus. Dieser arbeitet dann als flexible
Verteilstation für Nachrichten.



Das Beobachter-Muster ist ein Muster der objektorientierten Programmierung,
beschäftigt sich also mit Klassen. Mit ihm lässt sich das Publish/Subscribe-Kommunikationsmuster
umsetzen, wobei das Kommunikationsmuster natürlich weiter
geht und auch in verteilten Szenarien anwendbar ist. Das Muster ist natürlich nicht
die Kommunikationsform, wie sich schon am Nachrichtenbus zeigt, daneben gibt es
noch andere Implementierungen.


Die Voraussetzungen dieses Muster sind:
* Es gibt ein Objekt (das Subjekt), dessen Zustand sich ändern kann.
* Und es gibt wenigstens ein Objekt (den Beobachter), der sich für diese Zustandsänderung
interessiert.
* Idealerweise gibt es mehrere dieser Objekte und sie sind dem Subjekt nur zur Laufzeit
bekannt. Das Subjekt weiß also vorher weder, welche Objekte es benachrichtigen
soll, noch ob es überhaupt Abonnenten gibt.

### Anwendungsfälle
Gesucht sind Anwendungsfälle, bei denen Objekte es selbst in der Hand haben sollen,
ob und welche Änderungen sie abonnieren und was sie daraufhin tun, sodass Subjekte
und Beobachter nur lose miteinander gekoppelt sind.
Lose ist dabei relativ, denn natürlich muss der Kalender die Datumsauswahl kennen,
er muss ja das aktuelle Datum abrufen können; aber umgekehrt ist das eben nicht
mehr notwendig. Zur Entwicklungszeit ist ein Zugriff der Klasse Subjekt auf einen
konkreten Beobachter nicht mehr notwendig, nur die Schnittstelle Beobachter muss
sie noch kennen.
Es kann auch mehrere Ereignisse geben, die einen Beobachter interessieren. Das
Muster eignet sich also auch für die Fälle, in denen Beobachter selektiv Ereignisse
abonnieren und wieder abbestellen sollen.

Einige Beispiele:
* Die klassischen Event-Mechanismen vieler Programmiersprachen arbeiten nach
diesem Muster oder doch ähnlich – nur die Syntax ist verschieden, und die Sprache
bzw. das Laufzeitsystem kümmert sich um die Benachrichtigung.
* Grafische Elemente einer Benutzeroberfläche nutzen häufig dieses Muster, weil
die Änderung an einem Control eigentlich immer zu Änderungen an anderen
Controls führt oder daraufhin Geschäftslogik ausgeführt werden soll.
* Das Muster Model View Controler (MVC) arbeitet auf diese Weise, wobei die Views
die Beobachter sind, die auf Änderungen an den Modellen (den Subjekten) reagieren
müssen, indem sie deren Daten zur Anzeige bringen.

#### Multicast und Broadcast
Das Beobachter-Muster verlangt eine vorherige Anmeldung, und ein Ereignis kann
beliebig vielen »Teilnehmern« (also Beobachtern) mitgeteilt werden. Das erfüllt die
Definition des Begriffs Multicast, und tatsächlich werden Ereignisse in C# über Multicast-
Delegates abgebildet.
Eine Alternative dazu sind Broadcast-Szenarien, bei denen keine vorherige Anmeldung
notwendig ist, wie bei der Übertragung von Videostreams über eine Satellitenanlage. Broadcasts erfolgen häufig parallel, während die Multicast-Kommunikation
(zumindest bei diesem Muster) seriell erfolgt.
Weil es gelegentlich zu Missverständnissen führt: Das Beobachter-Muster unterstützt
im Standard nur Multicasts, wenn auch in der Praxis für diese Art der Kommunikation
dennoch der Begriff Broadcast verwendet wird.


#### Benachrichtigungsreihenfolge
Die Implementierung unseres Beispiels benachrichtigt diejenigen Beobachter zuerst,
die sich zuerst angemeldet haben – und ist damit in guter Gesellschaft. Dennoch
kann man davon abweichen. So könnte einer Anmeldung eine Priorität zugeteilt
werden, nach der bei der Benachrichtigung sortiert werden kann, oder es gibt
geschäftliche Gründe, einige Beobachter zeitlich vorzuziehen.
Die Benachrichtigung erfolgt im Beispiel außerdem sequenziell, was nicht immer der
Fall sein muss. Es kommt durchaus vor, dass eine parallele Verarbeitung (also einen
parallele Benachrichtigung von Beobachtern) stattfindet.


#### Mehrere Subjekte, mehrere Beobachter
Mehrere Beobachter für ein Subjekt kennen wir. Aber es kann auch der Fall eintreten,
dass ein Gesamtzustand über mehrere Subjekte verteilt ist. Nehmen wir an, für
Kalender und Aufgabenliste gäbe es mehrere Filterobjekte (Subjekte):
 Datumsauswahl
 Postfach
 Gelesen/Ungelesen
Hier macht es endgültig keinen Sinn mehr, dass ein Beobachter (z. B. der Kalender)
einzelne Abonnements für Ereignisse bei allen drei Subjekten abschließt und bei
jeder Änderung seinen Inhalt neu lädt.
Nein, etwas Leistungsfähigeres muss her, etwas, das auf die Schaltfläche »Filter
anwenden« hört. Man nennt ein solches Objekt einen Änderungsmanager, und er
betritt immer dann die Bühne, wenn die Beziehungen zwischen Subjekten und Beobachtern
komplexer werden, sodass die Logik der Benachrichtigung in diese eigenständige
Klasse ausgelagert werden muss. Diese Komplexität kann auch ein
einzelnes Subjekt erreichen, wenn sein innerer Zustand eben komplex ist. Sie ist aber
sicher erreicht, wenn mehrere Subjekte gemeinsam einen für den Beobachter interessanten
Zustand bilden.

Die Abfolge der Ereignisse:
1. Ein Beobachter meldet sich weiterhin bei »seinem« Subjekt für die Ereignisse an,
die ihn interessieren. Um den Änderungsmanager muss er sich nicht kümmern, ja
er muss ihn noch nicht einmal kennen.
2. Das Subjekt verwaltet jetzt aber nicht mehr die An- und Abmeldungen selbst, sondern
delegiert diese Aufgabe an den Änderungsmanager.
3. Nun tritt ein Ereignis ein. Der Zustand des Subjekts ändert sich, und es wird folgerichtig
die Benachrichtige-Funktion aufgerufen.
4. Auch dieser Aufruf wird an den Änderungsmanager delegiert.
5. Dieser ruft nun die Aktualisiere-Methoden der Beobachterobjekte auf, wenn er
das für angezeigt hält.


Ein Änderungsmanager kümmert sich also um so Einiges:
* Er verwaltet Subjekte und Beobachter und übernimmt die An- und Abmeldung.
* Er entkoppelt Subjekt und Beobachter weiter voneinander, weil die Subjekte jetzt
ihre Beobachter nicht mehr kennen müssen.
* Er aktualisiert alle Beobachter, wenn vom Subjekt gewünscht.
* Und er setzt dafür eine Aktualisierungsstrategie um, bildet also Geschäftslogik ab.


Die Logik steckt nun also im Änderungsmanager, wobei das Muster vorsieht, dass es
auch hier die abstrakte Basisklasse AenderungsManager gibt. Darauf können Sie getrost
verzichten, wenn es in Ihrer Anwendung nur einen davon gibt, was häufig der
Fall sein wird.
Und doch: Die konkreten Änderungsmanager haben schon ihren Sinn, denn es
könnte ja verschiedene Implementierungen geben, die jeweils eigens festlegen, wann und unter welchen Umständen die Beobachter informiert werden sollen. Für
jede Implementierung gäbe es dann einen Änderungsmanager. Ein Beispiel dafür
wäre ein Szenario, in dem einzelne Objekte weit entfernt gehostet wären, sagen wir in
Singapur. Ein Änderungsmanager könnte sich dann um die Objekte in der Nähe
kümmern, die alle Benachrichtigungen sofort erhalten, und ein zweiter Änderungsmanager
könnte Ereignisse sammeln und in Blöcken über die Leitung senden.


Ähnliche und ergänzende Muster
Es gibt eine ganze Reihe von Alternativen zu diesem Muster, oder sagen wir besser:
andere Technologien. Denn das Beobachter-Muster ist eben ein Muster, das auf Klassen
und ihren Beziehungen aufbaut. Die hinter dem Muster stehenden Prinzipien
greifen weiter, auch über Komponenten und Systemgrenzen hinweg – dennoch
bleibt einiges des hier Gesagten auch für solche Fälle gültig. Die folgende Liste stellt
einige Technologien und Muster vor, die das Beobachter-Muster ergänzen oder ablösen
können:
* Vom Nachrichtenbus (Message Bus) war schon die Rede. Dort lauschen mehrere
Anwendungen bzw. Komponenten (als Beobachter) an einem gemeinsam genutzten
Bus. Viele Implementierungen erlauben es auch bei dieser Technologie, dass
Beobachter die Ereignisse spezifizieren können, die sie interessieren.
* Nachrichtenwarteschlangen (Message Queues) können zwei Systeme dergestalt
lose koppeln, dass Subjekte die zu verteilenden Nachrichten (Ereignisse) in Warteschlangen
einreihen, von wo sie aus weiter verteilt oder direkt von Beobachtern
abgerufen werden können.
* Die Eventmodelle gängiger Programmiersprachen bieten sich natürlich an, oder
die Technologie verlangt dies sogar, wie das im Falle der meisten UI-Technologien
und Frameworks (wie Swing, WPF oder WinForms) der Fall ist.
* Callbacks sind meist Methoden, die als Parameter einem anderen Objekt übergeben
werden. Hat dieses Objekt seine Arbeit erledigt, ruft es die übergebene
Methode auf.
* Das Vermittler-Muster hat eine gewisse Ähnlichkeit, auch wenn dort das flexible
An- und Abmelden von Ereignissen fehlt.

## State

Das Zustandsmuster ist das Chamäleon unter den Mustern. Mit seiner Hilfe kann
man das Verhalten eines Objekts zur Laufzeit ändern, und zwar immer dann, wenn
sich der Zustand des Objekts ändert.

### Beschreibung
Dass sich der Zustand eines Objekts verändert, ist die Regel, auch ohne dieses Muster.
Klassen besitzen einen inneren Zustand, und die Kapselung sorgt dafür, dass dieser
Zustand sorgsam nach außen abgeschirmt wird.
Auch dass sich das Verhalten eines Objekts ändert, wenn sich sein Zustand ändert, ist
nur natürlich; wozu sollte man sonst den Zustand speichern wollen? Was ist nun also
das Besondere an diesem Muster?
Für gewöhnlich ändert sich das Verhalten eines Objekts, wenn sich sein Zustand
ändert, indem an irgendeiner Stelle im Code eine Fallunterscheidung getroffen wird,
zum Beispiel durch so eine Zeile wie:

    if (einePrivateVariable > einVergleichswert) { … }  

Das reicht oft aus, und grundsätzlich kann man damit auch alles umsetzen. Allerdings
ändert sich manchmal ein Zustand so grundlegend, dass man sich wünschen
würde, man hätte ein Objekt einer anderen Klasse. Und genau das leistet dieses Muster:
Wenn sich der Zustand ändert, wird das Objekt ausgetauscht, genauer der Teil,
der den Zustand abbildet.
Betrachten wir das anhand eines Thread-Objekts, das eine Reihe von Zuständen
annehmen kann.
Das Threadobjekt durchlebt während seiner Existenz verschiedene Zustände. Am
Anfang ist es noch nicht gestartet (Unstarted), danach wird es gestartet (Running) und
läuft, bis es sein natürliches Ende findet (Stopped). Es kann auch vorkommen, dass im
Code ein Thread angehalten (WaitSleepJoin) und wieder fortgesetzt wird (Running)
oder dass der Thread abgebrochen werden soll (Abort Requested und Aborted bzw.
Stopped).

Es leuchtet sofort ein, dass ein Thread, der gerade wartet, sich völlig anders verhält als
ein Thread, der läuft oder der schon beendet wurde. Die herkömmliche Art, dies zu
lösen, wären Zustandsvariablen, zum Beispiel ein enum (public enum ThreadState) oder
ein Integer. Und eine Menge Fallunterscheidungen natürlich, z. B. die, dass ein laufender
Thread nicht noch einmal gestartet werden kann oder dass ein Thread, der
abgebrochen werden soll, nicht auf einmal angehalten werden kann. Den entsprechenden
Versuch würden wir vermutlich mit einer Exception quittieren.
Der Nachteil dieses Verfahrens ist seine Komplexität, denn die vielen Fallunterscheidungen
(in der Mehrheit if- und switch-Anweisungen) liegen zerstreut in der Klasse
und machen es schwer, die Klasse zu verstehen. Noch schwieriger wird es, wenn die
Klasse um einen neuen Zustand erweitert werden soll, weil wir dann daran denken
müssen, alle relevanten Stellen mit neuen Fallunterscheidungen zu versehen. Der
klassische Ansatz erschwert also die Wartung, die Testbarkeit und die Wiederverwendbarkeit.
Nicht so bei diesem Muster: Das Zustandsmuster trennt das eigentliche Objekt und
das Objekt, das seinen Zustand verkörpert. Ändert sich der Zustand, wird dieses
Zustandsobjekt ausgetauscht. Da hinter jedem Zustandsobjekt eine Klasse steckt,
wird der Code für jeden Zustand fein säuberlich von dem Code der anderen Zustände
getrennt.
Bevor ich erläutere, wie das genau funktioniert, noch einige Worte zum Thema
Zustände. Zustände werden in der Softwareentwicklung (wie auch sonst in der IT) mithilfe von Zustandsautomaten beschrieben (die auch als endliche Automaten oder
Zustandsmaschinen bezeichnet werden).

Zunächst gibt es die Zustände, wie ich sie schon beschrieben habe. Nicht jeder
Zustand kann ein Startzustand sein (oft gibt es nur einen), und mindestens ein
Zustand (wenn der Automat nicht endlos laufen soll) ist ein Terminator, beendet also
die Zustandsmaschine. Zwischen den Zuständen gibt es Zustandsübergänge, und wie
schon im Beispiel der Threads ist nicht jeder Übergang möglich. Auf jeden Fall gibt es
aber Bedingungen, die für den Übergang erfüllt sein müssen. Im Beispiel oben ist es
die korrekte Eingabe der PIN.
Wenn ein Zustand »betreten« wird, kann Code ausgeführt werden (die sogenannte
Eingangsaktion), wenn ein Zustand verlassen wird, können Ausgangsaktionen definiert
werden. Daneben gibt es noch einige Kontrollstrukturen, wie die Kreuzung, die
Entscheidung oder die Gabelung. Alle haben das Ziel, den Kontrollfluss zwischen den
Zuständen zu verändern.
Nun will ich gar nicht verhehlen, dass ich eine Reihe von Diagrammen, die einem so
im Alltag begegnen, für wenig sinnvoll erachte. Aber es ist auf jeden Fall lohnend,
sich mit Zustandsautomaten und den Zustandsdiagrammen zu beschäftigen. Hat
man sie erst einmal verinnerlicht, erkennt man sie ganz automatisch in vielen alltäglichen
Aufgabenstellungen und kann sie gewinnbringend einsetzen – und dieses
Muster. Daher kommen wir nun wieder zurück zum Zustandsmuster.

### Anwendungsfälle
Damit sich das Zustandsmuster anwenden lässt, sollten einige Voraussetzungen
erfüllt sein:
* Es gibt natürlich erst einmal verschiedene Zustände, die ein Objekt annehmen
könnte.
* Diese Zustände unterscheiden sich nennenswert voneinander, und vor allem
unterscheidet sich das Verhalten des Objekts je nach Zustand.
* Ein Objekt kann zu einer Zeit immer nur in genau einem Zustand sein.
* Idealerweise würden ohne dieses Muster zahlreiche komplexe Fallunterscheidungen
im Code verstreut sein und ihn unübersichtlich und fehleranfällig machen.
* Vielleicht kommen weitere Zustände dazu.
* Oder die Zustände sollen unabhängig voneinander variiert werden können.
Kurz: Es muss sich lohnen, an die Stelle von Fallunterscheidungen eigene Klassen zu
setzen, die ja auch erst einmal implementiert sein wollen.
Oft findet man dieses Muster in rein technischen Klassen, wie
* in der oben schon erwähnten Thread-Klasse,
* in der TCP-Klasse oder in anderen Klassen, die für eine Verbindung (z. B. zu einem
Netzwerkknoten) stehen.


Aber auch Klassen in UI-Frameworks können dieses Muster anwenden. Denken Sie
dabei an Bildschirmobjekte, die ein Anwender modifiziert, und an die verschiedenen
Status, die ein solches Objekt dabei annehmen kann (wird gerade gezogen, ist selektiert
usw.).
Dann gibt es noch die Fälle, in denen der Zustandsautomat Teil der Aufgabenstellung
ist. Ein Klassiker ist der Geldautomat, der eine ganze Reihe von Zuständen annehmen
kann – allerdings sprechen wir hier von einem OO-Muster, und es gibt noch weitere,
umfassendere Ansätze, Statusmaschinen umzusetzen, z. B. Workflow-Systeme, die
Zustandsautomaten unterstützen.
Gut geeignet sind auch Systeme mit einem hohen Interaktionsgrad, zum Beispiel
Spiele, Bedienungspanel von Geräten oder auch Klassen, die mit Authentifizierung
zu tun haben. In beiden Fällen gibt es eigentlich immer Zustände, die sich mit Klassen
gut abbilden lassen.
Eine weitere Kategorie von Anwendungen sind Steuerungssysteme jedweder Art.
Das kann im einfachsten Fall ein Lichtschalter sein (an/aus) oder – ein wenig praxisnäher
– eine Steuerung mit vielen Sensoren, die eine Zustandsänderung bewirken,
und Aktoren, die jeweils in verschiedenen Zuständen sein können. Das Wichtigste bei diesem Muster ist es aber, wachsam zu sein. Denn wie gesagt
erkennt man mit ein wenig Übung in vielen Aufgaben Zustandsautomaten.


### Weitere Überlegungen und Alternativen
Die Zustände in eigenen Klassen abzubilden hat zahlreiche Vorteile, wie wir gesehen
haben. Um einen vollständigen Zustandsautomaten zu implementieren, ist aber
noch mehr nötig bzw. sind einige Überlegungen zu tätigen.


#### Zustandsübergänge
Im Beispiel war die Kontextklasse, also die Klasse Bewerbung, für den Zustandsübergang
zuständig. Welche Zustände möglich sind, wurde in einer Methode geprüft. Das
ist übersichtlich und einfach zu implementieren und zu erweitern – einfach deshalb,
weil es eine zentrale Methode dafür gibt. Die Kontextklasse für die Definition der
Zustandsübergänge verantwortlich zu machen ist also einfach und eignet sich vor
allem dann, wenn die Definitionen eher einfach sind und sich vielleicht nie oder
nicht sehr häufig ändern.
Flexibler ist es allerdings schon, wenn die Zustandsklassen die Übergänge selbst kennen,
weil diese Information dann dort gekapselt ist, wo sie ursächlich hingehört –
welche Folgezustände es gibt, hängt schließlich vom aktuellen Zustand ab. Diesen
Vorteil erkaufen wir allerdings durch eine engere Bindung, denn natürlich müssen
die Zustände dann wiederum ihre Folgezustände kennen.
Wie dem auch sei: An die Stelle einer Definition im Code kann auch eine Tabelle oder
eine andere Datenstruktur treten, die zur Laufzeit geladen und ausgewertet wird.

Was fehlt noch zum Zustandsautomaten?
Das Zustandsmuster hilft dabei, einen Zustandsautomaten zu implementieren,
umfasst aber nicht alle Aspekte eines solchen. Den Zustandsübergang haben wir
ganz einfach als einfache Variablenzuweisung ausgeführt:

    zustand = neuerZustand;

Diese Zuweisung lässt sich nun noch erweitern um:
* Bedingungen für den Zustandsübergang: Ob eine Zustandsänderung möglich ist
oder nicht, kann von dem aktuellen Zustand und auch vom neuen Zustand abhängen.
Beide Zustände können den Übergang also verhindern. Man kann sagen, dass
für den aktuellen Zustand Ausgangsbedingungen erfüllt sein müssen und für den
neuen Zustand Eingangsbedingungen. Umsetzen lässt sich das leicht mit zwei entsprechenden
Methoden für beide Arten von Bedingungen. Im Beispiel könnte der
Zustand Talentpool vorher das Einverständnis des Bewerbers erfordern, in einen
solchen aufgenommen zu werden.
* Ein- und Austrittsverhalten: Der Übergang selbst lässt sich ebenso trennen:
Zuerst wird das Austrittsverhalten des aktuellen Zustands ausgeführt und im Anschluss das Eintrittsverhalten des neuen Zustands. Auch dafür können zwei
Methoden herhalten. Beispiel: Beim Eintreten in einen neuen Bewerberzustand
könnte gleich die Methode benachrichtige aufgerufen werden.
* Initialisierung: Zustände könnten initialisiert werden, häufig im Konstruktor,
aber eine Initialisierung von außen ist möglich. Die E-Mail-Adresse des Bewerbers
für die Benachrichtigung wäre eine solche Information. Sie könnte sowohl im
Kontext gespeichert werden als auch im Zustand selbst – oder in beiden Objekten.


### Atomar
Ein Zustandsautomat ist in genau einem Zustand, also
* weder in keinem Zustand
* noch in einer Art Zwischenzustand
* noch in zwei oder mehr Zuständen.
Das bedeutet, dass auch der Zustandsübergang entsprechend atomar sein muss.
Wird die Eingangsbedingung des neuen Zustands nicht erfüllt, so wird der Übergang
nicht ausgeführt und auch das Eintrittsverhalten des neuen Zustands braucht nicht
ausgeführt zu werden. Was ist aber mit dem Austrittsverhalten des aktuellen Zustands,
oder was ist, wenn es bei der Ausführung desselben zu einem Fehler kommt?
Solche Fragen sind elementar und zuallererst fachlich zu lösen. Bei der Bewerberverwaltung
wollen wir sicherlich nicht, dass ein Bewerber mehr als eine Benachrichtigung
pro Zustandsänderung erhält. Wir werden also eher konservativ sein. Die
technische Umsetzung kann mit Transaktionen geschehen, im Zusammenhang mit
klassischem Exceptionhandling.

#### Zustandsinstanzen
Es gibt zwei Möglichkeiten: Entweder werden die Zustandsobjekte beim Zustandsübergang
jeweils neu erzeugt oder die Objekte werden wiederverwendet, wie übrigens
auch im Praxisbeispiel mittels der Enum-Implementierung. Im zweiten Fall
lassen sich die Zustandsobjekte erst bei Bedarf, also »lazy« erzeugen oder bereits im
Voraus für alle möglichen Zustände.
Für viele Anwendungsfälle spielt das keine große Rolle. Sind aber sehr viele Kontextobjekte
im Spiel, die noch dazu sehr häufig ihre Zustände wechseln, so spricht das
eher für die Wiederverwendung von Kontextobjekten und das Erzeugen derselben
bereits zu Beginn der Anwendung. Gibt es hingegen sehr viele Zustände, die aber selten
gewechselt werden und sind diese Zustände gar erst zur Laufzeit bekannt, so ist
die Erzeugung des Kontextobjekts auch erst zur Laufzeit sinnvoll.

#### Vererbungshierarchien
Die konkreten Kontextklassen müssen nicht alle direkt von Zustand erben, sondern
können auch komplexeren Hierarchien angehören. Im Beispiel könnten wir gut die
Klasse BenachrichtigungZustand gebrauchen, in der die Kommunikation mit dem
SMTP-Server geregelt wird und die allen Zuständen, die E-Mails schicken können, als
Basisklasse dient.




## Strategy
Das Strategiemuster beschäftigt sich mit Algorithmen, also mit Wegen zum Ziel, und
macht diese auf einfache Weise austauschbar.

### Beschreibung
Manchmal führen viele Wege zum Ziel, und Wege in der Softwareentwicklung sind
Algorithmen, also Verhalten. Welcher Algorithmus zum Ziel kommt, entscheidet
vielleicht der Anwender, oder der Klient wählt den optimalen Algorithmus aufgrund
der konkreten Aufgabenstellung aus.
Einzelne Algorithmen mögen ihre spezifischen Vor- und Nachteile haben. Vielleicht
liefert ein Algorithmus besonders genaue Ergebnisse, benötigt aber viel Rechenzeit
und Arbeitsspeicher, während ein anderer Algorithmus eine gute Näherung mit
wenig Ressourceneinsatz errechnet. Oder Algorithmen unterscheiden sich aufgrund
ihres Outputs, den sie mit demselben Input generieren.
Wie auch immer ein Algorithmus aussieht und wer ihn auch immer auswählen mag:
Eine Software muss natürlich alle infrage kommenden Algorithmen implementieren.
Das Strategiemuster hilft dabei, eine solche Familie von alternativen, austauschbaren
Algorithmen zu definieren und einer Anwendung zur Verfügung zu stellen.
Wie so häufig ist auch hier das Ziel, dass die Festlegung auf einen bestimmten Algorithmus
erst zur Laufzeit erfolgen soll.


Die Vorteile springen ins Auge:
* Die Kontextklasse kann mit der Schnittstelle Strategie arbeiten, ist also nicht von
der konkreten Implementierung abhängig.
* Die Kapselung der Algorithmen in eigene Klassen fördert die Kapselung, macht
den Code übersichtlicher und – grundsätzlich jedenfalls – leichter wart- und
testbar.
* Außerdem sind die Algorithmen auf diese Weise wiederverwendbar, also auch in
anderen Kontexten einsetzbar.
* Neue Algorithmen lassen sich auf einfache Art und Weise hinzufügen. Die Änderungen
am bestehenden Code sind gering.


Dem gegenüber steht auch hier wieder einmal die wundersame Klassenvermehrung:
Aus einer Klasse werden (bei drei Algorithmen) nun fünf Klassen. Der Kontext muss
all diese Klassen kennen (und die Algorithmen, die dort implementiert sind), denn
schließlich muss er ja einen passenden Algorithmus auswählen können.

### Anwendungsfälle
Für dieses Muster müssen nicht viele Voraussetzungen erfüllt sein:
* Zunächst wird eine Aufgabenstellung benötigt, die zu ihrer Erfüllung einen Algorithmus
braucht. Was bedeutet: Ein Teil der Kontextklasse muss sich in Strategieklassen
auslagern lassen.
* Dann muss es nicht nur einen, sondern mehrere Algorithmen geben, aus denen
man einen konkreten zur Laufzeit auswählen kann.


Die Kapselung der jeweiligen Algorithmen in eigenen Klassen verbirgt deren Funktionsweise
nach außen. Die Algorithmen können also umfangreiche Datenstrukturen
benötigen und in ihrer inneren Arbeitsweise so komplex sein, wie sie wollen.
Das Wort Algorithmus ist dabei in manchen Fällen vielleicht ein wenig zu hoch gegriffen
und lässt einen schnell an Mathematik oder höhere Informatik denken. Es genügt
aber, wenn es ein beliebiges Verhalten gibt, das austauschbar sein soll und das
an einer oder mehreren Stellen in der Kontextklasse benötigt wird.
Diese Voraussetzungen sind schnell erfüllt. Entsprechend gibt es eine Unmenge
möglicher Anwendungsfälle. Um nur einige zu nennen:
* Eine Dokumentenklasse könnte über dieses Muster ihren Inhalt in verschiedenen
Dokumentenformaten speichern (z. B. eine Textdatei in RTF oder Open XML speichern).
* Klassische Algorithmen, wie das Sortieren von Werten oder das Durchsuchen von
Datenstrukturen, lassen sich in Strategieklassen unterbringen.
* Algorithmen, die Werte berechnen (wie bei der oben erwähnten Lagerbewertung)
eignen sich ebenfalls gut für dieses Muster.
* Oder ein Algorithmus transformiert einen Datenstrom. Ein Klient könnte aus verschiedenen
Verschlüsselungsverfahren wählen oder einen Datenstrom packen,
wofür etliche Komprimierungsalgorithmen zur Verfügung stehen.
* Verschiedene Validierungen könnten in Strategieklassen untergebracht werden,
sodass sich zur Laufzeit bestimmen lässt, wie streng eine Eingabe validiert werden
soll.
* Spiele können dieses Muster nutzen, was ja schon der Name verrät.
* Jede Form von Optimierung – seien es die Lagerwege bei der Kommissionierung
von Waren oder die Reihenfolge bei der Bearbeitung von Kreditanträgen – eignen
sich für dieses Muster, wenn es jeweils mehrere alternative Verfahren dafür gibt.
Die verschiedenen Algorithmen können sich in ihrer Ausführungszeit oder im benötigten
Speicherplatz unterscheiden und auch sonst ganz spezifische Vor- und Nachteile
besitzen.


### Weitere Überlegungen und Alternativen
Das Strategiemuster ist breit anwendbar, und folglich gibt es auch einige Alternativen
und Implementierungsvarianten.


#### Die Schnittstelle der Strategieklassen
Im Originalmuster und auch in vielen Beispielen ist die Schnittstelle des Algorithmus
auf nur eine Methode beschränkt. So auch bei uns, wo es nur die Bewerte-
Methode gibt.
Das ist aber keine Einschränkung des Musters. Es kann weitere Methoden geben,
sofern sie spezifisch für den Algorithmus sind. Neben der Bewertung des aktuellen
Lagerbestands ist z. B. eine weitere Methode zur Bewertung der verkauften Lieferungen
denkbar.

Wie ich schon erwähnt habe, können wir dem Algorithmus als Übergabeparameter
all das geben, was er für seine Arbeit benötigt. Das ist eigentlich vorzuziehen: Das
Information Hiding ist schließlich nicht ohne Grund ersonnen worden. Der Algorithmus
könnte auch wie im Beispiel Zugriff auf den gesamten Kontext erhalten, was
sich immer dann anbietet, wenn der Algorithmus häufig erweitert wird oder ohnehin
sehr viele Daten des Kontexts benötigt.
Die Schnittstelle Strategie verlangt, dass alle Algorithmen diese Schnittstelle bedienen.
Es ist durchaus möglich, dass einige dieser Algorithmen nicht alle Daten für
ihre Ausführung benötigen und die Übergabeparameter daher gar nicht verwendet
werden.


#### Unterklassenbildung des Kontexts
Anstatt dieses Muster zu verwenden, hätten wir natürlich auch die Klasse Kontext
(Lager) weiter differenzieren können. Allerdings ist die Bewertung des Lagerbestands
nur eine (kleine) Teilaufgabe innerhalb der umfangreichen Klasse Lager. Es fühlt sich
bereits falsch an, z. B. eine Klasse LagerDurchschnittswert von Lager abzuleiten. Wenn
noch weitere Differenzierungsmerkmale dazukommen (sagen wir, verschiedene
Algorithmen für die Berechnung des Meldebestands), dann wird das Dilemma
schnell klar: Die Algorithmen alle in der Klasse Lager abzubilden, indem man Unterklassen
differenziert, würde schnell eine ganze Menge von Unterklassen erfordern.
Das Strategiemuster hilft hier, weil es einzelne Aspekte der Lagerverwaltung (d. h.
die Algorithmen zur Bewertung des Lagers und zur Ermittlung des Meldebestandes)
in eigene Strategieobjekte verlagert und die Klasse Lager von dieser Komplexität
fernhält.
Vereinfacht kann man es so ausdrücken: Wenn sich nur ein einzelnes Verhalten verändert,
dann ist das Strategiemuster die richtige Wahl. Wenn es aber wirklich verschiedene
Typen des Kontexts gibt, die sich in der Struktur und in vielen Details
unterscheiden, dann sollten Sie die Kontextklasse durch Unterklassenbildung weiter
differenzieren.

## Template Method
Die Schablonenmethode ist eine Alternative zum gerade vorgestellten Strategiemuster,
unterscheidet sich aber in der Granularität. Aber sehen Sie selbst.

### Beschreibung
Während das Strategiemuster einen ganzen Algorithmus abstrahiert und in eigene
Klassen packt, geht es bei diesem Muster um Teile eines Algorithmus. Ein Algorithmus
ist ja eine »Handlungsvorschrift zur Lösung eines Problems«. Dabei gibt es für
einzelne Teile dieser Handlung, des Algorithmus, manchmal durchaus Alternativen.
Der Algorithmus ist dann nur noch ein Rumpf oder ein Skelett, der bzw. das einfach
seine einzelnen Teile in der richtigen Reihenfolge aufruft. Die einzelnen Teile wiederum
sind in Schablonenmethoden definiert, die in Unterklassen definiert sind.
Dabei kann es durchaus vorkommen, dass so ein Teil in einer Implementierung gar
nichts tut und in anderen Implementierung sehr wohl etwas Nützliches verrichtet. In
einem solchen Fall können einem Algorithmus dynamisch, sprich zur Laufzeit, Funktionalitäten
hinzugefügt werden.
Der Kontrollfluss ist dabei umgekehrt. Für gewöhnlich würden die abgeleiteten Klassen
Methoden der Basisklasse aufrufen. Bei der Schablonenmethode, die in der Basisklasse
implementiert ist, ruft eben diese hingegen die primitiven Operationen der
abgeleiteten Klassen auf. Das wiederum ist eine wichtige Voraussetzung für die Wiederverwendung
von Code.
Was dieses Muster von einem einfachen Überschreiben von Methoden unterscheidet,
ist die Tatsache, dass die abstrakte Klasse eben abstrakt ist: Es muss also wenigstens
eine Implementierung geben, also eine Klasse, die davon ableitet.
Voraussetzung für dieses Muster ist es außerdem, dass sich ein Algorithmus überhaupt
sinnvoll in Teilschritte zerlegen lässt, für die dann jeweils mehrere Implementierungen
vorhanden sind. Den Algorithmus können wir als generischen Algorithmus
bezeichnen und die dafür nötigen Teile als Schritte des Algorithmus.
Auch dieses Muster sollten wir nicht zu hoch hängen. Ein Algorithmus kann auch nur
aus zwei Schritten bestehen, die erst einmal ohne Funktion sind und erst durch eigene
Ableitungen der Klasse mit Leben gefüllt werden.


Das ist fürwahr nicht kompliziert, und es wäre nicht verwunderlich, hätten Sie dieses
Muster ganz allein »entdeckt«, also ohne von seiner Existenz zu wissen.
Die abstrakte Basisklasse kann vier Arten von Methoden besitzen:
* die Schablonenmethode, die den Algorithmus implementiert
* ganz konkrete Methoden, die von der Schablonenmethode für Teilaufgaben aufgerufen
werden und die nicht dazu gedacht sind, in Unterklassen überschrieben zu
werden, die aber vielleicht von Unterklassen verwendet werden können (falls sie
protected oder public sind)
* virtuelle Methoden, die in der Basisklasse eine Standardimplementierung haben,
die in der Unterklasse aber verändert werden können
* abstrakte Methoden, die in den Unterklassen daher unbedingt implementiert werden
müssen


### Anwendungsfälle
Die Schablonenmethode verwende ich in meiner eigenen Praxis recht häufig. Das
liegt an ihrer universellen Natur, denn es gibt nur wenige, allgemeine Voraussetzungen
für dieses Muster:
* Wir benötigen zunächst einen generischen Algorithmus, der ein allgemeines Verhalten
implementiert.
* Und wir brauchen wenigstens ein Verhalten, das in abgeleiteten Klassen variiert
werden kann (aber vielleicht nicht unbedingt variiert werden muss).


Das klingt vielleicht ein wenig abstrakt, aber es gibt einen einfachen Anwendungsfall:
Stellen Sie sich einen Algorithmus vor, in dem Sie einem Entwickler die Möglichkeit
geben wollen, an bestimmten Stellen einzugreifen, also zum Beispiel vor der
Ausführung und nach der Ausführung eigenen Code unterzubringen. Am Anfang
und am Ende werden also zwei Methoden aufgerufen (sagen wir, die Methoden Start
und Ende), die beide abstrakt sind, also in der (gleichfalls abstrakten) Basisklasse ohne
Implementierung sind.
Eine konkrete Klasse kann dann keine, eine oder beide Methoden implementieren
oder eben leer lassen, wenn gar nicht gewünscht ist, an den entsprechenden Stellen
Code auszuführen.
Ganz natürliche Kunden dieses Musters sind daher Klassenbibliotheken, die ihren
Entwicklern an genau vorherbestimmten Stellen Erweiterungspunkte bereitstellen
wollen.

Einige Kandidaten für die Schablonenmethode sind:
* Algorithmen, die Daten verarbeiten, wobei die konkrete Verarbeitung erst in den
konkreten Klassen definiert wird. Der Algorithmus kann dann selbst die Daten
laden und ruft anschließend die konkrete Verarbeitungsmethode der Unterklasse
auf.
* Algorithmen, die etwas ausführen, wobei ein Entwickler vor und nach der Ausführung
durch Ableitung eigenen Code einbringen kann. Man nennt solche Methoden
manchmal auch Einschubmethoden.
* Algorithmen, die etwas erstellen, wobei einzelne Teile der zu erstellenden Datenstruktur
erst in Unterklassen erstellt werden

Erkennen können Sie dieses Muster zum Beispiel, wenn Sie in zwei Klassen ein
nahezu identisches Verhalten implementieren und Sie die Unterschiede klar benennen
können.


### Weitere Überlegungen und Alternativen
Die Schablonenmethode ist natürlich ein ziemlich einfaches Muster. Daher gibt es
weniger zur Implementierung als zum Einsatz zu sagen.


#### Weniger ist mehr
Der umgedrehte Kontrollfluss – die Basisklasse ruft die Methode der Unterklasse auf
und nicht umgekehrt – verlangt vom Entwickler der Unterklasse häufig, dass er den
Algorithmus verstanden hat, der ja in der Schablonenmethode der Basisklasse zu finden
ist. Bei Einschubmethoden ist es schon wichtig, an welcher Stelle die Einschubmethode
aufgerufen wird.
Oder betrachten wir das Stream-Beispiel: Die write-Methode erhöht einen Zähler; die
konkrete Klasse ByteArrayOutputStream verwaltet also selber die Anzahl der gerade im
Byte-Array enthaltenen Bytes. Außerdem überschreibt sie die Schablonenmethode
der Basisklasse, weil sie lieber arraycopy verwendet, anstatt jedes Byte eigens wegzuschreiben
– wie es der Algorithmus der Basisklasse vorsieht.
Dafür muss sie natürlich den Algorithmus kennen, wie er in der Basisklasse Output-
Stream umgesetzt wurde. Im Falle des Java-Frameworks ist das freilich kein Problem,
ist doch dasselbe Unternehmen für beide Klassen verantwortlich. Das mag in Ihren
eigenen Projekten anders sein, wenn Sie Unterklassen von Basisklassen bilden, die
Sie nicht selbst geschrieben haben.
Kurz: Sie sollten die Anzahl der primitiven Operationen, die in Schablonenmethoden
verwendet werden, gering halten, denn
* jede abstrakte primitive Operation zwingt die Unterklasse zur Implementierung,
erhöht also den Aufwand.
* eine Unterklasse könnte auch Dinge tun, die nicht im Sinne des Erfinders waren
und so Stabilität oder Laufzeit negativ beeinträchtigen.
* wie Sie gesehen haben, ist häufig ein Grundverständnis der Arbeitsweise einer
Basisklasse mit Schablonenmethoden nötig.


#### Dokumentation
Wenn Sie Schablonenmethoden einsetzen, dann ist es schon nützlich, wenn
* die Methoden sauber benannt sind, beispielsweise durch ein Präfix, das die Ausführungsreihenfolge
anzeigt (before… oder after…).
* der Algorithmus in der Schablonenmethode dokumentiert ist, sodass der Entwickler
der Unterklassen weiß, wie der Algorithmus inklusive seiner eigenen Methoden
aussieht.
* Warnhinweise auf mögliche Risiken oder Laufzeitprobleme hinweisen, zum Beispiel
wenn einzelne Methoden nur sehr kurz sein dürfen.

#### Ähnliche und ergänzende Muster
Das Strategiemuster kann den gesamten Algorithmus durch Unterklassenbildung
austauschen, während die Schablonenmethode nur Teile eines Algorithmus variabel
macht.


## Visitor
Nach den recht einfachen Mustern aus den letzten beiden Abschnitten ist das Besuchermuster
wieder ein Jota komplexer, auch wenn ich die Meinung, das Besuchermuster
sei das komplexeste Muster von allen, nicht teilen mag. Im Kern geht es
darum, eine Klassenhierarchie und die Operationen voneinander zu trennen, die auf
die einzelnen Klassen dieser Hierarchie angewendet werden können.


### Beschreibung
In einer Klassenhierarchie werden natürlich alle Member nach unten vererbt, wo sie
überschrieben werden können. Welche Methode dann konkret aufgerufen wird – die
der Basisklasse oder die überschriebene Methode in der Unterklasse –, das hängt vom
konkreten Typ ab und wird während der Laufzeit (durch dynamische Bindung) ermittelt.
Man nennt das dann polymorphes Verhalten. Eine öffentliche Methode, die in
der Basisklasse definiert wurde, findet sich also auch in allen Klassen darunter, egal
wie tief und weit verzweigt der Vererbungsbaum auch ist. So weit ist das nichts Neues
und für die meisten Fälle auch gut und praktikabel.
Ein Beispiel aus der Welt des Onlinehandels mag das illustrieren. Sagen wir, ein Onlinehändler
würde Bücher, Filme und Spiele verkaufen.

Das Problem bei dieser Klassenhierarchie sind nun die verschiedenen Zuständigkeiten:
Die ersten drei Methoden sind für die Darstellung, also für die Konvertierung der
Daten in verschiedene Darstellungsformate zuständig. Die anderen Methoden haben wiederum alle ganz unterschiedliche Zuständigkeiten. Und wir wissen natürlich,
dass es nicht bei diesen Methoden bleiben wird: Schon morgen soll vielleicht die
HTML-Ausgabe auf verschiedene Ziele (z. B. Mobilgeräte) hin optimiert werden, und
eine weitere Methode soll ein Element im Warenkorb auf die Wunschliste setzen.
Auch wenn es in der Praxis häufig schwer (und manchmal gar nicht) umzusetzen ist,
hat das Single Responsibility Principle (das Prinzip, dass eine Klasse nur eine Aufgabe
erfüllen sollte) durchaus seine Berechtigung. Oder, anders gesagt: Es sollte immer
nur einen Grund geben, eine Klasse anzufassen. Die Einhaltung dieses wichtigen
Design-Grundsatzes führt, ganz allgemein gesprochen, zu gut lesbarem, gut wartbarem,
gut erweiterbarem und gut testbarem Code.
Und genau hierbei hilft dieses Muster: Es trennt die Klassenhierarchie von ihren
Operationen, die stattdessen in eine zweite Hierarchie wandern. Und anstatt die Operationen
direkt auszuführen, erstellen wir ein Objekt von der auszuführenden Operation
(den sogenannten Besucher) und übergeben es dem Objekt der fachlichen
Klassenhierarchie, die dann die Operation aufruft.
Die Schnittstellen der Klassen werden dadurch wieder kleiner, wenn auch auf Kosten
zusätzlicher Klassen.

Noch einmal in Kurzfassung:
* Ein Besucher steht für eine ganz bestimmte Operation, weswegen es für jede Operation
eine eigene Besucherklasse gibt.
* Die Klassen der fachlichen Klassenhierarchie benötigen nur eine Methode, die
einen solchen Besucher entgegennimmt.
* Dort entscheidet die Methode, ob sie den Besuch annehmen möchte. Tut sie das,
ruft sie einfach die richtige Besuche-Methode des Besuchers auf. Da es für jede
fachliche Klasse eine eigene Besuche-Methode gibt, muss sie nur darauf achten, die
richtige aufzurufen.
* Der Besucher verrichtet nun seine Arbeit. Da er eine Referenz auf die fachliche
Klasse besitzt, kann er auf diese zugreifen, damit er den Kontext seiner Ausführung
kennt.


#### Anwendungsfälle
Das Besuchermuster wird gern gemieden, weil ihm der schlechte Ruf der Komplexität
anhängt. Dabei stehen die Chancen gut, dass Sie dieses Muster umso häufiger verwenden,
je mehr Erfahrung Sie damit sammeln konnten. Dann entdecken Sie dieses
Muster immer häufiger in Ihren praktischen Aufgabenstellungen.
Warum, das wird klar, wenn wir uns noch einmal den Hauptvorteil dieses Musters
klarmachen: Mithilfe des Besuchermusters kann man den Elementen einer Struktur
neue Operationen beibringen, ohne diese ändern zu müssen. Die Schnittstellen der
fachlichen Klassen bleiben schlank, die »Concerns« (sprich: Zuständigkeiten) sind
sauber voneinander getrennt.
Da die Besucherklassen eigene Datenstrukturen haben können, lassen sich auf diese
Weise auch viele Elemente nacheinander besuchen und so die Datenstrukturen der
Besucherklassen füllen. Ein Beispiel wäre das Traversieren der Knoten eines Baums,
wobei die Knoten des Baums von ganz verschiedenen Klassen stammen können.
Kurz gesagt lohnt sich das Muster, wenn
* die Operationen verschiedene Zuständigkeiten haben.
* die Operationen leicht zu erweitern sein sollen, ohne dass dafür die Elementklassen
neu übersetzt werden müssten.
* die Schnittstellen der Elementklassen schlank sein (und bleiben) sollen.
* eine Objektstruktur viele Elemente enthält, die von ganz verschiedenen Klassen
stammen, und auf allen diesen Elementen eine Operation ausgeführt werden soll.
* Sie aus anderen Gründen nicht wollen, dass die Elementklassen die Operationen
selbst beinhalten.


Wie andere Muster auch bricht das Besuchermuster die statische Bindung auf, die
nötig ist, wenn eine Methode direkt auf der Elementklasse aufgerufen wird. Stattdessen
wird ja immer dieselbe Methode, nimmEntgegen, aufgerufen, sodass die Bindung
daran zur Laufzeit erfolgen kann, weil als Parameter dieser Methode der Besucher
übergeben wird. Praktisch gesehen führt das beispielsweise dazu, dass über Plug-in-
Schnittstellen zur Laufzeit neue Operationen hinzugeladen werden können und dass
ein Neukompilieren der Elementklassen nicht mehr nötig ist, wenn neue Operationen
dazukommen.

Demgegenüber sollte man Folgendes abwägen:
* Die Komplexität steigt erst einmal, weil neue Klassen benötigt werden, ja sogar
eine ganze Klassenhierarchie.
* Wenn es viele Elementklassen gibt, so werden die Besucher sehr unübersichtlich,
weil es ja eine besuche-Methode für jede Elementklasse gibt – dazu später aber
noch mehr.
* Bei jeder neuen Elementklasse muss man die Besucherklasse anpassen. Dort
kommt eine neue abstrakte Methode hinzu, und damit auch alle konkreten
Besucherklassen.


Praktischerweise lohnt sich dieses Muster also vor allem dann, wenn die Elementklassen
konstant bleiben, aber die Operationen häufig erweitert werden oder aber
der Algorithmus der Operationen häufig verändert wird.

Typische Anwendungsfälle für dieses Muster sind:
* Warenkörbe und andere Elementmengen, die über mehrere Operationen verfügen
* Bäume mit verschiedenen Objekttypen, wobei die Knoten in ein anderes Format
konvertiert werden sollen (zum Beispiel Produktbäume oder, etwas abstrakter,
auch der Syntaxbaum eines Compilers)
* grafische Elemente, die auf verschiedene Weisen modifiziert werden können (z. B.
rotieren, verschieben und spiegeln)

### Weitere Überlegungen und Alternativen
Ich habe es schon erwähnt: Die meisten Muster machen den Code erst einmal komplexer,
um ihn letztendlich beherrschbarer zu machen. Da macht auch das Besuchermuster
keine Ausnahme.
Kosten, Kosten, Kosten
Die Kosten für dieses Muster lassen sich wie folgt beziffern:
* Anpassungen, sofern neue Elementklassen dazukommen: a*(n+1) Anpassungen,
wobei a die Anzahl der neuen Elementklassen ist.
* zusätzliche n+1 Klassen, wobei n die Anzahl der zu realisierenden Operationen ist
* Durch die Umkehr des Aufrufverhaltens werden aus einem Aufruf (die Methode
wird direkt auf der Elementklasse aufgerufen) drei Aufrufe. (1. Aufruf der Methode
nimmEntgegen, 2. Aufruf der Methode besuche, 3. gegebenenfalls Aufruf von Methoden
der Elementklasse aus der Besucherklasse)
Das soll heißen: Wenn Sie um Millisekunden feilschen, ist das Muster vermutlich fehl
am Platz.
Den Aufwand, der durch weitere Elementklassen entsteht, kann man gegebenenfalls
dadurch ein wenig abfedern, dass die Basisklasse eine Standardimplementierung zur
Verfügung stellt, sofern das im konkreten Fall möglich ist. Etwas weiter hinten zeige
ich zudem noch eine Variante, die mit Reflection arbeitet und die das Problem weiter
verringern kann.
Ein Aspekt, der bei der Diskussion der Muster eigentlich immer zu kurz kommt, ist
der Aufwand beim Debugging. Auch dieser erhöht sich durch die zusätzlichen Indirektionen
ein wenig, muss man doch gegebenenfalls mehrere Haltepunkte setzen,
um das gewünschte Verhalten zu untersuchen.


#### Hierarchien
Es wäre auch möglich, dass Besucherklassen weitere Unterklassen ausbilden. Man
könnte zum Beispiel eine Klasse DarstellungsBesucher erstellen, von der die drei
Konvertierungen HtmlBesucher, TextBesucher und PdfBesucher ableiten und die gemeinsam
genutzte Funktionen für diese implementiert.
Selbiges gilt auch für die Elementklassen, die in beliebiger Verschachtelungstiefe auftreten
können – Hauptsache, in der obersten Basisklasse gibt es eine abstrakte
Methode nimmEntgegen, die alle Nachfahren auf diese Weise implementieren müssen.
Allerdings könnte man auf diese Basisklasse aber auch verzichten und so Klassen, die
über keinen gemeinsamen Vorfahren verbunden sind, dem Besuchermuster zugänglich
machen. Ganz praktisch gesehen würde man aus der abstrakten Basisklasse
Besucher eine Schnittstelle machen, die keine Vererbung erfordert.


#### Zustand des Besuchers
Ein schönes »Feature« dieses Muster ist es, dass Besucherklassen ihre ganz eigenen
Datenstrukturen aufbauen können – sie kapseln ja den Algorithmus vollständig. Das
bedeutet nun aber auch, dass Besucherklassen Zustände über mehrere Besuche hinweg
ansammeln können, wie bei der Preisberechnung den aktuellen Preis, der
immer der Gesamtpreis aller bisher besuchten Warenkorbelemente ist.


#### Wer durchläuft die Elemente?
Der vorherige Abschnitt bringt uns gleich zur nächsten Frage: Wer ist eigentlich
dafür zuständig, die Elemente zu durchlaufen, um sie zu besuchen und auf diese
Weise den Zustand des Besuchers Element für Element aufzubauen?
Im Beispiel (und häufig auch in der Praxis) ist dies die Klasse ObjektStruktur, also wie
in unserem Beispiel der Warenkorb.

Es sind aber auch noch andere Verfahren denkbar:
* Ein weiteres Objekt, zum Beispiel ein Iterator könnte diese
Aufgabe übernehmen.
* Eine rekursiv arbeitende Methode bei einer Baumstruktur könnte nur den Wurzelknoten
entgegennehmen und selbst alle weiteren Knoten des Baums durchlaufen.
* Man könnte bei besonders komplexen Iterationsalgorithmen das Durchlaufen
selbst auch wiederum als Operation betrachten, für die man einen eigenen Besucher
erstellen könnte, wenn man es auch sonst kompliziert im Leben mag.


#### Enge Kopplung
Besucher- und Elementklassen sind recht eng miteinander gekoppelt. Die Elementklassen
müssen ja auch auf die Besucherklassen zugreifen, um diese zum Besuch einzuladen,
während die Besucherklassen für die Erfüllung ihrer Aufgaben auf die
Elementklassen zugreifen.
Das führt häufig zu öffentlichen Methoden. Das ist aber unschön, wenn vielleicht nur
die Besucherklassen auf diese Methoden zugreifen sollen. Ohne dieses Muster wären
sie einfach privat. Ob das zu lösen ist, hängt von der Programmiersprache ab. C++
kennt friends, also den Zugriff einer befreundeten Klasse (der Besucherklasse) auf
Methoden, die mit private oder protected gekennzeichnet wurden. C# kennt den
Zugriffsmodifizierer internal, der den Zugriff auf Klassen innerhalb derselben
Assembly einschränkt und – wenn auch auf andere Weise – praktisch dasselbe
erreicht.


#### Überladende Methoden
Das Besuchermuster verlangt ja nach einer eigenen Methode für jede Elementklasse.
Im Beispiel haben wir diese dementsprechend benannt, also besucheBuch, besuche-
Film und besucheSpiel. Alternativ hätten wir auch nur einen Methodennamen verwenden
und die Methoden auf diese Weise überladen können:

    besuche(Buch buch)
    besuche(Film film)
    besuche(Spiel spiel)

