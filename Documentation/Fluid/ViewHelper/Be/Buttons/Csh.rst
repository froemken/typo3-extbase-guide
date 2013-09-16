.. ==================================================
.. FOR YOUR INFORMATION
.. --------------------------------------------------
.. -*- coding: utf-8 -*- with BOM.

.. include:: ../../../../Includes.txt

f:be.buttons.csh
================

Eigentlich viel zu selten verwendet. Aber so ist es nun mal mit der Dokumentation. Mit diesem ViewHelper könnt Ihr
dem Benutzer kleine Hilfestellungen geben, was sie z.B. in ein bestimmtes Feld eingeben sollen. TYPO3 selbst verwendet
es nahezu durchgängig und Ihr erkennt es an diesen kleinen runden Fragezeichen in der Nähe eines jeden Eingabefeldes.
Wenn Ihr mit der Maus drüberfahrt und knapp ne Sekunde oder 2 wartet, dann erscheint der kurze Hilfetext. Klickt Ihr
auf das Icon öffnet sich ein PopUp mit weiteren Informationen zu diesem Eingabefeld. In den Benutzereinstellungen
könnt Ihr einstellen, ob dieses PopUp nach einem Klick erscheinen soll oder nicht.

Eigenschaften
-------------

.. t3-field-list-table::
 :header-rows: 1

 - :Property,20:    Eigenschaft
   :Datatype,20:    Datentyp
   :Description,40: Beschreibung
   :Standard,10:    Standard
   :Mandatory,10:   Mandatory

 - :Property:    table
   :Datatype:    String
   :Description: Der Tabellenname
   :Standard:    NULL
   :Mandatory:   Nein

 - :Property:    field
   :Datatype:    String
   :Description: Der Key aus der Sprachdatei
   :Standard:    Leerer String
   :Mandatory:   Nein

 - :Property:    iconOnly
   :Datatype:    Boolean
   :Description: Es wird nur das Bildchen dargestellt, nicht aber der Hilfetext
   :Standard:    FALSE
   :Mandatory:   Nein

 - :Property:    styleAttributes
   :Datatype:    String
   :Description: Zusätzliches style-Attribut, das in die umschließende Tabelle eingebunden wird
   :Standard:    Leerer String
   :Mandatory:   Nein

**Hinweis zu einem Sonderfall bei der Eigenschaft "table"**

Es gibt einen Sonderfall bei dem die Eigenschaft "table" überhaupt nicht angegeben werden muss. Dieser tritt dann
ein, wenn es in dem Modul zwar Formularfelder gibt, die aber keine 1zu1-Spalte in der Datenbank besitzen. So werden
beim scheduler z.B. alle Formularfelder serialisiert in einem DB-Feld abgespeichert (kein 1zu1-Abbild der Felder)
oder z.B. Textfelder, die für Suchanfragen benötigt werden (keine DB-Speicherung). Diesen besonderen Felder kann mit
Hilfe eines Eintrages in der ext_tables.php ein csh-Icon zugewiesen werden::

 \TYPO3\CMS\Core\Utility\ExtensionManagementUtility::addLLrefForTCAdescr(
   '_MOD_VollständigerModulname',
   'PfadZuEinerSprachdatei'
 );

Beachtet bitte, dass es sich bei dem Modulnamen um den vollständig ausgeschriebenen Modulnamen handelt. Dieser
besteht aus Kategorie (z.B. web), Extensionname (upper camel case) und Modulname (upper camel case). In meiner
Beispielextension "sfextbase" mit dem Modulnamen "extbase" würde der vollständige Name so lauten::

 web_SfextbaseExtbase

Hier eine Möglichkeit, wie es bei Euch aussehen könnte::

 \TYPO3\CMS\Core\Utility\ExtensionManagementUtility::addLLrefForTCAdescr(
   '_MOD_web_SfextbaseExtbase',
   'EXT:' . $_EXTKEY . '/Resources/Private/Language/locallang_extbase.xlf'
 );

Der Eintrag für die E-Mail-Adresse sieht in meiner xlf-Datei so aus::

 <trans-unit id="email.alttitle" xml:space="preserve">
   <source>E-Mail</source>
 </trans-unit>
 <trans-unit id="email.description" xml:space="preserve">
   <source>Tragen Sie hier Ihre E-Mail-Adresse ein</source>
 </trans-unit>
 <trans-unit id="email.details" xml:space="preserve">
   <source>Ich bin eine ewig lange Beschreibung, die Ihnen on Detail erklärt, wobei es sich um eine
   E-Mail-Adress handelt, wo Sie eine her bekommen und was zu beachten ist,
   um eine valide Adresse in dieses Feld einzutragen</source>
 </trans-unit>

Beispiele
---------

.. caution::

 Im Quellcode der csh-API befindet sich eine Abfrage auf die BE-User-Option: edit_showFieldHelp. Diese ist per
 Default nicht gesetzt und verhindert somit grundsätzlich die Ausgabe dieser csh-Hilfetexte. Um diesen ViewHelper in
 Aktion sehen zu können, müsst Ihr entweder in den Benutzer- oder Gruppenoptionen folgende Einstellung in die
 UserTSconfig setzen::

  setup.override.edit_showFieldHelp = text

Beispiel: Datenbankfelder
#########################

Bei TYPO3 eigenen Tabellen und meist auch bei Extbaseextensions, die mit Hilfe des Extension-Builders erzeugt
wurden kann folgendermaßen auf die CSH-Informationen zugegriffen werden::

 <f:be.buttons.csh table="tt_content" field="header" />

Beispiel mit Stil
#################

::

 <f:be.buttons.csh table="tt_content" field="header" styleAttributes="background-color: red;" />

Beispiel: Icons für Formularfelder ohne 1zu1-Abbild in der Datenbank
####################################################################

Hier ein Beispiel für den oben beschriebenen Soderfall, bei dem der Tabellenname nicht angegeben werden darf::

 <f:be.buttons.csh field="email" />
