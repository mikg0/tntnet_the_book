Eine MVC-Archeitektur implementieren
====================================

Wie schon im vorangegangenem Kapitel erklärt, präferieren wir für größerer
Projekte das MVC-Konzept. Die Datei-Hierarchie könne wie folgt aussehen.


    /projektname
    /projektname/src
    /projektname/src/main/
    /projektname/src/main/views
    /projektname/src/main/models
    /projektname/src/main/controller
    /projektname/src/main/resources
    /projektname/src/component_1/
    /projektname/src/component_1/views
    /projektname/src/component_1/models
    /projektname/src/component_1/controller
    /projektname/src/component_1/resources
    /projektname/src/component_2/
    /projektname/src/component_2/views
    /projektname/src/component_2/models
    /projektname/src/component_2/controller
    /projektname/src/component_2/resources

Unterhalb von /src sind die Sourcen zu finden. Um das Projekt in logische
Einheiten zu unterteilen, gibt es darunter pro Komponente ein Verzeichnis.
Die Unterteilung soll später helfen, das Entwickler-Teams in verschiedenen
Bereichen arbeiten können ohne sich gegenseitig zu behindern. Zudem soll die
Wiederverwertbarkeit von Komponenten angestrebt werden. Es empfiehlt sich
deshalb die einzelnen Komponenten auch durch Namensräume von einander zu
trennen. Also zum Beispiel:

    ProjectName::ComponentOne::View

Den Namensraum "ProjectName::Main" würden wir für das Kernmodul empfehlen.
