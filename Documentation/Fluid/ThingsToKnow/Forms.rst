.. ==================================================
.. FOR YOUR INFORMATION
.. --------------------------------------------------
.. -*- coding: utf-8 -*- with BOM.

.. include:: ../../Includes.txt

Formulare
=========

In diesem Tutorial zeigen wir Euch, wie Ihr mit Extbase und Fluid Formulare erstellen könnt. Wir fangen hier mit
old-school Formularen an, wie viele sie evtl. von früheren Zeiten her kennen. Wir zeigen Euch hier Formulare,
die völlig Fluid fremd sind und wie Ihr Extbase trotzdem dazu bewegen könnt auf diese unkonventionellen Daten
entsprechend zu reagieren. Dabei kommen die wahnwitzigsten Konstrukte zu stande, wie wir sie am Liebsten nie sehen
wollen und doch ist es für so Manchen einmal mehr wichtig zu wissen, was und vor allem wie etwas
innerhalb von Extbase funktioniert.

In den folgenden Beispielen haben wir immer das gleiche Formular mit Feldern, die in das Domainmodel Hotel portiert
werden müssen: Titel, Beschreibung und Räume. Die Abfrage bzgl. dem Pflichtfeld für Titel findet im Controller statt.
Extbaseprofis wissen, dass dies an andere Stelle gehört, aber dazu später mehr.

Fangen wir mit einem echt old-school-Formular an.

Wie man es nicht machen sollte!!!
---------------------------------

Hier das HTML-Gerüst für das Formular::

 <form action="index.php?id=75&tx_sfextbase_extbase[controller]=Hotel&tx_sfextbase_extbase[action]=create" method="post">
   <dl>
     <dt>
       <label for="title"><f:translate key="tx_sfextbase_domain_model_hotel.title" />
         <span class="required">(required)</span>
       </label>
     </dt>
     <dd><input name="title" id="title" value="{newHotel.title}" /></dd>
   </dl>
   <dl>
     <dt><label for="description"><f:translate key="tx_sfextbase_domain_model_hotel.description" /></label></dt>
     <dd><textarea name="description" id="description" cols="40" rows="15">{newHotel.description}</textarea></dd>
   </dl>
   <dl>
     <dt><label for="rooms"><f:translate key="tx_sfextbase_domain_model_hotel.rooms" /></label></dt>
     <dd><input name="rooms" id="rooms" value="{newHotel.rooms}" /></dd>
   </dl>
   <dl>
     <dt>&nbsp;</dt>
     <dd><input type="submit" value="Create new" /></dd>
   </dl>
 </form>

Dieses Formular ist auf Anhieb nicht ungewöhnlich. OK, die UID der Zielseite ist nicht dynamisch und die URI
erscheint etwas arg lang, aber es handelt sich immer noch um ein voll funktionstüchtiges Formular. Schauen wir uns
nun den PHP-Quelltext der createAction im Hotelcontroller an::

 /**
  * action create
  *
  * @return void
  */
 public function createAction() {
   /** @var \SF\Sfextbase\Domain\Model\Hotel $newHotel */
   $newHotel = $this->objectManager->get('SF\\Sfextbase\\Domain\\Model\\Hotel');
   $newHotel->setTitle(\TYPO3\CMS\Core\Utility\GeneralUtility::_POST('title'));
   $newHotel->setDescription(\TYPO3\CMS\Core\Utility\GeneralUtility::_POST('description'));
   $newHotel->setRooms(\TYPO3\CMS\Core\Utility\GeneralUtility::_POST('rooms'));

   // save new hotel if required property title is set
   if ($newHotel->getTitle()) {
     $this->hotelRepository->add($newHotel);
     $this->flashMessageContainer->add('Your new Hotel was created.');
     $this->redirect('list');
   } else {
     $this->flashMessageContainer->add('You have forgotten to set a title.');
     $this->forward('new', 'Hotel', 'sfextbase', array('newHotel' => $newHotel));
   }
 }

