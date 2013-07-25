.. ==================================================
.. FOR YOUR INFORMATION
.. --------------------------------------------------
.. -*- coding: utf-8 -*- with BOM.

.. include:: ../../../Includes.txt

f:form.button
=============

Dieser ViewHelper erzeugt einen Button (button-Tag). Diesem Button könnt Ihr ein Bild zuweisen,
ihn als Submit- oder auch Reset-Button verwenden.

Eigenschaften
-------------

.. include:: ../../UniversalTagAttributes.txt

Eigenschaften speziell für das HTML-Element
###########################################

.. t3-field-list-table::
 :header-rows: 1

 - :Property,20:    Eigenschaft
   :Datatype,20:    Datentyp
   :Description,40: Beschreibung
   :Standard,10:    Standard
   :Mandatory,10:   Mandatory

 - :Property:    autofocus
   :Datatype:    String
   :Description: Der Button erhält automatisch den Focus wenn die Webseite geladen wird.
   :Standard:    NULL
   :Mandatory:   Nein

 - :Property:    disabled
   :Datatype:    String
   :Description: Der Button wird beim Webseitenaufruf deaktiviert dargestellt.
   :Standard:    NULL
   :Mandatory:   Nein

 - :Property:    form
   :Datatype:    String
   :Description: Zu welchem Formular oder Formularen gehört dieser Button
   :Standard:    NULL
   :Mandatory:   Nein

 - :Property:    formaction
   :Datatype:    String
   :Description: Nur wenn der Typ auf "submit" gesetzt wurde, kann hiermit angegeben werden wohin die Formulatdaten
                 gesendet werden sollen.
   :Standard:    NULL
   :Mandatory:   Nein

 - :Property:    formenctype
   :Datatype:    String
   :Description: Wie sollen die Daten codiert werden bevor das Formular abgesendet wird. Funktioniert nur mit dem
                 Typen "submit". Mögliche Werte wären z.B. "application/x-www-form-urlencoded", "multipart/form-data"
                 oder "text/plain"
   :Standard:    NULL
   :Mandatory:   Nein

 - :Property:    formmethod
   :Datatype:    String
   :Description: Wenn der Typ "submit" gewählt wurde, kann hier angegeben werden wie die Formulardaten gesendet
                 werden sollen. Mögliche Werte "get" oder "post"
   :Standard:    NULL
   :Mandatory:   Nein

 - :Property:    formnovalidate
   :Datatype:    String
   :Description: Wenn der Typ "submit" gewählt wurde, kann hier angegeben werden, ob die Formulardaten beim Absenden
                 überprüft werden sollen.
   :Standard:    NULL
   :Mandatory:   Nein

 - :Property:    formtarget
   :Datatype:    String
   :Description: Wenn der Typ "submit" gewählt wurde, kann hier angegeben werden wo das Formularergebnis angezeigt
                 werden soll. Mögliche Werte sind: "_blank", "_self", "_parent", "_top" oder "framename"
   :Standard:    NULL
   :Mandatory:   Nein

 - :Property:    name
   :Datatype:    String
   :Description: Der Name des HTML-Elementes
   :Standard:    NULL
   :Mandatory:   Nein

 - :Property:    property
   :Datatype:    String
   :Description: Diese Eigenschaft klappt nur unter Verwendung von <f:form object="...">. "name" und "value"
                 Eigenschaften werden ignoriert
   :Standard:    NULL
   :Mandatory:   Nein

 - :Property:    value
   :Datatype:    Mixed
   :Description: Der Wert, der beim Absenden übermittelt werden soll.
   :Standard:    NULL
   :Mandatory:   Nein

Eigenschaften speziell für diesen ViewHelper
############################################

.. t3-field-list-table::
 :header-rows: 1

 - :Property,20:    Eigenschaft
   :Datatype,20:    Datentyp
   :Description,40: Beschreibung
   :Standard,10:    Standard
   :Mandatory,10:   Mandatory

 - :Property:    type
   :Datatype:    String
   :Description: Welche Funktion hat der Button. Mögliche Werte: "button", "reset" oder "submit"
   :Standard:    submit
   :Mandatory:   Nein

Beispiel: Button, um die Formularfelder zu leeren
-------------------------------------------------

::

 <f:form.button type="reset">Felder leeren</f:form.button>

**Ausgabe**

::

 <button type="reset" name="" value="">Felder leeren</button>

Beispiel: Absendebutton mit Bild und Text
-----------------------------------------

Außerdem wird noch eine zusätzliche Variable "zusatz" mit dem Wert "abgeschickt" an die Actionmethode übergeben::

 <f:form.button type="submit" name="zusatz" value="abgeschickt">
   <p>
     <f:image src="fileadmin/user_upload/images/logo/TYPO3-Logo-8bit.png" width="100" height="50" alt="Tooltip" /><br>
     <b>Buttontext</b>
   </p>
 </f:form.button>

**Ausgabe**

::

 <button type="submit" name="tx_sfextbase_extbase[zusatz]" value="wert">
   <p>
     <img alt="Tooltip" src="/typo3temp/_processed_/csm_TYPO3-Logo-8bit_fd6af7eb20.png" width="100" height="50"><br>
     <b>Buttontext</b>
   </p>
 </button>
