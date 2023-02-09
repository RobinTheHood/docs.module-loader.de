# System Modul

??? note "Textstatus 3"

    Status: 3 von 5 - Dieser Abschnitt könnte besser geschrieben werden.

In diesem Abschnitt erklären wir dir alles, was du wissen musst, um ein System Modul für modified zu programmieren.

Ein System Modul ist der zentrale Anker deines Moduls. Es ist eine PHP Klasse, die hauptsächlich die Installation deines Moduls steuert und stellt den ersten Anlaufpunkt für grundlegende Einstellungen zu deinem Modul dar.

Alle System Module findest du im Verzeichnis `/admin/includes/modules/admin/`. Sie werden dir im Admininterface unter dem Menüpunkt _Admin > Module > System Module_ aufgelistet. Dort erscheinen diese entweder als _installiert_ oder _deinstalliert_.

Ein Modul solltest du immer zusammen mit einem System Modul entwickeln, auch wenn das von modified nicht vorgeschrieben wird. Du könntest zwar Autoincludes unabhängig von einem System Modul programmieren, best practice ist aber, dass dein Modul ein System Modul beinhaltet. Durch dieses kann der Anwender an einem zentralen Ort im Shop alle Module und Erweiterungen steuern und bedienen.

Du kannst dir ein System Modul wie eine [Klassenerweiterung](#) vorstellen, die jedoch im Gegensatz zu dieser keine modified Methoden erweitert. Aus deinem Grund könnten wir ein System Module ebenfalls als Klassenerweiterung bezeichnen. Klassenerweiterungen schauen wir uns in späteren Abschnitten an.

## Wie wird ein System Modul in modified programmiert?

??? note "Textstatus 2"

    Status: 2 von 5 - Erster Entwurf

Wenn du ein System Modul von Hand (ohne das StandardModul aus dem MMLC) erstellen möchtest, kannst du den kompletten Programmcode am Ende dieses Abschnitts kopieren und auf deine Bedürfnisse anpassen.

Im nächsten Abschnitt zeigen wir dir, wie viel weniger Arbeit du hast, wenn du ein System Modul mit dem StandardModul aus dem MMLC erstellst.

Nicht erschrecken, in diesem Abschnitt zeigen wir dir eine Menge Programmcode. Dieser ist glücklicherweise darauf optimiert, leicht lesbar zu sein. Du musst diesen Code nur an wenige Stellen verändern, um ihn an ein eigenes Modul anzupassen. Wir gehen zudem auf alle Codeabschnitte genau ein und erklären dir, was die jeweiligen Zeilen machen und warum diese nötig sind. Oft erklären wir die Funktionalität anhand von Kommentaren im Quellcode.

Wie du sehen wirst, benötigt ein System Modul relativ viel Code. Die Schreibarbeit könnte uns das modified Entwicklerteam mit einer einfachen Anpassung im Code ersparen. Das würde es Entwicklern erleichtert eigene System Module und Klassenerweiterungen zu schreiben.

Mit dem StandardModul aus dem MMLC lässt sich dieser immer wiederkehrende Code jedoch trotzdem vermeiden. Mehr dazu später im Abschnitt [???](#). Als Erstes schauen wir uns ein System Modul ohne das StandardModul an, damit du die grundlegende Arbeitsweise von System Modulen verstehst.

Eine System Modul-Datei bzw. eine kleinst mögliche System Modul Klasse besteht mindestens aus den folgenden Elementen:

- Attribute
    - string $code
    - string $title
    - string $description
    - bool $enabled
    - int $sort_order
    - string[] $keys

- Methoden
    - keys(): array
    - check(): int
    - install(): void
    - remove(): void

Was die jeweiligen Aufgaben dieser Elemente sind, werden wir uns im folgenden Abschnitt ausführlich ansehen. Am Ende zeigen wir dir eine vollständige System Modul Datei.

### Die Klasse

??? note "Textstatus 2"

    Status: 2 von 5 - Erster Entwurf

Eine System Modul Datei besteht aus genau einer Klasse. Diese Klasse muss den gleichen Namen (ohne `.php`) wie die Datei heißen, in der sie liegen. Alle System Module Dateien liegen im Verzeichnis `/admin/includes/modules/`. Als Beispiel nehmen wir eine Datei mit dem Namen `mc_my_first_module.php`. An welche Namenskonventionen du dich halten sollten, kannst du im Abschnitt [???](#) lesen.

Das bedeutet für uns, dass wir die Klasse `mc_my_first_module` nennen müssen. Wenn wir uns nicht an diese Konvention halten, lädt der Klassenloader im modified Core die Datei nicht in den Speicher und wir können die Klasse nicht verwenden. Uns wird dann im Adminbereich unter Module > System Module die Klasse nicht angezeigt. Auch würden abhängige Autoinclude-Dateien nicht funktionieren. Hier ein Beispiel:

```php title="/admin/includes/modules/mc_my_first_module.php"
<?php

declare(strict_types=1);

defined('_VALID_XTC') or die('Direct Access to this location is not allowed.');

class mc_my_first_module
{
    ...
}
```

### Die Attribute

??? note "Textstatus 2"

    Status: 2 von 5 - Erster Entwurf

Die Klasse benötigt 6 Attribute auf die das modified zugreift, um sich Informationen von unserer System Modul Klasse zu holen. Aus diesem Grund müssen wir die folgenden Attribute definieren. Machen wir das nicht, wirft uns PHP je nach Einstellung Notice, Warnings oder Erorrs aus. Das sollten wir vermeiden.

```php
class mc_my_first_module
{
    /**
     * Eine Bezeichnung oder Id, mit der das Modul bzw.
     * die Klasse eindeutig angesprochen werden kann.
     *
     * Im Grunde kann hier der Klassennamen verwendet werden,
     * da diese auch nur einmal vorkommen darf.
     *
     * Beispielwert: 'mc_my_first_module'
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

### Der Constructor - in einfacher Form (nicht verwenden)

??? note "Textstatus 2"

    Status: 2 von 5 - Erster Entwurf

Als Erstes erklären wir dir die Aufgabe des Contructors anhand eines einfachen Beispiels. Dieses Beispiel solltest du in dieser Form aber nicht verwenden. Das Modul ist so nicht mehrsprachig und kann auch nicht über das Admininterface vom Nutzer aktiviert oder deaktiviert werden, da der Wert für `$this->enabled` immer auf `true` steht.

Im Constructor müssen wir jetzt die Attribute mit Werte füllen. Wir könnten für viele Attribute feste Werte verwenden.

```php title="mc_my_first_module.php"
class mc_my_first_module
{
    ...

    public function __construct()
    {
        $this->code        = 'mc_my_first_module';
        $this->title       = 'Mein ersten Modul von My Company';
        $this->description = 'Das ist mein ersten Modul. Es ...';
        $this->sort_order  = 0;
        $this->enabled     = true;
    }

    ...
}
```

### Der Constructor - mehrsprachig und dynamisch

??? note "Textstatus 2"

    Status: 2 von 5 - Erster Entwurf

Um die Unzulänglichkeiten aus dem ersten Constructor Beispiel zu umgehen, schauen wir uns jetzt an, wie wir dieses besser machen könnten.

```php title="mc_my_first_module.php"
class mc_my_first_module
{
    ...

    public function __construct()
    {
        // Der Wert in $prefix ist: MODULE_MC_MY_FIRST_MODULE
        $prefix = 'MODULE_' . strtoupper(self::class);

        $this->code        = self::class;
        $this->title       = $prefix . '_TITLE';
        $this->description = $prefix . '_DESC';
        $this->sort_order  = $prefix . '_SORT_ORDER';
        $this->enabled     = defined($prefix . '_STATUS') && 'true' === constant($prefix . '_STATUS');
    }

    ...
}
```

Statt feste Werte für `$this->title` und `$this->description`, holen wir uns diese Werte aus Konstanten, die wir später in einer Sprachdatei definieren. Siehe hierfür den Abschnitt [???](#).

Den vorderen Teil der Konstanten `MODULE_MC_MY_FIRST_MODULE` lassen wir uns bequem automatisch erzeugen und speichern in der Variablen `$prefix` zwischen.

```php
$prefix = 'MODULE_' . strtoupper(self::class);
```

Die Konstanten `MODULE_MC_MY_FIRST_MODULE_SORT_ORDER` und `MODULE_MC_MY_FIRST_MODULE_STATUS` lädt der modifed Core für uns aus der Datenbanktabelle `configure`, bevor er versucht eine Instanz der Klasse zu erzeugen und der Constructor aufgerufen wird.

Wie wir die Konstanten in die Datenbank bekommen, schauen wir uns auch noch an. Das passiert automatisch vom modified Core, sobald die Funktion `install()` aufgerufen wird, wie wir uns auch gleich noch anschauen werden.

### Die Methode - keys(): array

??? note "Textstatus 2"

    Status: 2 von 5 - Erster Entwurf

Der modified Core ruft diese Methode auf, um sich eine Liste mit allen
Einstellungen aus der Datenbank aus der Tabelle Configuration zu holen. Diese Werte werden z. B. in der Admin Modul Übersicht und beim Bearbeiten angezeigt. Gibst du keinen Key an, wird dir auch keiner angezeigt und du kannst keinen bearbeiten. Zudem kann der modified Core anhand dieser key abgleichen, ob keys in der Datenbank fehlen. Unabhängig davon welche key hier angegeben werden, lädt modified zu Beginn eines jeden Requests alle Configuration Werte aus der Datenbank als Konstante.

```php
class mc_my_first_module
{
    ...

    /**
     * @return string[]
     */
    public function keys(): array
    {
        return [];
    }

    ...
}
```

### Die Methode - check(): int

??? note "Textstatus 2"

    Status: 2 von 5 - Erster Entwurf

Diese Methode überprüft, ob das Modul installiert ist. Das kann auf unterschiedliche Art und Weise passieren. Wenn das Modul ordnungsgemäß installiert ist, muss die Methode eine Zahl ungleich 0 zurückgeben.

In diesem Beispiel kontrollieren wir, ob in der Datenbanktabelle `configuration` in der Spalte `configuration_key` ein Eintrag mit dem Wert `MODULE_MC_MY_FIRST_MODULE_STATUS` vorhanden ist.

Du kannst dir theoretisch einen eigenen Weg ausdenken, um zu überprüfen, ob dein Modul korrekt installiert ist. Z. B. ob ein anderer Wert vorhanden ist, oder ob bestimmte Dateien vorhanden sind. Die meisten System Module machen das jedoch immer auf dem hier gezeigten Weg über die Konstante `MODULE_MC_MY_FIRST_MODULE_STATUS`:

```php
class mc_my_first_module
{
    ...

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

    ...
}
```

### Die Methode - install(): void

??? note "Textstatus 2"

    Status: 2 von 5 - Erster Entwurf

Diese Methode wird aufgerufen, wenn der Nutzer im Admin beim Modul auf die Taste "Installieren" klickt. In diesem Moment müssen wir selbst die nötigen Configurationen in der Datenbank anlegen. Wie du das machst, siehst du im folgenden Beispiel:

_Erklären welche Dinge man noch in der install Methode machen kann, wie z. B. weitere Configurationen anlegen oder den Access auf Admin Controller Dateien erlauben._

```php
class mc_my_first_module
{
    ...

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

    ...
}
```

### Die Methode - remove(): void

??? note "Textstatus 1"

    Status: 1 von 5 - Skizze

Lorem ...

```php
class mc_my_first_module
{
    ...

    public function remove(): void
    {
        // MODULE_MC_MY_FIRST_MODULE
        $prefix = 'MODULE_' . strtoupper(self::class);

        // MODULE_MC_MY_FIRST_MODULE_STATUS
        $key = $prefix . '_STATUS';

        xtc_db_query("DELETE FROM " . TABLE_CONFIGURATION . " WHERE configuration_key = '$key'");
    }

    ...
}
```

### Die Methode - display(): string

??? note "Textstatus 1"

    Status: 1 von 5 - Skizze

```php

/**
 * @return string[] in Form von ['text' => 'HTML']
 */
public function display(): array
```

In der Bearbeitungsansicht eines Moduls generiert der modified Core die Eingabefelder für jede Eigenschaft, die mit `function keys(): array` zurückgeben wird. Weitere Eingabefelder oder Buttons wie Speichern oder Abbruch sind nicht vorhanden. Über die `display()` Methode kann man beliebe Buttons wie den Save Button generieren oder eine andere beliebige HTML-Ausgabe. Die Ausgabe wird nach den Eigenschaften/Keys ausgegeben / gerendert.

### Die Methode - custome()

??? note "Textstatus 1"

    Status: 1 von 5 - Skizze

Sie Beispiel `/admin/includes/modules/system/image_processing_step.php`

Lorem ...

### Die Methode - process(string $filePath): void

??? note "Textstatus 1"

    Status: 1 von 5 - Skizze

Wird vom modified Core aufgerufen, sobald auf im Bearbeitungsmodus auf die Taste `Save` geklickt wird, bzw. sobald die Action `save`. In diesem Fall wird als Parameter `$filePath` ??? übergeben (siehe `admin/module_export.php:136`) Mit `$_POST['process'] == 'module_processing_do'` kann verhindert werden, dass die Methode `function process(string $filePath): void` durch den Core aufgerufen wird.

Eine alternative die Methode durch den modified Core aufrufen zu lassen ist über `?action=module_processing_d` in diesem Fall wird `$_GET['file']` an die Methode als Parameter `$filePath` übergeben.

## Die vollstänige Beispiel Datei

??? note "Textstatus 1"

    Status: 1 von 5 - Skizze

Lorem ...

```php
<?php

declare(strict_types=1);

defined('_VALID_XTC') or die('Direct Access to this location is not allowed.');

class mc_my_first_module
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

### Wie funktionieren setFunctions

??? note "Textstatus 1"

    Status: 1 von 5 - Skizze

In diesem Abschnitt schauen wir uns an, was es mit der `setFunction` auf sich hat. Mit der `setFunction` können wir eine Funktion oder Methode bestimmen, die modified aufrufen soll, wenn die Anzeige für die Bearbeitung eines Konfigurationswertes in der Eingabemaske der Konfiguration gerendert werden soll. Du kannst dir das wie folgt vorstellen. Du befindest dich im Admin unter *Module > System Module* und klickst dort bei einem Modul auf bearbeiten. Jetzt werden dir alle Werte angezeigt, die du zu diesem Modul bearbeiten kannst. Bei manchen Werten reicht ein einfaches Texteingabefeld aus. Bei anderen Werten möchten wir z. B. ein DropDownMenü oder sogar etwas noch Spezielleres anzeigen. Hier kommt jetzt die `setFunction` ins Spiel.

Wie schon kurz erwähnt, können wir mit der `setFunction` eine Funktion oder Methode bestimmen, die modifed verwenden soll, um das Eingabefeld zu rendern. Diese Funktion oder Methode sollte als Rückgabe einen HTML-String liefern. Für einige Tricks kann man die `setFunction` auch dazu verwenden, um seinen eigenen HTML-Code an eine bestimmte Stelle in der Ausgabe der Konfiguration mit einfließen zu lassen. Das ist aber nicht Thema dieses Abschnitts.

Eine `setFunction` muss über den global Scope erreichbar. Das Interface oder die Signatur einer `setFunction` muss dabei wie folgt aufgebaut sein, damit modified die `setFunction` ohne Fehler aufrufen kann:

```php
/**
 * @param: ... (optional) beliebige Parameter
 * @param: string $configurationValue
 * @param: string $configurationKey
 * 
 * @return: string HTML Z.B. ein DropDown Menü
 */
function mySetFunction(..., string $configurationValue, string $configurationKey = ''): string
```

Was du vielleicht bereits gesehen hast, ist der Parameter `...`, den es so in PHP nicht gibt. Was es damit auf sich hat, erfährst du gleich, vorher müssen wir verstehen, wie modified intern die `setFunction` aufruft.

In der Tabelle `configuration` in der Datenbank ist die `setFunction` zusammen mit dem `$configurationValue` und dem `$configurationKey` je Zeile gespeichert. Hier ein Beispiel:

| `configuration_key` | `configuration_value` | `set_function` |
|--|--|--|
| `MODULE_COLOR` | `red` | `globalFuncSelectColor(`

In der Datenbank steht als `setFunction` der Wert `global_func_select_color(`. Wenn modified die Konfiguration rendern möchte, hängt es an die Funktion, die Werte aus `configuration_value` und `configuration_key` an die Funktion, bevor modified die Funktion aufruft. modified macht Folgendes:

```php
echo eval("globalFuncSelectColor(" . "'red', 'MODULE_COLOR')");
```

Übersetzt würde modified bzw. PHP dann folgenden Code ausführen:

```php
echo globalFuncSelectColor('red', 'MODULE_COLOR');
```

Wie man sieht, wurden hier die Werte `red` als `$configurationValue` und  `MODULE_COLOR` als `$configurationKey` jeweils als string an die Funktion `globalFuncSelectColor()` übergeben.

Jetzt wo wir verstanden haben, wie modifid die `setFunction` aufruft, können wir uns ansehen, was es mit den `...` in der Signatur auf sich hat. Da wir die `setFunction` als String in die Datenbank speichern, können wir selbst noch beliebig viele Werte an die `setFunction` anhängen. Hier ein Beispiel mit einem weiteren Wert `'hex'`:

| `configuration_key` | `configuration_value` | `set_function` |
|--|--| -- |
| `MODULE_COLOR` | `green` | `selectColor('hex',`


In unserem Code kann oder muss es jetzt eine Funktion im globalen Scope mit dem Name `selectColor` geben, die drei Parameter entgegennehmen kann, da modified die Funktion wie folgt anrufen wird:

```php
echo eval("selectColor('hex'," . "'red', 'MODULE_COLOR')");
```

Übersetzt würde modified bzw. PHP dann folgenden Code aufrufen:

```php
echo selectColor('hex', 'green', 'MODULE_COLOR');
```

Eine passende Funktion `globalFuncSelectColor` könnte wie folgt aussehen:

```php
/**
 * @param: string $outputAs
 * @param: string $configurationValue
 * @param: string $configurationKey
 * 
 * @return: string HTML Z.B. ein DropDown Menü
 */
function selectColor(string $outputAs, string $value, string $key = ''): string
{
	// ...
	if ($outputAs === 'hex') {
		return // HTML as string ...
	} else {
		return // HTML as string ...
	}
}
```

Hier ein paar Beispiele für `setFunction`:

| Signatur | set_function |
| -- | -- |
| `public static selectColor(string $value, string $key = ''): string` | `MyClass::selectColor(` |
| `public static selectColor(string $value, string $key = ''): string` | `self::selectColor(` |
| `function selectColor(string $value, string $key = ''): string` | `selectColor(` |
| `function selectColor(string $outputAs, string $value, string $key = ''): string` | `selectColor('hex',` |

### Liste der möglichen setFunctions

??? note "Textstatus 1"

    Status: 1 von 5 - Skizze

modified bringt von Haus aus einige `setFunction` mit. Das ist ganz praktisch und erspart dir einiges an Programmierarbeit. Die meisten Funktionen, die unter `/admin/includes/functions/` mit dem Präfix `xtc_cfg_` anfangen, kannst du als `setFunction` verwenden. Hier einige Beispiele:

-   `xtc_cfg_select_option()`
-   `xtc_cfg_textarea()`
-   `xtc_cfg_pull_down_order_statuses()`
-   ...

Es gibt noch viele weitere `setFunction`. Um diese zu finden, kannst du das gesamte modified Projekt im Quellcode nach `function xtc_cfg_` durchsuchen.

### Was macht die useFunction?

??? note "Textstatus 2"

    Status: 2 von 5 - Erster Entwurf

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

## Configuration Group

??? note "Textstatus 1"

    Status: 1 von 5 - Skizze

- Keine feste Goup id setzten da es ein Autoincrement wert ist.
- Do wie lässt sich dann eine Menüpunkt erstelle. Dazu kann in kann man eine configuration setzen in der die Configuration Group Id steht. Im Menü kann dann nach der Id gefilter werdern

## Wie ein System Modul in modified mit dem Standard Modul programmiert wird

- [ ] Ort / Verzeichnis
- [ ] Dateiname
- [ ] Klassen name
- [ ] Methoden / \_constuct, init, install, keys ...
- [ ] configutation ohne StdModul / Textfelder / Dropdowns etc.
- [ ] Access-Entries für Admin Controller-Datein

??? note "Textstatus 1"

    Status: 1 von 5 - Skizze

Hier schauen wir uns das gleiche Modul wie in dem Abschnitt [_"System Modul ohne das StdModul"_](#) noch einmal an, wie man es mit dem StdModul programmieren aus dem MMLC programmieren würden und wie viel weniger Code du benötigst.

```php
<?php

declare(strict_types = 1);

use RobinTheHood\ModifiedStdModule\Classes\StdModule;

class mc_my_first_module extends StdModul
{
    public function __construct()
    {
        $this->init('MODULE_MC_MY_FIRST_MODULE');
    }
}
```

Das Standard Modul übernimmt die für uns die wichtigsten Konfigurationsarbeiten, die wir ohne das StdModul per Hand selbst erledigen mussten. Zudem bietet uns das StdModul nützliche Helper-Methoden, die uns die Arbeit mit System Modulen und Klassenerweiterungen erleichtern, wie wir in den nächsten Beispielen sehen werden.

Lorem ...

## Wichtige Sprachdateien zum System Modul

??? note "Textstatus 2"

    Status: 2 von 5 - Erster Entwurf

Jeweils das System Modul aus dem Beispiel mit dem StandardModul und ohne dem StandardModul haben wir so vorbereitet, dass es mit mehreren Sprachen funktioniert. Jetzt müssen wir die passenden Sprachdateien erstellen, damit ein System Modul in den gewünschten Sprachen angezeigt werden kann.

Die Sprachdateien zu einem System Modul liegen in `/lang/<LANGUAGE>/modules/system/`. Wobei `<LANGUAGE>` der Name einer Sprache (auf englisch mit kleinem ersten Buchstabe) entspricht. Die Datei solltest du wieder nach unserer Namenskonvention aus Abschnitt XXX benennen. In diesem Fall wäre das `mc_my_first_module.php`.

Wie wir das Modul in deutscher Sprache bereitstellen möchten, können wir eine PHP-Datei erstellen, die wie folgt aussieht:

```php
<?php

// File: /lang/german/modules/system/mc_my_first_module.php

declare(strict_types = 1);

$moduleType = 'MODULE';
$moduleName = 'MC_MY_FIRST_MODULE';
$prefix = $moduleType . '_' . $moduleName  . '_';

define($prefix . 'TITLE', 'Mein ersten Modul von My Company');
define($prefix . 'LONG_DESCRIPTION', 'Das ist mein ersten Modul. Es ...');
define($prefix . 'STATUS_TITLE', 'Modul aktivieren?');
define($prefix . 'STATUS_DESC', 'Hier kannst das Modul deaktiveren ...');
```

Wenn du das Standard Modul über den MMLC verwendest, kannst du deinen Code auch wie folgt schreiben:

_Achtung: Die Möglichkeit die Sprachdatei mit dem StdModul umzusetzen gibt es noch nicht, soll aber trotzdem hier schon einmal aufgeführt werden._

```php
<?php

// File: /lang/german/modules/system/mc_my_first_module.php

declare(strict_types = 1);

use RobinTheHood\ModifiedStdModule\Classes\StdModule;

$translations = [
    'TITLE'            => 'Mein ersten Modul von My Company',
    'LONG_DESCRIPTION' => 'Das ist mein ersten Modul. Es ...',
    'STATUS_TITLE'     => 'Modul aktivieren?',
    'STATUS_DESC'      => 'Hier kannst das Modul deaktiveren ...',
];

StdModule::registerLanguage($translations, mc_my_first_module::class);

StdModule::registerLanguage($translations, mc_my_first_module::class, StdModule::TYPE_SYSTEM);

StdModule::registerLanguage($translations, 'MC_MY_FIRST_MODULE', StdModule::TYPE_SYSTEM);
```

## Autoinclude-Dateien nur ausführen bei aktivem Modul

??? note "Textstatus 1"

    Status: 1 von 5 - Skizze

Wie bereist im Abschnitt [_"Autoinclude System - Allgemeines Beispiel"_](#) beschrieben, können oder sollten Autoinclude zusmmen mit System Module arbeiten. Im Grunde möchte man, dass Autoinclude-Dateien nur einen Effekt hervorrufen, wenn ein zugehöriges System Modul einen aktiven Status hat. Der Shop-Nutzer kann im Adminbereich einstellen, welche System Module installiert und/oder aktiv sind. Es ist wünschenswert, wenn sich zugehörige Autoinclude-Dateien an diesen Wunsch des Shop-Nutzers halten. So kann der Nutzer nach seinen Bedürfnissen Module aktivieren oder deaktivieren.

Das funktioniert auch bei Menü Datei-Erweiterungen siehe Abschnitt ???.

Das Ganze lässt sich glücklicherweise leicht bewerkstelligen, dazu müssen wir unsere Autoinclude-Datei nur um folgenden Code erweitern:

```php
if (!defined('MODULE_MC_MY_FIRST_MODULE_STATUS') || MODULE_MC_MY_FIRST_MODULE_STATUS != 'true') {
    return;
}
```

Der Anfang eine Autoinclude-Datei zu unserem Modul würde dann wie folgt aussehen:

```php
<?php

declare(strict_types = 1);

if (!defined('MODULE_MC_MY_FIRST_MODULE_STATUS') || MODULE_MC_MY_FIRST_MODULE_STATUS != 'true') {
    return;
}

// Ab hier folgt dein Code der z. B. Core Variblen manipulieren kann.
...

```

_Hier sollte noch eine Erklärung kommen, was dieser Code macht, also wie das `return` funktioniert und woher wir wissen, wie die Konstante heißen muss, die wir in dem Code verwenden._

## Erweiterung der Menüpunkte

??? note "Textstatus 1"

    Status: 1 von 5 - Skizze

In modified kannst du einige Menüpunkte erweitern.

### Menüpunkte

??? note "Textstatus 1"

    Status: 1 von 5 - Skizze

Hier eine List mit allen Standard Menüpunkten, die du dynamisch mit einem Modul erweitern kannst:

| Konstante                     | Menüpfad               | Erweiterbar |
| ----------------------------- | ---------------------- | :---------: |
| `BOX_HEADING_CUSTOMERS`       | **Kunden**             |     ✅      |
| `BOX_HEADING_PRODUCTS`        | **Katalog**            |     ✅      |
| `BOX_HEADING_MODULES`         | **Module**             |     ✅      |
| `BOX_HEADING_PARTNER_MODULES` | **Partner Module**     |     ✅      |
| `BOX_HEADING_STATISTICS`      | **Statistiken**        |     ✅      |
| `BOX_HEADING_TOOLS`           | **Hilfsprogramme**     |     ✅      |
| `BOX_HEADING_GV_ADMIN`        | **Gutscheine/Coupons** |     ✅      |
| `BOX_HEADING_ZONE`            | **Land/Steuer**        |     ✅      |
| `BOX_HEADING_CONFIGURATION`   | **Konfiguration**      |     ✅      |
| `BOX_HEADING_CONFIGURATION2`  | **Erw. Konfiguration** |     ✅      |

Eine [Liste mit allen Standard Menüpunkten](modified-menu.md) findest im Anhang.

Eigenschaften der Menüpunkte

```php
// BOX_HEADING_TOOLS = Name der box in der der neue Menüeintrag erscheinen soll
$add_contents[BOX_HEADING_TOOLS][] = [
    'admin_access_name' => 'example',   // Eintrag für Adminrechte
    'filename' => 'example.php',        // Dateiname der neuen Admindatei
    'boxname' => XXX,                   // Anzeigename im Menü
    'parameters' => '',                 // zusätzliche Parameter z.B. 'set=export'
    'ssl' => ''                         // SSL oder NONSSL, kein Eintrag = NONSSL
];
```

Wie sollte der `boxname` gewählt werden. Namingconventions?
In welcher Datei sollte der Eintrag liegen und wie sollte die Datei benannt sein? Namingconventions.

Beipsieldatei

### Menü erweitern mit Configure Groups

??? note "Textstatus 1"

    Status: 1 von 5 - Skizze

Lorem ...

```php
<?php

declare(strict_types=1);

$query = xtc_db_query("SELECT configuration_value FROM " . TABLE_CONFIGURATION . " WHERE configuration_key = 'MODULE_MC_MY_FIRST_MODULE_CONFIGURATION_GROUP_ID'");

if (!xtc_db_num_rows($query)) {
    return;
}

$rows = xtc_db_fetch_array($rows);

$add_contents[BOX_HEADING_CONFIGURATION][] = [
    'admin_access_name' => 'configuration',
    'filename' => 'configuration.php?gID=' . $rows['configuration_value'],
    'boxname' => MENU_NAME_ ...,
    'parameters' => '',
    'ssl' => ''
];

```

_Möglicherweise ist auch die folgende Version möglich, wenn die Configuration bereits aus der Datenbank geladen wurde:_

```php
<?php

declare(strict_types=1);


if (!defined(MODULE_MC_MY_FIRST_MODULE_CONFIGURATION_GROUP_ID)) {
    return;
}

$add_contents[BOX_HEADING_CONFIGURATION][] = [
    'admin_access_name' => 'configuration',
    'filename' => 'configuration.php?gID=' . MODULE_MC_MY_FIRST_MODULE_CONFIGURATION_GROUP_ID,
    'boxname' => MENU_NAME_ ...,
    'parameters' => '',
    'ssl' => ''
];

```