Das System funktioniert zwar, aber das Thema Sicherheit wurde hier völlig umgangen. Auch die Art und Weise wie das
Pflichtfeld "Titel" abgefragt wurde ist nicht unbedingt schön gelöst. Was, wenn mal ein paar mehr Pflichtfelder
hinzukommen? Wie groß soll die IF-Anweisung werden? Wer Extbase kennt, würde diese Lösung eher als abenteuerlich
bezeichnen.

Die verbesserte piBase-Variante
-------------------------------

TYPO3 bringt von Haus aus schon ein paar vorgegebene URI-Parameter wie "id", "type" und "M" mit. Damit es kein
Durcheinander zwischen den jeweiligen Extensions und Plugins gibt, haben die TYPO3-Entwickler sich schon sehr
früh für Namespaces in den URI-Parametern entschieden. Diese Namespaces bestehen aus
tx\_[Extensionname]_[Pluginname]. In unseren Beispielen heißt die Extension "sfextbase" und das Plugin "extbase".
Somit lautet der Namespace in unseren Beispielen: "tx_sfextbase_extbase". Extbase kann von Haus aus diesen Namespace
selbständig generieren und somit auf die Variablen in diesem Namespace zugreifen. All dies geschieht hier im
RequestBuilder (extbase/Classes/Mvc/Web/RequestBuilder.php)::

 $pluginNamespace = $this->extensionService->getPluginNamespace($this->extensionName, $this->pluginName);
 $parameters = \TYPO3\CMS\Core\Utility\GeneralUtility::_GPmerged($pluginNamespace);
 $files = $this->untangleFilesArray($_FILES);
 if (isset($files[$pluginNamespace]) && is_array($files[$pluginNamespace])) {
   $parameters = \TYPO3\CMS\Extbase\Utility\ArrayUtility::arrayMergeRecursiveOverrule($parameters,
   $files[$pluginNamespace]);
 }

Wie Ihr seht greift Extbase auch auf die globale Variable $_FILES zu und stellt Euch die Informationen über
hochgeladene Dateien direkt in dem Pluginnamespace zur Verfügung.

Schauen wir uns nun das Formular in etwas überarbeiteter Fassung an::

 <form action="{f:uri.action(controller: 'Hotel', action: 'create')}" method="post">
   <dl>
     <dt><label for="title"><f:translate key="tx_sfextbase_domain_model_hotel.title" /><span class="required">(required)</span></label></dt>
     <dd><input name="tx_sfextbase_extbase[title]" id="title" value="{newHotel.title}" /></dd>
   </dl>
   <dl>
     <dt><label for="description"><f:translate key="tx_sfextbase_domain_model_hotel.description" /></label></dt>
     <dd><textarea name="tx_sfextbase_extbase[description]" id="description" cols="40" rows="15">{newHotel.description}</textarea></dd>
   </dl>
   <dl>
     <dt><label for="rooms"><f:translate key="tx_sfextbase_domain_model_hotel.rooms" /></label></dt>
     <dd><input name="tx_sfextbase_extbase[rooms]" id="rooms" value="{newHotel.rooms}" /></dd>
   </dl>
   <dl>
     <dt>&nbsp;</dt>
     <dd><input type="submit" value="Create new" /></dd>
   </dl>
 </form>

Durch die Verwendung des f:uri-ViewHelpers im action-Attribut des Formulars kann Extbase die Seiten-UID der
aktuellen Seite selbst heraus finden. Die Angaben für Controller und Action werden automatisch in den
Pluginnamespace eingetragen und Ihr müsst weniger schreiben. Den name-Attributen ist nun der Pluginnamespace
vorangestellt und die dazugehörige Action "könnte" so aussehen::

 /**
  * action create
  *
  * @return void
  */
 public function createAction($title, $description, $rooms) {
   /** @var \SF\Sfextbase\Domain\Model\Hotel $newHotel */
   $newHotel = $this->objectManager->get('SF\\Sfextbase\\Domain\\Model\\Hotel');
   $newHotel->setTitle($title);
   $newHotel->setDescription($description);
   $newHotel->setRooms($rooms);

   // save new hotel if required property title is set
   if ($newHotel->getTitle()) {
     $this->hotelRepository->add($newHotel);
     $this->flashMessageContainer->add('Your new Hotel was created.');
     $this->redirect('list');
   } else {
     $this->flashMessageContainer->add('You have forgotten to set a title.');
     $this->forward('new', 'Hotel', 'sfextbase', array('newHotel' => $newHotel));
   }
 }

