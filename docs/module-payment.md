# Payment Modul

??? note "Textstatus 2"

    Status: 2 von 5 - Erster Entwurf

## Einleitung
Einige Infomationen zur Entwicklung zu Payment Modulen findest du im modified Forum unter https://www.modified-shop.org/forum/index.php?topic=21701

Für einen besseren Überblick schaue dir diese Dokumentation an.

## Allgemeines

Ein Zahlungs- oder Payment Modul ist wie ein normales System Modul aufgebaut. Es benötigt jedoch zusätzliche Mehtoden, die von modified während des Bestellablaufs aufgerufen werden können.

Um ein Shipping Modul zu erstellen, musst du eine Modul Datei in das Verzeichnis `/includes/modules/payment/` anlegen und in dieser Datei eine Modul Klasse hinzufügen.

Die Sprachdateien liegen in `/lang/<LANGUAGES>/modules/payment/`.

## Der Bestellablauf

| **Schritt** | **Name** | **Controller-Datei** |
|-------------|----------|----------------------|
| 1 | `shipping` | /checkout_shipping.php |
| 2 | `payment` | /checkout_payment.php |
| 3 | `confirmation` | /checkout_confirmation.php |
| 4a | `process` | /checkout_process.php |
| 4b | `iFrame` | /checkout_payment_iframe.php (optional) |
| 5 | `success` | /checkout_success.php |


Die Reihenfolge wie die Payment-Modul-Methoden vom modified System abgearbeitet werden ist folgende:

### 1. checkout_shipping.php

In Schritt `shipping` bekommt der Käufer eine Liste mit Versandoptionen angezeigt. Hier kann er sich für eine Versanoption entscheiden. Zahlungsmodule greifen in diesem Schritt nicht in den Bestellablauf ein.

### 2. checkout_payment.php

In Schritt `payment` bekommt der Käufer eine Liste mit Zahlungsoptionen angezeigt. Hier kann er sich für eine Zahlungsoption entscheiden und die nötigen Informationen in die Eingabemaske eintrage. (z. B. IBAN, Kreditkartennummer, etc.).

1. `selection()` - rendert die Eingabemaske
1. `get_error()` - liefert Fehlermeldungen, z. B. bei fehlerhafter Eingabe

### 3. checkout_confirmation.php

In Schritt `confirmation` bekommt der Käufer eine Übersicht mit allen Daten angezeigt, die während des Kaufsprozess erhoben wurden. Wie z. B. Warenkorb, Rechnungs- und Lieferadresse, Versandart und Zahlungsoptionen. Der Käufer wird zudem aufgefordert diese Daten zu bestätigen, um den Kauf abzuschließen.

1. `update_status()` - überprüft, ob die Zahlungsoption möglich ist
1. `pre_confirmation_check()`
1. `confirmation()`
1. `process_button()` - zeigt zusätzliche Daten auf der Confirmation an oder leitet Daten per POST reqeust an process weiter

### 4a. checkout_process.php

In Schritt `process` werden die Daten, die während des Kaufsprozess gesammeltwurden verarbeiet. 

1. `before_process()`
1. `payment_action()`
1. `before_send_order()`
1. `after_process()`

### 4b. checkout_payment_iframe.php (optional)
1. `iframeAction()`

### 5. checkout_success.php

In Schritt `success` wird dem Käufer angezeigt, dass er seine Bestellung erfolgreich getätigt hat.

1. `success()`


## Payment Klassen Methoden

In diesem Abschnitt gehen wir auf alle Methoden im Detail ein.

### update_status()

```php
public function update_status(): void
```

| Option   | Value |
|----------|-------|
| optional | ✅ |
| caller   | checkout_confirmation.php |

#### Beschreibung
Diese Methode wird vom System aufgerufen, um zu kontrollieren, ob die Zahlungsart (weiterhin) zur Verfügung steht. Z. B. können Bedingungen, wie Warenwert, Land, Kundenstatus etc. dazu führen, dass eine Zahlart nicht zur Verfühgung stehen soll. Oft wird dazu auf `$order` oder `$xtPrice` per `global` Statement zugegriffen. Als Ergebnis kann die Methode die Klassenvariable `$this->enabled` auf `true` oder `false` setzen.

#### Zeitpunkt der Verwendung
Die Methode wird zu beginn der `checkout_confirmation.php` aufgerufen.

#### Beispiel

