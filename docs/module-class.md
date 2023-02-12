---
title: Modul Klassen
description: Aufbau und Programmierung von System-Modulen, Shipping-Modulen, Payment-Modulen und anderen Modulen für das modified Shop System.
---

# Modul Klassen (abstract)

??? note "Textstatus - Entwurf"

    Status: 2 von 5 - Erster Entwurf: Erste Ausformulierung einiger Informationen. 

!!! note "Hinweis"

    In diesem Abschnitt schauen wir uns das Konzept der Modul Klasse im modified Shop System an. Die Überschrift wurde als *abstract* deklariert, da es keine konkrete Modul Klasse in modifiedt gibt. Sondern nur "Spezifische Modul Klassen" oder "Klassenerweiterungen", die dem Aufbau einer Modul Klasse folgen.

Wir unterscheiden in dieser Dokumentation zwischen "Spezifische Modul Klassen" und "Klassenerweiterungen". Spezifische Modul Klassen fügen dem System eine neue spezifische Funktionalität zum System hinzu. Wie z. B. eine Versandart oder Zahlungsmethode. Mit Klassenerweiterungen, können von einigen PHP-Klassen die Methoden erweitert werden.

Es gibt eine ganze Reihe an Modul Klassen, wie System, Shipping und die Klassenerweiterungen. Hier wird der gemeinsame Aufbau beschrieben, bzw. das Konzept, das alle Modul Klassen gemeinsam haben. In anderen Abschnitten gehen wir auf die jeweiligen Besonderheiten der jeweiligen Klasse ein.


## Konzept

??? note "Textstatus - Entwurf"

    Status: 2 von 5 - Erster Entwurf: Erste Ausformulierung einiger Informationen. 

Mit Modul Klassen lässt sich das modified Shop System erweitern. Die Modul Klassen werden vom System für unterschiedliche Aufgaben geladen. Mit ihnen können z. B. Verstand und Zahlungsmodule realisiert werden. Zudem bieten sie einen Anlaufpunkt für Einstellungen, die ein User im Adminbereich zum jeweiligen Modul tätigen kann.

## Aufbau

### Dateien

??? note "Textstatus - Entwurf"

    Status: 2 von 5 - Erster Entwurf: Erste Ausformulierung einiger Informationen. 

Alle Modul Klassen bestehen in der Regel aus mindestens zwei Datein. Eine Datei mit einer PHP-Klassen-Definition und einer Sprachdatei.

- PHP-Klassen-Defintion - Eine PHP je nach Modul Klassen Typ in `/includes/modules/<TYPE>/` oder `/admin/includes/modules/<TYPE>/`
- Sprachdatei - Eine PHP-Datei je Sprache im Verzeichnis `/lang/<LANGUAGE>/modules/<TYPE>/`.

