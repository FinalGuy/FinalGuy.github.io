---
#layout: "posts"
locale: "de-DE"
title: "Die Kraft der objektorientierten Programmierung"
excerpt: "Modellierung fachlicher Konzepte als Klassen ist der Antrieb der objektorientierten Programmierung."
date: "2020-05-24 22:35:48 +0100"
last_modified_at: "2020-01-17"
categories: programming
header:
  overlay_color: "#5e616c"
  overlay_image: /assets/images/solar-flare.jpg
tags: programming ddd java oop
---

Java gilt als objektorientierte Programmiersprache – dies ist hinlänglich bekannt. Das Beherrschen der Syntax allein
geht jedoch nicht automatisch mit einem Verständnis der zugrundeliegenden Paradigmen einher. Dass durch die bloße Wahl
von Java als Programmiersprache, automatisch auch objektorientierter Code entsteht, ist ein Irrtum.

## Ein Paradigma

Bei der objektorientierten Programmierung (OOP) handelt es sich um ein Programmierparadigma. Fragt
man [Wikipedia](https://de.wikipedia.org/wiki/Programmierparadigma) so ist OOP nur ein Paradigma unter vielen aus dem
Bereich der imperativen Programmierung. Etwas kompakter dagegen ist Robert „Uncle Bob“
Martins [Sicht auf die Programmierparadigmen](https://blog.cleancoder.com/uncle-bob/2012/12/19/Three-Paradigms.html). Er
sieht OOP als eines von insgesamt drei fundamentalen Paradigmen:

1. objektorientierte Programmierung
2. funktionale Programmierung
3. strukturierte Programmierung

Für ihn definiert sich ein Paradigma vor allem durch das Beschränken der Möglichkeiten, die der Entwicklerin bei der
Programmierung zur Verfügung stehen. Im Falle der OOP wird die Möglichkeit entfernt beliebige Funktionen aufzurufen, da
Funktionen in Form von Methoden in der Idee der OOP im Kontext von Objekten existieren und entsprechend verwendet werden
müssen. Objektorientierte Programmiersprachen bieten in der Regel viele weitere Features wie beispielsweise Polymorphie
und Vererbung. Diese Techniken sowie deren sachgemäße Verwendung werden aufgrund des engen Rahmens jedoch nicht
behandelt. Es soll im Folgenden vielmehr um das Konzept der Bündelung von Daten sowie Operationen auf diesen Daten
gehen, also um die Idee des Objektes. Im Kontrast dazu nun zunächst ein Beispiel für nicht objektorientierten Code in
Java.

```java
class Application {
    public static void main(String[] arguments) {
        List<String> lines = readLinesFromFile();
        lines = sortLinesByFirstCharacter(lines);
        print(lines);
    }
}
```

## Prozeduraler Code in Java

Dass man in Java leicht Code schreiben kann, der nichts mit OOP zu tun hat, zeigt obier Code. Man erkennt auf den ersten
Blick, unter anderem am fehlenden Schlüsselwort **new**, dass OOP hier keine Rolle spielt. Es werden ausschließlich
statische Hilfsmethoden ohne einen umgebenden Objektkontext aufgerufen. So handelt es sich zwar um imperativen, nicht
aber um objektorientierten Code. Obiges Beispiel ist bewusst darauf getrimmt, rein imperativen Code zu zeigen. Bei der
täglichen Arbeit an echtem Produktivcode fällt es etwas schwerer zu erkennen, wo Chancen für objektorientierte
Modellierung verpasst wurden. Welche Vorteile eine objektorientierte Modellierung mit sich bringt und welche
potenziellen Gefahren der Verzicht von OOP birgt, soll nun näher betrachtet werden.

## Geschäftslogik außerhalb des Objektes

Hier ein weiteres, weniger offensichtliches, Beispiel für nicht konsequent objektorientierten Code, der aus einem
regulären Softwareprojekt stammen könnte:

```java
class SomeService {
    public void addItemToCart(Item item, CartId cartId) {
        Cart cart = loadCart(cartId);
        if (cart.total() + item.price() > 500.00f) {
            throw new CartLimitExceeded();
        }
        cart.addItem(item);
    }
}
```

Es geht um einen Service, der einen Artikel zu einem Warenkorb hinzufügt. Obiger Code enthält viel Potenzial für
Verbesserungen, es kommt aber zunächst auf die enthaltene Geschäftslogik an: Offenbar ist es in diesem System wichtig,
dass der Gesamtwert eines Warenkorbs 500 Währungseinheiten nicht übersteigen darf. Konsequenterweise findet sich diese
Regel auch im Code wieder. Worin besteht also die Kritik? Das Problem an der gezeigten Implementierung besteht darin,
dass diese Geschäftsregel außerhalb des Objektes Cart implementiert ist. Verwendet eine Kollegin nicht diesen
CartService zum Hinzufügen des Artikels, sondern beispielsweise direkt den Cart, umgeht sie ungewollt die obige Regel
und könnte so größere Warenkörbe erstellen als vorgesehen.

## Geschäftslogik in das Objekt verschieben

Die Lösung des Problems besteht darin, diese Logik in das Objekt Cart zu verlagern. Durch diese Maßnahme ist
gewährleistet, dass die Logik an der richtigen Stelle steht und der Warenkorb stets in sich konsistent bleibt.

```java
class SomeService {
    public void addItemToCart(Item item, Cart cart) {
        Cart cart = loadCart(cartId);
        cart.addItem(item);
    }
}

public class Cart {
    private static final Float LIMIT = 500.00f;

    private List<Item> items = new ArrayList<>();

    public void addItem(Item item) {
        if (this.total() + item.price() > LIMIT) {
            throw new CartLimitExceeded();
        }
        items.add(item);
    }
} 
```

Mit dem Umbau gehen ganz automatisch weitere Änderungen einher, die der Idee der OOP entgegenkommen. So ist in der neuen
Variante zum einen das Warenkorblimit nun Bestandteil der richtigen Klasse Cart während sie zuvor war außerhalb
definiert wurde. Zum anderen muss der Warenkorb, zumindest zu diesem Zweck, nun keine Methode zum Abfragen seines
aktuellen Gesamtwerts mehr bereitstellen und kann so seine Interna für sich behalten. Beide Veränderungen sorgen dafür,
dass Logik in die Objekte hinein wandert, statt außerhalb implementiert zu werden. Dieses Grundprinzip der OOP ist auch
unter dem Namen [*Tell, don‘t ask*](https://pragprog.com/articles/tell-dont-ask) bekannt und besagt, dass man Objekten
per Methodenaufruf sagen soll, was sie tun sollen, statt sie nach ihren Daten zu fragen, um es selbst zu tun:

> „Procedural code gets information then makes decisions. Object-oriented code tells objects to do things.“ — Alec Sharp

Von allen Prinzipien zum Erstellen von gut wartbarem objektorientierten Code ist dieses Prinzip sicher eines der
wichtigsten. Werden, bei vorgeblich objektorientiertem Code, Prinzipien der OOP konsequent nicht beachtet, spricht man
in der Softwareentwicklung von sogenannten Anti-Patterns. Das Anti-Pattern für die Nichtbeachtung des Tell, don‘t ask
Prinzips wird als [*anämische Datenmodell*](https://www.martinfowler.com/bliki/AnemicDomainModel.html) bezeichnet. In
einem anämischen Datenmodell besteht ein Großteil der Klassen lediglich aus Feldern sowie deren "settern" und "gettern".
Sämtliche Logik ist hingegen außerhalb der Klassen implementiert. Diese Klassen stellen also reine Datenkonstrukte dar
und verdienen es nicht als Objekte im Sinne der OOP bezeichnet zu werden. Beim Antreffen eines solchen anämischen
Datenmodells in einem Projekt ist höchste Vorsicht geboten. Bevor ein Beispiel diese Gefahr erläutert, soll aber
zunächst der Begriff des Value Objects eingeführt werden.

# Werte als Objekte: Value Objects

Zurück zum Code rund um den Warenkorb: Nicht nur Dinge wie der Warenkorb, die einem Lebenszyklus in Form von
Zustandsänderungen unterliegen, können und sollten als Objekte modelliert werden. Ebenso gewinnen Eigenschaften von
Objekten an Bedeutung und Sicherheit, wenn sie als Objekte modelliert werden. In obigem Beispiel könnten etwa die
Währungsbeträge als Objekte mit eigenen, sprechenden Methoden dargestellt werden. Diese Art von Objekten wird auch Value
Object genannt und erfreut sich, nicht zuletzt dank der Renaissance des [*domain-driven
design*](https://www.goodreads.com/book/show/179133.Domain_Driven_Design), immer stärkerer Beliebtheit.

```java
public class Cart {
    private static final Money LIMIT = new Money(500, 0, EURO);

    public void addItem(Item item) {
        if (this.total().plus(item.price()).exceeds(LIMIT)) {
            throw new CartLimitExceeded();
        }
        items.add(item);
    }
}
```

Welcher Vorteil ergibt sich daraus? Nun, speziell über das Thema Währungsbeträge und deren Modellierung mit primitiven
Typen wurde, unter anderem in dem
Klassiker [Effective Java](https://www.goodreads.com/book/show/34927404-effective-java) von Joschua Bloch, schon viel
geschrieben. Im Kern bietet die Modellierung mit Value Objects vor allem folgende Vorteile:

1. die Absicherung der Wertebereiche
2. die Kontrolle über verfügbare Operationen
3. ein zentraler Ort für Geschäftslogik, die sich auf den Wert bezieht

In obigem Kontext sind negative Zahlen für Preise nicht sehr sinnvoll. Dies kann in einer konkreten Implementierung
direkt im Konstruktor sichergestellt werden, sodass es nicht mehr möglich ist, in diesem Sinne ungültige Währungsbeträge
zu erstellen. Als Entwicklerin sollte man sich vor der Verwendung von primitiven Typen stets die Frage stellen: Stimmt
der fachliche Wertebereich mit dem technischen Wertebereich des verwendeten Typs überein? Ist dies nicht der Fall, ist
eine Modellierung des Wertes als Value Object sinnvoll. Des Weiteren sind insbesondere die Addition und Subtraktion von
Fließkommazahlen nie exakt. Das in Listing 4 gezeigte Value Object Money verlangt deshalb separate, ganzzahlige Werte
für den Vor- und den Nachkommateil des Preises und definiert eine eigene Methode für die Addition, um sicherzustellen,
dass die Operation stets exakt funktioniert. Glücklicherweise steht dem JDK
dank [JSR 354](https://jcp.org/en/jsr/detail?id=354) mittlerweile ein Modul zur Verfügung, das keine Wünsche im Umgang
mit Währungsbeträgen offen lässt. Probleme bei der Modellierung von Währungsbeträgen gehören damit der Vergangenheit an.
Beim dritten Vorteil der Value Objects soll es, wie versprochen, noch einmal um die Gefahren des anämischen Datenmodells
gehen. Angenommen in einem System existieren zwei sogenannte Mandanten: Deutschland und Österreich. Beide haben
unterschiedliche Arten von Bestellnummern, die durch ein magisches Kürzel am Ende der Bestellnummer („-23“ für
Deutschland und „-42“ für Österreich)
gekennzeichnet sind. Das System muss an verschiedenen Stellen zwischen beiden Version unterscheiden, so beispielsweise
beim Übergeben der Bestellung an die Logistik:

```java
class SomeService {
    public void processOrder(Order order) {
        String orderId = order.id();
        if (orderId.endsWith("- 23")) {
            germanLogisitcs.process(order);
        } else {
            austrianLogistics.process(order);
        }
    }
}
```

In obigem Beispiel ist erneut Geschäftslogik aus dem Modell _heraus gesickert_ und wird stattdessen vom Benutzer der
Klasse implementiert. Das Wissen zur Berechnung der Mandanten befindet sich nun an jeder Stelle im System, die
dieseUnterscheidung benötigt. Eventuell geschieht dies sogar in leicht abgewandelter Form, je nachdem wer es
implementiert hat, was das Auffinden und Erkennen dieser Stellen zusätzlich erschwert. Was passiert nun, wenn sich die
Kürzel ändern oder die Schweiz als weiterer Mandant mit eigenem Kürzel eingeführt wird? Es muss jede dieser Stellen
gefunden und korrekt angepasst werden. Ein solches Vorgehen ist nicht nur sehr aufwändig, sondern gleichzeitig auch
fehleranfällig. Modelliert man hingegen die Bestellnummer als Value Object, so wird die Logik an genau einer Stelle
implementiert, nämlich der Bestellnummer selbst.

```java
class SomeService {
    public void transferOrder(Order order) {
        OrderId orderId = order.id();
        if (orderId.isGermanOrder()) {
            germanLogisitcs.process(order);
        } else {
            austrianLogistics.process(order);
        }
    }
}
```

# Modellierung abstrakter, fachlicher Konzepte als Objekt

Bei der Modellierung eines Geschäftsfeldes durch Klassen sollte man sich, gerade mit Blick auf das Tell, don‘t ask
Prinzip, nicht darauf beschränken ausschließlich greifbare Subjekte der Domäne, wie beispielsweise den Kunden, den
Warenkorb oder den Artikel zu modellieren. Das Modell gewinnt durch die Modellierung abstrakter fachlicher Konzepte, wie
etwa spezieller Kennzahlen an Bedeutung. So könnte der durchschnittliche Warenwert der Warenkörbe pro Monat eine
wichtige Kennzahl für ein Unternehmen sein. Schauen wir, wie dies aussehen könnte:

```java
class SomeService {
    public void updateAverageCartValueForCurrentMonth() {
        Orders orders = ordersCurrentMonth();
        AverageCartValue average = new AverageCartValue(orders);
        monitoring.updateAverageCartValueKPI(average.toMoney());
    }
}

public class AverageCartValue() {
    public AverageCartValue(Orders orders) {
        this.orders = orders;
    }

    public Money toMoney() {
        Money sum = new Money(0, 0, EUR);
        orders.forEach(order -> sum = order.addTotalTo(sum));
        return sum.divideBy(orders.size());
    }
}
```

Einmal für diesen Gedanken sensibilisiert, lassen sich in fast jeder Domäne weitere Konzepte identifizieren, die einer
separaten Modellierung würdig sind. Weitere Inspiration sowie einige streitbare Thesen zur Implementierung von Klassen
finden sich in dem empfehlenswerten
Erstlingswerk [Elegant Objects](https://www.goodreads.com/book/show/29326350-elegant-objects) von Yegor Bugayenko.

## Fazit

Die Java-Syntax und die Klassen des JDK zu kennen sind eine solide Basis für eine Tätigkeit als Softwareentwicklerin.
Will man das Handwerk der Softwareentwicklung professionell betreiben, und dabei auf OOP setzen, gibt es Konzepte hinter
dem Code, die man sich aneignen sollte. Neben den Referenzen, die schon im Verlauf des Artikels erwähnt wurden, ist ein
Verständnis der [SOLID-Prinzipien](https://en.wikipedia.org/wiki/SOLID) ein guter Schritt. Ebenso gibt es Standardwerke,
die es gelesen zu haben gilt. Zu diesen zählen meiner Meinung nach
mindestens [Clean Code](https://www.goodreads.com/book/show/3735293-clean-code),
das [GOOS-Buch](https://www.goodreads.com/book/show/4268826-growing-object-oriented-software-guided-by-tests)
sowie das [POODR-Buch](https://www.goodreads.com/book/show/13507787-practical-object-oriented-design-in-ruby). Diese
Bücher eigenen sich auch hervorragend für eine kooperative Lektüre, beispielsweise in Form einer Leserunde unter
Kollegen und Kolleginnen. Um das neu erworbene Wissen zu verstetigen, haben sich die Ideen des gemeinsamen CodeReviews
als Termin mit dem gesamten Team sowie PeerReviews (falls das Team mit merge requests arbeitet) als hilfreich erwiesen.
Abschließend noch ein Hinweis zur Einordnung der in diesem Artikel behandelten Themen:
Der Rahmen eines Artikels ermöglicht natürlich keine erschöpfende Behandlung aller Themen aus dem Themenkomplex der OOP.
Des Weiteren bleiben Themen unerwähnt, die auf den höheren Abstraktionsebenen liegen oder einen gänzlich eigenen
Themenkomplex darstellen:

1. Organisation von Klassen innerhalb einer Anwendung
2. Organisation von Anwendungen innerhalb einer System-Infrastruktur
3. Modellierung des Geschäftsfeldes durch domain-driven design

Der Artikel vermittelt einen ersten Eindruck, wie viel es über objektorientierte Programmierung, abseits von Syntax und
Sprachfeatures, zu lernen gibt. Bei Fragen oder Anmerkungen kontaktiert mich gern. :hugs:
