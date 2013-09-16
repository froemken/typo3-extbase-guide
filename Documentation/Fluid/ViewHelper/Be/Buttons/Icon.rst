.. ==================================================
.. FOR YOUR INFORMATION
.. --------------------------------------------------
.. -*- coding: utf-8 -*- with BOM.

.. include:: ../../../../Includes.txt

f:be.buttons.icon
=================

Mit diesem ViewHelper könnt Ihr kleine (verlinkte) Icons generieren lassen wie z.B. bearbeiten, löschen, neu und
viele mehr. Schaut Euch auch das Beispiel an, wie Ihr alle verfügbaren Icons anzeigen lassen könnt.

Eigenschaften
-------------

.. t3-field-list-table::
 :header-rows: 1

 - :Property,20:    Eigenschaft
   :Datatype,20:    Datentyp
   :Description,40: Beschreibung
   :Standard,10:    Standard
   :Mandatory,10:   Mandatory

 - :Property:    uri
   :Datatype:    String
   :Description: Die URL wohin die Reise führen soll. Kann auch mit einem der f:uri.*-ViewHelper kombiniert werden
   :Standard:    Leerer String
   :Mandatory:   Nein

 - :Property:    icon
   :Datatype:    String
   :Description: Der Name des Icons, dass verwendet werden soll
   :Standard:    actions-document-close
   :Mandatory:   Nein

 - :Property:    title
   :Datatype:    String
   :Description: Der hier eingegebene Wert wird als title-Attribut für den Link verwendet
   :Standard:    Leerer String
   :Mandatory:   Nein

Beispiel
--------

::

 <f:be.buttons.icon uri="{f:uri.action(action:'new')}" icon="button_unhide" />

Auflistung aller möglichen Iconnamen
------------------------------------

Je nach System und installierten Extensions können die Iconnamen variieren, deshalb hier eine Möglichkeit,
wie Ihr ALLE verfügbaren Iconnamen mit Bild auflisten lassen könnt. Im HTML-Template::

 <ul>
   <f:for each="{iconsAvailable}" as="icon">
     <li><f:be.buttons.icon icon="{icon}" /> - {icon}</li>
   </f:for>
 </ul>

Im Controller müsst Ihr dann noch die verfügbaren Iconnamen für das Template zur Verfügung stellen. Geht dazu in die
entsprechende Action und tragt folgendes ein::

 $this->view->assign('iconsAvailable', $GLOBALS['TBE_STYLES']['spriteIconApi']['iconsAvailable']);