```php
public function update_status(): void
{
    global $order;
    
    if ($order->billing['country']['id'] === 1) {
        $this->enabled = true;
    } else {
        $this->enabled = false;
    }
}
```

### pre_confirmation_check()

```php
public function pre_confirmation_check(): void
```

| Option   | Value |
|----------|-------|
| optional | ✅ |
| caller   | checkout_confirmation.php |

#### Beschreibung:

// TODO: Die Method macht ...

#### Hinweis

Einige Module geben bei dieser Methode als Rückgabewert `boolean` `true` oder `false` zurück. Das ist jedoch nicht nötig, da vom modified Core kein Rückgabewert von der Methode erwartet oder verarbeitet wird.

#### Zeitpunkt der Verwendung
Die Methode wird in `checkout_confirmation.php` aufgerufen.

#### Beispiel

```php
public function pre_confirmation_check(): void
{
    // ...
}
```

### selection()

```php
public function selection(): SelectionArray
```

| Option   | Value |
|----------|-------|
| optional | 🚫 |
| caller   | checkout_payment.php |

#### Beschreibung

Diese Methode wird vom System aufgerufen, um eine Eingabemaske für die Daten zur Zahlungsabwicklung anzuzeigen. In der Methode wird von vielen Modulen auf `$order` per `global` Statement zugegriffen. Als Ergbnis gibt die Methode ein Eingabeformular als Array zurück. Z. B. Felder zur Eingabe von Vor- und Nachname, IBAN, Kreditkartennummer etc.

#### Zeitpunkt der Verwendung
Die Methode wird in `checkout_payment.php` aufgerufen.

#### Objekte und Arraystrukturen

**`SelectionArray (array)`**

| Key  | Typ | Beschreibung | Beispiel |
| - | - | - | - |
| `code`        | string  | Eindeutiger Bezeichner der Versandart in snake_case | mc_my_first_payment_module |
| `module`      | string  | Anzeigeinformation für den Nutzer | Bezahlen mit MyPayment |
| `description` | string  | // TODO: ... | // TODO: ... |
| `fields`      | array   | Array aus mind. einem `SelectionFieldArray` | // TODO: ... |

**`SelectionFieldArray (array)`**

| Key | Typ | Beschreibung | Beispiel |
| - | - | - | - |
| `title` | string  | // TODO: ... | // TODO: ... |
| `field` | string  | Html Eingabefeld | // TODO: ... |

#### Beispiel

```php
public function selection(): array
{
    // ...
    
    $selectionFieldArray = [
        'title' => '// TODO: ...',
        'field' => '<input type="text" name="iban">'
    ];
        
    $selectionArray = [
        'code' => 'mc_my_first_payment_module',
        'module' => 'Bezahlen mit MyPayment',
        'description' => '// TODO: ...',
        'fields' => [$selectionFieldArray]
    ];
        
    return $selectionArray;
}
```


### confirmation()

```php
public function confirmation(): ConfirmationArray
```

| Option   | Value |
|----------|-------|
| optional | ✅ |
| caller   | checkout_confirmation.php |

#### Beschreibung

// TODO: Die Method macht ...

#### Zeitpunkt der Verwendung
Die Methode wird in `checkout_confirmation.php` aufgerufen.

### Objekte und Arraystrukturen

**`ConfirmationArray (array)`**

| Key  | Typ | Beschreibung | Beispiel |
| - | - | - | - |
| `title`  | string  | // TODO: ... | // TODO: ... |
| `fields` | array   | Array aus mind. einem `ConfirmationFieldArray` | |

**`ConfirmationFieldArray (array)`**

| Key | Typ | Beschreibung | Beispiel |
| - | - | - | - |
| `title` | string  | // TODO: ... | // TODO: ... |
| `field` | string  | // TODO: ... | // TODO: ... |

#### Beispiel

```php
public function confirmation(): array
{
    // ...
}
```

## process_button()

```php
public function process_button(): string
```

| Option   | Value |
|----------|-------|
| optional | ✅ |
| caller   | checkout_confirmation.php |

#### Beschreibung

Oft wird diese Methode dazu verwendet, um mit Hilfe von hidden Inputfeldern Informationen per `POST` request weiterzugeben. In der Methode wird von vielen Modulen auf `$order` per `global` Statement zugegriffen. Zudem kann mit dieser Methode weiterer Content auf der Seite angezeigt werden. 

