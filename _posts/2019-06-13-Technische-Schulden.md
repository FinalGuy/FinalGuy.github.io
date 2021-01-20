---
title: "Technische Schulden" 
date: "2019-06-13"
excerpt: "Entwickler*innen und POs haben oft unterschiedliche Vorstellungen von technischen Schulden."
header:
  overlay_image: "/assets/images/technische-schulden.jpg"
---

Der Begriff der technischen Schulden fällt häufiger, wenn sich Entwickelnde untereinander oder mit ihren Product Ownern
unterhalten. Oftmals haben die Entwickelnden eines Teams jedoch unterschiedliche Vorstellungen im Kopf, wenn sie von
technischen Schulden sprechen. Missverständnisse sind vorprogrammiert.

Motiviert durch eine kleine Twitter-Diskussion zwischen meinen Kollegen Artur Tomas, Dennis Reimann und mir zu diesem
Thema möchte ich noch etwas Öl ins Feuer gießen und in diesem kurzen Text meine ganz persönliche Vorstellung davon zum
Besten geben, was technische Schulden sind und was nicht. Ich freue mich schon auf die Diskussion. ;)
Wie entstehen technische Schulden?

Die Aufnahme technischer Schulden ist stets eine bewusste Entscheidung des gesamten Teams, insbesondere inklusive des
Product Owners. Die Zielsetzung ist, schnellstmöglich ein Produkt oder ein Feature auf den Markt zu bekommen, um sich
entweder einen Wettbewerbsvorteil zu sichern oder schlicht, um am Markt zu bestehen. In unserem schnelllebigen
Online-Zeitalter können schon wenige Wochen einen signifikanten Unterschied zwischen Erfolg und Misserfolg ausmachen.

Das Tempo beim Ausliefern dieses Produkts oder Features wird durch einen Verzicht auf handwerkliche Tugenden bei der
Entwicklung erkauft. So wird etwa auf das Schreiben ausführlicher Tests, das Refaktorisieren schwer verständlichen Codes
oder die Durchführung von Peer Reviews verzichtet.

Diese vom Team wohl wissend in Kauf genommenen und daher zwingend im Backlog verzeichneten Defizite stellen die
technischen Schulden des Projekts dar. Sie müssen zu einem späteren Zeitpunkt vom Team abgezahlt werden, wenn das
Projekt nicht dauerhaft durch die verrottete Codebasis ausgebremst werden soll. Das Abzahlen der Schulden erfolgt in
Sprints, die keine oder kaum neue Features hervorbringen, sondern lediglich dafür sorgen, Defizite zu beseitigen. Was
sind keine technischen Schulden?

Im Gegensatz zu dem oben beschriebenen Prozess stellen handwerkliche Defizite, die ohne Übereinkunft des Teams
entstehen, keine technischen Schulden dar. So schreiben beispielsweise unerfahrene Entwickelnde oft unbeabsichtigt
unverständlichen Code. Fehlt es dann im Team noch an geeigneten Reviewprozessen, schleichen sich solche Defizite
unbemerkt in die Codebasis ein. Aber auch erfahrene Entwickelnde schreiben mitunter, vermeintlich aus Zeitdruck,
Produktionscode ohne Tests und machen ihren frischen Code damit direkt zu schwer wartbarem legacy code.

Es ist die Aufgabe des gesamten Entwicklungsteams, suboptimale Stellen in der Codebasis zu identifizieren und diese
Defizite zu beheben. Reparaturen erfolgen in diesem Fall nebenbei im regulären Alltagsgeschäft und bedürfen keiner
separaten Tickets. Es gehört zu den Aufgaben eines professionellen Entwickelnden, die Pflege der Codebasis im
Tagesgeschäft zu leben.

## Fazit

Gründe für handwerkliche Defizite in der Codebasis gibt es also genug. Wichtig ist die Abgrenzung zu technischen
Schulden. Diese entstehen nicht einfach, sie werden bewusst aufgenommen.