Eine Liste mit allen Modul Klassen und deren Methoden, die du erweitern kannst, gibt es als Muster-Dateien unter [github.com/RobinTheHood/class-extensions](https://github.com/RobinTheHood/class-extensions)

### Die Klasse

??? note "Textstatus - Entwurf"

    Status: 2 von 5 - Erster Entwurf: Erste Ausformulierung einiger Informationen. 

In dieser Dokumentation zeigen wir dir jeweils zwei Wege, wie du Modul Klassen programmieren kannst.

1. **Ohne StdModul** - Wenn du eine Modul Klasse ohne Hilfsklassen erstellen möchtest, kannst du den Beispielprogrammcode am Ende dieses Abschnitts als Basis verwenden und an deine Bedürfnisse anpassen. Vorteil ... Nachteil ...

2. **Mit StdModul** - Wenn du eine Modul Klasse mit der StdModul Hilfsklasse erstellen möchtest, kannst du den Beispielprogrammcode am Ende dieses Abschnitts als Basis verwenden und an deine Bedürfnisse anpassen. Vorteil ... Nachteil ...

Eine Modul Klassen-Datei besteht aus genau einer PHP-Klasse. Diese PHP-Klasse muss den gleichen Namen (ohne `.php`) haben, wie die Datei, in der sie befindet. Als Beispiel nehmen wir eine Datei mit dem Namen `mc_my_first_module.php`. An welche Namenskonventionen du dich halten sollten, kannst du im Abschnitt [???](#) lesen.

Das bedeutet für uns, dass wir die PHP-Klasse `mc_my_first_module` nennen müssen. Wenn wir uns nicht an diese Konvention halten, lädt der Klassenloader im modified Core die Datei nicht in den Speicher und wir können die Klasse nicht verwenden.

=== "Ohne StdModule"

    ```php title="mc_my_first_module.php"
    <?php

    declare(strict_types=1);

    defined('_VALID_XTC') or die('Direct Access to this location is not allowed.');

    class mc_my_first_module
    {
        ...
    }
    ```

=== "Mit StdModule"

    ```php title="mc_my_first_module.php"
    <?php

    declare(strict_types=1);

    use RobinTheHood\ModifiedStdModule\Classes\StdModule;

    class mc_my_first_module extends StdModul
    {
        ...
    }
    ```

### Voraussetungen für Modul Klassen

Eine Modul-Datei besteht meistens aus den folgenden Elementen:

#### Attribute

- `public string $code`
- `public string $title`
- `public string $description`
- `public bool $enabled`
- `public int $sort_order`

#### Methoden

- `public function __construct()`
- `public function keys(): array`
- `public function check(): int`
- `public function install(): void`
- `public function remove(): void`

Eine ausführliche Beschreibung, was die jeweiligen Aufgaben dieser Elemente sind, findest du im Abschnitt [Referenzen > Modul Klasse](/references/module-class/). Wir gehen hier hauptsächlich auf die Methode `__construct()` ein.


### Der Constructor - in einfacher Form (nicht verwenden)

??? note "Textstatus - Entwurf"

    Status: 2 von 5 - Erster Entwurf: Erste Ausformulierung einiger Informationen. 

Als Erstes erklären wir dir die Aufgabe des Contructors anhand eines einfachen Beispiels. Dieses Beispiel solltest du in dieser Form aber nicht verwenden. Das Modul ist so nicht mehrsprachig und kann auch nicht über das Admininterface vom Nutzer aktiviert oder deaktiviert werden, da der Wert für `$this->enabled` immer auf `true` steht.

Im Constructor müssen wir jetzt die Attribute mit Werte füllen. Wir könnten für viele Attribute feste Werte verwenden.

```php title="mc_my_first_module.php"
public function __construct()
{
    $this->code        = 'mc_my_first_module';
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

=== "Ohne StdModule"

    ```php title="mc_my_first_module.php"
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

=== "Mit StdModule"

    ```php title="mc_my_first_module.php"
    public function __construct()
    {
        parrent::__construct('MC_MY_FIRST_MODULE');
    }
    ```

Statt feste Werte für `$this->title` und `$this->description`, holen wir uns diese Werte aus Konstanten, die wir später in einer Sprachdatei definieren. Siehe hierfür den Abschnitt [???](#).

Den vorderen Teil der Konstanten `MODULE_MC_MY_FIRST_MODULE` lassen wir uns bequem automatisch erzeugen und speichern in der Variablen `$prefix` zwischen.

```php
$prefix = 'MODULE_' . strtoupper(self::class);
```

Die Konstanten `MODULE_MC_MY_FIRST_MODULE_SORT_ORDER` und `MODULE_MC_MY_FIRST_MODULE_STATUS` lädt der modifed Core für uns aus der Datenbanktabelle `configure`, bevor er versucht eine Instanz der Klasse zu erzeugen und der Constructor aufgerufen wird.

Wie wir die Konstanten in die Datenbank bekommen, schauen wir uns auch noch an. Das passiert automatisch vom modified Core, sobald die Funktion `install()` aufgerufen wird, wie wir uns auch gleich noch anschauen werden.

## Beispiel

=== "Ohne StdModule"

    ```php title="mc_my_first_module.php"
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
    
=== "Mit StdModule"

    ```php title="mc_my_first_module.php"
    <?php

    declare(strict_types=1);

    use RobinTheHood\ModifiedStdModule\Classes\StdModule;
    
    class mc_my_first_module extends StdModule
    {
    
        public function __construct()
        {
            parrent::__construct('MC_MY_FIRST_MODULE');
        }
        
        public function install(): void
        {
            parent::install();
        }
        
        public function uninstall(): void
        {
            parent::uninstall();
        }
    }
    ```
