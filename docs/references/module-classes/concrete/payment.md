# Payment - Kontrekte Modul Klasse - Referenz

Erweitert die [abtrakte Modul Klasse](../module-class-abstract.md) um folgende Attribute und Methoden.

## Meta

### Name

| name               | schema / example                                                               | convention         |
|--------------------|--------------------------------------------------------------------------------|--------------------|
| class name         | `<namspce_prefix>_<module_name>` <br> `payment_mc_my_module`                   | [`snake_case`](#)  |
| file name          | `<class_name>.php` <br> `payment_mc_my_module.php`                             | [`snake_case`](#)  |
| configuration name | `<CONFIGURATION_PREFIX>_<CLASS_NAME>`<br>`MODULE_PAYMENT_PAYMENT_MC_MY_MODULE` | [`SCREAM_CASE`](#) |

!!! info "Info"

    modified verwendet keine Namespaces. Unterschiedliche Modulklassen zum selben Modul können dadurch nicht den gleichen Klassennamen erhalten. Um Namenskollisionen und PHP-Fehler zu vermeiden, empfehlen wir den Namespace-Prefix `payment` bei Payment-Klassen zu verwenden. Das hat zur Folge, dass `..._PAYMENT_PAYMENT_...` doppelt im Configuration-Name erscheint, was aber kein Nachteil ist.

### Verzeichnis

| name            | value                               |
|-----------------|-------------------------------------|
| class directory | `/includes/modules/payment/`        |
| lang directory  | `/lang/<LANGUAGE>/modules/payment/` |

### Prefix

| name                 | value                               | convention       |
|----------------------|-------------------------------------|------------------|
| namespace prefix     | `payment`                           | [`snake_case`](#)  |
| configuration prefix | `MODULE_PAYMENT`                    | [`SCREAM_CASE`](#) |



## Attribute

### $form_action_url

```php
public string $form_action_url
```
<h4>Beschreibung</h4>

Mit diesem Attribut kann festgelegt wedern, auf welche Seite das Form Element auf `checkout_confirmation.php`
weiterleitet, wenn auf den "Kaufen" Button geklickt wird. Ist dieses Attribut nicht vorhanden, wird standardmäßig auf
`checkout_process.php` weitergleitet, wo die Bestellung erstellt wird. Soll der Shop den POST-Request auf eine andere
Seite leiten, kann hier die Url eingetragen werden. Siehe auch `$tmpOrders`.

### $tmpOrders

```php
public bool $tmpOrders
```

<h4>Beschreibung</h4>

Mit diesem Attribut kann festgelegt werden, ob in `checkout_process.php` eine temporäre Bestellung erstellt werden soll.
Ist `$tmpOrders` auf `true` gesetzt, wird in `checkout_process.php` eine Bestellung erstellt, der Kunde wird jedoch
nicht darüber per E-Mail informiert. Zudem bekommt die Bestellung den Bestellstatus aus `$tmpStatus`. Ist `$tmpStatus`
Attribut nicht gesetzt, bekommt die Bestellung standardmäßig den Status 0 "Nicht bestätigt". Wenn `$tmpOrders` auf
`true` gesetzt ist, leitet der Shop in `checkout_confirmation.php` nicht mehr nach `$form_action_url` weiter.

### $tmpStatus

```php
public int $tmpStatus
```

<h4>Beschreibung</h4>

Mit `$tmpStatus` kann festgelegt werden, welcher StatusId eine temporäre Bestellung soll. Siehe auch `$tmpOrders`.

## Methoden

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

| Key           | Typ    | Beschreibung                                        | Beispiel                   |
|---------------|--------|-----------------------------------------------------|----------------------------|
| `id`          | string | Eindeutiger Bezeichner der Zahlart in snake_case    | mc_my_first_payment_module |
| `module`      | string | Anzeigename auf `checkout_payment.php`              | Bezahlen mit MyPayment     |
| `description` | string | Anzeigebeschreibung auf `checkout_payment.php`      | Einfach bezahlen mit ...   |
| `fields`      | array  | Array aus mind. einem `SelectionFieldArray`         | // TODO: ...               |


**`SelectionFieldArray (array)`**

| Key     | Typ    | Beschreibung     | Beispiel     |
|---------|--------|------------------|--------------|
| `title` | string | // TODO: ...     | // TODO: ... |
| `field` | string | Html Eingabefeld | // TODO: ... |


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
        'id' => 'mc_my_first_payment_module',
        'module' => 'Bezahlen mit MyPayment',
        'description' => 'Einfach bezahlen mit MyPayment',
        'fields' => [$selectionFieldArray]
    ];

    return $selectionArray;
}
```

![Payment Module Class Method Selection - modifed Shop](/assets/images/modified_payment_method_selection_01.png)

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

| Key      | Typ    | Beschreibung                                   | Beispiel     |
|----------|--------|------------------------------------------------|--------------|
| `title`  | string | // TODO: ...                                   | // TODO: ... |
| `fields` | array  | Array aus mind. einem `ConfirmationFieldArray` |              |

**`ConfirmationFieldArray (array)`**

| Key     | Typ    | Beschreibung | Beispiel     |
|---------|--------|--------------|--------------|
| `title` | string | // TODO: ... | // TODO: ... |
| `field` | string | // TODO: ... | // TODO: ... |


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

Die Methode wird nur aufgerufen, wenn eine temporäre Bestellung erzeugt wird. Siehe auch `$tmpOrder`.
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

| Option   | Value                |
|----------|----------------------|
| optional | ✅                   |
| caller   | checkout_process.php |

<h4>Beschreibung</h4>

// TODO: Die Method macht ...

<h4>Zeitpunkt der Verwendung</h4>

Die Methode wird in `checkout_process.php` aufgerufen.

### success()

```php
public function success(): SuccsessArray
```

| Option   | Value                |
|----------|----------------------|
| optional | ✅                   |
| caller   | checkout_success.php |

<h4>Beschreibung</h4>

// TODO: Die Method macht ...

<h4>Zeitpunkt der Verwendung</h4>

Die Methode wird in `checkout_success.php` aufgerufen.

<h4>Objekte und Arraystrukturen</h4>

**`SuccsessArray (array)`**

| Key      | Typ    | Beschreibung                                        | Beispiel                   |
|----------|--------|-----------------------------------------------------|----------------------------|
| `title`  | string | Anzeigeinformation für den Nutzer                   | Bezahlen mit MyPayment     |
| `class`  | string | Eindeutiger Bezeichner der Versandart in snake_case | mc_my_first_payment_module |
| `fields` | array  | Array aus mind. einem `SuccsessFieldArray`          | ...                        |


**`SuccsessFieldArray (array)`**

| Key     | Typ    | Beschreibung | Beispiel     |
|---------|--------|--------------|--------------|
| `title` | string | // TODO: ... | // TODO: ... |
| `field` | string | // TODO: ... | // TODO: ... |

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

| Key     | Typ    | Beschreibung            | Beispiel     |
|---------|--------|-------------------------|--------------|
| `title` | string | Titel der Fehlermeldung | // TODO: ... |
| `error` | string | Fehlermeldung           | // TODO: ... |


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

### javascript_validation()

```php
public function javascript_validation(): string
```

| Option   | Value |
|----------|-------|
| optional | ✅ |
| caller   | checkout_shipping.php, checkout_payment.php |

<h4>Beschreibung</h4>

Diese Methode wird vom System aufgerufen, um Javascript in den Header zu laden.

// TODO: Die Method macht ...

<h4>Zeitpunkt der Verwendung</h4>

Die Methode wird in `checkout_shipping.php` und `checkout_payment.php` aufgerufen.

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

## Beispiele

Beispieldateien findest du unter [github.com/RobinTheHood/class-extensions](https://github.com/RobinTheHood/class-extensions/blob/master/new_files/includes/modules/payment/).
