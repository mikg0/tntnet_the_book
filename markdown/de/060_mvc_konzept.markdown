# Eine MVC-Archeitektur implementieren #

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


## Verknüpfung von View, Controll und Model ##


### Contoller ###


Mit qparam.arg<TYPE>(KEYWORD) wird ein Argument ausgelesen. TYPE ist der
Variablen Type den man zurück bekommen möchte. KEYWORD ist der Bezeichner
mit dem der Wert übergeben wird. 

    // URL arguments
    std::string arg_login_name =
        qparam.arg<std::string>("arg_login_name");


Möchte man eine Liste zurückbekommen.
Z.B. Listen in denen Mehrfachauswahl erlaubt ist muss eine andere Funktion
genutzt werden. Die Funktion heißt "args" statt "arg" und gibt ein Vector von
Typen zurück den man angibt:

    std::vector<std::string>  args_userroles =
        qparam.args<std::string>("args_userroles");

Um nicht durcheinander zu kommen mit Argumenten und shared Variablen kann es
hilfreich sein, sich auf die Konvention zu einigen, das Argumente mit den 
Präfix "arg_" beginnen. Die Namen in den HTML-Formularen muss natürlich der
gleichen Konvention folgen.

    <p>
        <label for="login_name">Login*: </label>
        <br>
        <input
            class="full-size"
            name="arg_login_name"
            type="text"
            value="<$ accountData.getLogin_name() $>"
            maxlength="80">
    </p>

Um Werte an den View zu übergeben nutzt man shared Opjekte und Variablen. 
Diese müssen mit einem Macro registriert und initialisiert werden.

     // shared variables
    TNT_SESSION_GLOBAL_VAR( UserSession, sh_userSession, ());

Der erste Parameter ist der Typ; der zweite Name und der Dritte ist
der aufzurufende Constructor. Wenn dieser einen Parameter braucht, kann diese
hier angegeben werden. Es empfehlt sich der Übersicht halber die 
Namenskonvention zu verwenden die shared Variablen ein "sh_" als Präfix 
voranstellen.


### View ###

Mit die shared Variablen des Controllers dem View auch zur Verfügung
stehen, müssen dies der View-Umgebung bekannt gemacht werden. Das beschied
auf die volgende Weise:

    <%session
        scope="global"
        include="models/UserSession.h">
            UserSession g_userSession;
            std::vector<std::string> sh_allRolls;
    </%session>


Mit dem scope-Wert "global" wird angezeigt das es sich um shared Variablen
handelt. Mit "include" können benötigte Header-Dateien eingebunden werden. In
diesem Fall die Klasse "UserSession" die wir brauchen mit der Type UserSession
bekannt ist. Zwischen den Tags werden dann die eigentlichen Variablen aufgelistet
bzw. bekannt gemacht.


### Routing ###

Mit das View und der Controller tatächlich gemeinsam eine Anfrage bearbeiten
müssen sie noch mit einer gemeinsamen Route verknüpft werden.

        app.mapUrl( "^/(.*)$", "$1Controller" );
        app.mapUrl( "^/(.*)$", "$1View" );

Diese Regel sagt aus, das jede URL ein mal um "Controller" und einmal um "View"
ergänzt werden, und damit zuerst der Conntroller und dann der View aufgerufen 
wird.