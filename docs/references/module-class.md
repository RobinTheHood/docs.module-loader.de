# Modul Klasse - Referenz

Basic Klasse. Wird von anderen Modul Klassen erweitert.

## Attribute

### $code

```php
/**
 * Eine Bezeichnung oder Id, mit der das Modul bzw.
 * die Klasse eindeutig angesprochen werden kann.
 *
 * Im Grunde kann hier der Klassennamen verwendet werden,
 * da diese auch nur einmal vorkommen darf.
 *
 * Beispielwert: 'mc_my_first_module'
 */
public string $code;
```

### $title

```php
/**
 * Der Name des Moduls, wie er im Backend in der jeweiligen
 * Sprache für den Benutzer angezeigt werden soll:
 *
 * Beispielwert: 'Mein ersten Modul von My Company'
 */
public string $title;
```

### $description

```php
/**
 * Neben dem Namen können wir im Backend auch noch eine
 * längere Beschreibung mit zum Modul ausgeben.
 *
 * Beispielwert: 'Das ist mein ersten Modul. Es dient
 * dem reinen Demonstationszweck und kann leider nicht viel'
 */
public $description;
```

### $enabled

```php
/**
 * Gibt an, ob das Modul aktiv sein soll oder nicht. Im
 * Backend wird dieses in der liste der System Module durch
 * ein grünen oder roten Punkt hinter dem Namen angezeigt.
 *
 * Beispielwert: true
 */
public bool $enabled;
```

### $sort_order

```php
/**
 * Mit diesem Wert kann die Position im Backend festgelegt werden,
 * an der das Modul angezeigt werden soll. Da wir kaum beeinflussen
 * können, welchen Wert hier andere Modul-Entwickler eintragen,
 * ist dieses Attribut meist nicht besonders hilfreich.
 *
 * Beispielwert: 0
 */
public int $sort_order;
```

### $keys

```php
/**
 * Auf $keys greift modified nicht direkt zu, sondern über die
 * Methode public function key(): array
 *
 * Für mehr Infos siehe Methode public function keys(): array
 */
protected array $keys;
```

### $keys_dispnone

```php
/**
 * Optional
 *
 * Diese Keys sollen nicht angezeigt werden.
 */
public array $keys_dispnone;
```

### $properties

```php
/**
 * Optional
 *
 * Hier fehlt die Beschreibung ...
 *
 * Mögliche Werte:
 * - form_edit : Beschreibung ...
 * - form_restore : Beschreibung ...
 * - restore : Beschreibung ...
 * - form_backup : Beschreibung ...
 * - backup : Beschreibung ...
 * - form_remove : Beschreibung ...
 * - remove : Beschreibung ...
 * - btn_edit : Beschreibung ...
 * - process_key : Nur bei System und Export Modulen, Beschreibug ..
 * - add_content : Beschreibung ...
 */
public array $properties;
```

### $extended_description

```php
/**
 * Optional
 *
 * Hier fehlt die Beschreibung ...
 *
 * Wird nur bei Klassenerweiterungen angezeigt
 */
public string $extended_description;
```


## Methoden

### __construct()

```php
public function __construct()
```

<h4>Beschreibung</h4>

??? note "Textstatus 1"

    Status: 1 von 5 - Skizze

Lorem ...

<h4>Beispiel</h4>

```php
public function __construct()
{
    // Der Wert in $prefix ist: MODULE_MC_MY_FIRST_MODULE
    $prefix = 'MODULE_' . strtoupper(self::class);

    $this->code        = self::class;
    $this->title       = constant($prefix . '_TITLE');
    $this->description = constant$prefix . '_DESC');
    $this->sort_order  = constant$prefix . '_SORT_ORDER');
    $this->enabled     = defined($prefix . '_STATUS') && 'true' === constant($prefix . '_STATUS');
}
```

### keys()

```php
public function keys(): array
```

<h4>Beschreibung</h4>

??? note "Textstatus 2"

    Status: 2 von 5 - Erster Entwurf

Der modified Core ruft diese Methode auf, um sich eine Liste mit allen
Einstellungen aus der Datenbank aus der Tabelle Configuration zu holen. Diese Werte werden z. B. in der Admin Modul Übersicht und beim Bearbeiten angezeigt. Gibst du keinen Key an, wird dir auch keiner angezeigt und du kannst keinen bearbeiten. Zudem kann der modified Core anhand dieser key abgleichen, ob keys in der Datenbank fehlen. Unabhängig davon welche key hier angegeben werden, lädt modified zu Beginn eines jeden Requests alle Configuration Werte aus der Datenbank als Konstante.

<h4>Beispiel</h4>

```php
public function keys(): array
{
    return [
        'MODULE_MC_MY_FIRST_MODULE_ATTRIBUTE_1',
        'MODULE_MC_MY_FIRST_MODULE_ATTRIBUTE_2'
    ];
}
```

### check()

```php
public function check(): int
```

<h4>Beschreibung</h4>

??? note "Textstatus 2"

    Status: 2 von 5 - Erster Entwurf

Diese Methode überprüft, ob das Modul installiert ist. Das kann auf unterschiedliche Art und Weise passieren. Wenn das Modul ordnungsgemäß installiert ist, muss die Methode eine Zahl ungleich 0 zurückgeben.

In diesem Beispiel kontrollieren wir, ob in der Datenbanktabelle `configuration` in der Spalte `configuration_key` ein Eintrag mit dem Wert `MODULE_MC_MY_FIRST_MODULE_STATUS` vorhanden ist.

