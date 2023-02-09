# Die Architektur von modified

Es ist hilfreich den Aufbau und die Organisation des modified System zu verstehen, damit du einfacher Module für dieses programmieren kannst. Aus diesem Grund erfährst du in diesem Abschnitt einige Hintergründe zum System.

## Grundlegende Technologien die modified verwendet

Historisch ist modified als Fork aus dem shopsystem xt:Commerce hervorgegangen und ähnelt einem Zusammenschluss einer Vielzahl von einzelnen Skripten, die zusammen die Aufgaben eines Onlineshopsystems übernehmen. Dieses Grundsystem wird auch als Core bezeichnet.

Eine lange Zeit war es nicht möglich Module im üblichen Sinne für das System zu programmieren. Zwar gab es die Technik der Klassenerweiterung, Module waren jedoch größtenteils Core-Hacks, die der Benutzer oder Entwickler per Anleitung in den bestehenden Programmcode einbauen musste.

Erst mit der Version 2.0.0.0 wurde das von modified benannte Autoinclude-System eingeführt. Mit diesem konnte sich das System besser ohne händische Anpassungen am Core erweitern lassen. Dieses Autoinclude-System wird Schrittweise mit Updates erweitert und soll laut [Ankündigung im modified Forum](https://www.modified-shop.org/forum/index.php?topic=41259.msg376282#msg376282) durch ein neues System ergänzt oder ersetzt werden.

Die modified Shopsoftware ist in der Programmiersprache PHP geschrieben, läuft auf einem Apache Server und verwendet zum größten Teil die Smarty Template Engine. Als Datenbank kann MySQL oder MariaDB verwendet werden.

## Limitierung der Architektur und des Programmcodes bei modified

Als Entwickler solltest du als erstes verstehen, dass das modified System historisch aus alten PHP Konzepten gewachsen und nur an wenigen Stellen objektorientiert aufgebaut ist. Das System verwendet keine [Model View Controller Architektur](https://de.wikipedia.org/wiki/Model_View_Controller) und basiert auch nicht auf einem [typischen PHP Framework](https://kinsta.com/de/blog/php-frameworks/). modified verwendet zudem keinen [Paketmanager wie Composer](https://getcomposer.org), um externe Codebibliotheken einzubinden oder zu verwalten.

Ein PHP Entwickler kann nur bedingt moderne Software- und Desingkonzepte anwenden, die mit der modernen OOP in PHP zur Verfügung stehen. Clean Code Konzepte wie [Keep it Simple, Stupid (KISS)](https://de.wikipedia.org/wiki/KISS-Prinzip), [Don't Repeat Yourself (DRY)](https://de.wikipedia.org/wiki/Don’t_repeat_yourself) oder [SOLID](https://de.wikipedia.org/wiki/Prinzipien_objektorientierten_Designs#SOLID-Prinzipien) werden selten angewedet. [Einige Entwickler würden den Programmcode von modified daher als Spaghetticode bezeichnen](https://www.sellerforum.de/shopsysteme-f34/modified-shop-auf-version-2-0-umstellen-t45690.html?sid=d6e7b5bd897a84963d7bad50a14b9e66#p551383). Viel Code besteht aus sehr langen verschachtelten Verzweigungen mit globalen Abhängigkeiten und sich nicht selbst dokumentierenden Bezeichner. Mit einer ebenfalls sehr hohen zyklomatische Komplexität hat das zur Folge, dass der Quellcode von modified schwer zu lesen und aus wissenschaftlicher Sicht fehleranfällig ist.

> These studies show that code complexity, such as cyclomatic complexity, correlates with the presence of bugs in code.
>
> [C. Chen, “An Empirical Investigation of Correlation between Code Complexity and Bugs,” Dec. 2019, doi: 10.48550/arxiv.1912.01142](https://arxiv.org/abs/1912.01142)

Im Programmcode werden ebenfalls keine Techniken wie [Type-Hints](https://www.php.net/manual/en/language.types.declarations.php) oder [PHPDocs](https://phpstan.org/writing-php-code/phpdocs-basics) verwendet, die eine mögliche falsche Typisierung und somit Fehler vorzeitig aufspüren könnten. Eine statische Codeanalyse z. B. durch [Psalm](https://psalm.dev), [PHPStan](https://phpstan.org) oder durch eine aktuelle IDE, ist kaum möglich. So können Programmfehler leicht übersehen werden.

Das alles hat zur Folge, dass das System nur sehr schwierig bis garnicht mit automatisierten Unit-Tests oder einer statischen Codeanalyse zu testen ist. Es werden von modified bei der Entwicklung möglicherweise keine programmatischen Tests verwendet oder mindestens nicht öffentlich zur Verfügung gestellt. Somit lässt sich nicht automatisiert testen, ob ein Modul Fehler im restlichen System verursacht. Tests von Drittanbietern sind ebenfalls nicht bekannt. Solltest du Lust auf ein Projekt haben, könntest du der Erste sein, der Tests für das modified System schreibt. Diese Tests solltest du unbedingt allen Entwicklern als OpenSource Projekt zur Verfügung stellen.

[Eine umfangreiche API, mit der man auf die Entitäten des modified Systems zugreifen kann, gibt es ebenfalls nicht](https://www.modified-shop.org/forum/index.php?topic=41259.0). Das erschwert die leichte Anbindung von Drittanbierter-Tools. Fast alle Requests werden komplett auf dem Server gerendert, wodurch der Server keine Last an den Client abgeben kann. Auch für eigene Client-Side Tools, musst du Serverseitig jedes mal einen eigenen Endpoint-Controller entwickeln.

Viele dieser Punkte lassen sich zum Teil mit der historischen Vergangenheit von modified erklären. In frühen PHP Versionen standen nur wenige objektorientierte Programmierkonzepte und keine statische Typisierung zur Verfügung.

## Controller-Dateien

Das System besteht wie bereits erwähnt aus einer Vielzahl von PHP-Dateien, die direkt über die URL aufgerufen werden können. Z. B. mit einem Browser. Diese direkt aufrufbaren PHP-Dateien, die jeweils eine Business-Logik-Aufgabe erfüllen (z. B. den Warenkorb anzeigen oder den Login durchführen), nennen wir in diesem Text _Controller-Dateien_.

Im Root-Verzeichnis des Shops befinden sich alle Controller-Dateien, wie z. B. `/index.php`, `/shopping_cart.php` oder `/login.php`. Diese gehören zur Storefront, die auch als Frontend bezeichnet wird.

Im Verzeichnis `/admin/` befinden sich alle Controller-Dateien, die zur Adminoberfläche gehören. Die Adminoberfläche wird auch Admin, Admininterface oder Backend genannt. Front- und Backend haben in diesem Kontex nichts mit den Technologien und Bezeichnungen Client- oder Server-Side zu tun.

Solange kein Modul über eine `.htaccess` die URLs auf eine individuelle Entrypoint- oder Endpoint-Datei umleitet, bilden die Namen der Controller-Dateien gleichzeitig die Routen des modified Systems. Eine Art `Router.php` oder `Dispatcher.php` gibt es nicht.

Die Controller-Dateien laden größtenteils weitere Include-Dateien nach (siehe Abschnitt [_"Include-Dateien"_](#)), haben jedoch auch selbst eine eigene Programmlogik. Oft ist diese Logik als einfaches Skript programmiert und nicht in einer Klasse oder Funktion gekapselt. Die meisten Controller-Dateien enden mit einer Ausgabe an den Client bzw. Browser. Die Ausgabe wird durch die Template Engine Smarty generiert.

Die Templates und Template-Dateien liegen im Verzeichnis `/templates/`. Viele Controller-Dateien im `/admin/` Verzeichnis generieren ihren HTML Code allerdings selbst. Hier wird oft nicht auf Smarty zurückgegriffen. Das macht es schwieriger Designänderungen oder Ergänzungen am Admininterface mit einem Modul vorzunehmen.

## Include-Dateien

Im Root- und im `/admin/` Verzeichnis befinden sich viele weitere Unterverzeichnisse, die wiederum viele weitere PHP-Dateien enthalten. Hier kann das `/inc/` oder `/includes/` Verzeichnis als Beispiel genannt werden. Die Dateien in diesen Verzeichnissen sind über eine `.htaccess` vor einem direkten Aufruf über den Client bzw. Browser geschützt. Können also bei richtiger Konfiguration des Servers nicht direkt über eine URL / Route aufgerufen werden. Diese Dateien übernehmen jeweils spezielle (Teil-)Aufgaben und werden in den Controller-Dateien inkludiert und zusammengesetzt, um die jeweilige benötigte (Teil-)Funktion oder Aufgabe auszuführen.

Wir nennen diese Dateien in diesem Text _Include-Dateien_. Diese Include-Dateien sollen, wie eben beschrieben, nicht direkt über den Browser aufgerufen werden und erfüllen ihre Aufgabe meist nur im Zusammenspiel mit anderen Controller- und/oder Include-Dateien. Manche Include-Dateien sind Skripte, manche beinhalten eine Sammlung an PHP Funktionen oder Klassen. Manche Include-Dateien beinhalten eine Mischung aus allem.

## Helper-Funktionen im /inc/ Verzeichnis

Viele Helper-Funktionen lassen sich an fast jeder Stelle im Programmcode verwenden. Sie sind im globalen Namespace und Scope definiert. Viele dieser Helper-Funktionen befinden sich im Verzeichnis `/inc/`. Ein gelegentlicher Blick in dieses Verzeicnis lohnt sich, wenn du eine nützliche Funktion suchst.

Beispielsweise befindet sich in der Datei `/inc/xtc_get_description.inc.php` eine gleichnamige Funktion `xtc_get_description(…)`, die zu einer ProductId die zugehörige Produktbeschreibung liefert.

## Scope - Alles global

Das modified System verwaltet viele Klassen, Funktionen, Variablen und Konstanten im globalen Scope und gibt uns keine Namenskonvention mit an die Hand, um die Kollision gleicher Symbole und Bezeichner zu vermeiden. Namespaces werden ebenfalls nicht verwendet. Aus diesem Grund kann es leicht zu unerwarteten Konflikten kommen, wenn wir unwissentlich eine Variable in unserem Modul verwenden, die bereits im modified Core oder in einem anderen Modul verwendet wird.

Derartige Fehler lassen sich zum Glück leicht vermeiden. Für Bezeichner sollte immer eine feste einheitliche Namenskonvention verwenden werden, sofern wir es nicht schaffen, Programmcode in eigene Scopes und Namespaces zu verpacken. Tips hierzu findest du im Abschnitt [_"???"_](#).

## Coding Standards / Codings Styles bei modified / PSR-12

Der Programmcode des modified Systems folgt keinem offiziellen, einheitlichen oder bekannten Coding Standard. Allerdings lassen sich mehrere verschiedene eigene Coding Styles im Quellcode beobachten, zu denen es aber keine Dokumentation gibt. [Eine Umfrage mit 5 Teilnehmern im modified Forum hat 2019 ergeben, dass ein einheitlicher Coding Style gewünscht ist](https://www.modified-shop.org/forum/index.php?topic=40017.0). Bis heute wurden noch keine Leitlinien herausgegeben.

Sofern es keinen guten Grund für einen anderen Coding Style gibt, ist der [PSR-12: Extended Coding Style der PHP-FIG](https://www.php-fig.org/psr/psr-12/) zu empfehlen, der von einer Vielzahl an Projekten eingesetzt wird.