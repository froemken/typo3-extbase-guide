Unsere erste Extbase Extension
==============================

Die erste Extension - Schritt fuer Schritt
------------------------------------------

Wir müssen uns zu aller erst von dem Gedanken der alten piBase-Programmierung lösen
und begreifen, dass die Programmierung auf Extbase nicht direkt mit Quellcode
beginnt, sondern schon während dem Gespräch mit dem Kunden. Uns als Programmierer
muss klar werden, was der Kunde will. Je besser wir ihn verstehen, desto besser
können wir planen und Planung ist für die Extbaseprogrammierung das A und O.

Zu aller erst benötigen wir also eine Idee. Wie wär's mit einer Produktübersicht?
Ich will auf der ersten Seite meinen Webseitenbesuchern eine Kategorieaufstellung
präsentieren. Pro Kategorie sollen die ersten 3 Produkte, die einer jeden Kategorie
zugeordnet sind mit Bild dargestellt werden. Nach einem Klick auf ein Produkt lande
ich auf der Detailansicht des Produktes und nach einem Klick auf den Kategorienamen
lande ich auf einer Aufstellung aller Produkte, die dieser Kategorie zugeordnet sind.
Je nach Anzahl der Produkte soll eine Seitennavigation eingeblendet werden, mit der
der Webseitenbesucher zu den nächsten 9 Produkten navigieren kann (pageBrowser).

Ein Programmierer sollte dieser Idee folgende Punkte entnehmen können:

* Wir brauchen 3 Ansichten: Kategorie, Produkte pro Kategorie und die
  Produktdetailansicht
* Datenbanktechnisch benötigen wir eine Tabelle Kategorie und eine Tabelle Produkt

Ein Tipp von meiner Seite: Übersetzt alle Namen, Eigenschaften und Klassen ins
Englische. Die deutschen Umlaute machen immer wieder Probleme. Erstens sind sie als
Namen nicht erlaubt und als ae, ue und oe nur schwer lesbar und zweitens machen Sie
auch als Kommentare Probleme, wenn man wie so oft Probleme mit der richtigen
Zeichencodierung von Dateien zu tun hat. Also: Unsere Tabellen heißen demnach
Category und Product.

Den Extension Builder installieren
----------------------------------

Um eine Extension auf Basis von Extbase zu erstellen müsst Ihr den Extension
Builder über den Extension Manager installieren. Ihr erhaltet nach der Aktivierung
dieser Extension einen neuen Eintrag "Extension Builder" in der linken Menüleiste
des TYPO3-Backends sofern Ihr als Administrator angemeldet seid. Nach einem Klick
auf diesen neuen Link landet Ihr auf der Einstiegsseite dieser Extension, die Euch
erst mal ein paar grundsätzliche Dinge erklärt. Über die Selectbox oben könnt Ihr
zwischen der Einstiegsseite und dem Domain Modelling wechseln.

Informationen zur Extension eingeben
------------------------------------

Nach der Auswahl von Domain Modelling aus der Selectbox oben erscheint nun ein
Formular in dem Ihr weitere Informationen zu der Extension angeben könnt und eine
weiße karierte Fläche, wie Ihr sie vielleicht aus dem Matheunterricht kennt. Wie
oben bereits erklärt, werde ich ab jetzt nur noch die englischen Begriffe für die
Erstellung der Extension verwenden.

Als Name geben wir "Product overview" an. Bei dem Key müsst Ihr selbst entscheiden,
ob Ihr diese Extension der Öffentlichkeit über das TER zur Verfügung stellen wollt
oder ob es sich bei der Extension um eine Individualprogrammierung für den Kunden
handelt. Bei der Individualprogrammierung könnt Ihr nahezu jeden beliebigen Key
verwenden während Ihr bei einer Programmierung für die TYPO3-Community erst einen
Key auf www.typo3.org registrieren müsst. Wir werden diese Extension nicht bei TYPO3
registrieren und entscheiden uns deshalb für den Key "productoverview". Die
Beschreibung erklärt, was die Extension macht. Diese Beschreibung erscheint auch im
TYPO3-Backend, wenn Ihr auf "About Modules" klickt. Ich habe mich hier für folgenden
Beschreibungstext entschieden: "This extension gives the customer the possibility to
present his products on the website."

Bei Persons könnt Ihr Personen angeben, die an der Extension mitgewirkt haben. Ich
als Entwickler entscheide mich hier natürlich für meinen eigenen Namen und die Rolle
Developer. Über add könnt Ihr noch weitere Personen hinzufügen.

Immer wenn wir etwas programmieren, das im Frontend sichtbar sein soll, müssen wir
ein Frontend Plugin unserer Extension hinzufügen. Das machen wir über den Link add
und geben unserem Plugin einen Namen (Show products) und einen Key (showproducts).
Der Name erscheint später in der Selectbox in der Ihr die verschiedenen Plugins
auswählen könnt. Der Key hingegen bildet zusammen mit dem Extensionkey einen
eindeutigen Identifier mit dem unser Plugin im TYPO3-System eindeutig identifiziert
werden kann. Der vom Kickstarter eindeutig erstellte Plugin key lautet intern:
productoverview_showproducts.

Unterhalb der Beschreibung gibt es noch einen Link zum Aufklappen weiterer
Extensionkonfigurationen (More options). Wer sich schon mal mit der Datei
ext_emconf.php auseinandergesetzt hat, wird einige Einträge wiedererkennen, aber
für unsere Extension können wir alle Einträge dort unberührt lassen.

Domain Model (Category)
-----------------------