"könnte" deshalb, weil diese Version zu folgendem Fehler führt::

 The argument type for parameter $title of method SF\Sfextbase\Controller\HotelController->createAction() could not
 be detected.

ALLE Werte von Formularen sind entweder Strings ODER Arrays. Formulare kennen kein Float,
Boolean oder Integer. Damit Extbase weiß, in welches Format ein solcher String konvertiert werden soll, muss diese
Information Extbase mitgeteilt werden. Dies erledigt Ihr innerhalb der PHPDoc einer jeden Actionmethode::

 /**
  * action create
  *
  * @param \string $title
  * @param \string $description
  * @param \integer $rooms
  * @return void
  */
 public function createAction($title, $description, $rooms) {
   /** @var \SF\Sfextbase\Domain\Model\Hotel $newHotel */
   $newHotel = $this->objectManager->get('SF\\Sfextbase\\Domain\\Model\\Hotel');
   $newHotel->setTitle($title);
   $newHotel->setDescription($description);
   $newHotel->setRooms($rooms);

   // save new hotel if required property title is set
   if ($newHotel->getTitle()) {
     $this->hotelRepository->add($newHotel);
     $this->flashMessageContainer->add('Your new Hotel was created.');
     $this->redirect('list');
   } else {
     $this->flashMessageContainer->add('You have forgotten to set a title.');
     $this->forward('new', 'Hotel', 'sfextbase', array('newHotel' => $newHotel));
   }
 }

Die Array-Version
-----------------

Was macht Ihr, wenn der Hotel-Datensatz wächst und Ihr 25 oder sogar 40 Eigenschaften für das Hotel habt? Nach den
Beispielen von oben müsstet Ihr nun die createAction mit 40 Parametern aufrufen. Das wird eine lange Zeile...eine
sehr lange Zeile. Wie bereits oben geschildert, können Formulare Daten als String und auch als Array
übergeben. Letzteres schaut im HTML-Quelltext so aus::

 <form action="{f:uri.action(controller: 'Hotel', action: 'create')}" method="post">
   <dl>
     <dt><label for="title"><f:translate key="tx_sfextbase_domain_model_hotel.title" /><span class="required">(required)</span></label></dt>
     <dd><input name="tx_sfextbase_extbase[hotel][title]" id="title" value="{newHotel.title}" /></dd>
   </dl>
   <dl>
     <dt><label for="description"><f:translate key="tx_sfextbase_domain_model_hotel.description" /></label></dt>
     <dd><textarea name="tx_sfextbase_extbase[hotel][description]" id="description" cols="40" rows="15">{newHotel.description}</textarea></dd>
   </dl>
   <dl>
     <dt><label for="rooms"><f:translate key="tx_sfextbase_domain_model_hotel.rooms" /></label></dt>
     <dd><input name="tx_sfextbase_extbase[hotel][rooms]" id="rooms" value="{newHotel.rooms}" /></dd>
   </dl>
   <dl>
     <dt>&nbsp;</dt>
     <dd><input type="submit" value="Create new" /></dd>
   </dl>
 </form>

