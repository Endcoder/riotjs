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

* riot.render(template, data)

Die Render Methode nutzt ein vorgebenen HTML-Template, um dieses mit Hilfe der gegebenen
Daten zu rendern. Somit werden Platzhalter in dem Template durch die gewünschten Daten
ersetzt und auf der Oberfläche dargestellt.

```JavaScript
// returns: "<h1>Hello Testuser</h1>"
riot.render("<h1>Hello {username}</h1>", { username: 'Testuser' });

```

## Beispiel: TODO Liste

Unter <a href="http://www.3den.org/riotjs-todomvc/">diesem Link</a> ist eine Beispielanwendung
zu finden, die eine TODO-Liste mit Hilfe von RIOT.JS zeigt.

Im folgenden soll mit Hilfe von relevanten Quelltextausschnitten die Funktionsweise erläutert werden.
Dies geschieht am Beispiel des Hinzufügens einer neuen Aufgabe.

Zunächst wird im View ein neues Template definiert, welches den Aufbau und das Aussehen eines neuen Eintrages
definiert:
```HTML
<template id="task-template">
      <li id="task_{id}" data-task="{id}">
        <div class="view todo-task">
          <input class="toggle" type="checkbox">
          <label>{name}</label>
          <button class="destroy"/>
        </div>
        <input class="edit" value="{name}">
      </li>
</template>
```

Anschließend wird im Model ein Objekt erzeugt, welches der Observermethode übergeben wird, um 
das Nutzen von Events zu ermöglichen. Für das Persistieren der Aufgaben wird der Local Storage
des Browsers genutzt, welcher in diesem Fall über das "DB"-Objekt erreichbar ist.
```JavaScript
function Todo() {
    var self = $.observable(this),
        db = DB('riot-todo'),
        items = db.get();

    self.add = function(name, done) {
        var item = {
          id: generateId(), name: name, done: done
        };

        items[item.id] = item;
        self.trigger('add', item);
    };
	
	// sync database
    self.on('add remove toggle edit', function() {
        db.put(items);
    });

	//  hier können weitere Funktionen definiert werden
}
```

Die Add-Methode generiert ein neues Objekt mit der übergebenen
Aufgabe, und generiert eine eindeutige ID. Anschließend wird
das "Add" Event getriggert und gleichzeitig das erzeugte Objekt
übergeben.
Durch die on-Methode wird auf dieses Event gelauscht und der Eintrag persistiert.

Im folgenden wird der Presenter definiert. Dieser muss zum einen darauf lauschen,
ob in dem Eingabefeld die Entertaste gedrückt wurde, um in diesem Fall die Eingaben
an das Model weiterzuleiten.
Ist dies erledigt, muss der Presenter auf das "add"-Event des Models reagieren
und das Objekt nach Vorgabe des Templates rendern:

```JavaScript
function todoPresenter(element, options) {
    element = $(element);
    var template = options.template,
        todo = options.model,
        $list = element.find('#todo-list'),
        ENTER_KEY = 13,
        ESC_KEY = 27;

    /* Listen to user events */

    element.on('keyup', '#new-todo', function(e) {
        var val = $.trim(this.value);
        if (val && e.which === 13) {
            todo.add(val);
            this.value = '';
        }
	}
	
	todo.on('add', function(item){
        $("#main", element).show();
        var task = $($.render(template, item));
        $list.append(task);
        toggle(task, !!item.done);
    });
```

Zuletzt müssen die einzelnen Komponenten initialisiert werden:

```JavaScript
(function ($) {
    var todo = new Todo();
    routes({todo: todo});

    // Binds the Todo Presenter
    todoPresenter($("#todoapp"), {
        model: todo,
        template: $('#task-template').html(),
    });

    // Binds the Footer Presenter
    footerPresenter($("#footer"), {
        model: todo,
        template: $('#footer-template').html(),
    });
})(jQuery);
```





