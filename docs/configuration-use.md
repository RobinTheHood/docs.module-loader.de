---
title: Wie funktioniert die useFunction?
description: Die useFunction wird eingesetzt, um einen Wert aus der Configuration zu formatieren. Wenn wir beispielsweise eine OrderStatusId in einem ConfigurationFeld speichern, wird uns in der Modulübersicht ebenfalls die Id angezeigt.
---

# Wie funktioniert die useFunction?

??? note "Textstatus - Entwurf"

    Status: 2 von 5 - Erster Entwurf: Erste Ausformulierung einiger Informationen. 

Die useFunction wird eingesetzt, um einen Wert aus der Configuration zu formatieren. Wenn wir beispielsweise eine OrderStatusId in einem ConfigurationFeld speichern, wird uns in der Modulübersicht ebenfalls die Id angezeigt. Oft möchte man jedoch nicht, dass einem die Id angezeigt wird, sondern der Name des dazugehörigen Status. Um die Id in den Namen zu konvertieren, können wir die useFunction verwenden. In der useFunction können wir eine Funktion angeben, die modified aufruft, um den Wert aus der Configuration zu formatieren.

Wenn wir bei unserem Beispiel mit OrderStatus bleiben, können wir folgende useFunction verwenden, um uns den Namen zu einer Id geben zu lassen:

```php
function xtc_get_orders_status_name($orders_status_id, $language_id = '')
```

Auch bei den useFunction lassen wir wieder das `)` und am Ende des Eintrags weg. In die Datenbank soll nur Folgendes geschrieben werden:

```php
xtc_get_orders_status_name(
```

Zubeachten ist auch, modifed die Function nur mit einem Parameter aufruft. Dieser Parameter ist der Wert, der in Configuration gespeichert ist. Wenn wir das mit unserem Beispiel vergleicht, wird für den Parameter `$languages_id` nie ein Wert gesetzt. Das ist in diesem Fall nicht so schlimm, da die Function `xtc_get_orders_status_name` intern den Wert von `$language_id` auf den Wert aus der Session setzt, wenn kein Wert angegeben ist. Allgemein kann jedoch gesagt werden, dass sich nur useFunction angeben lassen, die nur einen Wert als Parameter benötigen, um ein brauchbares Ergebnis zu liefern.