Zusätzlich zum Pluginnamespace haben wir nun ein weiteres Element "hotel" im name-Attribut der Inputfelder und
versenden die Hoteldaten somit im Arrayformat. Schauen wir uns wieder die createAction von Extbase an::

 /**
  * action create
  *
  * @param \array $hotel
  * @return void
  */
 public function createAction(array $hotel) {
   /** @var \SF\Sfextbase\Domain\Model\Hotel $newHotel */
   $newHotel = $this->objectManager->get('SF\\Sfextbase\\Domain\\Model\\Hotel');
   $newHotel->setTitle($hotel['title']);
   $newHotel->setDescription($hotel['description']);
   $newHotel->setRooms($hotel['rooms']);

   // save new hotel if required property title is set
   if ($newHotel->getTitle()) {
     $this->hotelRepository->add($newHotel);
     $this->flashMessageContainer->add('Your new Hotel was created.');
     $this->redirect('list');
   } else {
     $this->flashMessageContainer->add('You have forgotten to set a title.');
     $this->forward('new', 'Hotel', 'sfextbase', array('newHotel' => $newHotel));
   }
 }

Zwar funktioniert auch diese Möglichkeit, aber die Werte innerhalb des Arrays sind alle Strings. Wir haben aber nun
mit Hilfe des PHPDoc keine Möglichkeit mehr Extbase mitzuteilen, dass die Räume innerhalb des Arrays ein Integerwert
ist. Dazu gibt es 2 Möglichkeiten.

Datentyp casten
###############

Die wohl langweiligste Version::

 $newHotel->setRooms((integer) $hotel['rooms']);

Array in Objekt konvertieren
############################

In der API von Extbase befindet sich eine Klasse mit dem Namen "DataMapper". Darin ist eine Methode enthalten,
die auf den Namen "map" hört. Hier könnt Ihr die Zielklasse angeben und die Daten als Array,
die in diesen angegebenen Typen konvertiert werden sollen. Die createAction schaut nun so aus::

 /**
  * action create
  *
  * @param \array $hotel
  * @return void
  */
 public function createAction(array $hotel) {
   /** @var \TYPO3\CMS\Extbase\Persistence\Generic\Mapper\DataMapper $dataMapper */
   $dataMapper = $this->objectManager->get('TYPO3\\CMS\\Extbase\\Persistence\\Generic\\Mapper\\DataMapper');
   /** @var \SF\Sfextbase\Domain\Model\Hotel $newHotel */
   list($newHotel) = $dataMapper->map('SF\\Sfextbase\\Domain\\Model\\Hotel', array('0' => $hotel));

   // save new hotel if required property title is set
   if ($newHotel->getTitle()) {
     $this->hotelRepository->add($newHotel);
     $this->flashMessageContainer->add('Your new Hotel was created.');
     $this->redirect('list');
   } else {
     $this->flashMessageContainer->add('You have forgotten to set a title.');
     $this->forward('new', 'Hotel', 'sfextbase', array('newHotel' => $newHotel));
   }
 }

Extbase die Konvertierung überlassen
------------------------------------

Wir haben gelernt, dass Extbase die Werte von Formularen anhand der Angaben im PHPDoc selbst konvertieren kann. Bisher
haben wir dies nur für Strings und Integer ausprobiert, aber auch die komplette Konvertierung eines Arrays zu einem
Objekt kann Extbase für uns abnehmen. Schauen wir uns dazu erstmal wieder das HTML an::

 <form action="{f:uri.action(controller: 'Hotel', action: 'create')}" method="post">
   <dl>
     <dt><label for="title"><f:translate key="tx_sfextbase_domain_model_hotel.title" /><span class="required">(required)</span></label></dt>
     <dd><input name="tx_sfextbase_extbase[newHotel][title]" id="title" value="{newHotel.title}" /></dd>
   </dl>
   <dl>
     <dt><label for="description"><f:translate key="tx_sfextbase_domain_model_hotel.description" /></label></dt>
     <dd><textarea name="tx_sfextbase_extbase[newHotel][description]" id="description" cols="40" rows="15">{newHotel.description}</textarea></dd>
   </dl>
   <dl>
     <dt><label for="rooms"><f:translate key="tx_sfextbase_domain_model_hotel.rooms" /></label></dt>
     <dd><input name="tx_sfextbase_extbase[newHotel][rooms]" id="rooms" value="{newHotel.rooms}" /></dd>
   </dl>
   <dl>
     <dt>&nbsp;</dt>
     <dd><input type="submit" value="Create new" /></dd>
   </dl>
 </form>