Bevor wir mit dem Domain Modelling starten, könnt Ihr das Projekt über den
save-Button abspeichern. Ihr werdet allerdings direkt mit einer Fehlermeldung
konfrontiert in der Euch mitgeteilt wird, dass das aktuelle Projekt noch keine
Actions enthält. Diese Fehlermeldung ist in meinen Augen für den ersten Einstieg
etwas unangemessen, da wir bis jetzt ja noch gar nicht wissen, was Actions
eigentlich sind. Sowas wie "Achtung! Ihr Projekt besteht derzeit nur aus einer
Grundkonfiguration. Wollen Sie nicht erst mal ein paar Modelle anlegen bevor Sie
speichern?" fänd ich eine ganze Ecke besser.

Das Formular für die Extensionkonfiguration können wir mit dem kleinen Pfeil oben
rechts im Formular schließen und uns nun auf diese karierte weiße Fläche
konzentrieren. Per Drag and Drop können wir uns nun aus dem Kasten "New Model
Object" ein eigenes Domainmodel herausziehen und auf der weißen Fläche platzieren.
Aber was ist ein Domainmodel überhaupt? Kurz gesagt werden uns die Datensätze für
unsere Kategorien und Produkte nicht mehr wie bei piBase über ein Array zur
Verfügung gestellt, sondern es wird für jeden einzelnen Datensatz ein eigenes
Objekt erstellt. Eine genauere Beschreibung findet Ihr im Glossar.

Nachdem wir uns ein Domainmodel aus dem grauen Kasten gezogen haben steht uns nun
ein leeres Domainmodel für unsere Datensätze zur Verfügung. Mit einen Klick auf
(click to edit) können wir unserem Domainmodel einen Namen geben. Geben wir Ihm den
Namen "Category". Doch Vorsicht: Der Name muss immer mit einem Großbuchstaben
beginnen und darf keine Sonderzeichen oder Leerzeichen enthalten.

Nach dem Öffnen der Domain object settings wählen wir Entity aus der Selectbox für
den Object Type aus. Unterschied zwischen Entity und Value object. Für Is aggregate
root müssen wir uns die Frage stellen, ob es sich bei diesem Domainmodel um ein
Elternobjekt mit oder ohne mehrerer abhängiger Untermodelle handelt. In unserem
Falle handelt es sich bei der Kategorie um ein Elternmodel von dem das Kindmodel
Produkt abhängig ist. Es wird in unserem Fall keine Seite geben, auf der ALLE
Produkte abgebildet werden. Wenn, dann werden immer nur Produkte angezeigt, die von
einer Kategorie abhängig sind. Deshalb wird unser späteres Produktmodel kein
Aggregate Root werden. Als Description wählen wir Categories. Es ist immer gut hier
den Plural zu verwenden. Über einen weiteren Klick auf Domain object settings könnt
Ihr den gerade editierten Bereich wieder zusammenklappen.

Klickt nun bitte auf Default actions und markiert die Checkbox list. Damit geben
wir an, dass wir uns für das Kategorienmodel eine Auflistung aller Kategorien
wünschen. Die anderen Checkboxen lassen wir zunächst leer.

Wählt nun den Eintrag Properties und legt eine neue Property mit dem Namen category
an. Im Gegensatz zu den Domainmodels muss der Property name am Anfang immer klein
geschrieben werden. Dieser Name wird später auch für die Erstellung der
Tabellenspalten in der Datenbank verwendet:
tx_productoverview_domain_model_category. Bitte Beachtet, dass sich Großbuchstaben
innerhalb von Property name nicht für die Datenbank verwenden lassen (lower camel
case). Aus diesem Grund werden alle Großbuchstaben in Kleinbuchstaben konvertiert
und ein Unterstrich vorangestellt. Beispiel: Wenn Ihr als Property name
"isWebSiteUserOnline" eintragt, dann wird dieser Name als folgender
Tabellenspaltenname konvertiert:
tx_productoverview_domain_model_is_web_site_user_online.

Aus der Selectbox für Property Type wählen wir den Eintrag String und geben unserer
Property über die Description den Namen Category. Die Description steht später auch
im Frontend als Label oder Überschrift zur Verfügung und kann mit Hilfe der
locallang.xml lokalisiert werden. Markiert noch die Checkbox für Is required, damit
keiner auf die Idee kommt, eine leere Kategorie anzulegen und lasst die Checkbox Is
exclude field zunächst demarkiert.

Domain Model (Product)
----------------------

Widmen wir uns nun dem Domainmodel für unsere Produkte. Wie auch schon bei dem
Domainmodel Category ziehen wir uns ein neues Domainmodel aus dem grauen Kasten
New Model Object. Vergebt den Namen Product. Öffnet die Domain object settings und
lasst die Checkbox Is aggregate root demarkiert, da wir nur Produkte, die von dem
Elternobjekt Category abhängig sind, anzeigen lassen wollen. Als Description
vergeben wir den Plural Products.

Als Actions wählen wir nicht nur list, sondern fügen diesmal auch noch show hinzu,
da wir eine Detailansicht der jeweiligen Produkte planen.

Fügt dem Domainmodel folgende Properties hinzu:

=============  =============  ===========  ===========  ================
Property name	 Property type  Description	 Is required  Is exclude field
=============  =============  ===========  ===========  ================
name	         String         Name         Yes          No
image          Image          Image        No           No
description	   Text           Description  No           No
=============  =============  ===========  ===========  ================

**Table of Contents**

.. toctree::
   :maxdepth: 5
   :titlesonly:
	 :glob:

	 Introduction/Index
	 Chapter1/Index
	 NextSteps/Index
	 Targets
