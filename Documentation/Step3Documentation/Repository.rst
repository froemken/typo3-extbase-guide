.. ==================================================
.. FOR YOUR INFORMATION
.. --------------------------------------------------
.. -*- coding: utf-8 -*- with BOM. ÄÖÜäöüß

.. include:: ../Includes.txt

Repository
==========

Das *Repository* könnt Ihr Euch vorstellen wie eine Onlinevideothek. Ihr wisst nur, dass es Film XY gibt,
aber von wo der Film kommt wisst Ihr nicht. Ihr wisst nicht von welchem Server, von welcher Datenbank oder was auch
immer er kommt. Genau so ist es auch mit dem Repository. Ihr befragt das Repository nach der UID oder dem Namen eines
Filmes und bekommt den Film als Datensatz zurück. An was das Repository angeschlossen ist wisst Ihr nicht.
Möglicherweise kommen die Daten aus einer Redis-Datenbank, oder aus einem XML-File oder im Normalfall aus einer
MySQL-Datenbank.

Extbase ist von Haus aus erstmal an das MySQL-basierte *StorageBackend* angeschlossen. Diese Information könnt Ihr aus
der ext_typoscript_setup.txt von Extbase entnehmen::

 config.tx_extbase {
   objects {
     Tx_Extbase_Persistence_Storage_BackendInterface {
       className = Tx_Extbase_Persistence_Storage_Typo3DbBackend
     }
   }
 }

Hier erhaltet Ihr schon einen kleinen Einblick in die Möglichkeiten des *ObjectMappers*. Überall wo die Klasse
*Tx_Extbase_Persistence_Storage_BackendInterface* angefordert wird, wird statt dieser die Klasse
*Tx_Extbase_Persistence_Storage_Typo3DbBackend* geladen. Auf diese Weise habt Ihr auch die Möglichkeit eigene
*StorageBackends* zu erstellen.

.. important::

   Beachtet bitte das sich die Auswirkungen des *ObjectMappers* immer auf die komplette Seite und Unterseiten
   auswirkt. Wenn Ihr also mehrere Plugins auf einer Seite habt, dann gelten diese Einstellungen immer für BEIDE
   Extensions. Es ist derzeit nicht möglich das *StorageBackend* nur für ein bestimmtes Plugin zu aktivieren. Es sei
   denn es ist das einzige Plugin auf dieser Seite.

Repository im ActionController verfügbar machen
-----------------------------------------------

Im *ActionController* ist nur ein *Repository* eingebunden, wenn Ihr im Extension Builder für ein Domainmodell
angegeben habt, dass es sich um eine *AggregateRoot* handelt. Hab Ihr diese Checkbox nicht gesetzt müsst Ihr das
*Repository* von Hand einbauen. Im *ActionController* tragt Ihr die Eigenschaft ein::

 /**
  * productRepository
  *
  * @var Tx_Productoverview_Domain_Repository_ProductRepository
  * @inject
  */
 protected $productRepository;

Dank der PHPDoc-Annotation @inject wird die Eigenschaft $productRepository automatisch mit einem Repository vom
Datentyp Tx_Productoverview_Domain_Repository_ProductRepository erzeugt. Dieser Datentyp musst auch noch erstellt
werden. Legt dazu die Datei *ProductRepository.php* im Verzeichnis an::

 typo3conf/ext/productsoverview/Classes/Domain/Repository/

Dateiinhalt::

 /**
  * @package productoverview
  * @license http://www.gnu.org/licenses/gpl.html GNU General Public License, version 3 or later
  */
 class Tx_Productoverview_Domain_Repository_ProductRepository extends Tx_Extbase_Persistence_Repository {

 }

.. important::

   Alle von Euch erstellten *Repositories* müssen von dem Objekt *Tx_Extbase_Persistence_Repository* erben.

Arbeiten mit Repositories
-------------------------

Im Folgenden die wichtigsten Methoden aus der *Repository* Klasse

Methode: add
::::::::::::

Fügt ein neues Objekt der Liste, der hinzuzufügenden Objekte, hinzu. Es ist wichtig zu wissen,
dass durch ein bloßes *add* kein Objekt der Datenbank hinzugefügt wird. Das Objekt wird nur für den Speicherprozess
vorgemerkt.

.. tip::

   Wurde das angegebene Objekt bereits der Liste der zu löschenden Datensätze hinzugefügt,
   so wird es mit *add* von der Liste der zu löschenden Datensätze entfernt.

Methode: remove
:::::::::::::::

Entfernt ein Objekt aus der Liste, der zu entfernenden Objekte. Es ist wichtig zu wissen,
dass durch ein bloßes *remove* kein Objekt der Datenbank entfernt wird. Das Objekt wird für den Löschprozess nur
vorgemerkt.

.. tip::

   Wurde das angegebene Objekt bereits der Liste der anzulegenden Datensätze hinzugefügt,
   so wird es mit *remove* von der Liste der anzulegenden Datensätze entfernt.