Es hat sich kaum etwas geändert, außer, dass unser Array nicht mehr "hotel" sondern "newHotel" heißt. Hier nun die
createAction dazu::

 /**
  * action create
  *
  * @param \SF\Sfextbase\Domain\Model\Hotel $newHotel
  * @return void
  */
 public function createAction(\SF\Sfextbase\Domain\Model\Hotel $newHotel) {
   // save new hotel if required property title is set
   if ($newHotel->getTitle()) {
     $this->hotelRepository->add($newHotel);
     $this->flashMessageContainer->add('Your new Hotel was created.');
     $this->redirect('list');
   } else {
     $this->flashMessageContainer->add('You have forgotten to set a title.');
     $this->forward('new', 'Hotel', 'sfextbase', array('newHotel' => $newHotel));
   }
 }

Extbase erkennt völlig automatisch, dass es sich bei dem Wert vom Formular um ein Array handelt und sucht sich
einen passenden Converter, der für die Konvertierung von Array zu Objekt zuständig ist raus. Der DataMapper aus dem
oberen Beispiel wird also gar nicht mehr gebraucht. Und wieder ist unser Quelltext etws kleiner geworden.

Aber Halt! Extbase wirft eine Fehlermeldung::

 Exception while property mapping at property path "":It is not allowed to map property "title". You need to use
 $propertyMappingConfiguration->allowProperties('title') to enable mapping of this property.

Das hier ist ein Seegen und ein Fluch gleichzeitig. Sobald wir uns im Bereich von Objekten befinden, schaltet Extbase
ein zusätzliches Sicherheitssystem ein. Dieses System muss mit dem neuen Property Mapper konfiguriert werden. Wir
erstellen nun eine Methode, die noch VOR dem eigentlichen Aufruf der createAction ausgeführt wird und teilen dem
neuen Property Mapper mit, dass es erlaubt ist die Eigenschaften title, description und rooms von dem Array
aus dem Formular in unser Objekt zu portieren::

 /**
  * initialize create action
  *
  * @return void
  */
 public function initializeCreateAction() {
   $this->arguments->getArgument('newHotel')->getPropertyMappingConfiguration()->allowProperties('title', 'description', 'rooms');
 }

Falls es zu viele Eigenschaften sein sollten, könnt Ihr auch einfach alle Eigenschaften erlauben::

 /**
  * initialize create action
  *
  * @return void
  */
 public function initializeCreateAction() {
   $this->arguments->getArgument('newHotel')->getPropertyMappingConfiguration()->allowAllProperties();
 }

Bei den Argumenten ($this->arguments) handelt es sich nicht um die Werte, die vom Formular kommen. Extbase ließt mit
Hilfe von Reflection-Klassen Euren Controller und kann heraus finden, was für Parameter Ihr in Eure Methoden
angegeben habt. In unserer createAction zum Beispiel befindet sich der Parameter $newHotel mit dem Datentyp:
\SF\Sfextbase\Domain\Model\Hotel

In diesen Argumenten stehen später auch so Informationen drin wie Standardwert,
Datentyp, Name, Überprüfungsroutinen (Validatoren) und natürlich auch der Wert,
der NACH einer entsprechenden Konvertierung vom Formular übergeben worden ist.

Aber selbst mit diesen Angaben meckert Extbase noch immer::

 Exception while property mapping at property path "":Creation of objects not allowed. To enable this,
 you need to set the PropertyMappingConfiguration Value "CONFIGURATION_CREATION_ALLOWED" to TRUE

