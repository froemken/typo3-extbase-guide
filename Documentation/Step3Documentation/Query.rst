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
     $query->logicalAnd(
       $query->greaterThan('price', '12'),
       $query->lessThan('price', '20')
     )
   )->execute();
 }

::

 public function testQuery() {
   $query = $this->createQuery();
   return $query->matching(
     $query->logicalAnd(
       $query->greaterThanOrEqual('price', '12'),
       $query->lessThanOrEqual('price', '20')
     )
   )->execute();
 }

.. important::

   Ohne die Angabe von *logicalAnd* würde nur die erste Bedingung in die Query eingebaut werden. Das Problem ist,
   dass ohne *logicalAnd* keine Fehlermeldung geworfen wird, weshalb es um so wichtiger ist,
   selbst an die *logical*-Begingungen zu achten.

Beinhaltet
----------

*contains* ist ähnlich der *in* Methode. Allerdings mit dem Unterschied, dass hier nicht geprüft wird,
ob sich der Spaltenwert in einer Liste von möglichen Werten befindet, sondern, ob sich ein gesuchter Wert in der
Spalte, die mehrere Werte beinhalten kann, befindet::

 public function testQuery() {
   $query = $this->createQuery();
   return $query->matching(
     $query->contains('categories', '23')
   )->execute();
 }

In dem Beispiel können einem Produkt mehrere Kategorien zugeordnet werden. Mit Hilfe von *contains* wird nun eine
*Query* in dieser Form erstellt::

 SELECT  tx_productoverview_domain_model_product.*
 FROM tx_productoverview_domain_model_product
 WHERE tx_productoverview_domain_model_product.uid=(
   SELECT tx_productoverview_domain_model_category.product
   FROM tx_productoverview_domain_model_category
   WHERE tx_productoverview_domain_model_category.uid='2'
 );

Like
----

Sucht nach Teilen eines String in den Spaltenwerten::

 public function testQuery() {
   $query = $this->createQuery();
   return $query->matching(
     $query->like('title', 'Stef%')
   )->execute();
 }

AND / OR
--------

Bei der Verwendung von mehreren Bedingungen müssen diese immer in *logicalAnd* oder *logicalOr* Methoden gepackt
werden, da sonst immer nur die erste Bedingung in die *Query* wandert. Es lassen sich auf mehrere AND und OR
Bedingungen verschachtel, wie das folgende Beispiel zeigt::

 public function testQuery() {
   $query = $this->createQuery();
   return $query->matching(
     $query->logicalOr(
       $query->equals('title', 'Stefan'),
       $query->logicalAnd(
         $query->greaterThanOrEqual('price', '12'),
         $query->lessThanOrEqual('price', '20')
       )
     )
   )->execute();
 }

In diesem Fall wird nach allen Datensätzen gesucht die entweder den Titel "Stefan" tragen oder der Preis zwischen 12
und 20 liegt.

Limit / Offset
--------------

Pro *Query* können die Datenmengen auf ein gewünschtes Minimum reduziert werden::

 public function testQuery() {
   $query = $this->createQuery();
   return $query->matching(
    $query->equals('title', 'Stefan')
   )->setLimit(15)->setOffset(60)->execute();
 }

Dieses Beispiel zeigt die nächsten 15 Datensätze ab dem 60ten Datensatz an.

Sortierung
----------

::

 public function testQuery() {
   $query = $this->createQuery();
   return $query->matching(
     $query->greaterThan(12)
   )->setOrderings(array(
     'title', Tx_Extbase_Persistence_QueryInterface::ORDER_ASCENDING,
     'price', Tx_Extbase_Persistence_QueryInterface::ORDER_DESCENDING
   ))->execute();
 }

Diese *Query* sucht alle Datensätze bei denen der Preis größer ist als "12". Das Ergebnis wird erst nach dem Titel
aufsteigend sortiert und dann nach dem Preis absteigend sortiert.