Du kannst dir theoretisch einen eigenen Weg ausdenken, um zu überprüfen, ob dein Modul korrekt installiert ist. Z. B. ob ein anderer Wert vorhanden ist, oder ob bestimmte Dateien vorhanden sind. Die meisten System Module machen das jedoch immer auf dem hier gezeigten Weg über die Konstante `MODULE_MC_MY_FIRST_MODULE_STATUS`:

<h4>Beispiel</h4>

```php
public function check(): int
{
    // Der Wert in $prefix ist: MODULE_MC_MY_FIRST_MODULE
    $prefix = 'MODULE_' . strtoupper(self::class);

    // Der Wert in $key ist: MODULE_MC_MY_FIRST_MODULE_STATUS
    $key = $prefix . '_STATUS';

    // SQL-Abfrage, ob MODULE_MC_MY_FIRST_MODULE_STATUS vorhanden ist
    $sql = "SELECT configuration_value FROM " . TABLE_CONFIGURATION . " WHERE configuration_key = '$key'"

    $query = xtc_db_query($sql);

    // Liefert 0, wenn kein Eintrag vorhanden ist, ansonsten
    // einen anderen Wert.
    return xtc_db_num_rows($query);
}
```

### install()

```php
public function install(): void
```

<h4>Beschreibung</h4>

??? note "Textstatus 2"

    Status: 2 von 5 - Erster Entwurf

Diese Methode wird aufgerufen, wenn der Nutzer im Admin beim Modul auf die Taste "Installieren" klickt. In diesem Moment müssen wir selbst die nötigen Configurationen in der Datenbank anlegen. Wie du das machst, siehst du im folgenden Beispiel:

!!! bug "TODO"

    Erklären welche Dinge man noch in der install Methode machen kann, wie z. B. weitere Configurationen anlegen oder den Access auf Admin Controller Dateien erlauben.

<h4>Beispiel</h4>

```php
public function install(): void
{
    // MODULE_MC_MY_FIRST_MODULE
    $prefix = 'MODULE_' . strtoupper(self::class);

    // MODULE_MC_MY_FIRST_MODULE_STATUS
    $key = $prefix . '_STATUS';

    $value = 'true';

    // Erklärung ...
    $groupId = 6;

    // Erklärung ...
    $sortOrder = 1;

    // Erklärung ...
    $setFunction = "xtc_cfg_select_option(array('true', 'false'),";

    // Erklärung ...
    $setFunction = str_replace("'", "\\'", $setFunction);

    // Erklärung ...
    $useFunction = '';

    // Erklärung ...
    $sql = "INSERT INTO `" . TABLE_CONFIGURATION . "` (`configuration_key`, `configuration_value`, `configuration_group_id`, `sort_order`, `set_function`, `use_function`, `date_added`) VALUES ('$key', '$value', '$groupId', '$sortOrder', '$setFunction', '$useFunction', NOW())"

    // Erklärung ...
    xtc_db_query($sql);
}
```

### remove()

```php
public function remove(): void
```

<h4>Beschreibung</h4>

??? note "Textstatus 1"

    Status: 1 von 5 - Skizze

Lorem ...

<h4>Beispiel</h4>

```php
public function remove(): void
{
    // MODULE_MC_MY_FIRST_MODULE
    $prefix = 'MODULE_' . strtoupper(self::class);

    // MODULE_MC_MY_FIRST_MODULE_STATUS
    $key = $prefix . '_STATUS';

    xtc_db_query("DELETE FROM " . TABLE_CONFIGURATION . " WHERE configuration_key = '$key'");
}
```

### display()

```php
public function display(): string
```

<h4>Beschreibung</h4>

??? note "Textstatus 1"

    Status: 1 von 5 - Skizze

```php

/**
 * @return string[] in Form von ['text' => 'HTML']
 */
public function display(): array
```

<h4>Beschreibung</h4>

In der Bearbeitungsansicht eines Moduls generiert der modified Core die Eingabefelder für jede Eigenschaft, die mit `function keys(): array` zurückgeben wird. Weitere Eingabefelder oder Buttons wie Speichern oder Abbruch sind nicht vorhanden. Mit der `display()` Methode können beliebe Buttons oder andere beliebige HTML-Ausgaben generiert werden. Sie wird fast immer für die Ausgabe des Save-Buttons verwendet. Die HTML-Ausgabe von `display()` wird in der Reinfolge hinter den Eigenschafte (Keys) dargestellt.

### custome()

```php
public function custome(): ???
```

<h4>Beschreibung</h4>

??? note "Textstatus 1"

    Status: 1 von 5 - Skizze

Sie Beispiel `/admin/includes/modules/system/image_processing_step.php`

Lorem ...

### process()

```php
public function process(string $filePath): void
```

<h4>Beschreibung</h4>

??? note "Textstatus 1"

    Status: 1 von 5 - Skizze

Wird vom modified Core aufgerufen, sobald auf im Bearbeitungsmodus auf die Taste `Save` geklickt wird, bzw. sobald die Action `save`. In diesem Fall wird als Parameter `$filePath` ??? übergeben (siehe `admin/module_export.php:136`) Mit `$_POST['process'] == 'module_processing_do'` kann verhindert werden, dass die Methode `function process(string $filePath): void` durch den Core aufgerufen wird.

Eine alternative die Methode durch den modified Core aufrufen zu lassen ist über `?action=module_processing_d` in diesem Fall wird `$_GET['file']` an die Methode als Parameter `$filePath` übergeben.