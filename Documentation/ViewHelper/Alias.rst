.. ==================================================
.. FOR YOUR INFORMATION
.. --------------------------------------------------
.. -*- coding: utf-8 -*- with BOM.

.. include:: ../Includes.txt

f:alias
=======

Mit diesem ViewHelper könnt Ihr eigene Variablen innerhalb des öffnenden und schließenden Tags zur Verfügung stellen.
Das ist sinnvoll, wenn Ihr einen bestimmten Wert (Texte, Zahlen oder Werte aus Objekten) in Eurem Template mehrfach benötigt.

.. t3-field-list-table::
 :header-rows: 1

 - :Property,20:    Eigenschaft
   :Data type,20:   Datentyp
   :Description,50: Beschreibung
   :Mandatory,10:   Mandatory

 - :Property:    map
   :Data type:   Array
   :Description: Der Key gibt den Namen der neuen Variable wieder, während der Value den Inhalt wieder spiegelt.
   :Mandatory    X

   
.. t3-field-list-table::
 :header-rows: 1

 - :Property,20:    Property
   :Data type,20:   Data type
	   :Description,50: Description
	   :Default,10:     Default

	 - :Property:    allWrap
	   :Data type:   wrap / stdWrap
	   :Description: Wraps the whole item

	 - :Property:    wrapItemAndSub

	   :Data type:   wrap / stdWrap

	   :Description:
	         Wraps the whole item and
	         any submenu concatenated to it.




Beispiel für einfache Texte
---------------------------

::

 <f:alias map="{vorname: 'Stefan', nachname: 'Froemken'}">
  <p>Hallo zusammen,<br />mein Name ist {vorname} {nachname}</p>
 </f:alias>

Beispiel für selbst erstellte Arrays
------------------------------------

Dieses Beispiel ist vielleicht wenig sinnvoll, aber es zeigt sehr gut, wie man innerhalb von Fluid mehrdimensionale Arrays erstellen kann.

::

 <f:alias map="{company: {name: 'Mueller und Co.', mitarbeiter: {0: {name: 'Stefan'}, 1: {name: 'Petra'}}}}">
   <p>Wir, die Firma {company.name}, haben folgende Mitarbeiter:</p>
   <ul>
     <f:for each="{company.mitarbeiter}" as="mitarbeiter">
       <li>{mitarbeiter.name}</li>
     </f:for>
   </ul>
 </f:alias>

Beispiel für Werte aus Objekten
-------------------------------

Dieses Beispiel funktioniert nur, wenn Ihr das feUser-Objekt in Eurer Extension per $this->view->assign() verfügbar gemacht habt.

::

 <f:alias map="{vorname: feUser.firstName, nachname: feUser.lastName}">
   <p>Hallo zusammen,<br />mein Name ist {vorname} {nachname}</p>
 </f:alias>