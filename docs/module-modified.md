---
title: Was ist ein Modul im modified Kontext?
description: Ein Modul für modified kannst du dir als Ansammlung von hauptsächlich Include-Dateien vorstellen, die du in die unterschiedlichen Verzeichnisse des modified Systems verteilen musst.
---

# Was ist ein Modul im modified Kontext?

Für Entwickler, die mit dem modifed Shop System arbeiten, ist es wichtig zu verstehen, wie Module in dieser Umgebung funktionieren. Im Gegensatz zu anderen Modulkonzepten unterscheidet sich die Struktur von Modulen im Kontext von modifed von anderen Modulkonzepten. Ein Modul besteht nicht aus einem einzigen Verzeichnis, das alle zugehörigen Dateien enthält und das vom modifed System einfach geladen werden kann.

Stattdessen ist ein Modul für modifed eine Sammlung von Dateien, die in die verschiedenen Verzeichnisse des modifed Systems verteilt werden müssen. Die Dateien können aus Klassenmodule, Klassenerweiterungen, Include, Autoincludes, Controller und Templates, sowie weiteren Hilfsdateien bestehen.

!!! warning "Achtung"

    Einige Entwickler umgehen die Einschränkung, nicht jede beliebige Stelle erweitern zu können, indem sie "nicht updatefähige Module" programmieren (siehe Abschnitt [*"Nicht updatefähige Module"*](#)). Das kann jedoch nicht empfohlen werden. Nicht updatefähige Module sollten nur im Ausnahmefall programmiert werden.

## Entwicklung von Modulen

Diese Dokumentation dient als Leitfaden für Entwickler, die Module für das modifed Shop System erstellen möchten. Du lernst die grundlegenden Konzepte und Komponenten kennen, aus denen ein Modul aufgebaut wird und wie diese funktionieren. Nachfolgend findest du einen Überblick über diese Komponenten:

- **Controller-Dateien**: Diese Dateien steuern die Logik deines Moduls und definieren, wie es mit Anfragen und Daten umgeht. Diese Controller-Dateien bilden gleichzeitig die Routen deines Moduls.

- **Include-Dateien**: Hierbei handelt es sich um Dateien, die in verschiedene Teile des Systems eingebunden werden, um spezifische Funktionen oder Ressourcen hinzuzufügen.

- **Autoinclude-Dateien**: Autoinclude-Dateien werden automatisch in den modifed Core geladen, um an bestimmten Stellen des Systems Erweiterungen deines Moduls einzufügen.

- **Klassenmodulen**: Konkrete Modul Klassen sind PHP Klassen, die grundlegende Erweiterungen oder Anpassungen am Core des modifed Systems erlauben.

- **Klassenerweiterungen**: Du kannst bestehende Klassen des Systems erweitern, um neue Funktionalitäten hinzuzufügen oder vorhandene zu modifizieren.


## Beispiele

Diese Dokumentation kann leider nicht jedes Detail abdecken. Es ist empfehlenswert, die Arbeitsweise und Lösungsansätze in bereits bestehenden Modulen zu studieren. Beachte jedoch, dass die Qualität und Struktur von Modulen variieren kann. Aus diesem Grund stellen wir eine Liste ausgewählter Module zur Verfügung, an denen du dich orientieren kannst. Der Quellcode dieser Module kann direkt im Browser eingesehen werden und wir aktualisieren die Liste kontinuierlich, um dir stets aktuelle Beispiele zu bieten. Dies ermöglicht es dir, von bewährten Praktiken anderer Entwickler zu lernen und deine Module effektiver zu entwickeln.

### Zahlungsmodule
- [robinthehood/stripe](https://github.com/RobinTheHood/modified-stripe)

### Sprachpaket Module
- [grandeljay/modified-spanish-language](https://github.com/grandeljay/modified-spanish-language) ([und viele mehr](https://github.com/grandeljay?tab=repositories&q=modified-shop&type=source&language=php))

### Sonstige Module
- [robinthehood/attribute-price-update](https://github.com/RobinTheHood/attribute-price-update)

## Nicht updatefähige Module

In der Welt von modifed wird ein Modul als "nicht updatefähig" bezeichnet, wenn es nicht nur zusätzliche Dateien in Verzeichnissen hinzufügt, sondern auch eine spezielle Installationsanleitung vorsieht, die dich auffordert, manuelle Änderungen an den Core-Dateien des Systems vorzunehmen. Das bedeutet, dass du vorhandenen Programmcode eigenhändig ergänzen oder bearbeiten sollst. Ein solches Vorgehen kann jedoch erhebliche Nachteile mit sich bringen. Wenn du dein modifed-System aktualisierst, besteht die Gefahr, dass diese manuellen Änderungen an den Core-Dateien überschrieben oder gelöscht werden. Das liegt daran, dass das modifed-System keine Kenntnis von den von dir vorgenommenen Anpassungen hat.

Im Ergebnis führt ein nicht updatefähiges Modul dazu, dass du dein modifed-System nicht mehr reibungslos aktualisieren kannst, ohne im Anschluss sorgfältig zu überprüfen, ob das Modul nach dem Update immer noch ordnungsgemäß integriert ist und wie beabsichtigt funktioniert. Dies kann zu potenziellen Problemen und Konflikten führen, die die Stabilität und Sicherheit deines Shops beeinträchtigen können. Daher ist es ratsam, sich der Auswirkungen von nicht updatefähigen Modulen bewusst zu sein und nach Möglichkeit solche Module zu vermeiden, um sicherzustellen, dass dein System reibungslos aktualisiert werden kann.

!!! warning "Achtung"

    Änderungen an den Core-Dateien sollten, wenn möglich, immer vermieden werden. Aus diesem Grund ist die Verwendung, der Einbau und die Entwicklung von nicht updatefähigen Modulen nicht zu empfehlen.

## Updatefähige Module

In in dieser Entwickler Dokumentation verwenden wir den Begriff "updatefähiges Modul", um ein Modul zu beschreiben, das bei Aktualisierungen des modifed Systems nicht gelöscht wird und somit sichergestellt ist, dass dein modified Shop weiterhin reibungslos funktioniert. Die Kennzeichnung "updatefähiges Modul" weist darauf hin, dass kein Programmcode direkt in den Core des Systems eingebettet werden sollte. Dies steht im klaren Gegensatz zu "nicht updatefähigen Modulen", bei denen manuelle Änderungen an den Core-Dateien erforderlich sind.

Ein updatefähiges Modul bietet den Vorteil, dass es die Integrität deines Shopsystems während Aktualisierungen aufrechterhält und dabei gleichzeitig sicherstellt, dass keine Konflikte mit den Core-Dateien auftreten. Dies trägt zur Stabilität und Sicherheit deines Shops bei und ermöglicht dir, von den neuesten Funktionen und Verbesserungen im modifed-System zu profitieren.

## Aufbau von updatefähigen Modulen für das modifed Shop System

Beim Erstellen von "updatefähigen Modulen" für das modifed Shop System stehen zwei Hauptansätze zur Verfügung, die oft in Kombination verwendet werden. Du kannst entweder das "Autoinclude-System" nutzen oder modifed-PHP-Klassen erweitern. Zusätzlich ist es ratsam, dein Modul über ein Klassenmodul zu verwalten, wobei oft ein System-Klassenmodul verwendet wird.

Unabhängig vom gewählten Ansatz ist es wichtig zu verstehen, dass du deine Dateien in verschiedenen Verzeichnissen innerhalb des modifed Systems verteilst. Im Unterschied zu anderen Modulkonzepten gibt es kein zentrales Verzeichnis, in dem dein Modul und alle zugehörigen Dateien gebündelt liegen. Das modifed System sucht gezielt in ausgewählten Verzeichnissen nach den Dateien deines Moduls und lädt den Code in den Core des Systems.

In der Regel besteht ein updatefähiges Modul aus folgenden Elementen:

- **Klassenmodul:** Diese Dateien befinden sich im Verzeichnis `/includes/modules/` oder `/admin/includes/modules/`.
- **Sprachdateien:** Diese Dateien liegen im Verzeichnis `/lang/<LANGUAGE>/modules/`.
- **Autoinclude-Dateien:** (optional) Diese Dateien befinden sich im Verzeichnis `/includes/extra/` oder `/admin/includes/extra/`.
- **Klassenerweiterungen:** (optional) Diese Dateien liegen im Verzeichnis `/includes/modules/` oder `/admin/includes/modules/`.
- **Controller-Dateien:** (optional) Diese Dateien befinden sich im Verzeichnis `/` oder `/admin/`.
- **Menü-Dateien:** (optional) Diese Dateien liegen im Verzeichnis `/admin/includes/extra/menu/`.
- **Template-Dateien:** (optional) Diese Dateien sind in `/templates/<TEMPLATE>/` zu finden.

Um die Programmierung von Modulen für das modified System zu beherrschen, empfehlen wir dir, die folgenden Themen in der angegebenen Reihenfolge zu studieren: Klassenmodule, Das Autoinclude System, Eigene Controller und Klassenerweiterungen. Diese grundlegenden Konzepte werden dir helfen, effiziente und leistungsfähige Module für das modified System zu entwickeln und anzupassen.
