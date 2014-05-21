#Tutorial: RIOT.JS

##Einleitung

RIOT.JS ist eine Javascript Bibliothek, die es ermöglicht Webanwendungen
nach dem Model-View-Presenter Konzept(kurz: MVP) zu entwickeln. Dies
erlaubt es große Anwendungen modular aufzubauen, und so die Übersichtlichkeit
zu bewahren.

RIOT.JS zeichnet sich vor allem durch seine Größe aus, denn der minimierte
Quelltext ist lediglich 1.6kb groß. Die folgende Übersicht zeigt, wie klein
diese Bibliothek im Vergleich zu anderen wirklich ist:

<img src="https://muut.com/m/img/riotjs/riot-size-1.6kb.png" />


Des Weiteren basiert RIOT.JS auf Vanilla Javascript, also purem
Javascript, wodurch eine Abhängigkeit zu anderen Frameworks ausgeschlossen wird.

Bestandteil von RIOT.JS ist eine Template Engine, die es erlaubt HTML Templates
zu bauen und zu rendern, eine Event Libraray, die die Kommunikation und Callbacks
ermöglicht, und eine Routerfunktion, um die Navigation zu ermöglichen.

##MVP

Das MVP-Pattern ist dem Model-View-Controller Pattern sehr ähnlich,
unterscheidet sich jedoch darin, dass es statt einem Controller einen Presenter
gibt. Im MVC Pattern konnten die einzelnen Komponen direkt miteinander interagieren,
im MVP Pattern ist dies nicht möglich.

Hier übernimmt der Presenter die Kommunikation zwischen View und Model in beide
Richtungen. Hieraus resultiert eine strikte Trennung von View und Model, wodurch
das Testen des Views mit Hilfe von Unit Tests ermöglicht wird.

* Model

Das Model kennt weder den Presenter, noch den View und modelliert die Geschäftslogik.
D.h. hier ist die Struktur der Daten modelliert, und die grundlegende Logik implementiert.

* View

Im View wird lediglich die Oberfläche dargestellt, und es ist lediglich für die Darstellung
der Ein- und Ausgaben zuständig. Die Steuerung des Views erfolgt ausschließlich
vom Presenter, in dem dieser auf Events des Views lauscht.

* Presenter

