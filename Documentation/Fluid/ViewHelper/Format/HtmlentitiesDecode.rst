.. ==================================================
.. FOR YOUR INFORMATION
.. --------------------------------------------------
.. -*- coding: utf-8 -*- with BOM.

.. include:: ../../../Includes.txt

f:format.htmlentitiesDecode
===========================

htmlEntityDecode

Eigenschaften
-------------

.. t3-field-list-table::
 :header-rows: 1

 - :Property,20:    Eigenschaft
   :Datatype,20:    Datentyp
   :Description,40: Beschreibung
   :Standard,10:    Standard
   :Mandatory,10:   Mandatory

 - :Property:    value
   :Datatype:    String
   :Description: Der Text der dekodiert werden soll
   :Standard:    NULL
   :Mandatory:   Nein

 - :Property:    keepQuotes
   :Datatype:    Boolean
   :Description: Sollen einfache und doppelte Anführungsstriche auch dekodiert werden?
   :Standard:    FALSE
   :Mandatory:   Nein

 - :Property:    encoding
   :Datatype:    String
   :Description: Wir sind hier im Bereich TYPO3 und da sollte der Zeichensatz auf UTF-8 und nix anderes stehen. Sollte wiedererwartend ein anderer Zeichensatz gewünscht sein, kann dieser hier angegeben werden. Siehe auch Info auf php.net.
   :Standard:    NULL
   :Mandatory:   Nein

Beispiel
--------

::

 <p><f:format.htmlentitiesDecode>M&uuml;ller &amp; Breuer</f:format.htmlentitiesDecode>

Im Quelltext sieht man wieder ein richtig sauberes: "Müller & Breuer".