.. ==================================================
.. FOR YOUR INFORMATION
.. --------------------------------------------------
.. -*- coding: utf-8 -*- with BOM. ÄÖÜäöüß

.. include:: ../Includes.txt

Query
=====

Innerhalb von Euren *Repositories* habt Ihr die Möglichkeit Eure eigenen ganz individuellen *Queries* zu erstellen.
Man kann sagen: Sobald Eure Anforderungen über ein einfaches Spalte = Wert hinaus geht erfordert dies
automatisch die Erstellung einer eigenen Query.

Erstellen eigener Queries
-------------------------

Hier eine selbst gebaute Query, wie Ihr sie in der *Repository* Methode: *findAll* findet::

 public function testQuery() {
   $query = $this->createQuery();
   return $query->execute();
 }

Jede selbst erstellte Query erfordert das Erzeugen eines neuen *Query*-Objektes. Werden keine weiteren Parameter
angegeben wird eine Query ähnlich: "SELECT * FROM table" erzeugt. Mit *execute* werden alle Einstellungen und
Parameter für die Query in einem *Result*-Objekt verpackt und zurück gegeben.

.. important::

   Mit *execute* wird die Query NICHT ausgeführt! Es werden nur alle zur Query gehörenden Einstellungen in einem
   *Result*-Objekt zusammen geasst. Es findet also KEINE Datenbankabfrage statt.

Vergleiche
----------

Das folgende Beispiel zeigt einen einfachen Splatenvergleich, wie Ihr ihn schon von den magic Methoden *findBy* kennt::

 public function testQuery() {
   $query = $this->createQuery();
   return $query->matching(
     $query->equals('title', 'Stefan')
   )->execute();
 }

Alle Bedingungen, die für eine Query angewendet werden, müssen mit *matching* der Query bekannt gemacht werden
. In diesem Fall gibt es nur die *equals*-Bedingung, die der Query mitteilt, dass die Spalte *title* dem Wert
"Stefan" entsprechen muss.

.. important::

   Bitte verwendet als Vergleichswert keine Arrays mehr! Diese Möglichkeit ist seit Version 1.3 deprecated und könnte
   mit dem nächsten Extbaseupdate entfernt worden sein.

IN-Vergleiche
-------------

In dem nächsten Beispiel wird der Spalteninhalt gegen mehrere Werte verglichen::

 public function testQuery() {
   $query = $this->createQuery();
   return $query->matching(
     $query->in('price', array(12,17,21))
   )->execute();
 }

Die erzeugte SQL-Query könnt so aussehen: SELECT * FROM [table] WHERE price IN ('12', '17', '21');

Größer Kleiner Gleich als
-------------------------

::

 public function testQuery() {
   $query = $this->createQuery();
   return $query->matching(
     $query->greaterThan('price', '12'),
     $query->lessThan('price', '20')
   )->execute();
 }

::

 public function testQuery() {
   $query = $this->createQuery();
   return $query->matching(
     $query->greaterThanOrEqual('price', '12'),
     $query->lessThanOrEqual('price', '20')
   )->execute();
 }