Methode: replace
::::::::::::::::

Ersetzt ein Object durch ein Anderes. Im Hintergrund wird überprüft, ob das zu ersetzende Objekt in einer der beiden
Listen zum hinzufügen oder löschen von Objekten vorhanden ist. Wenn ja, wird das zu ersetzende Objekt von der Liste
entfernt und das neue Objekt dieser Liste hinzugefügt.

.. important::

   Es wird kein Abgleich der UID der Objekte gemacht.

Methode: update
:::::::::::::::

Hier passiert das Gleiche, wie bei *replace*. Im Hintergrund wird sogar die *replace* Methode aufgerufen,
aber nur, wenn wenn in einer der Listen zum hinzufügen oder löschen bereits ein Objekt mit der gleichen UID vorhanden
ist.

Methode: getAddedObjects
::::::::::::::::::::::::

Liefert alle zum Hinzufügen markierten Objekte in Form eines *ObjectStorage* zurück.

Methode: getRemovedObjects
::::::::::::::::::::::::::

Liefert alle zum Löschen markierten Objekte in Form eines *ObjectStorage* zurück.

Methode: findAll
::::::::::::::::

Liefert alle Objekte aus der Datenbank zurück. Die Einstellungen aus enableFields werden berücksichtigt.

Methode: countAll
:::::::::::::::::

Zählt alle in der Datenbank gefundenen. Die Einstellungen aus enableFields werden berücksichtigt.

Methode: removeAll
::::::::::::::::::

Leert die Liste der hinzuzufügenden Datensätze und LÖSCHT ALLE Datensatze unter Berücksichtigung der enableFields aus
der Datenbank.

Methode: findByUid
::::::::::::::::::

Wenn Ihr wisst, wie die UID des gesuchten Objektes ist, dann erhaltet Ihr mit dieser Methode den gewünschten
Datensatz zurück.

Methode: setDefaultOrderings
::::::::::::::::::::::::::::

Falls angegeben wird die Sortierung aus den TCA-Einstellungen ausgelesen. Falls diese Einstellungen jedoch nicht
gesetzt wurden, oder Ihr für Eure eigene Datenbankabfrage eine andere Sortierung der Datensätze wünscht,
dann könnt Ihr das global für dieses Repository ändern::

 $this->productRepository->setDefaultOrderings(
   array(
     'title' => Tx_Extbase_Persistence_QueryInterface::ORDER_ASCENDING
     'price' => Tx_Extbase_Persistence_QueryInterface::ORDER_DESCENDING
   )
 );

Methode: setDefaultQuerySettings
::::::::::::::::::::::::::::::::

Auch diese Einstellungen, die durch diese Methode erziehlt werden, wirken sich auf ALLE Methoden innerhalb des
aktuellen *Repositories* aus. Ihr könnt mit den *QuerySettings* zum Beispiel bestimmen,
dass der *StoragePid* bei den Queries nicht berücksichtigt werden soll. Außerdem ist es möglich die Query parts für
enableFields und/oder Language komplett von der Query auszuschließen.

Methode: createQuery
::::::::::::::::::::

Falls Euch die Methoden innerhalb des Repositories nicht reichen, könnt Ihr Euch mit createQuery eine eigene Query
holen und dann eine eigene Query zusammen stellen.

Methode: __call
:::::::::::::::

Diese call-Methoden fangen alle aufgerufenen Methoden ab, die sich nicht in der aktuellen Klasse befinden. So ist es
zum Beispiel möglich, die nicht vorhandene Methode findByTitle aufzurufen. PHP merkt das und prüft,
ob sich innerhalb der Klasse eine solche call-Methode befindet. Wenn ja wird diese aufgerufen und dort weiter
verarbeitet.

Auf diese Weise haben die Extbase-Entwickler es geschafft 3 ganz besondere Methoden hinzuzufügen:

Magic Method: findByXXX
***********************

XXX ist ein Platzhalter und kann durch einen Spaltennamen in UpperCamelCase-Syntax ersetzt werden. So würde zum
Beispiel ein Aufruf der nicht vorhandenen Methode *findByTitle('Stefan')* zu einer Query führen,
die alle Datensätze findet, bei der der Titel "Stefan" entspricht.

Magic Method: findOneByXXX
**************************

XXX ist ein Platzhalter und kann durch einen Spaltennamen in UpperCamelCase-Syntax ersetzt werden. So würde zum
Beispiel ein Aufruf der nicht vorhandenen Methode *findOneByTitle('Stefan')* zu einer Query führen,
die den ersten gefundenen Datensatz zurück liefert, bei dem der Titel "Stefan" entspricht.

Magic Method: countByXXX
************************

XXX ist ein Platzhalter und kann durch einen Spaltennamen in UpperCamelCase-Syntax ersetzt werden. So würde zum
Beispiel ein Aufruf der nicht vorhandenen Methode *countByTitle('Stefan')* zu einer Query führen,
die alle Datensätze zählt, bei denen der Titel "Stefan" entspricht.