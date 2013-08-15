# Icon in Browser hinzufügen #

Wenn man ein selbst gestaltetes kleine Icon im Browser-Tab angezeigten lassen
möchte muss man ein favicon an den Browser ausliefern. Dazu braucht es drei
schritte in Intnet:

* Das Icon zum Build-Prozess hinzufügen
* Eine Route für das Icon anlegen
* Und das Icon in den View intrigieren


## Das Icon zum Build-Prozess hinzufügen ##

Hier ein Auszug aus der Automake-Datei ("makefile.am"). Unser Icon trägt den 
Namen "favicon.ico".

    AUTOMAKE_OPTIONS = subdir-objects
    bin_PROGRAMS = peruschim_cpp
    AM_CPPFLAGS = -I$(srcdir)

    # [...]

    staticSources = \
        resources/favicon.ico \
        resources/peruschim.css \
        resources/small_screens.css \
        resources/feed-icon.png

    # [...]

    nodist_peruschim_cpp_SOURCES = \
        resources.cpp


    CLEANFILES = $(ecppSources:.ecpp=.cpp) $(ecppSources:.ecpp=.deps) resources.cpp

    #
    # Rules for tntnet applications
    #
    resources.cpp: $(staticSources) Makefile.am
        $(ECPPC) -bb -z -n resources -p -o resources.cpp -I$(srcdir) $(staticSources)

    SUFFIXES=.ecpp .ico .cpp

    .ecpp.cpp:
        $(ECPPC) -I $(top_srcdir)/src -o $@ $<
    .ecpp.deps:
        $(ECPPC) -M -n $* -I $(srcdir) -I $(srcdir)/include $< | $(SED) '1s/\(.*\).cpp:/\1.cpp \1.deps:/' > $@
    .ico.cpp:
        $(ECPPC) -b -m image/x-icon -o $@ $<

    -include $(ecppSources:.ecpp=.deps)



        app.mapUrl("^/feed-icon.png$", "resources")
           .setPathInfo("resources/feed-icon.png");

In staticSources listen wir alle Sourcen auf die wir in das Projekt einbinden 
und später verwenden wollen. Unter anderem unser Icon ("resources/favicon.ico").
In CLEANFILES wird angezeigt, das beim Aufräumen des Verzeichnis (bei dem Aufruf
des Befehls "make clean") die generierte C++-Datei mit unserem Icon gelöscht
werden soll.

In dem Abschnitt der mit der Zeile "resources.cpp:" beginnt generiert der 
Tntnet-Pre-Compiler "ecppc" aus den Binärdateien eine C++-Code-Datei, die
"resources.cpp". Diese wird dann ganz normal mit dem C++-Compiler 
weiterverarbeitet.


## Eine Route für das Icon anlegen ##

Mit die Icon-Datei dann zur Laufzeit an einen bestimmen (virtuellen) Ort zur
Verfügung steht und in den HTML-Code eingebettet werden kann, brauch es noch
eine Route. Das Tut man in aller Regel an der Stelle im Code, wo der 
Application-Server initialisiert wird. Das könnte dann (in Auszügen) so 
aussehen...


    #include <tnt/tntnet.h>

[...]

    int main ( int argc, char* argv[] )
    {

        tnt::Tntnet app;

[...]

        app.mapUrl("^/favicon.ico$", "resources")
            .setPathInfo("resources/favicon.ico");


Die Funktion "mapUrl()" fügt eine neue Route hinzu. Der erste Wehrt ist ein regulärer
Ausdruck. Ist dieser "wahr" wird die Anfrage an die Komponente "resources" 
weitergereicht. "wahr" heißt in unseren Fall, die aufgerufene URL muss exakt 
"/favicon.ico" lauten. Davor und da hinter darf nichts stehen. Die URL
"meineWebsite.de/favicon.ico" wäre und würde weitergeleitet. Die URL 
"meineWebsite.de/bilde/favicon.ico" wäre falsch und würde nicht weitergeleitet.

Wenn die Anfrage an die Komponente "resources" weitergeleitet wurde, wird Darin
nach dem Bild/Icon favicon.ico und wenn vorhanden, auch ausgeliefert von der 
Komponente. Der Funktionsaufruf ".setPathInfo()" ist optional. Darüber was er
tatsächlich tut habe ich bisher keine Doku gefunden. Offenbar scheint es
ein Alias zu sein unter der man die Resource auch erreicht.

## Das Icon in den View intrigieren ## 

Um das Icon jetzt in die Website einzubinden gibt es zwei Wege:


* Hinterlegung unter dem festen Namen „favicon.ico“ im Basisverzeichnis der 
Domain, wie zum Beispiel bei //de.wikipedia.org/favicon.ico. 

* Referenzierung über ein HTML-Element, das in die Kopfdaten (<head>) einer 
HTML-Seite eingebunden wird.

Im ersteren Fall haben wir nichts mehr zu tun, da wir das Icon bereits über die 
richtige URL anbieten. Dieses Verfahren ist aber nicht bei allen Browsern 
gewährleistet. Also ist es die sichere Methode die zweite. Hier zu ist im Header
die folgende Zeile zu ergänzen:

    <link rel="icon" href="http://example.com/favicon.ico" type="image/x-icon">

Die zweite Methode erlaubt auch, das dass Icon anders heißt oder unter einem
anderem Pfad zu finden ist. Möglicherweise will man auch mit unterschiedlichen
Icons arbeiten. So zum Beispiel, wenn man der User für dein Smartphon in eine
alternative Ansicht wechselt.

