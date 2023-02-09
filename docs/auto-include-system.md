# Das Autoinclude System von modified

??? note "Textstatus 3"

    Status: 3 von 5 - Dieser Abschnitt könnte besser geschrieben werden.

Das Autoinclude System wurde geschaffen, damit Modulentwickler das modified Shopsystem ohne Core-Anpassungen erweitern können. Hierzu existieren mittlerweile Hookpoints in den Controller- und Include-Dateien. Diese Hookpoints werden bei modified Autoincludes genannt.

Die Autoincludes laden weiteren Programmcode aus einer Autoinclude-Datei. Diese Datei müssen wir dazu an einen dafür vorgesehenen Ort ablegen. Für das Autoinclude System spielt es zum größten Teil keine Rolle, wie du diese Datei benennst. Zwar ist oft alleine die Dateiendung `.php` ausreichend, allerding solltest du als guter PHP Entwickler Wert darauf legen, einen sinnhaften Dateinamen zu verwenden. Der Abschnitt [_Das Autoinclude System - Namenskonventionen von Autoinclude-Dateien_](#) gibt dir eine gute Grundlage für die Bennung von Autoinclude-Dateien.

Wie das Autoinclude System genau funktioniert und wie wir es verwenden können, um das modified System zu erweitern, schauen wir uns im folgenden Abschnitt an.

## Allgemeines Beispiel

??? note "Textstatus 3"

    Status: 3 von 5 - Dieser Abschnitt könnte besser geschrieben werden.

Als Erstes schauen wir uns ein allgemeines Beispiel zum Verständnis an, danach werden wir uns mit einem realen Beispiel beschäftigen, in dem wir die Login-Funktion von modified mit einer Autoinclude-Datei erweitern.

_Hinweis: Die hier aufgeführten Beispiele sind nicht mit einem System Modul verknüpft. Schaue dir den Abschnitt [_"System Module"_](#) an, damit du erfährst, wie du eine Autoinclude-Datei erweiterst, damit diese in Kombination mit einem System Modul arbeitet._

Für dieses allgemeine Beispiel stellen wir uns vor, dass es im modified Shop eine Seite bzw. eine Controller-Datei im Rootverzeichnis mit dem Namen `example_xyz.php` gibt, die wir erweitern möchten. Diese fiktive Seite gibt immer den Wert 10 an den Browser aus. Wir sehen im Browser immer die Zahl 10, wenn wir diese Seite aufrufen. Nun möchten wir `example_xyz.php` so erweitern oder verändern, dass mit unserem Modul bzw. unsere Autoinclude-Datei nur noch die Zahl 20 an den Browser ausgegeben wird.

Wir schauen uns dazu als Erstes die fiktive Controller-Datei `example_xyz.php` an. Hier stellen wir jetzt fest, dass es einen Autoinclude-Eintrag in Zeile 8 gibt, der fast immer wie folgt aussieht:

```php linenums="1" title="example_xyz.php"
<?php

$modifedVariable = 10;

foreach (auto_include(DIR_FS_CATALOG.'includes/extra/example/','php') as $file) require_once ($file);

echo $modifedVariable
```

Der Programmcode in Zeile 5 wird bei modified Autoinclude genannt. Dieser Autoinclude kann für uns Code aus einer Datei mit der Endung `.php` nachladen. Die Konstante `DIR_FS_CATALOG` beinhaltet den Pfad des Rootverzeichnises. Somit werden mit `require_once` alle Datei mit der Endung `.php` aus dem Verzeichnis `/includes/extra/example/` in die Datei `/example_xyz.php` zwischen Zeile 7 und 9 eingefügt.

<<<<<<< HEAD
_Hinweis: Beachte das nicht immer `require_once()` verwendet wird um Code zu laden. Es gibt Autoincludes, bei denen `require()` oder `include()` verwendet werden. Du solltest dir die Datei vorher immer ansehen und überprüfen, mit welcher Funktion deine Datei in den Programmcode hinzugefügt wird._
=======
_Hinweis: Beachte, dass nicht immer `require_once()` verwendet wird, um Code zu laden. Es gibt Autoincludes, bei denen `require()` oder `include()` verwendet werden. Du solltest dir dediei Datei vorher immer ansehen und überprüfen, mit welcher Funktion deine Datei in den Programmcode hinzugefügt wird._
>>>>>>> a4f6b98 (docs: fix some spellings)

Um die Zeile 5 in unserem fiktiven Controller Programmcode zu erweitern, benötigen wir eine Datei, die in `/includes/extra/example/` liegt. Diese Datei solltest du nach der Konvention aus Abschnitt [_"???"_](#) benennen. Wir wählen z. B. `mc_my_first_module.php`. Diese Datei soll jetzt dafür sorgen, dass wir die Variable `$modifedVariable` aus der `/example_xyz.php` Datei mit unserem eigenen Wert überschreiben.

Unser Code in `mc_my_first_module.php` könnte zu diesem Zweck wie folgt aussehen:

```php linenums="1" title="/includes/extra/example/mc_my_first_module.php"
<?php

declare(strict_types=1);

$modifiedVariable = 20 // Überschreibe mit unserem Wert.
```

<<<<<<< HEAD
Mit unserem Modul bzw. mit unserer Autoinclude-Datei `mc_my_first_module.php` in `/includes/extra/example/` konnten wir den Shop soweit erweitern, dass statt der Zahl `10` die Zahl `20` im Browser ausgegeben wird.
=======
Mit unserem Modul bzw. mit unserer Autoinclude-Datei `mc_my_first_module.php` in `/includes/extra/example/` konnten wir den Shop so weit erweitern, dass statt der Zahl `10` die Zahl `20` im Browser ausgegeben wird.
>>>>>>> a4f6b98 (docs: fix some spellings)

??? note "Textstatus 1"

    Status: 3 von 5 - Skizze

<<<<<<< HEAD
Zu bachten ist das `declare(strict_types = 1);` und das am Ende der Datei kein `?>` vorkommt, wieso du am Ende von PHP Dateien kein PHP Closing Tag `?>` verwenden solltest, beschreibt der PSR Standard X und folgende Link: https://stackoverflow.com/questions/3219383/why-do-some-scripts-omit-the-closing-php-tag
=======
Zubachten ist das `declare(strict_types = 1);` und dass am Ende der Datei kein `?>` vorkommt, wieso du am Ende von PHP Dateien kein PHP Closing Tag `?>` verwenden solltest, beschreibt der PSR Standard X und folgende Link: https://stackoverflow.com/questions/3219383/why-do-some-scripts-omit-the-closing-php-tag
>>>>>>> a4f6b98 (docs: fix some spellings)

## Konkretes Beispiel anhand von login.php

??? note "Textstatus 2"

    Status: 2 von 5 - Erster Entwurf

<<<<<<< HEAD
Anhand der `/login.php` wollen wir uns ansehen, wie das _Autoinclude System_ für eine richtige Shop Funktion funktioniert. Wir wollen `/login.php` so erweitern, dass der Warenkorb des Kunden beim Einloggen nicht wieder hergestellt wird. Dazu schauen wir uns als erstes die Controller-Datei `/login.php` an und überprüfen, an welcher Stelle wir per Autoinclude in das System eingreifen können.
=======
Anhand der `/login.php` wollen wir uns ansehen, wie das _Autoinclude System_ für eine richtige Shop-Funktion funktioniert. Wir wollen `/login.php` so erweitern, dass der Warenkorb des Kunden beim Einloggen nicht wieder hergestellt wird. Dazu schauen wir uns all erstes die Controller-Datei `/login.php` an und überprüfen, an welcher Stelle wir per Autoinclude in das System eingreifen können.
>>>>>>> a4f6b98 (docs: fix some spellings)

```php title="/login.php"
<?php

...

$_SESSION['cart']->restore_contents();

...

foreach (auto_include(DIR_FS_CATALOG.'includes/extra/login/','php') as $file) require_once ($file);

...
```

Normalerweise lädt `$_SESSION['cart']->restore_contents();` den Warenkorb des Kunden aus der Datenbank. Das können wir nicht verhindern. Allerdings können wir versuchen, den Warenkorb nachträglich zu verändern.

<<<<<<< HEAD
Hier sehen wir, dass wir nach `$_SESSION['cart']->restore_contents();` einen Autoinclude / Hookpoint vorfinden. An dieser Stelle können wir Code nachladen, der den Warenkorb wieder leert. Das können wir mit einer Datei machen, die wir unter `/includes/extra/login/`. Die Datei bennen wir wieder nach unserer Namenskonvention (Abschnitt [_"???"_](#)) `mc_my_first_module.php`.
=======
Hier sehen wir, dass wir nach `$_SESSION['cart']->restore_contents();` einen Autoinclude / Hookpoint vorfinden. An dieser Stelle können wir Code nachladen, der den Warenkorb wieder leert. Das können wir mit einer Datei machen, die wir unter `/includes/extra/login/`. Die Datei benennen wir wieder nach unserer Namenskonvention (Abschnitt [_"???"_](#)) `mc_my_first_module.php`.
>>>>>>> a4f6b98 (docs: fix some spellings)

```php title="/includes/extra/login/mc_my_first_module.php"
<?php

declare(strict_types=1);

$_SESSION['cart']->reset();
```

Der Code in der Datei ist relativ simpel gehalten. Mit nur einer Zeile können wir den Inhalt des Warenkorbs wieder zurücksetzen.

## Die Reihenfolge - mehrere Autoinclude-Dateien in einem Verzeichnis

??? note "Textstatus 2"

    Status: 2 von 5 - Erster Entwurf

Wenn in einem Verzeichnis mehrere Autoinclude-Dateien vorhanden sind, werden die Dateien in alphabetischer Reihenfolge geladen.

## Wo liegen die Datein, die per Autoinclude in modified hinzugefügt werden können?

??? note "Textstatus 2"

    Status: 2 von 5 - Erster Entwurf

Alle Dateien liegen unter:

-   `/includes/etxra/`
-   `/admin/includes/extra/`
-   `/lang/{LANGUAGE}/extra/`

## Eine Liste aller verfügbaren Autoincludes in modified

??? note "Textstatus 3"

    Status: 3 von 5 - Dieser Abschnitt könnte besser geschrieben werden.

Da sich die Anzahl der Autoincludes von Version zu Version des modified Systems ändert und immer mal wieder neue hinzukommen, listet dir dieser Text nicht alle möglichen Autoincludes auf. An dieser Stelle möchten wir dir jeodoch erklären, wie du selbst herausfinden kannst, welche Autoincludes dir zur Verfügung stehen.

Wenn du eine IDE oder einen Code-Editor wie VS Code verwendest, in dem du global über dein gesamtes Projekt eine Suche starten kannst, bietet es sich an, nach dem Vorkommen der Zeichenkette 'auto_include' zu suchen, um alle Autoincludes in deiner modified Version zu finden.

Im Wiki von modified gibt es mittlerweile ebenfalls einen Eintrag, der versucht alle Autoincludes aufzuführen (https://www.modified-shop.org/wiki/Auto_include_Modul_System). Falls du eine sehr neue modified Version verwendest, werden hier jedoch nicht immer ganz aktuell alle Autoincludes aufgelistet.

## Namenskonventionen von Autoinclude-Dateien

??? note "Textstatus 2"

    Status: 2 von 5 - Erster Entwurf

<<<<<<< HEAD
Es ist immer sinnvoll feste Namenskonventionen zu verwenden. Diese solltest du auch auf Autoinclude-Datein anwenden. Das hilft Namenskollisionen mit Bezeichnern aus dem Core und anderen Modulen zu vermeiden und erleichtert das Wiederfinden und Zuordnen von Dateien, die im System verteilt wurden.

Wenn Dateien die zu einem Modul gehören unterschiedlich benannt wurden, können Entwickler und Anwender, nicht mehr auf den ersten Blick erkennen, zu welchem Modul eine Datei gehört. Möglicherweise lässt sich nicht einmal erkennen, ob die Datei sogar Teil des Cores ist. Besonders wenn ein Modul wieder aus dem System entfernt werden soll, bleibt oft nur der Weg jedes Verzeichnis zu kontrollieren und betroffene Dateien zu entfernen. Lassen sich die Dateien nicht an ihrem Namen erkennen, ist der Aufwand um ein Vielfaches größer.
=======
Es ist immer sinnvoll, feste Namenskonventionen zu verwenden. Diese solltest du auch auf Autoinclude-Datein anwenden. Das hilft Namenskollisionen mit Bezeichnern aus dem Core und anderen Modulen zu vermeiden und erleichtert das Wiederfinden und Zuordnen von Dateien, da im System verteilt wurden.

Wenn Dateien, die zu einem Modul gehören, unterschiedlich benannt wurden, können Entwickler und Anwender, nicht mehr auf den ersten Blick erkennen, zu welchem Modul eine Datei gehört. Möglicherweise lässt sich nicht einmal erkennen, ob die Datei sogar Teil des Cores ist. Besonders wenn ein Modul wieder aus dem System entfernt werden soll, bleibt oft nur der Weg jedes Verzeichnis zu kontrollieren und betroffenen Dateien zu entfernen. Lassen sich die Dateien nicht an ihrem Namen erkennen, ist der Aufwand um ein Vielfaches größer.
>>>>>>> a4f6b98 (docs: fix some spellings)

Leider gibt uns modified keine Namenskonvention vor, an die wir uns halten könnten. Aus diesem Grund übernimmt das dieser Text. Wir stellen dir eine Namenskonvention vor, die sich über viele Module hinweg bewährt hat und die selbst gesteckten Anforderungen erfüllt.

## Erklärung der Namenskonvention

??? note "Textstatus 1"

    Status: 3 von 5 - Skizze

<<<<<<< HEAD
Autoinclude-Dateinamen sollten alle in snakecase geschrieben sein.
=======
Autoinclude-Dateinamen sollten alle in `snake_case` geschrieben sein.
>>>>>>> a4f6b98 (docs: fix some spellings)

Der Vendorprefix ist eine kurze Zeichenkombination, die den Hersteller des Moduls eindeutig bestimmen kannen. Er kann z. B. aus den Anfangsbuchstaben deines Namens bestehen oder aus den Anfangsbuchstaben eines Firmennamens. Wichtig ist, dass du versuchst einen VendorPrefix zu wählen, von dem du glaubst oder ausgehen kann, dass er im modified Umfeld nur von dir verwendet wird. Leider gibt es zurzeit keine Möglichkeit, um herauszufinden, dass du einen VendorPrefix verwendest, der von sonst niemanden verwendet wird. Solltest du Module mit dem MMLC programmieren, können mit diesem nur Module veröffentlicht werden, die einen eindeutigen VendorPrefix verwenden.

## Beispiele zur Benennung von Autoinclude-Dateien

??? note "Textstatus 2"

    Status: 2 von 5 - Erster Entwurf

<<<<<<< HEAD
In diesem Abschnitt schauen wir uns zwei Beispiele an, wie du Autoinclude-Dateien bennen solltest. Wie bereits geschrieben, setzt sich der Dateiname (Filename) aus folgenden Elementen zusammen: dem Vendorprefix und Modulnamen in Snakecase.
=======
In diesem Abschnitt schauen wir uns zwei Beispiele an, wie du Autoinclude-Dateien benennen solltest. Wie bereits geschrieben, setzt sich der Dateiname (Filename) aus folgenden Elementen zusammen, dem Vendorprefix und Modulnamen in `snake_case`.
>>>>>>> a4f6b98 (docs: fix some spellings)

Filename: `<Vendor-Prefix>_<SnakeCase>.php`

| Bezeichnung  | Beispiel 1               | Beispiel 2               |
| ------------ | ------------------------ | ------------------------ |
| Vendor       | My Company               | RobinTheHood             |
| Modulname    | My first Module          | Example Module           |
| Vendorprefix | `mc`                     | `rth`                    |
| SnakeCase    | `my_first_module`        | `example_module`         |
| Filename     | `mc_my_first_module.php` | `rth_example_module.php` |

Weitere Informationen zu Namensconventionen findest du unter:
(https://module-loader.de/docs/naming_convention.php)
