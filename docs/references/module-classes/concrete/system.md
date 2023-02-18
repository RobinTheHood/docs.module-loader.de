# System - Kontrekte Modul Klasse - Referenz

Erweitert die [abtrakte Modul Klasse](../module-class-abstract.md) um folgende Attribute und Methoden.

## Meta

| name                 | value                                               | example                   |
|----------------------|-----------------------------------------------------|---------------------------|
| class directory      | `/admin/includes/modules/system/`                   |                           |
| lang directory       | `/lang/<LANGUAGE>/modules/system/`                  |                           |
| name                 | [`snake_case`](#)                                   | `my_module`               |
| class name           | `system_<NAME>` in [`snake_case`](#)                | `system_my_module`        |
| file name            | `<CLASS_NAME>.php` in [`snake_case`](#)             | `system_my_module.php`    |
| configuration type   | `MODULE_SYSTEM` in [`SCREAM_CASE`](#)               |                           |
| configuration prefix | `<CONFIGURATION_TYPE>_<NAME>` in [`SCREAM_CASE`](#) | `MODULE_SYSTEM_MY_MODULE` |

## Attribute

keine

## Methoden

keine

## Beispiele

Beispieldateien findest du unter [github.com/RobinTheHood/class-extensions](https://github.com/RobinTheHood/class-extensions/blob/master/new_files/admin/includes/modules/system/).
