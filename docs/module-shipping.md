---
title: Shipping Modul Klasse für modified programmieren
description: In diesem Abschnitt erklären wir dir alles, was du wissen musst, um ein Shipping Modul für modified zu programmieren.
---

# Shipping Modul Klasse für modified programmieren

In diesem Abschnitt erklären wir dir alles, was du wissen musst, um ein Shipping Modul für modified zu programmieren.

## Konzept

??? note "Textstatus - Entwurf"

    Status: 2 von 5 - Erster Entwurf: Erste Ausformulierung einiger Informationen.

Mit einer Shipping Modul Klasse, kannst du dem modified Shopsystem eine neue Versandart / Liefermethode hinzufügen, die der Kunde im Bestellprozess auswählen kann.

## Aufbau

??? note "Textstatus - Entwurf"

    Status: 2 von 5 - Erster Entwurf: Erste Ausformulierung einiger Informationen.

Eine Shipping Modul Klasse unterscheidet sich nicht von der abstrakten Modul Klasse, die wir im Abschnitt [Abstrakte Modul Klasse](/module-class-abstract/) beschreiben. Eine Shipping Modul Klasse fügt jedoch die Methode `quote()` hinzu. Lese dir den Abschnitt [Abstrakte Modul Klasse](/module-class-abstract/) durch, um den Aufbau zu verstehen. In diesem Abschnitt gehen wir auf einige konkrete Aspekte der Shipping Modul Klasse ein, die nicht im Abschnitt [Abstrakte Modul Klasse](/module-class-abstract/) beschrieben werden.

Um ein Shipping Modul zu erstellen, musst du eine Modul-Datei in das Verzeichnis `/includes/modules/shipping/` anlegen. Die Sprachdateien liegen in `/lang/<LANGUAGE>/modules/shipping/`.

