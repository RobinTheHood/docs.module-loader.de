---
title: Payment Modul Klasse für modified programmieren
description: Ein Zahlungs- oder Payment Modul ist wie ein normales System Modul aufgebaut. Es benötigt jedoch zusätzliche Mehtoden, die von modified während des Bestellablaufs aufgerufen werden können.
---

# Payment Modul Klasse für modified programmieren

??? note "Textstatus - Entwurf"

    Status: 2 von 5 - Erster Entwurf: Erste Ausformulierung einiger Informationen.

## Konzept

Einige Informationen zur Entwicklung zu Payment Modulen findest du im modified Forum unter [www.modified-shop.org/forum/index.php?topic=21701](https://www.modified-shop.org/forum/index.php?topic=21701)

Für einen besseren Überblick schaue dir diese Dokumentation an.

## Aufbau

Ein Zahlungs- oder Payment Modul ist wie ein normales System Modul aufgebaut. Es benötigt jedoch zusätzliche Mehtoden, die von modified während des Bestellablaufs aufgerufen werden können.

Um eine Payment Modul Klasse zu erstellen, musst du eine Modul-Datei in das Verzeichnis `/includes/modules/payment/` anlegen. Die Sprachdateien liegen in `/lang/<LANGUAGE>/modules/shipping/`.

Die Sprachdateien liegen in `/lang/<LANGUAGES>/modules/payment/`.

```
├── includes
│   └── modules
│       └── payment
│           └── mc_my_first_module.php
└── lang
	└── <LANGUAGE>
		└── modules
			└── payment
				└── mc_my_first_module.php
```

Eine Liste mit allen Modul Klassen und deren Methoden, die du erweitern kannst, gibt es als Muster-Dateien unter [github.com/RobinTheHood/class-extensions](https://github.com/RobinTheHood/class-extensions)

## Der Bestellablauf

| **Schritt** | **Name** | **Controller-Datei** |
|-------------|----------|----------------------|
| 1 | `shipping` | /checkout_shipping.php |
| 2 | `payment` | /checkout_payment.php |
| 3 | `confirmation` | /checkout_confirmation.php |
| 4a | `process` | /checkout_process.php |
| 4b | `iFrame` | /checkout_payment_iframe.php (optional) |
| 5 | `success` | /checkout_success.php |


Die Reihenfolge, wie die Payment-Modul-Methoden vom modified System abgearbeitet werden, ist folgende:

### 1. checkout_shipping.php { data-toc-label='checkout_shipping' }

In Schritt `shipping` bekommt der Käufer eine Liste mit Versandoptionen angezeigt. Hier kann er sich für eine Versandoption entscheiden. Zahlungsmodule greifen in diesem Schritt nicht in den Bestellablauf ein.

### 2. checkout_payment.php { data-toc-label='checkout_payment' }

In Schritt `payment` bekommt der Käufer eine Liste mit Zahlungsoptionen angezeigt. Hier kann er sich für eine Zahlungsoption entscheiden und die nötigen Informationen in die Eingabemaske eintrage. (z. B. IBAN, Kreditkartennummer, etc.).

1. `selection()` - rendert die Eingabemaske
1. `get_error()` - liefert Fehlermeldungen, z. B. bei fehlerhafter Eingabe

### 3. checkout_confirmation.php { data-toc-label='checkout_confirmation' }

In Schritt `confirmation` bekommt der Käufer eine Übersicht mit allen Daten angezeigt, die während des Kaufprozesses erhoben wurden. Wie z. B. Warenkorb, Rechnungs- und Lieferadresse, Versandart und Zahlungsoptionen. Der Käufer wird zudem aufgefordert, diese Daten zu bestätigen, um den Kauf abzuschließen.

1. `update_status()` - überprüft, ob die Zahlungsoption möglich ist
1. `pre_confirmation_check()`
1. `confirmation()`
1. `process_button()` - zeigt zusätzliche Daten auf der Confirmation an oder leitet Daten per POST reqeust an process weiter

### 4a. checkout_process.php { data-toc-label='checkout_process' }

In Schritt `process` werden die Daten, die während des Kaufsprosses gesammelt wurden, verarbeitet.

1. `before_process()`
1. `payment_action()`
1. `before_send_order()`
1. `after_process()`

### 4b. checkout_payment_iframe.php (optional) { data-toc-label='checkout_payment_iframe' }
1. `iframeAction()`

### 5. checkout_success.php { data-toc-label='checkout_success' }

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

<h4>Beschreibung</h4>

Diese Methode wird vom System aufgerufen, um zu kontrollieren, ob die Zahlungsart (weiterhin) zur Verfügung steht. Z. B. können Bedingungen, wie Warenwert, Land, Kundenstatus etc. dazu führen, dass eine Zahlart nicht zur Verfühgung stehen soll. Oft wird dazu auf `$order` oder `$xtPrice` per `global` Statement zugegriffen. Als Ergebnis kann die Methode die Klassenvariable `$this->enabled` auf `true` oder `false` setzen.

<h4>Zeitpunkt der Verwendung</h4>

Die Methode wird zu Beginn der `checkout_confirmation.php` aufgerufen.

<h4>Beispiel</h4>

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

<h4>Beschreibung</h4>

// TODO: Die Method macht ...

!!! note "Anmerkung"

    Einige Module geben bei dieser Methode als Rückgabewert `boolean` `true` oder `false` zurück. Das ist jedoch nicht nötig, da vom modified Core kein Rückgabewert von der Methode erwartet oder verarbeitet wird.

<h4>Zeitpunkt der Verwendung</h4>
Die Methode wird in `checkout_confirmation.php` aufgerufen.

<h4>Beispiel</h4>

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

<h4>Beschreibung</h4>

Diese Methode wird vom System aufgerufen, um eine Eingabemaske für die Daten zur Zahlungsabwicklung anzuzeigen. In der Methode wird von vielen Modulen auf `$order` per `global` Statement zugegriffen. Als Ergebnis gibt die Methode ein Eingabeformular als Array zurück. Z. B. Felder zur Eingabe von Vor- und Nachname, IBAN, Kreditkartennummer etc.

<h4>Zeitpunkt der Verwendung</h4>

Die Methode wird in `checkout_payment.php` aufgerufen.

<h4>Objekte und Arraystrukturen</h4>

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

<h4>Beispiel</h4>

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

<h4>Beschreibung</h4>

// TODO: Die Method macht ...

<h4>Zeitpunkt der Verwendung</h4>

Die Methode wird in `checkout_confirmation.php` aufgerufen.

<h4>Objekte und Arraystrukturen</h4>

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

<h4>Beispiel</h4>

```php
public function confirmation(): array
{
    // ...
}
```

### process_button()

```php
public function process_button(): string
```

| Option   | Value |
|----------|-------|
| optional | ✅ |
| caller   | checkout_confirmation.php |

<h4>Beschreibung</h4>

Oft wird diese Methode dazu verwendet, um mithilfe von hidden Inputfeldern Informationen per `POST` request weiterzugeben. In der Methode wird von vielen Modulen auf `$order` per `global` Statement zugegriffen. Zudem kann mit dieser Methode weiterer Content auf der Seite angezeigt werden.

Als Ergebnis gibt die Methode ein Eingabeformular als HTML (string) zurück. Z. B. Felder für Vor- und Nachname, IBAN, Kreditkartennummer etc.

Im Checkout Schritt `proccess` kann dann mit `$_POST` auf die Daten zugegriffen werden.

<h4>Zeitpunkt der Verwendung</h4>

Die Methode wird in `checkout_confirmation.php` aufgerufen.

<h4>Beispiel</h4>

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


### before_process()

```php
public function before_process(): void
```

| Option   | Value |
|----------|-------|
| optional | ✅ |
| caller   | checkout_process.php |

<h4>Beschreibung</h4>

In dieser Methode können Daten für den Zahlungsablauf aufbereitet werden, kurz bevor die Bestellung in die Datenbank eingetragen wird.

// TODO: Die Method macht ...

!!! note "Anmerkung"

    Einige Module geben bei dieser Methode als Rückgabewert `boolean` `true` oder `false` zurück. Das ist jedoch nicht nötig, da vom modified Core kein Rückgabewert von der Methode erwartet oder verarbeitet wird.

<h4>Zeitpunkt der Verwendung</h4>

Die Methode wird in Checkout-Step `process` in `checkout_confirmation.php` aufgerufen, bevor die Bestellung `order` verarbeitet und in die Datenbank geschrieben wird.

### payment_action()

```php
public function payment_action(): void
```

| Option   | Value |
|----------|-------|
| optional | ✅ |
| caller   | checkout_process.php |

<h4>Beschreibung</h4>

// TODO: Die Method macht ...

<h4>Zeitpunkt der Verwendung</h4>

Die Methode wird in `checkout_process.php` aufgerufen.

### before_send_order()

```php
public function before_send_order(): void
```

| Option   | Value |
|----------|-------|
| optional | ✅ |
| caller   | checkout_process.php |

<h4>Beschreibung</h4>

// TODO: Die Method macht ...

<h4>Zeitpunkt der Verwendung</h4>

Die Methode wird in `checkout_process.php` aufgerufen.

### after_process()

```php
public function after_process(): void
```

| Option   | Value |
|----------|-------|
| optional | ✅ |
| caller   | checkout_process.php |

<h4>Beschreibung</h4>

// TODO: Die Method macht ...

<h4>Zeitpunkt der Verwendung</h4>

Die Methode wird in `checkout_process.php` aufgerufen.

### success()

```php
public function success(): SuccsessArray
```

| Option   | Value |
|----------|-------|
| optional | ✅ |
| caller   | checkout_success.php |

<h4>Beschreibung</h4>

// TODO: Die Method macht ...

<h4>Zeitpunkt der Verwendung</h4>

Die Methode wird in `checkout_success.php` aufgerufen.

<h4>Objekte und Arraystrukturen</h4>

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

<h4>Beschreibung</h4>

// TODO: Die Method macht ...

<h4>Zeitpunkt der Verwendung</h4>

Die Methode wird in `checkout_payment.php` aufgerufen.

<h4>Objekte und Arraystrukturen</h4>

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

<h4>Beschreibung</h4>

Diese Methode wird vom System aufgerufen, eine URL zu generieren. Diese URL wird von modified in einem iFrame geöffnet.

// TODO: Die Method macht ...

<h4>Zeitpunkt der Verwendung</h4>

Die Methode wird in `checkout_payment_iframe.php` aufgerufen.

### create_paypal_link()

```php
public function create_paypal_link(): ???
```

| Option   | Value |
|----------|-------|
| optional | ✅ |
| caller   | none |

<h4>Beschreibung</h4>

Es sieht so aus, als würde diese Methode niemals vom modified Core aufgerufen werden, obwohl diese in der Klasse `/includes/classes/payment.php` vorhanden ist. Es gibt externe (Drittanbieter) Module, wie *Micropayment* die die Methode `create_paypal_link()` über die `payment.php` Klasse aufrufen. Der modified Core selbst scheint dieses nicht zu tun.

// TODO: Die Method macht ...

<h4>Zeitpunkt der Verwendung</h4>

Diese Methode wird nicht vom modified Core auferufen.

### info()

```php
public function info(): mixed
```

| Option   | Value |
|----------|-------|
| optional | ✅ |
| caller   | none |

<h4>Beschreibung</h4>

Es sieht so aus, als würde diese Methode niemals vom modified Core aufgerufen werden, obwohl diese in der Klasse `/includes/classes/payment.php` vorhanden ist. Es gibt externe (Drittanbieter) Module, wie *Micropayment* die die Methode `info()` über die Klasse `payment.php` aufrufen. Der modified Core selbst scheint dieses nicht zu tun.

// TODO: Die Method macht ...

<h4>Zeitpunkt der Verwendung</h4>

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
     * @return string Eine URL die in einem iFrame geöffnet werden soll.
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
