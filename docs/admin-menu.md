---
title: Erweiterung der Menüpunkte
description: In modified kannst du einige Menüpunkte erweitern. Hier ist eine Liste mit allen Standard Menüpunkten, die du dynamisch mit einem Modul erweitern kannst.
---

# Erweiterung der Menüpunkte

??? note "Textstatus - Skizze"

    Status: 1 von 5 - Skizze: Ideen und Informationen in Stichpunkten unvollständig festgehalten.

In modified kannst du einige Menüpunkte erweitern.

### Menüpunkte

??? note "Textstatus - Skizze"

    Status: 1 von 5 - Skizze: Ideen und Informationen in Stichpunkten unvollständig festgehalten.

Hier ist eine Liste mit allen Standard Menüpunkten, die du dynamisch mit einem Modul erweitern kannst:

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

Eine [Liste mit allen Standard Menüpunkten](modified-menu.md) findest du im Anhang.

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

!!! bug "TODO"

    - Wie sollte der `boxname` gewählt werden. Namingconventions?
    - In welcher Datei sollte der Eintrag liegen und wie sollte die Datei benannt sein? Namingconventions.

Beipsieldatei

### Menü erweitern mit Configure Groups

??? note "Textstatus - Skizze"

    Status: 1 von 5 - Skizze: Ideen und Informationen in Stichpunkten unvollständig festgehalten.

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

!!! bug "TODO"

    Möglicherweise ist auch die folgende Version möglich, wenn die Configuration bereits aus der Datenbank geladen wurde


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