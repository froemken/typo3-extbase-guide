.. ==================================================
.. FOR YOUR INFORMATION
.. --------------------------------------------------
.. -*- coding: utf-8 -*- with BOM.

.. include:: ../Includes.txt

f:base
======

Dieser ViewHelper bindet den `base-Tag <http://de.selfhtml.org/html/kopfdaten/basis.htm>`_ in Ihr HTML-Template ein.

Parameter
---------

Dieser ViewHelper besitzt keine Parameter

Beispiel
--------

::

 <f:base />

.. tip::

   Dieser ViewHelper macht nur dann Sinn, wenn Sie Ihr Webseitentemplate (inkl. Kopfbereich <head>) mit Hilfe von Fluid aufbauen.
   Wenn kein <head>-Bereich vorhanden ist, wird dieser HTML-Tag im Bodybereich der Webseite eingebunden, wo er nicht hingehört.