---
title: System Modul Klasse für modified programmieren
description: In diesem Abschnitt erklären wir dir alles, was du wissen musst, um ein System Modul für modified zu programmieren.
---

# System Modul Klasse für modified programmieren

In diesem Abschnitt erklären wir dir alles, was du wissen musst, um ein System Modul für modified zu programmieren.

!!! warning "Achtung"

	Ein Modul solltest du immer zusammen mit einem System Modul entwickeln, auch wenn das von modified nicht vorgeschrieben wird. Du könntest zwar Autoincludes unabhängig von einem System Modul programmieren, best practice ist aber, dass dein Modul ein System Modul beinhaltet. Durch dieses kann der Anwender an einem zentralen Ort im Shop alle Module und Erweiterungen steuern und bedienen.


## Konzept

??? note "Textstatus - Entwurf"

    Status: 2 von 5 - Erster Entwurf: Erste Ausformulierung einiger Informationen.

Eine System Modul Klasse ist der zentrale Anker deines Moduls. Es ist eine PHP Klasse, die hauptsächlich die Installation deines Moduls steuert. Zudem stellt das System Modul den ersten Anlaufpunkt für grundlegende Einstellungen für den User zu deinem Modul dar.

System Module werden dir im Admininterface unter dem Menüpunkt  *Admin > Module > System Module*  aufgelistet. Dort erscheinen diese entweder als *installiert* oder *deinstalliert*.

Eine System Modul Klasse basiert vom Aufbau auf eine [Modul Klasse](/module-class/), die keine Attribute oder Methoden erweitert.


## Aufbau

Eine System Modul Klasse unterscheidet sich nicht von der abstrakten Modul Klasse, die wir im Abschnitt [Modul Klasse (abstract)](/module-class/) beschreiben. Lese dir den Abschnitt [Modul Klasse (abstract)](/module-class/) durch, um den Aufbau zu verstehen. In diesem Abschnitt gehen wir auf einige konkrete Aspekte der System Modul Klasse ein, die nicht im Abschnitt [Modul Klasse (abstract)](/module-class/) beschrieben wurden.

Für eine System Modul Klasse benötigen wir mindestens zwei Dateien. Eine Modul Klasse in `/admin/includes/modules/system/` und eine Sprachdatei in `/lang/<LANGUAGES>/modules/system/`. Du kannst wie bei allen Modul Klassen je Sprache eine weitere Datei zum System hinzufügen.

