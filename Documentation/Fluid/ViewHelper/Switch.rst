.. ==================================================
.. FOR YOUR INFORMATION
.. --------------------------------------------------
.. -*- coding: utf-8 -*- with BOM.

.. include:: ../../Includes.txt

f:switch
=========

Seit TYPO3 6.2 gibt es diesen ViewHelper, um mehrere Fallunterscheidungen in einem ViewHelper abzubilden. Dies ist
eine ganze Ecke lesbarer als mehrere f:if-ViewHelper untereinander einzubinden.

Eigenschaften
-------------

Eigenschaften speziell für diesen ViewHelper
############################################

.. t3-field-list-table::
 :header-rows: 1

 - :Property,20:    Eigenschaft
   :Datatype,20:    Datentyp
   :Description,40: Beschreibung
   :Standard,10:    Standard
   :Mandatory,10:   Mandatory

 - :Property:    expression
   :Datatype:    Mixed
   :Description: Gebt hier einen Wert oder eine Variable an an Hand der entschieden werden soll,
                 welcher case geladen werden soll.
   :Standard:
   :Mandatory:   Ja

Beispiel
--------

::

 <f:switch expression="{person.geschlecht}">
   <f:case value="1">Sehr geehrter Herr {person.lastName}</f:case>
   <f:case value="2">Sehr geehrte Frau {person.lastName}</f:case>
   <f:case value="3">Sehr geehrte Damen und Herren</f:case>
 </f:switch>

**Ausgabe bei einem Wert von 2**

::

 Sehr geehrte Frau Müller

.. caution::

 Die Verwendung diesen ViewHelpers ist meist ein Anzeichen für eine schlechte Programmierarchitektur. Bei mehrfacher
 Verwendung diesen ViewHelpers wird angeraten die Architektur innerhalb der Actions und Controller neu zu überdenken.

.. tip::

 Das Beispiel von oben könnte auch mit f:partial oder f:section gelöst werden.

 ::

  <f:render partial="person/{person.geschlecht}" />
