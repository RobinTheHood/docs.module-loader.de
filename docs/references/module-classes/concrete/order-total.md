# Order Total - Kontrekte Modul Klasse - Referenz

Erweitert die [abtrakte Modul Klasse](../module-class-abstract.md) um folgende Attribute und Methoden.

## Meta

!!! danger "Wichtiger Hinweis"

    Datei- und Klassennamen unterscheiden sich vom configuration type. Datei- und Klassennamen beginnen mit `ot_`, der configuration type ist `MODULE_ORDER_TOTAL`.

    - :x: order_total_my_module / order_total_my_module.php

    - :white_check_mark: ot_my_module / ot_my_module.php

| name                 | value                                               | example                        |
|----------------------|-----------------------------------------------------|--------------------------------|
| class directory      | `/includes/modules/order_total/`                    |                                |
| lang directory       | `/lang/<LANGUAGE>/modules/order_total/`             |                                |
| name                 | [`snake_case`](#)                                   | `my_module`                    |
| class name           | `ot_<NAME>` in [`snake_case`](#)                    | `ot_my_module`                 |
| file name            | `<CLASS_NAME>.php` in [`snake_case`](#)             | `ot_my_module.php`             |
| configuration type   | `MODULE_ORDER_TOTAL` in [`SCREAM_CASE`](#)          |                                |
| configuration prefix | `<CONFIGURATION_TYPE>_<NAME>` in [`SCREAM_CASE`](#) | `MODULE_ORDER_TOTAL_MY_MODULE` |

## Attribute

??? note "Textstatus - Skizze"

    Status: 1 von 5 - Skizze: Ideen und Informationen in Stichpunkten unvollständig festgehalten.

## Methoden

??? note "Textstatus - Skizze"

    Status: 1 von 5 - Skizze: Ideen und Informationen in Stichpunkten unvollständig festgehalten.

## Beispiele

Beispieldateien findest du unter [github.com/RobinTheHood/class-extensions](https://github.com/RobinTheHood/class-extensions/blob/master/new_files/admin/includes/modules/system/).
