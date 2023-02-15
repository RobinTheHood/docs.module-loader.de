---
title: Wie funktionieren setFunctions
description: In diesem Abschnitt schauen wir uns an, was es mit der `setFunction` auf sich hat. Mit der `setFunction` können wir eine Funktion oder Methode bestimmen, die modified aufrufen soll, wenn die Anzeige für die Bearbeitung eines Konfigurationswertes in der Eingabemaske der Konfiguration gerendert werden soll.
---

# Wie funktionieren setFunctions

??? note "Textstatus - Skizze"

    Status: 1 von 5 - Skizze: Ideen und Informationen in Stichpunkten unvollständig festgehalten.

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

| `configuration_key` | `configuration_value` | `set_function`           |
| ------------------- | --------------------- | ------------------------ |
| `MODULE_COLOR`      | `red`                 | `globalFuncSelectColor(` |

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

| `configuration_key` | `configuration_value` | `set_function`       |
| ------------------- | --------------------- | -------------------- |
| `MODULE_COLOR`      | `green`               | `selectColor('hex',` |

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

| Signatur                                                                          | set_function            |
| --------------------------------------------------------------------------------- | ----------------------- |
| `public static selectColor(string $value, string $key = ''): string`              | `MyClass::selectColor(` |
| `public static selectColor(string $value, string $key = ''): string`              | `self::selectColor(`    |
| `function selectColor(string $value, string $key = ''): string`                   | `selectColor(`          |
| `function selectColor(string $outputAs, string $value, string $key = ''): string` | `selectColor('hex',`    |

## Liste der möglichen setFunctions

??? note "Textstatus - Skizze"

    Status: 1 von 5 - Skizze: Ideen und Informationen in Stichpunkten unvollständig festgehalten.

modified bringt von Haus aus einige `setFunction` mit. Das ist ganz praktisch und erspart dir einiges an Programmierarbeit. Die meisten Funktionen, die unter `/admin/includes/functions/` mit dem Präfix `xtc_cfg_` anfangen, kannst du als `setFunction` verwenden. Hier einige Beispiele:

-   `xtc_cfg_select_option()`
-   `xtc_cfg_textarea()`
-   `xtc_cfg_pull_down_order_statuses()`
-   ...

Es gibt noch viele weitere `setFunction`. Um diese zu finden, kannst du das gesamte modified Projekt im Quellcode nach `function xtc_cfg_` durchsuchen.