# Thread-Programmierung #

Thtnet arbeitet, im Gegensatz zu anderen Webservern und Application-Servern,
nicht mit Kinderprozessen sondern mit Threads. Die s.g. Nebenläufigkeit wird
in Tntnet gewünscht und gebraucht um mehrere Anfragen parallel abarbeiten zu
können. Anders als Kinder-Prozesse teilen sich Threads einen gemeinsamen
Speicherbereich. Das sorgt für hohe Effektivität im Umgang mit den Ressourcen.
Mit es aber bei den Zugriffen nicht zu Konflikten kommt,müssen einige
Besonderheiten beachtet werden.

Thread-Programmierung an sich ist nichts Tntnet-Spezifisches. Um das Thema
über das Kapitel hinaus zu vertiefen, findest sich im Internet und im Buchhandel
einiges.

## cxxtools-mutex-Unterstüzung ##

In der cxxtools-Lib von Tntnet gibt es einen mutex-Unterstüzung. mutex wird
gebracht um bestimmte Code-Bereiche zu kennzeichnen, das sie immer nur exklusiv
von einem Thread ausgeführt werden dürfen. Kritisch in der Thread-Programmierung
sind immer Dinge wo Daten gelesen, geschrieben oder gelöscht werden. Zum
einbinden der mutex-Unterstüzung wird der folgende Header benötigt.

 #include <cxxtools/mutex.h>

Der klassische Anwendungsfall für mutex zum Schützte vor unkontrollierten
Zugriffen auf Speicherbereichen, sind static deklarierte Klasseneigenschaffen.
Bei Eigenschaften (Properties) in Klasen die static deklarierte benutzen alle
Objekt-Instanzen den selben Speicher. Wenn das bei paralleler Programmierung dann
tatsächlich gleichzeitig beschied, wird das Programm abstürzen.

Hier ein ein kurzes Beispiel einer Fabrik-Methode. Die Methode getInstance()
erstellt eine Instanz einer Klasse "Impl" und gibt sie als Referenz zurück.

 BibleManager::Impl& BibleManager::Impl::getInstance()
 {
     static Impl impl;
     static bool initialized;
     static cxxtools::Mutex mutex;
     cxxtools::MutexLock lock(mutex);
     if (!initialized)
     {
         impl.initializeBibleBooks();
         initialized = true;
     }
     return impl;
 }

Dabei macht die Funktion zwei interessante Dinge. Zum einen schützt sie mit
dem Aufruf "cxxtools::MutexLock lock(mutex);" die darauf folgenden Aufrufe bis
zum Ende der Funktion vor gleichzeitigen Zugriffen. Zum anderen überprüft sie
mit "if (!initialized)" ob der Aufruf von initialen Funktionen überhaupt noch
nötig sind. Gibt es schon eine Instanz der Klasse wurden die initialen
Funktionen schon aufgerufen.
