# Klassenerweiterung für modified programmieren

In diesem Abschnitt erklären wir dir alles, was du wissen musst, um eine Klassenerweiterung für modified zu programmieren.

## Konzept

??? note "Textstatus - Skizze"

    Status: 1 von 5 - Skizze: Ideen und Informationen in Stichpunkten unvollständig festgehalten.

Lorem ...

## Aufbau

??? note "Textstatus - Entwurf"

    Status: 2 von 5 - Erster Entwurf: Erste Ausformulierung einiger Informationen. 

Klassenerweiterungen unterscheiden sich nicht von der abstrakten Modul Klasse, die wir im Abschnitt [Modul Klasse (abstract)](/module-class/) beschreiben. Eine jeweilige Klassenerweiterungen kann jedoch weitere Methoden behinhalten, die die jeweilige PHP-Klasse im modified System wie ein Hook-Point erweitert. Lese dir den Abschnitt [Modul Klasse (abstract)](/module-class/) durch, um den Aufbau zu verstehen. In diesem Abschnitt gehen wir auf einige konkrete Aspekte von Klassenerweiterungen ein, die nicht im Abschnitt [Modul Klasse (abstract)](/module-class/) beschrieben werden.

Eine Liste mit allen Modul-Klassenerweiterungen und deren Methoden, die du erweitern kannst, gibt es als Muster-Dateien unter [github.com/RobinTheHood/class-extensions](https://github.com/RobinTheHood/class-extensions)

## Arten von Klassenerweiterungen

??? note "Textstatus - Skizze"

    Status: 1 von 5 - Skizze: Ideen und Informationen in Stichpunkten unvollständig festgehalten.


Mit den Klassenerweiterungen kannst du die folgenden modified Klassen erweitern. Du kannst in der jeweiligen Datei nach den jeweiligen Hookpoints suchen.

| Typ             | Erweitert die Datei(en)                                         | Suche nach Hookpoint           |
|-----------------|-----------------------------------------------------------------|--------------------------------|
| `categories`    | /admin/includes/classes/categories.php                          | `$this->catModules->`          |
| `checkout`      | /includes/classes/payment.php<br>/includes/classes/shipping.php | `$this->checkoutModules->`     |
| `main`          | /includes/classes/main.php                                      | `$this->mainModules->`         |
| `order`         | /includes/classes/order.php                                     | `$this->orderModules->`        |
| `product`       | /includes/classes/product.php                                   | `$this->productModules->`      |
| `shopping_cart` | /includes/classes/shopping_cart.php                             | `$this->shoppingCartModules->` |
| `xtcPrice`      | /includes/classes/xtcPrice.php                                  | `$this->priceModules->`        |


In der folgenden Tabelle erfährst du, wo du deine Klassenerweiterung als Datei ablegen musst, damit diese vom modified System geladen wird.

| Typ             | Verzeichnis | Beispiel Dateiname | Beispiel Klassenname |
|-----------------|-------------|--------------------|----------------------|
| `categories`    | /admin/includes/modules/categories/ | mc_my_first_module.php | mc_my_first_module |
| `checkout`      | /includes/modules/checkout/ | mc_my_first_module.php | mc_my_first_module |
| `main`          | /includes/modules/main/ | mc_my_first_module.php | mc_my_first_module |
| `order`         | /includes/modules/order/ | mc_my_first_module.php | mc_my_first_module |
| `product`       | /includes/modules/product/ | mc_my_first_module.php | mc_my_first_module |
| `shopping_cart` | /includes/modules/shopping_cart/ | mc_my_first_module.php | mc_my_first_module |
| `xtcPrice`      | /includes/modules/xtcPrice/ | mc_my_first_module.php | mc_my_first_module |

Weitere Beispiele findest du auch unter [github.com/RobinTheHood/class-extensions](https://github.com/RobinTheHood/class-extensions).