Als Ergbnis gibt die Methode ein Eingabeformular als HTML (string) zurück. Z. B. Felder für Vor- und Nachname, IBAN, Kreditkartennummer etc.

Im Checkout Schritt `proccess` kann dann mit `$_POST` auf die Daten zugegriffen werden.

#### Zeitpunkt der Verwendung

Die Methode wird in `checkout_confirmation.php` aufgerufen.

#### Beispiel

```php
public function process_button(): string
{
    global $order;
    
    $prefix = 'mc_my_first_payment_module_';
    
    $htmlStr = 
        xtc_draw_hidden_field($prefix . 'firstname', 'Max') .
        xtc_draw_hidden_field($prefix . 'lastname', 'Mustermann') .
        xtc_draw_hidden_field($prefix . 'iban', 'DE...');
    
    return $htmlStr;
}
```


## before_process()

```php
public function before_process(): void
```

| Option   | Value |
|----------|-------|
| optional | ✅ |
| caller   | checkout_process.php |

#### Beschreibung

In dieser Methode können Daten für den Zahlungsablauf aufbereitet werden, kurz bevor die Bestellung in die Datenbank eingetragen wird.

// TODO: Die Method macht ...

Anmerkung: Einige Module geben bei dieser Methode als Rückgabewert `boolean` `true` oder `false` zurück. Das ist jedoch nicht nötig, da vom modified Core kein Rückgabewert von der Methode erwartet oder verarbeitet wird.

#### Zeitpunkt der Verwendung

Die Methode wird in Checkout-Step `process` in `checkout_confirmation.php` aufgerufen, bevor die Bestellung `order` verarbeitet und in die Datenbank geschrieben wird.

## payment_action()

```php
public function payment_action(): void
```

| Option   | Value |
|----------|-------|
| optional | ✅ |
| caller   | checkout_process.php |

#### Beschreibung

// TODO: Die Method macht ...

#### Zeitpunkt der Verwendung

Die Methode wird in `checkout_process.php` aufgerufen.

## before_send_order()

```php
public function before_send_order(): void
```

| Option   | Value |
|----------|-------|
| optional | ✅ |
| caller   | checkout_process.php |

#### Beschreibung

// TODO: Die Method macht ...

#### Zeitpunkt der Verwendung

Die Methode wird in `checkout_process.php` aufgerufen.

## after_process()

```php
public function after_process(): void
```

| Option   | Value |
|----------|-------|
| optional | ✅ |
| caller   | checkout_process.php |

#### Beschreibung

// TODO: Die Method macht ...

#### Zeitpunkt der Verwendung

Die Methode wird in `checkout_process.php` aufgerufen.

## success()

```php
public function success(): SuccsessArray
```

| Option   | Value |
|----------|-------|
| optional | ✅ |
| caller   | checkout_success.php |

#### Beschreibung

// TODO: Die Method macht ...

#### Zeitpunkt der Verwendung

Die Methode wird in `checkout_success.php` aufgerufen.

### Objekte und Arraystrukturen

**`SuccsessArray (array)`**

| Key  | Typ | Beschreibung | Beispiel |
| - | - | - | - |
| `title`  | string  | Anzeigeinformation für den Nutzer | Bezahlen mit MyPayment |
| `class`  | string  | Eindeutiger Bezeichner der Versandart in snake_case | mc_my_first_payment_module |
| `fields` | array   | Array aus mind. einem `SuccsessFieldArray` | ... |

**`SuccsessFieldArray (array)`**

| Key | Typ | Beschreibung | Beispiel |
| - | - | - | - |
| `title` | string  | // TODO: ... | // TODO: ... |
| `field` | string  | // TODO: ... | // TODO: ... |

### get_error()

```php
public function get_error(): ErrorArray
```

| Option   | Value |
|----------|-------|
| optional | ✅ |
| caller   | checkout_payment.php |

#### Beschreibung

// TODO: Die Method macht ...

#### Zeitpunkt der Verwendung

Die Methode wird in `checkout_payment.php` aufgerufen.

#### Objekte und Arraystrukturen

**`ErrorArray (array)`**

| Key | Typ | Beschreibung | Beispiel |
| - | - | - | - |
| `title` | string  | Titel der Fehlermeldung | // TODO: ... |
| `error` | string  | Fehlermeldung | // TODO: ... |

### iframeAction()

```php
public function iframeAction(): string
```

| Option   | Value |
|----------|-------|
| optional | ✅ |
| caller   | checkout_payment_iframe.php |

#### Beschreibung

