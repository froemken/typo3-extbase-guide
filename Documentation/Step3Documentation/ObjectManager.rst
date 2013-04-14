.. ==================================================
.. FOR YOUR INFORMATION
.. --------------------------------------------------
.. -*- coding: utf-8 -*- with BOM. ÄÖÜäöüß

.. include:: ../Includes.txt

ObjectManager
=============

Der *ObjectManager* kümmert sich um die Bereitstellung von instanziierten Klassen. Ihr übergebt an den
*ObjectManager* eine Klasse und erhaltet das fertige Objekt zurück. Man könnte sagen,
dass der *ObjectManager* die neue Implementation von t3lib_div::makeInstance() ist. Allerdings mit einem Haufen
weiterer Features.

ObjectMapping
-------------

Ein besonderes Feature ist das *ObjectMapping*. So kann dem *ObjectManager* mitgeteilt werden,
dass bei allen Anfragen an eine bestimmte Klasse ein völlig anderes Objekt zurück geliefert werden soll. Um den Sinn
dieser Möglichkeit besser zu verstehen hier ein Beispiel für eine Dependency Injection::

 /**
  * @param Tx_Extbase_Persistence_Storage_BackendInterface $storageBackend
  * @return void
  */
 public function injectStorageBackend(Tx_Extbase_Persistence_Storage_BackendInterface $storageBackend) {
   $this->storageBackend = $storageBackend;
 }

Hier wird schnell klar, dass etwas nicht stimmt. Denn aus Interfaces lassen sich keine Objekte erstellen.
Normalerweise würde PHP an dieser Stelle Fehler werfen, aber auf Grund einer besonderen Konfiguration innerhalb des
*ObjectManagers* ist diese Schreibweise trotzdem möglich.

In der ext_localconf.php befinden sich ein paar Zeilen, die Klassennamen auf andere Klassennamen mappen::

 $extbaseObjectContainer->registerImplementation('TYPO3\CMS\Extbase\Persistence\QueryInterface',
 'TYPO3\CMS\Extbase\Persistence\Generic\Query');
 $extbaseObjectContainer->registerImplementation('TYPO3\CMS\Extbase\Persistence\QueryResultInterface',
 'TYPO3\CMS\Extbase\Persistence\Generic\QueryResult');
 $extbaseObjectContainer->registerImplementation('TYPO3\CMS\Extbase\Persistence\PersistenceManagerInterface',
 'TYPO3\CMS\Extbase\Persistence\Generic\PersistenceManager');
 $extbaseObjectContainer->registerImplementation
 ('TYPO3\\CMS\\Extbase\\Persistence\\Generic\\Storage\\BackendInterface', 'TYPO3\\CMS\\Extbase\\Persistence\\Generic\\Storage\\Typo3DbBackend');
 $extbaseObjectContainer->registerImplementation('TYPO3\\CMS\\Extbase\\Persistence\\Generic\\QuerySettingsInterface',
 'TYPO3\\CMS\\Extbase\\Persistence\\Generic\\Typo3QuerySettings');

Unter anderem befindet sich hier auch das *BackendInterface*, das auf die Klasse *Typo3DbBackend* gemappt werden soll.
Alle Anfragen, die nun das *BackendInterface* anforder, liefern nun ein Objekt vom Typ Typo3DbStorage zurück.

Falls Ihr den Wunsch habt Euer eigenes *StorageBackend* zu erstellen,
dann könnt Ihr die von Extbase vorgegebene Konfiguration mit Hilfe von ein paar Zeilen im TypoScript
überschreiben::

 config.tx_extbase {
   objects {
     Tx_Extbase_Persistence_Storage_BackendInterface {
       className = Tx_MyOwnExtension_Persistence_Storage_MsSqlBackend
     }
   }
 }

Die Umsetzung für dieses Feature findet Ihr in der Klasse *Tx_Extbase_Object_Container_Container*::

 protected function getImplementationClassName($className) {
   if (isset($this->alternativeImplementation[$className])) {
     $className = $this->alternativeImplementation[$className];
   }
   if (substr($className, -9) === 'Interface') {
     $className = substr($className, 0, -9);
   }
   return $className;
 }

Hier wird auch noch ein weiteres Feature deutlich: Wenn eine Interface wie RepositoryInterface angefordert
wird, dann wird automatisch das Objekt *Repository* geladen.

