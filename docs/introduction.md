---
title: Einleitung
description: In diesem Text lernst du wichtige Konzepte und Hintergrundinformationen zum modified System kennen, die dir dabei helfen werden, Module für die modified eCommerce Shopsoftware zu programmieren.
---

# Einleitung

In diesem Text lernst du wichtige Konzepte und Hintergrundinformationen zum modified System kennen, die dir dabei helfen werden, Module für die modified eCommerce Shopsoftware zu programmieren. Er dient als Einstieg in die Modulentwicklung für modified. Dieser Text richtet sich an PHP Entwickler, die Module und Erweiterungen für das modified Shopsystem programmieren möchten. Er beschreibt die Sicht eines PHP Entwicklers auf das System.

Du erfährst zudem die Konzepte, wie du ohne und mit dem [Modified Module Loader Client (MMLC)](https://module-loader.de) Module für die modified Shopsoftware entwickeln kannst.

## Loslegen mit einem Tutorial

Wenn du sofort mit der Programmierung eines Moduls loslegen möchtest, kannst du mit dem Tutorial [„Dein erstes modified Modul mit dem MMLC programmieren“](https://module-loader.de/docs/tutorial.php) beginnen. Wenn du allerdings die Konzepte verstehen möchtest, solltest du diesen Text lesen.

## Literatur zur PHP Programmierung

Als PHP Anfänger solltest du zusätzlich weitere Literatur zur modernen PHP Entwicklung lesen. Die Programmiersprache PHP hat sich in den letzten 15 Jahren stark weiterentwickelt. Viele der hier aufgeführten Programmier- und Designkonzepte, welche im modified Ökosystem verwendet werden, entsprechen nicht mehr dem aktuellen Stand der Technik. Die gängigen Arbeitsweisen der weltweiten PHP Entwickler-Community haben sich verändert und du solltest nicht anhand überholter Konzepte die PHP Entwicklung lernen.

Hier ist eine Liste mit guter Literatur zur moderner PHP Programmierung:

- [PHP The Right Way](https://phptherightway.com)
- [TheCodingMachine Best Practices PHP](https://bestpractices.thecodingmachine.com)
- [Best practices for PHP exception handling](https://moxio.com/blog/best-practices-for-php-exception-handling/)
- [PHP Code Quality Tools to Check and Improve your Code](https://thevaluable.dev/code-quality-check-tools-php/)
- [Open Source Guides](https://opensource.guide)

Hier ist eine Liste mit guten Videos zur modernen PHP Programmierug:

- [PHP for Beginners (2023 Edition) - Youtube](https://www.youtube.com/watch?v=U2lQWR6uIuo&list=PL3VM-unCzF8ipG50KDjnzhugceoSG3RTC)
- [Vitalij Mik - Youtube](https://www.youtube.com/@VitalijMik)

Dieser Text versucht dir dennoch Möglichkeiten mit an die Hand zu geben, um moderne PHP Programmierung in deine modified Modulentwicklung mit einfließen zu lassen.

## Bezeichnungen

Dieser Text verwendet selbst gewählte Bezeichnungen, sofern es keine offiziellen Bezeichnungen für in modified eingesetzte Konzepte gibt. Für diese eigenen Bezeichnungen orientieren wir uns an aktuelle oder ähnliche Technologien.

An dieser Stelle kann zudem erwähnt werden, dass die offizielle Schreibweise von modified mit einem kleinen `m` beginnt, worauf im modified Umfeld Wert gelegt wird.

## Was ist modified?

modified ist eine unter der GNU General Public License (GPL v2) lizenzierte E-Commerce Online Shopsoftware, mit der Einzelpersonen oder Firmen einen Online-Shop auf ihrem eigenen Server oder bei einem Hostingprovider im Internet betreiben können.

Der Programmcode von modified wird auf der offiziellen Webseite [www.modified-shop.org/download](https://www.modified-shop.org/download) kostenlos veröffentlicht und von einem Entwicklerteam in einem SVN Repository entwickelt. Ein Großteil der aktuellen Entwicklung findet dort von den Geschäftsführern Gerhard Waldemair und Torsten Riemer der modified UG (haftungsbeschränkt) & Co. KG statt.

## Wie du dich am Projekt modified beteiligen kannst

In diesem Text fokussieren wir uns darauf, wie du dich durch die Entwicklung von Modulen, am modified Projekt beteiligen kannst. Abgesehen davon gibt es weitere Möglichkeiten der Beteiligung, die wir dir in diesem Abschnitt kurz vorstellen möchten.

- Eine direkte Mitarbeit am Quellcode ist z. B. möglich, sobald du dich als erfahrener Entwickler beweist. Eine öffentliche Anleitung, die beschreibt, welche Anforderungen ein Entwickler erfüllen muss, damit er als erfahren gilt, gibt es nicht. Versuche dich im modified Forum zu beteiligen und zeige dort, was du kannst.

- Fehler, Bugs und Verbesserungswünsche können von jedem in einen Bugtracker eingetragen werden. modified verwendet hierfür die Software Trac [trac.modified-shop.org](https://trac.modified-shop.org).

- Ein reger Austausch findet zudem im Forum [www.modified-shop.org/forum/](https://www.modified-shop.org/forum/) auf der modified Webseite statt.

- Auch im [modified Wiki](https://www.modified-shop.org/wiki/Hauptseite) kannst du dich über eine Mitarbeit beteiligen. In diesem Wiki befinden sich Informationen für Endanwender und einige Themen zur Programmierung im modified Ökosystem. Wenn du eine ausführliche Dokumentation zum Thema Modulentwicklung suchst, empfehlen wir dir den Text, den du gerade liest. An diesem kannst du dich ebenfalls über GitHub beteiligen. [github.com/RobinTheHood/docs.module-loader.de](https://github.com/RobinTheHood/docs.module-loader.de)