Diese Methode wird vom System aufgerufen, eine Url zu generieren. Diese Url wird von modified in einem iFrame geöffnet.

// TODO: Die Method macht ...

#### Zeitpunkt der Verwendung

Die Methode wird in `checkout_payment_iframe.php` aufgerufen.

### create_paypal_link()

```php
public function create_paypal_link(): ???
```

| Option   | Value |
|----------|-------|
| optional | ✅ |
| caller   | none |

#### Beschreibung

Es sieht so aus, als würd diese Methode niemals vom modified Core aufgerufen werden, obwohl Sie in in der Klasse /includes/classes/payment.php vorhanden ist. Es gibt externe (drittanbieter) Module wie *Micropayment* die die Methode create_paypal_link() über payment.php Klasse aufrufen. Der modified Core selbst scheint dieses nicht zu tun.

// TODO: Die Method macht ...

#### Zeitpunkt der Verwendung

Diese Methode wird nicht vom modified Core auferufen.

### info()

```php
public function info(): mixed
```

| Option   | Value |
|----------|-------|
| optional | ✅ |
| caller   | none |

#### Beschreibung

Es sieht so aus, als würd diese Methode niemals vom modified Core aufgerufen werden, obwohl Sie in in der Klasse /includes/classes/payment.php vorhanden ist. Es gibt externe (drittanbieter) Module wie *Micropayment* die die Methode info() über payment.php Klasse aufrufen. Der modified Core selbst scheint dieses nicht zu tun.

// TODO: Die Method macht ...

#### Zeitpunkt der Verwendung

Diese Methode wird nicht vom modified Core aufgerufen.


## Payment Klasse Codebeispiel

```php

class mc_my_first_payment_module extends StdModule
{
    public function __construct()
    {
        parent::__construct(
            'MODULE_PAYMENT_MC_MY_FIRST_PAYMENT_MODULE'
        );

        $this->checkForUpdate(true);
    }
    
    /**
     * Diese Methode wird vom System aufgerufen, um zu kontrollieren, ob die Zahlungsart
     * (weiterhin) zur Verfügung steht. Z. B. können Bedingungen, wie Warenwert, Land, Kundenstatus etc.
     * dazu führen, dass eine Zahlart nicht zur Verfühgung stehen soll. Oft wird dazu auf $order
     * oder $xtPrice per global Statement zugegriffen. Als Ergebnis kann die Methode die
     * Klassenvariable $this->enabled auf true oder false setzen.
     *
     * @return void
     */
    public function update_status(): void
    {
        global $order, $xtPrice;

        // ...

        $this->enabled = true;
    }

    /**
     * ...
     * 
     * Einige Module geben als Rückgabewert boolean true oder false zurück. Das ist jedoch nicht nötig, da vom
     * modified Core kein Rückgabewert von der Methode erwartet oder verarbeitet wird.
     *  
     * @return void
     */
    public function pre_confirmation_check(): void
    {
    }

    /**
     * ???
     * 
     * @return array ConfirmationArray
     */
    public function confirmation(): array
    {
        // ...
        
        $confirmationFieldArray = [
            'title' => string,
            'field' => string
        ]
            
        
        $confirmationArray = [
            'title' => string,
            'fields' => [
                $confirmationFieldArray
            ]
        ];
        
        return $confirmationArray;
    }

    /**
     * ???
     * 
     * @return string
     */
    public function process_button(): string
    {
        global $order;
    }

    /**
     * ???
     *
     * @return void
     */
    public function before_process(): void
    {
    }

    /**
     * ???
     *
     * @return void
     */
    public function payment_action(): void
    {
    }

    /**
     * ???
     * 
     * @return void
     */
    public function before_send_order(): void
    {
    }

    /**
     * ???
     * 
     * @return void
     */
    public function after_process(): void
    {
    }

    /**
     * ???
     * 
     * @return array SuccessArray
     */
    public function success(): array
    {
    }

    /**
     * ???
     *
     * @return array ErrorArray
     */
    public function get_error() array
    {
    }

    /**
     * ???
     *
     * @return string Eine Url die in einem iFrame geöffnet werden soll.
     */
    public function iframeAction(): string
    {
    }

    /**
     * ???
     *
     * @return ??? type is unknown
     */
    public function create_paypal_link(): ???
    {
    }

    /**
     * ???
     *
     * @return mixed ???
     */
    public function info(): mixed
    {
    }
}
```