Jetzt ist es Extbase zwar erlaubt die Daten aus dem Array in ein Objekt zu übertragen,
aber Extbase darf dieses Objekt nicht erzeugen/anlegen. Auch dafür gibt es wieder eine Einstellung im neuen
Property Mapper. Fügt noch eine weitere Zeile der initializeCreateAction hinzu::

 /**
  * initialize create action
  *
  * @return void
  */
 public function initializeCreateAction() {
   $this->arguments->getArgument('newHotel')->getPropertyMappingConfiguration()->allowProperties('title', 'description', 'rooms');
   $this->arguments->getArgument('newHotel')->getPropertyMappingConfiguration()->setTypeConverterOption('TYPO3\\CMS\\Extbase\\Property\\TypeConverter\\PersistentObjectConverter', \TYPO3\CMS\Extbase\Property\TypeConverter\PersistentObjectConverter::CONFIGURATION_CREATION_ALLOWED, TRUE);
 }

Extbase kann zwar von alleine heraus finden, mit welchem Konverter ein Array in ein Objekt konvertiert werden soll,
aber diese Konverter bringen teilweise auch individuelle Optionen mit. Und, solange diese Optionen nicht explizit
gesetzt worden sind, gelten die Standardwerte, die wie hier ein Anlegen und Bearbeiten von Objekten grundsätzlich
verbieten. Mit der letzten Zeile wird also das Anlegen von Objeten explizit erlaubt.

Ab jetzt klappt auch wieder unser Formular

Der f:form-ViewHelper
---------------------

Wir ersetzen nun im HTML-Quelltext den Formular-Tag mit dem f:form-ViewHelperTag::

 <f:form action="create" method="post">
   <dl>
     <dt><label for="title"><f:translate key="tx_sfextbase_domain_model_hotel.title" /><span class="required">(required)</span></label></dt>
     <dd><input name="tx_sfextbase_extbase[newHotel][title]" id="title" value="{newHotel.title}" /></dd>
   </dl>
   <dl>
     <dt><label for="description"><f:translate key="tx_sfextbase_domain_model_hotel.description" /></label></dt>
     <dd><textarea name="tx_sfextbase_extbase[newHotel][description]" id="description" cols="40" rows="15">{newHotel.description}</textarea></dd>
   </dl>
   <dl>
     <dt><label for="rooms"><f:translate key="tx_sfextbase_domain_model_hotel.rooms" /></label></dt>
     <dd><input name="tx_sfextbase_extbase[newHotel][rooms]" id="rooms" value="{newHotel.rooms}" /></dd>
   </dl>
   <dl>
     <dt>&nbsp;</dt>
     <dd><input type="submit" value="Create new" /></dd>
   </dl>
 </f:form>

Der f:form-ViewHelper hat bereits einen eingebauten UriBuilder an Board. Dadurch reicht es nun, nur noch die Action
"create" anzugeben. Im nächsten Schritt wollen wir durch die Abänderung von den Input-Feldern zu ViewHelpern noch
ein wenig mehr an Quelltext einsparen::

 <f:form action="create" method="post">
   <dl>
     <dt><label for="title"><f:translate key="tx_sfextbase_domain_model_hotel.title" /><span class="required">(required)</span></label></dt>
     <dd><f:form.textfield name="newHotel[title]" id="title" value="{newHotel.title}" /></dd>
   </dl>
   <dl>
     <dt><label for="description"><f:translate key="tx_sfextbase_domain_model_hotel.description" /></label></dt>
     <dd><f:form.textarea name="newHotel[description]" id="description" cols="40" rows="15" value="{newHotel.description}" /></dd>
   </dl>
   <dl>
     <dt><label for="rooms"><f:translate key="tx_sfextbase_domain_model_hotel.rooms" /></label></dt>
     <dd><f:form.textfield name="newHotel[rooms]" id="rooms" value="{newHotel.rooms}" /></dd>
   </dl>
   <dl>
     <dt>&nbsp;</dt>
     <dd><input type="submit" value="Create new" /></dd>
   </dl>
 </f:form>