Caching
-------

Der *ObjectManager* bringt einen 2-fachen Cache mit: Einen first und einen second level Cache. Der 1-level Cache
speichert alle Klasseninformationen, die während des Webseitenaufrufes angefordert werden. Er muss mit jedem
Webseitenaufruf neu aufgebaut werden. Falls eine Klasseninformation in diesem Cache nicht gefunden wird,
dann wird der 2nd-level Cache befragt. Dieser greift auf das TYPO3 CachingFramework zu und versucht die
KLasseninformationen von dort zu laden. Schlägt auch diese Anforderung fehl, dann wird die angeforderte Klasse
analysiert und die gesammelten Informationen in beiden Caches bereit gestellt und stehen somit für spätere Abrufe
direkt bereit.

.. important::

   Wenn Ihr als Entwickler an Euren Klassen arbeitet, dann werden die Namen der "inject"-Methoden serialisiert
   im CachingFramework abgespeichert. Wenn Ihr nun weitere "inject"-Methoden hinzufügt,
   wird der ObjectManager feststellen, dass er die Klasseninformation bzgl. Eurer Klasse bereits hat und wird die
   neue "inject"-Methode nicht ausführen. Um Extbase über diese neue Methode zu informieren müsst Ihr den kompletten
   Cache leeren. Nur dann werden auch alle Daten im CachingFramework entfernt. Alternativ könnt Ihr auch ein TRUNCATE
   auf die Tabellen cf_extbase_object und cf_extbase_reflection machen.

Klasseninformationen
::::::::::::::::::::

Welche Klasseninformation aus den jeweiligen Objekten extrahiert werden,
könnt Ihr der Klasse *Tx_Extbase_Object_Container_ClassInfoFactory* entnehmen::

 $constructorArguments = $this->getConstructorArguments($reflectedClass);
 $injectMethods = $this->getInjectMethods($reflectedClass);
 $injectProperties = $this->getInjectProperties($reflectedClass);
 $isSingleton = $this->getIsSingleton($className);
 $isInitializeable = $this->getIsInitializeable($className);

Zuerst werden die Infomationen zum Constructor der Klasse ausgelesen, wenn vorhanden. Im nächsten Step werden die
Objekte nach Methoden durchsucht, die mit "inject" beginnen. Diese inject Methoden kümmern sich um die Realisierung
des Dependency Injection Features. Hier noch mal das Beispiel von oben::

 public function injectStorageBackend(Tx_Extbase_Persistence_Storage_BackendInterface $storageBackend) {
   $this->storageBackend = $storageBackend;
 }

Dieses Konstrukt liest den Parameter $storageBackend und den Datentyp
*Tx_Extbase_Persistence_Storage_BackendInterface* aus. Mit diesen Informationen wird nun im Hintergrund ein neues
Objekt vom Datentyp *Tx_Extbase_Persistence_Storage_BackendInterface* erstellt. Der *ObjectManager* merkt,
dass er statt *Tx_Extbase_Persistence_Storage_BackendInterface* das *Typo3DbBackend*-Objekt ausliefern muss und weißt
dem Parameter $storageBackend dieses neue Objekt zu. Anschließend wird diese inject-Methode ausgeführt wodurch der
Eigenschaft **$this->storageBackend** das zurückgelieferte Objekt zugewiesen wird.

An dritter Position wird das Dependency Injection Feature auch für entsprechend konfigurierte Eigenschaften
realisiert::

 /**
  * productRepository
  *
  * @var Tx_Productoverview_Domain_Repository_ProductRepository
  * @inject
  */
 protected $productRepository;

Mit Hilfe des *ReflectionServices* wird dieser Kommentar analysiert und Extbase kann dadurch den Variablennamen und
den dazugehörigen Typ auslesen. Mit diesen Infos weist Extbase der Variable $productRepository das benötigte Objekt
direkt zu OHNE Verwendung einer "inject"-Methode.

Als Viertes wird überprüft, ob es sich bei der Klasse um ein SingleTon handelt und zu guter letzt wird die Klasse
nach einer Methode "initializeObject" durchsucht. Wenn vorhanden wird diese Methode unmittelbar nach dem Erzeugen des
Objektes ausgeführt. Also eine verbesserte Variante des Konstruktors.