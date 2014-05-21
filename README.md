#Tutorial: RIOT.JS

##Einleitung

RIOT.JS ist eine Javascript Bibliothek, die es ermöglicht Webanwendungen
nach dem Model-View-Presenter Konzept (kurz: MVP) zu entwickeln. Dies
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

Der Presenter ist das Bindeglied zwischen Model und View. Er übernimmt die gesamte Steuerung,
also sowohl der Oberfläche, als auch der Logik, und sorgt für Kommunikation zwischen Model
und View. 

##Methoden

Um Webanwendungen nach dem MVP Pattern mit Hilfe von Riot.JS zu entwickeln, werden
lediglich wenige Methoden benötigt. Hier zu zählen:
* riot.observable(element)

Die Observable Methode erlaubt es dem gewählten Objekt die Event-basierte Kommunikation
zu nutzen, d.h. es können Events gefeuert und auf diese gelauscht werden.
```Javascript
function Car() {
 
  // Make Car instances observable
  var self = riot.observable(this);
 
  // listen to "start" event
  self.on("start", function() {
    // engine started
  });
}
 
// make a new Car instance
var car = new Car();
 
// trigger "start" event
car.trigger("start")
```
* riot.trigger(event[, arguments])

Die Trigger Methode erlaubt es einem Objekt Events zu feuern. Hierdurch
kann die Kommunikation zwischen Model und Presenter realisiert werden.
```Javascript
el.trigger("start");
```
* riot.on(event, callback)
 
Die On-Methode bindet eine Funktion an ein Event, um so den Callback zu ermöglichen.
D.h. wenn beispielsweise das Model ein Event feuert, kann der Presenter mit Hilfe
der On-Methode auf dieses reagieren.
```Javascript
// listen to single event
el.on("start", function() {
 
});
 
// listen to multiple events, the event type is given as the argument
el.on("start stop", function(type) {
 
  // type is either "start" or "stop"
 
});
```
