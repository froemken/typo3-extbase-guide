.. ==================================================
.. FOR YOUR INFORMATION
.. --------------------------------------------------
.. -*- coding: utf-8 -*- with BOM.

.. include:: ../../Includes.txt

Formulare mit DateTime-Objekten
===============================

Dieses Tutorial soll Euch zeigen, wie Ihr Formulare erzeugen könnt, die zu 100% kompatibel mit DateTime-Objekten sind.
Das ist nämlich nicht sehr einfach. Warum? Dies zeigt zum Beispiel das erste Beispiel.

Um möglichst Fluid und Extbase kompatibel zu sein, sollten die Inputfelder mit dem Attribut "property" erstellt
werden::

 <dl>
   <dt><label for="firstName"><f:translate key="tx_sfextbase_domain_model_person.firstName" /></label></dt>
   <dd><f:form.textfield property="firstName" id="firstName" /></dd>
 </dl>
 <dl>
   <dt><label for="lastName"><f:translate key="tx_sfextbase_domain_model_person.lastName" /></label></dt>
   <dd><f:form.textfield property="lastName" id="lastName" /></dd>
 </dl>
 <dl>
   <dt><label for="birthday"><f:translate key="tx_sfextbase_domain_model_person.birthday" /></label></dt>
   <dd><f:form.textfield property="birthday" id="birthday" /></dd>
 </dl>

So schön wie es wäre, wenn dieser Weg funktionieren würde, denn nach Eingabe eines neuen Datensatzes kommt es zu
dieser Fehlermeldung::

 newPerson.birthday: The date "15.12.1980" was not recognized (for format "Y-m-d\TH:i:sP").

Obwohl. Es ist keine Fehlermeldung. Es heißt nur, dass wir das Datum im falschen Format angegeben haben. Denn der
17.01.2013 müsste folgendermaßen eingetragen werden: 2013-01-17T00:00:00+01:00. Denn dies ist das vorgegebene Format,
das im DateTimeConverter vorgegeben ist (extbase/Classes/Property/TypeConverter/DateTimeConverter).

Dieses vorgegebene Format lässt sich mit Hilfe des neuen Property Mappers und einer initializeCreateAction abändern::

 /**
  * initialize create action
  *
  * @param void
  */
 public function initializeCreateAction() {
   $this->arguments->getArgument('newPerson')
     ->getPropertyMappingConfiguration()->forProperty('birthday')
     ->setTypeConverterOption(
       'TYPO3\\CMS\\Extbase\\Property\\TypeConverter\\DateTimeConverter',
       \TYPO3\CMS\Extbase\Property\TypeConverter\DateTimeConverter::CONFIGURATION_DATE_FORMAT,
       'd.m.Y'
     );
 }

Bis jetzt funktioniert das Anlegen von neuen Datensätzen und auch das wieder Anzeigen der falsch eingetragenen
Datumswerte im Formular mit CSS-Errorklasse und rotem Hintergrund.

Was nicht funktioniert ist das Bearbeiten von Datensätzen. Alleine beim Aufruf des Bearbeitungsformulares bleibt das
Datumsfeld leer. Warum? Weil getBirthday() im Domainmodel von Person ein DateTime-Objekt zurück liefert und ein
Objekt lässt sich nicht in einem Textfeld anzeigen. Dieses Problem führt in der Community zu folgendem Lösungsansatz::

 <dl>
   <dt><label for="firstName"><f:translate key="tx_sfextbase_domain_model_person.firstName" /></label></dt>
   <dd><f:form.textfield property="firstName" id="firstName" /></dd>
 </dl>
 <dl>
   <dt><label for="lastName"><f:translate key="tx_sfextbase_domain_model_person.lastName" /></label></dt>
   <dd><f:form.textfield property="lastName" id="lastName" /></dd>
 </dl>
 <dl>
   <dt><label for="birthday"><f:translate key="tx_sfextbase_domain_model_person.birthday" /></label></dt>
   <dd><f:form.textfield name="{prefix}[birthday]" value="{personObject.birthday -> f:format.date(format: 'd.m.Y')}" id="birthday" /></dd>
 </dl>

In personObject befindet sich beim Anlegen eines Datensatzes ein leeres PersonObject,
das in der newAction erstellt wurde. In "prefix" steht je nach dem, ob ein Datensatz angelegt oder bearbeitet wird
"newPerson" oder "person".

Diese Lösung hat allerdings auch wieder ein Problem. Wird beim Neuanlegen ein fehlerhaftes Datum eingetragen
verschwindet das Datum im Formular, wenn der Speichernbutten gedrückt wurde. Schlimmer ist es beim Bearbeiten. Wird
beim Bearbeiten ein falsches Datum eingetragen und auf Speichern gedrückt, so wird das Datum aus der Datenbank
angezeigt und nicht das fehlerhafte Datum.

Wie Ihr seht ist es ohne Quellcodeanpassungen innerhalb von Extbase und Fluid nicht möglich 100% kompatible Formulare
mit Extbas und Fluid zu erzeugen. Ich habe allerdings einen Patch geschrieben:

http://forge.typo3.org/issues/52480

Dieser Patch kümmert sich darum Inputfelder, die mit einem DateTime-Objekt befüllt werden so umzuformatieren,
dass nur noch das lesbare Format in diesem Feld landet. Dazu gibt es nach Einspielen des Patches ein neues Attribut
formatDateTime. Falls dies nicht angegeben wird, wird automatisch das Datumsformat verwendet,
wie es im Installtool konfiguriert wurde (y-m-d).

Änder wir also unser Formular wieder zurück auf Anfang::

 <dl>
   <dt><label for="firstName"><f:translate key="tx_sfextbase_domain_model_person.firstName" /></label></dt>
   <dd><f:form.textfield property="firstName" id="firstName" /></dd>
 </dl>
 <dl>
   <dt><label for="lastName"><f:translate key="tx_sfextbase_domain_model_person.lastName" /></label></dt>
   <dd><f:form.textfield property="lastName" id="lastName" /></dd>
 </dl>
 <dl>
   <dt><label for="birthday"><f:translate key="tx_sfextbase_domain_model_person.birthday" /></label></dt>
   <dd><f:form.textfield property="birthday" formatDateTime="d.m.Y" id="birthday" /></dd>
 </dl>

Die Neuanlage hatte ja auch vorher schon funktioniert, aber mit diesem 4-Zeiler an Patch klappt nun auch das
Bearbeiten. Wird ein falsches Datum angegeben und der Webseitenbesucher klickt auf Speichern,
dann wird nun endlich auch der fehlerhafte Wert angezeigt und nicht mehr wie vorher der Wert aus der Datenbank. Dank
dem neuen Attribut könntet Ihr sogar mit der Variable {settings.*} das Datumsformat völlig dynamisch setzen.

Noch ist der Patch nicht akzeptiert worden und wartet darauf in den Core zu wandern. Falls auch Ihr eine
vollständige DateTime-Objekt-Unterstützung wollt würde ich mich über ein paar +1-Votes im Reviewprozess freuen.
