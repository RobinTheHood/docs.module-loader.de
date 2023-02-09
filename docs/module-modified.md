# Was ist ein Modul im modified Kontext?

??? note "Textstatus 3"

    Status: 3 von 5 - Dieser Abschnitt könnte besser geschrieben werden.

Anders als bei anderen Modulkonzepten besteht ein Modul im modified Kontext nicht aus einem Verzeichnis, in dem dein Modul und alle dazugehörigen Dateien gebündelt liegen (wie z. B. in einem Verzeichnis `/modules`, das vom modified System geladen werden könnte). Solltest du den MMLC verwenden, kann der dir diese Funktion jedoch simulieren.

Ein Modul für modified kannst du dir als Ansammlung von hauptsächlich Include-Dateien vorstellen, die du in die unterschiedlichen Verzeichnisse des modified Systems verteilen musst. Komplexere modified Module beinhalten oft zusätzlich Controller- und Template-Dateien.

Die Autoinclude-Dateien werden durch Autoinclude-Stellen in den modified Core geladen, an denen du mit deinem Modul eingreifen möchtest. Nicht jede beliebige Stelle das modified System ist erweiterbar. Auf die Möglichkeiten und Grenzen gehen wir im Abschnitt [_"Das Autoinclude System"_](#) ein.

_Hinweis: Einige Entwickler umgehen die Einschränkung, nicht jede beliebige Stelle erweitern zu können, indem sie "nicht updatefähige Module" programmieren (siehe Abschnitt [_"Nicht updatefähige Module"_](#)). Das kann jedoch nicht empfohlen werden. Nicht updatefähige Module sollten nur im Ausnahmefall programmiert werden._

## Entwicklung von Modulen

??? note "Textstatus 3"

    Status: 3 von 5 - Dieser Abschnitt könnte besser geschrieben werden.

Dieser Text erklärt dir die nötigen Konzepte, wie du Module für modified programmierst. Dazu gehen wir zuerst auf alle Bestandteile ein, aus denen ein Modul aufgebaut werden kann und schauen uns an, wozu sie dienen und wie sie funktionieren. Hier ein Überblick:

-   Controller-Dateien
-   Inklude-Dateien
-   Autoinclude-Dateien
-   System Modul
-   Shipping Modul
-   Payment Modul
-   Klassenerweiterungen
-   etc.

Da dir dieser Text nicht jedes Detail erklären kann, kannst du dir zusätzlich ansehen, wie Probleme in anderen Modulen gelöst werden. Leider schwankt die Qualität von Modul zu Modul. Aus diesesm Grund führen wir hier eine Liste mit ausgewählten Modulen für dich, an denen du dich orientieren solltest. Der Quellcode der aufgeführten Module lässt sich direkt im Browser ansehen. Zudem erweitern wir die Liste kontinuierlich:

-   [robinthehood/attribute-price-update](https://github.com/RobinTheHood/attribute-price-update)
-   [grandeljay/modified-spanish-language](https://github.com/grandeljay/modified-spanish-language) ([und viele mehr](https://github.com/grandeljay?tab=repositories&q=modified-shop&type=public))

## Nicht updatefähige Module

??? note "Textstatus 3"

    Status: 3 von 5 - Dieser Abschnitt könnte besser geschrieben werden.

Im modified Umfeld wird von einem _nicht updatefähigen Modul_ gesprochen, wenn ein Modul nicht nur Dateien in Verzeichnissen ergänzt, sondern eine mitgelieferte Installationsanleitung verlangt, dass du Core-Dateien per Hand veränderst. Also wenn du bestehenden Programmcode per Hand hinzufügen oder ändern sollst. Wenn du ein Update deines modified System machst, werden diese manuellen Änderungen an den Core-Dateien oft überschrieben oder gelöscht, da das modified System keine Kenntnis über deine Änderungen hat.

Ein nicht updatefähiges Modul verhindert also, dass du dein modified System fehlerfrei updaten kannst, ohne dass du nach dem Update kontrollieren musst, ob das Modul noch ordnungsgemäß eingebaut ist und funktioniert.

_Hinweis: Änderungen an den Core-Dateien sollten wenn möglich immer vermieden werden. Aus diesem Grund ist die Verwendung, der Einbau und die Entwicklung von nicht updatefähigen Modulen nicht zu empfehlen._

## Updatefähige Module

??? note "Textstatus 3"

    Status: 3 von 5 - Dieser Abschnitt könnte besser geschrieben werden.

Ein Modul wird als _updatefähigs Modul_ bezeichnet, wenn es bei einem Update des modified Systems nicht (in Teilen) gelöscht wird und dein Shopsystem weiterhin ordnungsgemäß funktioniert. Programmcode darf nicht in den Core eingebaut werden. Es ist als das Gegenteil zum _nicht updatefähigen Modul_ zu sehen.

## Aufbau von updatefähigen modified Shop Modulen

??? note "Textstatus 3"

    Status: 3 von 5 - Dieser Abschnitt könnte besser geschrieben werden.

Grundsächlich gibt es zwei Möglichkeiten, wie du updatefähige Module in modified aufbauen kannst. In den meisten Fällen wirst du diese beiden Möglichkeiten vermischen. Du kannst das "Autoinclude System" verwenden, das wir dir im Abschnitt [_"x"_](#) vorstellen oder einige modified-PHP-Klassen erweitern, was wir in Abschnit x besprechen werden. Zudem solltest du dein Modul immer über eine System-Modul-Klasse verwalten bzw. ins System einbinden, was Thema in Abschnitt [_"x"_](#) sein wird.

In all diesen Fällen, verteilst du deine PHP-Dateien in den Verzeichnissen von modifed. Es gibt kein Verzeichnis, indem dein Modul und alle dazugehörigen Dateien gebündelt liegen (wie z. B. in einem Ordner `/modules`, das vom modified System geladen wird). Das modified System sucht in ausgewählte Verzeichnisse nach deinen Dateien und lädt den Code in den Core.

In der Regel besteht ein updatefähiges Module aus folgenden Elementen:

-   Eine System Modul Datei/Klasse die im Verzeichnis `/admin/includes/modules/system/` liegt.
-   Eine oder mehrere Sprachdateien die im Verzeichnis `/lang/<LANGUAGE>/modules/system/` liegen.
-   Optional: Eine oder mehrere Auto-Include-Dateien die in `/includes/extra` oder `/admin/includes/extra/` liegen.
-   Optional: Eine oder mehrere Klassenerweiterungen die in `/includes/modules` oder `/admin/includes/modules/` liegen.
-   Optional: Eine oder mehrere Controller-Dateien die in `/` oder `/admin/` liegen
-   Optional: Eine oder mehrere Menu-Dateien die in `/admin/includes/extra/menu/` liegen.
-   Optional: Eine oder mehrere Themplate-Dateien die in `/templates/<TEMPLATE>/` liegen.

Um die Programmierung von Modulen für das modified System zu verstehen, solltest du dir folgende Themen in der aufgeführten Reihenfolge ansehen:

1. Das Autoinclude System
1. System Modul
1. Eigene Controller
1. Klassenerweiterungen