Du solltest beiden Dateien gleich benennen, damit für jeden auf den ersten Blick erkennbar ist, dass die beiden Dateien zusammengehören. In diesem Beispiel haben wir die beiden Dateien `myfirstshippingmodule.php` benannt. Orientiere dich für das Benennen von Dateien an den Namingconventions aus dem Abschnitt [???](#).

!!! danger "Wichtiger Hinweis"

    Der PHP-Klassen Name und der Dateiname darf bei Shipping Modul Klassen keine Unterschriche `_` beinhalten.

    - :x: my_first_shipping_module / my_first_shipping_module.php

    - :white_check_mark: myfirstshippingmodule / myfirstshippingmodule.php

```
├── includes
│   └── modules
│       └── shipping
│           └── myfirstshippingmodule.php
└── lang
	└── <LANGUAGE>
		└── modules
			└── shipping
				└── myfirstshippingmodule.php
```

Zusätzlich muss das Modul eine `*_ALLOWED` (`MODULE_SHIPPING_MC_MY_FIRST_MODULE_ALLOWED`) Konstante/Einstellung zur Verfügung stellen, mit der modified entscheidet, ob die Versandart im Checkout angezeigt werden soll. Üblicherweise befinden sich dort die Zonen (bzw. zweistellige Länderkürzel), in der die Versandart angeboten werden soll, wie z. B. `DE,AT`.

Weiterhin, sollte dein Versandmodul auch eine property names `quotes` zur Verfügung stllen, auf die modified während des Checkouts zugreift, um festzustellen welche der angebotenen Versandarten am günstigsten ist:
```php
public array $quotes = array();
```
Der array sollte dann den Rückgabewert von deiner `quote()` Methode enthalten.

Eine Liste mit allen Modul Klassen und deren Methoden, die du erweitern kannst, gibt es als Muster-Dateien unter [github.com/RobinTheHood/class-extensions](https://github.com/RobinTheHood/class-extensions).

## Shipping Klassen Methoden

In diesem Abschnitt gehen wir auf alle Methoden im Detail ein.

### quote()

```php
public function quote(string $method = '', string $module = ''): ShippingQuoteArray
```

| Option   | Value |
|----------|-------|
| optional | 🚫 |
| caller   | checkout_shipping.php |


#### Beschreibung

Die Methode `quote()` muss ein Array zurückliefern. Wir nennen dieses Array in diesem Text `ShippingQuoteArray`. Mit diesem Array wird dem Shop mitgeteilt, wie hoch die Versandkosten für die aktuelle Bestellung (die sich im Bestellablauf befindet) ausfallen werden. Zudem können in dem Array `ShippingQuoteArray` mehrere Versandoption definiert werden. Diese nennen sich im modified-Kontext `methods`. Wir nennen einen Eintrag `ShippingQuoteMethodArray`. So könnte dem Käufer z. B. ein *Standard-Versand* und ein *Express-Versand* angeboten werden. Das Array `ShippingQuoteArray` muss mindestens ein `ShippingQuoteMethodArray` beinhalten.

Das `ShippingQuoteMethodArray` darf keine array keys (strings) enthalten und muss aus Indizes, beginnend mit `0` bestehen.

In der Methode `quote()` werden typischerweise auf die globalen Variablen `$total_weight`, `$shipping_weight`, `$shipping_quoted` und `$shipping_num_boxes` zugegriffen.

| Variable | Beschreibung |
|----------|--------------|
| `$order` | Die Bestelldaten |
| `$total_weight` | Gesamtgewicht der Bestellung, inklusive Paketleergewicht (`SHIPPING_BOX_WEIGHT`) |
| `$shipping_weight` | Gesamtgewicht der Bestellung, exclusive Paketleergewicht (`SHIPPING_BOX_WEIGHT`) |
| `$shipping_quoted` | ??? |
| `$shipping_num_boxes` | ???. Vermutlich die Anzahl der Pakete, wie das berechnet/definiert wird ist noch unklar. |

#### Objekte und Arraystrukturen

**`ShippingQuoteArray (array)`**

| Key | Typ | Beschreibung | Beispiel |
|-----|-----|--------------|----------|
| `id` | string  | Eindeutiger Bezeichner der Versandart. Nur Buchstaben, keine `_` erlaubt. | myfirstshippingmodule |
| `module` | string | Anzeigeinformation für den Nutzer | Versand mit MyShipping |
| `methods` | array | Array aus mind einem `ShippingQuoteMethodArray` | |

**`ShippingQuoteMethodArray (array)`**

| Key | Typ | Beschreibung | Beispiel |
|-----|-----|--------------|----------|
| `id` | string | Eindeutiger Bezeichnet der Versandart. Nur Buchstaben, keine `_` erlaubt. | express |
|`title` | string | Anzeige Information für den Nutzer | Express Versand für 9,90 € |
| `cost` | float | Versandkosten | 9.90 |


#### Beispiel

```php
/**
 * @param string $method Gibt an, welche Shipping-Method zurückgegeben
 * werden soll. Wenn leer, alle zur Auswahl stehenden Methoden
 * zurückgeben.
 * @param string $module ??? lorem
 *
 * @return array Siehe Beispiel in quote()
 */
public function quote(string $method = '', string $module = ''): array
{
    // [...]

    $shippingQuoteMethodArray1 = [
        'id'    => 'standard', // keine Unterstriche möglich!
        'title' => 'Standard Versand',
        'cost'  => 4.90,
    ];

    $shippingQuoteMethodArray2 = [
        'id'    => 'express', // keine Unterstriche möglich!
        'title' => 'Express Versand',
        'cost'  => 9.90,
    ];

    $shippingQuoteArray = [
        'id'      => 'myfirstshippingmodule', // keine Unterstriche möglich!

        'module'  => sprintf(
            'My First Module (%s kg)', round($shipping_weight, 2)
        ),

        'methods' => [
            $shippingQuoteMethodArray1,
            $shippingQuoteMethodArray2
        ]
    ];

    return $shippingQuoteArray;
}
```

??? note "Textstatus - Skizze"

    Status: 1 von 5 - Skizze: Ideen und Informationen in Stichpunkten unvollständig festgehalten.

### ignore_cheapest()

```php
public function ignore_cheapest(): bool
```

### display_free()

```php
public function display_free(): bool
```

### address()

```php
public function address(): array
```

### session()

```php
public function session($method, $module, $quote): void
```
