# Shipping Modul Klasse - Referenz

Erweitert die Modul Klasse um folgende Attribute und Methoden.


## Meta

!!! danger "Wichtiger Hinweis"

    Der PHP-Klassen Name und der Dateiname darf bei Shipping Modul Klassen keine Unterschriche `_` beinhalten.

    - :x: my_first_shipping_module / my_first_shipping_module.php

    - :white_check_mark: myfirstshippingmodule / myfirstshippingmodule.php

| | |
|-------------| - |
| Class directory | `/includes/modules/shipping/` |
| Lang directory | `/lang/<LANGUAGE>/modules/shipping/` | 
| Class name | [`snake_case`](#) oder [`PascalCase`](#) ohne `_` | 
| File name | `<CLASS_NAME>.php` | 
| Configuration-Prefix | `MODULE_` oder `MODULE_SHIPPING_` in [`SCREAM_CASE`](#) | 
| Configuration | `<CONFIGURATION_PREFIX><CLASS_NAME>` in [`SCREAM_CASE`](#) | 

## Attibute

keine

## Methoden

### quote()

```php
public function quote(string $method = '', string $module = ''): ShippingQuoteArray
```

| Option   | Value |
|----------|-------|
| optional | 🚫 |
| caller   | checkout_shipping.php |


<h4>Beschreibung</h4>

Die Methode `quote()` muss ein Array zurückliefern. Wir nennen dieses Array in diesem Text `ShippingQuoteArray`. Mit diesem Array wird dem Shop mitgeteilt, wie hoch die Versandkosten für die aktuelle Bestellung (die sich im Bestellablauf befindet) ausfallen werden. Zudem können in dem Array `ShippingQuoteArray` mehrere Versandoption definiert werden. Diese nennen sich im modified-Kontext `methods`. Wir nennen einen Eintrag `ShippingQuoteMethodArray`. So könnte dem Käufer z. B. ein *Standard-Versand* und ein *Express-Versand* angeboten werden. Das Array `ShippingQuoteArray` muss mindestens ein `ShippingQuoteMethodArray` beinhalten.

In der Methode `quote()` werden typischerweise auf die globalen Variablen `$total_weight`, `$shipping_weight`, `$shipping_quoted` und `$shipping_num_boxes` zugegriffen.

| Variable | Beschreibung |
|----------|--------------|
| `$order` | Die Bestelldaten |
| `$shipping_weight` | Gesamtgewicht der Bestellung |
| `$shipping_quoted` | ??? |
| `$shipping_num_boxes` | ???. Vermutlich die Anzahl der Pakete, wie das berechnet/definiert wird ist noch unklar. |

<h4>Objekte und Arraystrukturen</h4>

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


<h4>Beispiel</h4>

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