Du solltest beiden Dateien gleich benennen, damit für jeden auf den ersten Blick erkennbar ist, dass die beiden Dateien zusammengehören. In diesem Beispiel haben wir die beiden Dateien `system_mc_my_first_module.php` benannt. Orientiere dich für das Benennen von Dateien an den Namingconventions aus dem Abschnitt [???](#).

```
├── admin
│   └── includes
│       └── modules
│           └── system
│               └── system_mc_my_first_module.php
└── lang
	└── <LANGUAGE>
		└── modules
			└── system
				└── system_mc_my_first_module.php
```

Eine Liste mit allen Modul Klassen und deren Methoden, die du erweitern kannst, gibt es als Muster-Dateien unter [github.com/RobinTheHood/class-extensions](https://github.com/RobinTheHood/class-extensions)


## Wie wird ein System Modul in modified programmiert?

??? note "Textstatus - Entwurf"

    Status: 2 von 5 - Erster Entwurf: Erste Ausformulierung einiger Informationen.

Wenn du ein System Modul von Hand (ohne das StandardModul aus dem MMLC) erstellen möchtest, kannst du den kompletten Programmcode am Ende dieses Abschnitts kopieren und auf deine Bedürfnisse anpassen.

Im nächsten Abschnitt zeigen wir dir, wie viel weniger Arbeit du hast, wenn du ein System Modul mit dem StandardModul aus dem MMLC erstellst.

Nicht erschrecken, in diesem Abschnitt zeigen wir dir eine Menge Programmcode. Dieser ist glücklicherweise darauf optimiert, leicht lesbar zu sein. Du musst diesen Code nur an wenige Stellen verändern, um ihn an ein eigenes Modul anzupassen. Wir gehen zudem auf alle Codeabschnitte genau ein und erklären dir, was die jeweiligen Zeilen machen und warum diese nötig sind. Oft erklären wir die Funktionalität anhand von Kommentaren im Quellcode.

Wie du sehen wirst, benötigt ein System Modul relativ viel Code. Die Schreibarbeit könnte uns das modified Entwicklerteam mit einer einfachen Anpassung im Code ersparen. Das würde es Entwicklern erleichtert eigene System Module und Klassenerweiterungen zu schreiben.

Mit dem StandardModul aus dem MMLC lässt sich dieser immer wiederkehrende Code jedoch trotzdem vermeiden. Mehr dazu später im Abschnitt [???](#). Als Erstes schauen wir uns ein System Modul ohne das StandardModul an, damit du die grundlegende Arbeitsweise von System Modulen verstehst.

### Minimale Voraussetungen für eine System Modul Klasse

Eine System Modul-Datei bzw. eine kleinst mögliche System Modul Klasse besteht mindestens aus den folgenden Elementen:

#### Attribute

- `public string $code`
- `public string $title`
- `public string $description`
- `public bool $enabled`
- `public int $sort_order`
- `string[] $keys`

#### Methoden

- `public function keys(): array`
- `public function check(): int`
- `public function install(): void`
- `public function remove(): void`

Was die jeweiligen Aufgaben dieser Elemente sind, werden wir uns im folgenden Abschnitt ausführlich ansehen. Am Ende zeigen wir dir eine vollständige System Modul Datei.

### Die Klasse

??? note "Textstatus - Entwurf"

    Status: 2 von 5 - Erster Entwurf: Erste Ausformulierung einiger Informationen.

Eine System Modul Datei besteht aus genau einer Klasse. Diese Klasse muss den gleichen Namen (ohne `.php`) wie die Datei heißen, in der sie liegen. Alle System Module Dateien liegen im Verzeichnis `/admin/includes/modules/`. Als Beispiel nehmen wir eine Datei mit dem Namen `system_mc_my_first_module.php`. An welche Namenskonventionen du dich halten sollten, kannst du im Abschnitt [???](#) lesen.

Das bedeutet für uns, dass wir die Klasse `system_mc_my_first_module` nennen müssen. Wenn wir uns nicht an diese Konvention halten, lädt der Klassenloader im modified Core die Datei nicht in den Speicher und wir können die Klasse nicht verwenden. Uns wird dann im Adminbereich unter Module > System Module die Klasse nicht angezeigt. Auch würden abhängige Autoinclude-Dateien nicht funktionieren. Hier ein Beispiel:

=== "Mit StdModule"

    ```php title="/admin/includes/modules/stytem/system_mc_my_first_module.php"
    <?php

    declare(strict_types=1);

    use RobinTheHood\ModifiedStdModule\Classes\StdModule;

    class system_mc_my_first_module extends StdModul
    {
        ...
    }
    ```

=== "Ohne StdModule"

    ```php title="/admin/includes/modules/stytem/system_mc_my_first_module.php"
    <?php

    declare(strict_types=1);

    defined('_VALID_XTC') or die('Direct Access to this location is not allowed.');

    class system_mc_my_first_module
    {
        ...
    }
    ```

### Die Attribute

??? note "Textstatus - Entwurf"

    Status: 2 von 5 - Erster Entwurf: Erste Ausformulierung einiger Informationen.

Die Klasse benötigt 6 Attribute auf die das modified zugreift, um sich Informationen von unserer System Modul Klasse zu holen. Aus diesem Grund müssen wir die folgenden Attribute definieren. Machen wir das nicht, wirft uns PHP je nach Einstellung Notice, Warnings oder Erorrs aus. Das sollten wir vermeiden.

```php
class system_mc_my_first_module
{
    /**
     * Eine Bezeichnung oder Id, mit der das Modul bzw.
     * die Klasse eindeutig angesprochen werden kann.
     *
     * Im Grunde kann hier der Klassennamen verwendet werden,
     * da diese auch nur einmal vorkommen darf.
     *
     * Beispielwert: 'system_mc_my_first_module'
     *
     * @var string $code
     */
    public string $code;

    /**
     * Der Name des Moduls, wie er im Backend in der jeweiligen
     * Sprache für den Benutzer angezeigt werden soll:
     *
     * Beispielwert: 'Mein ersten Modul von My Company'
     *
     * @var string $title
     */
    public string $title;

    /**
     * Neben dem Namen können wir im Backend auch noch eine
     * längere Beschreibung mit zum Modul ausgeben.
     *
     * Beispielwert: 'Das ist mein ersten Modul. Es dient
     * dem reinen Demonstationszweck und kann leider nicht viel'
     *
     * @var string $description
     */
    public $description;

    /**
     * Gibt an, ob das Modul aktiv sein soll oder nicht. Im
     * Backend wird dieses in der liste der System Module durch
     * ein grünen oder roten Punkt hinter dem Namen angezeigt.
     *
     * Beispielwert: true
     *
     * @var bool $enabled
     */
    public bool $enabled;


    /**
     * Mit diesem Wert kann die Position im Backend festgelegt werden,
     * an der das Modul angezeigt werden soll. Da wir kaum beeinflussen
     * können, welchen Wert hier andere Modul-Entwickler eintragen,
     * ist dieses Attribut meist nicht besonders hilfreich.
     *
     * Beispielwert: 0
     *
     * @var int $sort_order
     */
    public int $sort_order;


    /**
     * Auf $keys greift modified nicht direkt zu, sondern über die
     * Methode public function key(): array
     *
     * Für mehr Infos siehe Methode public function keys(): array
     *
     * @var string[] $keys
     */
    protected array $keys;


    /**
     * Optional
     *
     * Diese Keys sollen nicht angezeigt werden.
     *
     * @var string[] $keys_dispnone
     */
    public array $keys_dispnone;

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
     * @var string[] $keys_dispnone
     */
    public array $properties;

    /**
     * Optional
     *
     * Hier fehlt die Beschreibung ...
     *
     * Wird nur bei Klassenerweiterungen angezeigt
     *
     * @var string $extended_description
     */
    public string $extended_description;

}
```

### Actions

- module_processing_do : Nur bei System und Export Modulen, Beschreibung ...
- save : Beschreibung ...
- install : Beschreibung ...
- update : Beschreibung ...
- backupconfirm : Beschreibung ...
- removeconfirm : Beschreibung ...
- restoreconfirm : Beschreibung ...
- custom : Beschreibung ...
- ready : Beschreibung ...
- edit : Beschreibung ...
- restore : Beschreibung ...
- backup : Beschreibung ...
- remove : Beschreibung ...

## System Modul Klassen Methoden

### Der Constructor - in einfacher Form (nicht verwenden)

??? note "Textstatus - Entwurf"

    Status: 2 von 5 - Erster Entwurf: Erste Ausformulierung einiger Informationen.

Als Erstes erklären wir dir die Aufgabe des Contructors anhand eines einfachen Beispiels. Dieses Beispiel solltest du in dieser Form aber nicht verwenden. Das Modul ist so nicht mehrsprachig und kann auch nicht über das Admininterface vom Nutzer aktiviert oder deaktiviert werden, da der Wert für `$this->enabled` immer auf `true` steht.

Im Constructor müssen wir jetzt die Attribute mit Werte füllen. Wir könnten für viele Attribute feste Werte verwenden.



=== "Ohne StdModule"

    ```php title="system_mc_my_first_module.php"
    public function __construct()
    {
        $this->code        = 'system_mc_my_first_module';
        $this->title       = 'Mein ersten Modul von My Company';
        $this->description = 'Das ist mein ersten Modul. Es ...';
        $this->sort_order  = 0;
        $this->enabled     = true;
    }
    ```

### Der Constructor - mehrsprachig und dynamisch

??? note "Textstatus - Entwurf"

    Status: 2 von 5 - Erster Entwurf: Erste Ausformulierung einiger Informationen.

Um die Unzulänglichkeiten aus dem ersten Constructor Beispiel zu umgehen, schauen wir uns jetzt an, wie wir dieses besser machen könnten.

=== "Mit StdModule"

    ```php title="system_mc_my_first_module.php"
    public function __construct()
    {
        parrent::__construct('MODULE_SYSTEM_MC_MY_FIRST_MODULE');
    }
    ```

=== "Ohne StdModule"

    ```php title="system_mc_my_first_module.php"
    public function __construct()
    {
        // Der Wert in $prefix ist: MODULE_SYSTEM_MC_MY_FIRST_MODULE
        $prefix = 'MODULE_' . strtoupper(self::class);

        $this->code        = self::class;
        $this->title       = constant($prefix . '_TITLE');
        $this->description = constant$prefix . '_DESC');
        $this->sort_order  = constant$prefix . '_SORT_ORDER');
        $this->enabled     = defined($prefix . '_STATUS') && 'true' === constant($prefix . '_STATUS');
    }
    ```

Statt feste Werte für `$this->title` und `$this->description`, holen wir uns diese Werte aus Konstanten, die wir später in einer Sprachdatei definieren. Siehe hierfür den Abschnitt [???](#).

Den vorderen Teil der Konstanten `MODULE_SYSTEM_MC_MY_FIRST_MODULE` lassen wir uns bequem automatisch erzeugen und speichern in der Variablen `$prefix` zwischen.

```php
$prefix = 'MODULE_' . strtoupper(self::class);
```

Die Konstanten `MODULE_SYSTEM_MC_MY_FIRST_MODULE_SORT_ORDER` und `MODULE_SYSTEM_MC_MY_FIRST_MODULE_STATUS` lädt der modifed Core für uns aus der Datenbanktabelle `configure`, bevor er versucht eine Instanz der Klasse zu erzeugen und der Constructor aufgerufen wird.

Wie wir die Konstanten in die Datenbank bekommen, schauen wir uns auch noch an. Das passiert automatisch vom modified Core, sobald die Funktion `install()` aufgerufen wird, wie wir uns auch gleich noch anschauen werden.

### keys()

```php
public function keys(): array
```

<h4>Beschreibung</h4>

??? note "Textstatus - Entwurf"

    Status: 2 von 5 - Erster Entwurf: Erste Ausformulierung einiger Informationen.

Der modified Core ruft diese Methode auf, um sich eine Liste mit allen
Einstellungen aus der Datenbank aus der Tabelle Configuration zu holen. Diese Werte werden z. B. in der Admin Modul Übersicht und beim Bearbeiten angezeigt. Gibst du keinen Key an, wird dir auch keiner angezeigt und du kannst keinen bearbeiten. Zudem kann der modified Core anhand dieser key abgleichen, ob keys in der Datenbank fehlen. Unabhängig davon welche key hier angegeben werden, lädt modified zu Beginn eines jeden Requests alle Configuration Werte aus der Datenbank als Konstante.

<h4>Beispiel</h4>

```php
public function keys(): array
{
    return [
        'MODULE_SYSTEM_MC_MY_FIRST_MODULE_ATTRIBUTE_1',
        'MODULE_SYSTEM_MC_MY_FIRST_MODULE_ATTRIBUTE_2'
    ];
}
```

### check()

```php
public function check(): int
```

<h4>Beschreibung</h4>

??? note "Textstatus - Entwurf"

    Status: 2 von 5 - Erster Entwurf: Erste Ausformulierung einiger Informationen.

Diese Methode überprüft, ob das Modul installiert ist. Das kann auf unterschiedliche Art und Weise passieren. Wenn das Modul ordnungsgemäß installiert ist, muss die Methode eine Zahl ungleich 0 zurückgeben.

In diesem Beispiel kontrollieren wir, ob in der Datenbanktabelle `configuration` in der Spalte `configuration_key` ein Eintrag mit dem Wert `MODULE_SYSTEM_MC_MY_FIRST_MODULE_STATUS` vorhanden ist.

Du kannst dir theoretisch einen eigenen Weg ausdenken, um zu überprüfen, ob dein Modul korrekt installiert ist. Z. B. ob ein anderer Wert vorhanden ist, oder ob bestimmte Dateien vorhanden sind. Die meisten System Module machen das jedoch immer auf dem hier gezeigten Weg über die Konstante `MODULE_SYSTEM_MC_MY_FIRST_MODULE_STATUS`:

<h4>Beispiel</h4>

```php
public function check(): int
{
    // Der Wert in $prefix ist: MODULE_SYSTEM_MC_MY_FIRST_MODULE
    $prefix = 'MODULE_' . strtoupper(self::class);

    // Der Wert in $key ist: MODULE_SYSTEM_MC_MY_FIRST_MODULE_STATUS
    $key = $prefix . '_STATUS';

    // SQL-Abfrage, ob MODULE_SYSTEM_MC_MY_FIRST_MODULE_STATUS vorhanden ist
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

??? note "Textstatus - Entwurf"

    Status: 2 von 5 - Erster Entwurf: Erste Ausformulierung einiger Informationen.

Diese Methode wird aufgerufen, wenn der Nutzer im Admin beim Modul auf die Taste "Installieren" klickt. In diesem Moment müssen wir selbst die nötigen Configurationen in der Datenbank anlegen. Wie du das machst, siehst du im folgenden Beispiel:

!!! bug "TODO"

    Erklären welche Dinge man noch in der install Methode machen kann, wie z. B. weitere Configurationen anlegen oder den Access auf Admin Controller Dateien erlauben.

<h4>Beispiel</h4>

```php
public function install(): void
{
    // MODULE_SYSTEM_MC_MY_FIRST_MODULE
    $prefix = 'MODULE_' . strtoupper(self::class);

    // MODULE_SYSTEM_MC_MY_FIRST_MODULE_STATUS
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

??? note "Textstatus - Skizze"

    Status: 1 von 5 - Skizze: Ideen und Informationen in Stichpunkten unvollständig festgehalten.

Lorem ...

<h4>Beispiel</h4>

```php
public function remove(): void
{
    // MODULE_SYSTEM_MC_MY_FIRST_MODULE
    $prefix = 'MODULE_' . strtoupper(self::class);

    // MODULE_SYSTEM_MC_MY_FIRST_MODULE_STATUS
    $key = $prefix . '_STATUS';

    xtc_db_query("DELETE FROM " . TABLE_CONFIGURATION . " WHERE configuration_key = '$key'");
}
```

### display()

```php
public function display(): string
```

<h4>Beschreibung</h4>

??? note "Textstatus - Skizze"

    Status: 1 von 5 - Skizze: Ideen und Informationen in Stichpunkten unvollständig festgehalten.

```php

/**
 * @return string[] in Form von ['text' => 'HTML']
 */
public function display(): array
```

<h4>Beschreibung</h4>

In der Bearbeitungsansicht eines Moduls generiert der modified Core die Eingabefelder für jede Eigenschaft, die mit `function keys(): array` zurückgeben wird. Weitere Eingabefelder oder Buttons wie Speichern oder Abbruch sind nicht vorhanden. Über die `display()` Methode kann man beliebe Buttons wie den Save Button generieren oder eine andere beliebige HTML-Ausgabe. Die Ausgabe wird nach den Eigenschaften/Keys ausgegeben / gerendert.

### custom()

```php
public function custom(): ???
```

<h4>Beschreibung</h4>

??? note "Textstatus - Skizze"

    Status: 1 von 5 - Skizze: Ideen und Informationen in Stichpunkten unvollständig festgehalten.

Sie Beispiel `/admin/includes/modules/system/image_processing_step.php`

Lorem ...

### process()

```php
public function process(string $filePath): void
```

<h4>Beschreibung</h4>

??? note "Textstatus - Skizze"

    Status: 1 von 5 - Skizze: Ideen und Informationen in Stichpunkten unvollständig festgehalten.

Wird vom modified Core aufgerufen, sobald auf im Bearbeitungsmodus auf die Taste `Save` geklickt wird, bzw. sobald die Action `save`. In diesem Fall wird als Parameter `$filePath` ??? übergeben (siehe `admin/module_export.php:136`) Mit `$_POST['process'] == 'module_processing_do'` kann verhindert werden, dass die Methode `function process(string $filePath): void` durch den Core aufgerufen wird.

Eine alternative die Methode durch den modified Core aufrufen zu lassen ist über `?action=module_processing_d` in diesem Fall wird `$_GET['file']` an die Methode als Parameter `$filePath` übergeben.

## Die vollstänige Beispiel Datei

??? note "Textstatus - Skizze"

    Status: 1 von 5 - Skizze: Ideen und Informationen in Stichpunkten unvollständig festgehalten.

Lorem ...

```php
<?php

declare(strict_types=1);

defined('_VALID_XTC') or die('Direct Access to this location is not allowed.');

class system_mc_my_first_module
{
    /** @var string $name **/
    public $prefix;

    /** @var string $code **/
    public $code;

    /** @var string $title **/
    public $title;

    /** @var string $description **/
    public $description;

    /** @var bool $enabled **/
    public $enabled;

    /** @var int $sort_order **/
    public $sort_order;

    /** @var string[] $keys **/
    public $keys;

    public function __construct()
    {
        $this->prefix      = strtoupper(self::class);
        $this->code        = self::class;
        $this->title       = $this->name . '_TITLE';
        $this->description = $this->name . '_DESC';
        $this->sort_order  = $this->name . '_SORT_ORDER';
        $this->enabled     = defined($this->prefix . '_STATUS') && 'true' === constant($this->prefix . '_STATUS');
    }

    /**
     * @return string[]
     */
    public function keys(): array
    {
        return [];
    }

    public function check(): int
    {

        $key = $this->prefix . '_STATUS';

        $query = xtc_db_query(
            "SELECT configuration_value FROM " . TABLE_CONFIGURATION . " WHERE configuration_key = '$key'"
        );

        return xtc_db_num_rows($query);
    }

    public function install(): void
    {
        $this->addConfiguration('STATUS', 'true', 6, 1, "xtc_cfg_select_option(array('true', 'false'),");
    }

    public function remove(): void
    {
        $this->deleteConfiguration('STATUS');
    }

    protected function addConfiguration(
        string $key,
        string $value,
        int $groupId,
        int $sortOrder,
        string $setFunction = '',
        string $useFunction = ''
    ): void {
        $key = $this->prefix() . '_' . $key;
        $setFunction = str_replace("'", "\\'", $setFunction);
        xtc_db_query("INSERT INTO `" . TABLE_CONFIGURATION . "` (`configuration_key`, `configuration_value`, `configuration_group_id`, `sort_order`, `set_function`, `use_function`, `date_added`) VALUES ('$key', '$value', '$groupId', '$sortOrder', '$setFunction', '$useFunction', NOW())");
    }

    protected function deleteConfiguration(string $key): void
    {
        $key = $this->prefix() . '_' . $key;
        xtc_db_query("DELETE FROM " . TABLE_CONFIGURATION . " WHERE configuration_key = '$key'");
    }
}
```

## Wie ein System Modul in modified mit dem Standard-Modul programmiert wird

- [ ] Ort / Verzeichnis
- [ ] Dateiname
- [ ] Klassen name
- [ ] Methoden / \_constuct, init, install, keys ...
- [ ] Configutation ohne das Stdandard-Modul / Textfelder / Dropdowns etc.
- [ ] Access-Entries für Admin Controller-Datein

??? note "Textstatus - Skizze"

    Status: 1 von 5 - Skizze: Ideen und Informationen in Stichpunkten unvollständig festgehalten.

Hier schauen wir uns das gleiche Modul wie in dem Abschnitt [_"System Modul ohne das Stdandard-Modul"_](#) noch einmal an, wie man es mit dem Stdandard-Modul programmieren aus dem MMLC programmieren würden und wie viel weniger Code du benötigst.

```php
<?php

declare(strict_types = 1);

use RobinTheHood\ModifiedStdModule\Classes\StdModule;

class system_mc_my_first_module extends StdModul
{
    public function __construct()
    {
        $this->init('MODULE_SYSTEM_MC_MY_FIRST_MODULE');
    }
}
```

Das Standard-Modul übernimmt die für uns die wichtigsten Konfigurationsarbeiten, die wir ohne das Stdandard-Modul per Hand selbst erledigen mussten. Zudem bietet uns das Stdandard-Modul nützliche Helper-Methoden, die uns die Arbeit mit System Modulen und Klassenerweiterungen erleichtern, wie wir in den nächsten Beispielen sehen werden.

Lorem ...

## Wichtige Sprachdateien zum System Modul

??? note "Textstatus - Entwurf"

    Status: 2 von 5 - Erster Entwurf: Erste Ausformulierung einiger Informationen.

Jeweils das System Modul aus dem Beispiel mit dem StandardModul und ohne dem StandardModul haben wir so vorbereitet, dass es mit mehreren Sprachen funktioniert. Jetzt müssen wir die passenden Sprachdateien erstellen, damit ein System Modul in den gewünschten Sprachen angezeigt werden kann.

Die Sprachdateien zu einem System Modul liegen in `/lang/<LANGUAGE>/modules/system/`. Wobei `<LANGUAGE>` der Name einer Sprache (auf englisch mit kleinem ersten Buchstabe) entspricht. Die Datei solltest du wieder nach unserer Namenskonvention aus Abschnitt XXX benennen. In diesem Fall wäre das `system_mc_my_first_module.php`.

Wie wir das Modul in deutscher Sprache bereitstellen möchten, können wir eine PHP-Datei erstellen, die wie folgt aussieht:

```php title="/lang/german/modules/system/system_mc_my_first_module.php"
<?php

declare(strict_types = 1);

$moduleType = 'MODULE_SYSTEM';
$moduleName = 'MC_MY_FIRST_MODULE';
$prefix = $moduleType . '_' . $moduleName  . '_';

define($prefix . 'TITLE', 'Mein ersten Modul von My Company');
define($prefix . 'LONG_DESCRIPTION', 'Das ist mein ersten Modul. Es ...');
define($prefix . 'STATUS_TITLE', 'Modul aktivieren?');
define($prefix . 'STATUS_DESC', 'Hier kannst das Modul deaktiveren ...');
```

Wenn du das Standard-Modul über den MMLC verwendest, kannst du deinen Code auch wie folgt schreiben:

!!! warning "Achtung"

    Die Möglichkeit die Sprachdatei mit dem Stdandard Modul umzusetzen gibt es noch nicht, soll aber trotzdem hier schon einmal aufgeführt werden.

```php title="/lang/german/modules/system/system_mc_my_first_module.php"
<?php

declare(strict_types = 1);

use RobinTheHood\ModifiedStdModule\Classes\StdModule;

$translations = [
    'TITLE'            => 'Mein ersten Modul von My Company',
    'LONG_DESCRIPTION' => 'Das ist mein ersten Modul. Es ...',
    'STATUS_TITLE'     => 'Modul aktivieren?',
    'STATUS_DESC'      => 'Hier kannst das Modul deaktiveren ...',
];

StdModule::registerLanguage($translations, system_mc_my_first_module::class);

StdModule::registerLanguage($translations, system_mc_my_first_module::class, StdModule::TYPE_SYSTEM);

StdModule::registerLanguage($translations, 'MC_MY_FIRST_MODULE', StdModule::TYPE_SYSTEM);
```

## Autoinclude-Dateien nur ausführen bei aktivem Modul

??? note "Textstatus - Skizze"

    Status: 1 von 5 - Skizze: Ideen und Informationen in Stichpunkten unvollständig festgehalten.

Wie bereist im Abschnitt [_"Autoinclude System - Allgemeines Beispiel"_](#) beschrieben, können oder sollten Autoinclude zusmmen mit System Module arbeiten. Im Grunde möchte man, dass Autoinclude-Dateien nur einen Effekt hervorrufen, wenn ein zugehöriges System Modul einen aktiven Status hat. Der Shop-Nutzer kann im Adminbereich einstellen, welche System Module installiert und/oder aktiv sind. Es ist wünschenswert, wenn sich zugehörige Autoinclude-Dateien an diesen Wunsch des Shop-Nutzers halten. So kann der Nutzer nach seinen Bedürfnissen Module aktivieren oder deaktivieren.

Das funktioniert auch bei Menü Datei-Erweiterungen siehe Abschnitt ???.

Das Ganze lässt sich glücklicherweise leicht bewerkstelligen, dazu müssen wir unsere Autoinclude-Datei nur um folgenden Code erweitern:

```php
if (!defined('MODULE_SYSTEM_MC_MY_FIRST_MODULE_STATUS') || MODULE_SYSTEM_MC_MY_FIRST_MODULE_STATUS != 'true') {
    return;
}
```

Der Anfang eine Autoinclude-Datei zu unserem Modul würde dann wie folgt aussehen:

```php
<?php

declare(strict_types = 1);

if (!defined('MODULE_SYSTEM_MC_MY_FIRST_MODULE_STATUS') || MODULE_SYSTEM_MC_MY_FIRST_MODULE_STATUS != 'true') {
    return;
}

// Ab hier folgt dein Code der z. B. Core Variblen manipulieren kann.
...

```

!!! bug "TODO"

    Hier sollte noch eine Erklärung kommen, was dieser Code macht, also wie das `return` funktioniert und woher wir wissen, wie die Konstante heißen muss, die wir in dem Code verwenden.