Die f:form.* ViewHelper arbeiten sehr eng zusammen. So kann der f:form-ViewHelper den Pluginnamespace herausfinden
und die ViewHelper für die Eingabefelder können darauf zugreifen und den Pluginnamespace automatisch dem
name-Attribut voranstellen. Schaut Euch mal den von Fluid generierten HTML-Quelltext an.

Und zu guter Letzt: Die perfekte Harmonie::

 <f:form action="create" objectName="newHotel" method="post" object="{newObject}">
   <dl>
     <dt><label for="title"><f:translate key="tx_sfextbase_domain_model_hotel.title" /><span class="required">(required)</span></label></dt>
     <dd><f:form.textfield property="title" id="title" /></dd>
   </dl>
   <dl>
     <dt><label for="description"><f:translate key="tx_sfextbase_domain_model_hotel.description" /></label></dt>
     <dd><f:form.textarea property="description" id="description" cols="40" rows="15" /></dd>
   </dl>
   <dl>
     <dt><label for="rooms"><f:translate key="tx_sfextbase_domain_model_hotel.rooms" /></label></dt>
     <dd><f:form.textfield property="rooms" id="rooms" /></dd>
   </dl>
   <dl>
     <dt>&nbsp;</dt>
     <dd><input type="submit" value="Create new" /></dd>
   </dl>
 </f:form>

Durch Angabe eines Objektnamens im f:form-ViewHelper könnt Ihr nun innerhalb der Formularfelder mit dem Attribut
"property" arbeiten. Sobald "property" verwendet wird, werden die Attribute "name" und "value" nicht mehr ausgewertet.
Sie sind also ohne Funktion. Die Angabe von "object" hilft die Formularfelder beim Bearbeiten von Datensätzen
zu befüllen. Der hier abgebildete HTML-Quelltext generiert exakt das gleiche HTML wie das Beispiel darüber.

Erst durch diese letzte Änderung haben wir ein voll Extbase kompatibles Formular. Hier nun der von Fluid generierte
Quelltext::

 <form method="post" action="/index.php?id=75&amp;no_cache=1&amp;tx_sfextbase_extbase%5Baction%5D=create&amp;tx_sfextbase_extbase%5Bcontroller%5D=Hotel&amp;cHash=a8517760d81b3e7a2098f0f621e7fb22">
   <div>
     <input type="hidden" name="tx_sfextbase_extbase[__referrer][@extension]" value="Sfextbase" />
     <input type="hidden" name="tx_sfextbase_extbase[__referrer][@controller]" value="Hotel" />
     <input type="hidden" name="tx_sfextbase_extbase[__referrer][@action]" value="new" />
     <input type="hidden" name="tx_sfextbase_extbase[__referrer][arguments]" value="YToyOntzOjY6ImFjdGlvbiI7czozOiJuZXciO3M6MTA6ImNvbnRyb2xsZXIiO3M6NToiSG90ZWwiO30=136ed158da806192818fa30295953198b410dbb8" />
     <input type="hidden" name="tx_sfextbase_extbase[__trustedProperties]" value="a:1:{s:8:&quot;newHotel&quot;;a:3:{s:5:&quot;title&quot;;i:1;s:11:&quot;description&quot;;i:1;s:5:&quot;rooms&quot;;i:1;}}5ceca582502538fa9ca579786b049134ec797b97" />
   </div>
   <dl>
     <dt><label for="title">Title<span class="required">(required)</span></label></dt>
     <dd><input id="title" type="text" name="tx_sfextbase_extbase[newHotel][title]" /></dd>
   </dl>
   <dl>
     <dt><label for="description">Description</label></dt>
     <dd><textarea rows="15" cols="40" id="description" name="tx_sfextbase_extbase[newHotel][description]"></textarea></dd>
   </dl>
   <dl>
     <dt><label for="rooms">Rooms</label></dt>
     <dd><input id="rooms" type="text" name="tx_sfextbase_extbase[newHotel][rooms]" /></dd>
   </dl>
   <dl>
     <dt>&nbsp;</dt>
     <dd><input type="submit" value="Create new" /></dd>
   </dl>
 </form>

