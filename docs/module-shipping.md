# Shipping Modul für modified programmieren

??? note "Textstatus 2"

    Status: 2 von 5 - Erster Entwurf

Ein Versand- oder Shipping Modul ist wie ein normales Modul (System Modul) aufgebaut. Es benötigt lediglich die zusätzliche Methode `quote()`. Mit dieser Methode werden die Versandkosten bestimmt.

Um ein Shipping Modul zu erstellen, musst du eine Modul-Datei in das Verzeichnis `/includes/modules/shipping/` anlegen. Achtung: Bei Shipping Modulen darf der Klassenname keine Unterstriche `_` enthalten.

Zusätzlich muss das Modul eine `*_ALLOWED` (`MODULE_SHIPPING_MC_MY_FIRST_MODULE_ALLOWED`) Konstante/Einstellung zur Verfügung stellen, mit der modified entscheidet, ob die Versandart im Checkout angezeigt werden soll. Üblicherweise befinden sich dort die Zonen (bzw. zweistellige Länderkürzel), in der die Versandart angeboten werden soll, wie z. B. `DE,AT`.

Die Sprachdateien liegen in `/lang/<LANGUAGE>/modules/shipping/`.

Zudem musst du die Methode `quote()` der Klasse hinzufügen.

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

In der Methode `quote()` werden typischerweise auf die globalen Variablen `$total_weight`, `$shipping_weight`, `$shipping_quoted` und `$shipping_num_boxes` zugegriffen.

| Variable | Beschreibung |
|----------|--------------|
| `$order` | Die Bestelldaten |
| `$shipping_weight` | Gesamtgewicht der Bestellung |
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