Erst durch die Angabe von objectName im f:form-ViewHelper wird auch die interne Variable "__trustedProperties"
gefüllt. Erinnert Ihr Euch noch an das Sicherheitssystem, das wir mit dem neuen Property Mapper konfigurieren
mussten? In dieser Variablen sind nun alle Eigenschaften unseres Objektes bereits enthalten und werden somit völlig
automatisch im Sicherheitssystem erlaubt. Ihr könnt die initializeCreateAction im Controller nun wieder entfernen.

Die Validierung
---------------

Bisher haben wir die Validierung selbst übernommen. Aber auch diese Arbeit wollen wir nun von Extbase machen lassen.
Schauen wir uns folgendes Konstrukt an::

 /**
  * initialize create action
  *
  * @return void
  */
 public function initializeCreateAction() {
   /** @var \TYPO3\CMS\Extbase\Validation\ValidatorResolver $validationResolver */
   $validationResolver = $this->objectManager->get('TYPO3\\CMS\\Extbase\\Validation\\ValidatorResolver');
   /** @var \TYPO3\CMS\Extbase\Validation\Validator\GenericObjectValidator $notEmptyValidator */
   $notEmptyValidator = $validationResolver->createValidator('TYPO3\CMS\Extbase\Validation\Validator\NotEmptyValidator');
   /** @var \TYPO3\CMS\Extbase\Validation\Validator\GenericObjectValidator $genericObjectValidator */
   $genericObjectValidator = $validationResolver->createValidator('TYPO3\CMS\Extbase\Validation\Validator\GenericObjectValidator');
   $genericObjectValidator->addPropertyValidator('title', $notEmptyValidator);
   $this->arguments->getArgument('newHotel')->setValidator($genericObjectValidator);
 }

 /**
  * action create
  *
  * @param \SF\Sfextbase\Domain\Model\Hotel $newHotel
  * @return void
  */
 public function createAction(\SF\Sfextbase\Domain\Model\Hotel $newHotel) {
   $this->hotelRepository->add($newHotel);
   $this->flashMessageContainer->add('Your new Hotel was created.');
   $this->redirect('list');
 }

Die createAction ist nun zwar sehr schlank geworden, aber dafür haben wir wieder eine initializeCreateAction. Sobald
es um das Thema Validierung geht brauchen wir immer den ValidatorResolver. Dieser kann verschiedene Validatoren
erzeugen. Aber das ist noch nicht alles. Er kann sogar die Action-Methoden aus dem Controller auslesen und mit Hilfe
der Informationen aus der PHPDoc völlig automatisch die Validatoren zusammen stellen. Mit einer weiteren Methode kann
der ValidatorResolver auch komplette Domainmodelle scannen und dort für jede Eigenschaft prüfen,
ob im PHPDoc Informationen bzgl. Validierung vorhanden sind. Genau das, wollen wir im nächsten Schritt umsetzen.

Wir ändern im Hotel-Domainmodel die PHPDoc für die Eigenschaft title ab::

 /**
  * Title
  *
  * @var \string
  * @validate NotEmpty
  */
 protected $title;

Durch die Angabe von @validate kann nun ein Validator angegeben werden, der für diese Eigenschaft gültig sein soll.

.. tip::

   Nur wenn Ihr mit Validatoren, Objekten und der Eigenschaft "property" im Formular arbeitet,
   nur dann werden bei Fehleingaben auch die Formularfelder rot hervorgehoben. Die Hervorhebung funktioniert NICHT,
   wenn Ihr mit "name" und "value"-Attributen arbeitet.

Ihr habt nun ein einfaches Extbase kompatibles Formular, habt erste Einblicke in die Verwendung des Property Mappers
erhalten und wisst jetzt, welche Aufgaben Extbase Euch abgenommen hat.
