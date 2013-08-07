# Konfiguration des Webservice #

In dem Abschnitt wird erklärt welche Optionen  es gibt und wie man sie 
einstellt um das Verhalten der Webapplikation zu steuern. Dabei ist das 
Vorgehen maßgeblich dafon abhängig, ob man den Tntnet-Application-Server
verwendet oder standalone arbeitet.


## Konfiguration des Tntnet-Application-Server ##

Der Tntnet-Application-Server erwartet eine Konfigurationsdatei. Defaultmäßig
wird die Konfiguration unter /etc/tntnet/tntnet.conf gesucht.


## Konfiguration einer standalone Webapplikation ##

Um in einer standalone Webapplikation das Verhalten des Web-Service zu verändern
muss die Hilfsklasse "tnt::Configurator". Diese ist ein Wrapper da das Verhalten
eigentlich über verschiedene Klassen gesteuert wird. 

Dem Constructor der Configurator-Klasse wird die Instanz der Tntnet-Klasse
übergeben...

 #include <tnt/tntconfig.h>

 tnt::Tntnet myApplication;
 tnt::Configurator config(myApplication);
 config.setMaxRequestTime(seconds);

...Um dann weiter mit der Instanz der Configurator-Klasse das Verhalten zu
steuern. Im Folgendem die Möglichen Einstellungen:

 setMinThreads (unsigned n)
    
Setzt das Minimum der Anzahl an gleichzeitigen Threads.
 
 setMaxThreads (unsigned n)

Setzt das Maximum der Anzahl an gleichzeitigen Threads.

 setTimerSleep (unsigned sec)

Die Anzahl Sekunden die vergehen bis wiederholt geprüft wird ob ein 
sessiontimeout erreicht wurde.
 
 setThreadStartDelay (unsigned sec)
    Sets the time in seconds between thread starts. More...

 setQueueSize (unsigned n)

Die maximale Anzahl von Jobs die auf eine Verarbeitung waren dürfen.

 setMaxRequestTime (unsigned sec)
    Sets the maximum request time, after which tntnet is automatically restarted in daemon mode. More...

 setEnableCompression (bool sw=true)

Ein und ausschalten der http-Kompression.

 setSessionTimeout (unsigned sec)

Die Anzahl der Sekunden Inaktivität, nach der die Session abgelaufen und deren 
Daten gelöscht werden.

 setListenBacklog (int n)
    Sets the listen backlog parameter (see also listen(2)). More...

 setListenRetry (int n)
    Sets the number of retries, when a listen retried, when failing. More...

 setMaxUrlMapCache (int n)

Die maximale Anzahl der gecachten urlmappings.

 setMaxRequestSize (size_t s)

Die maximale Größe für ein Request (Client-Anfrage).

 setSocketReadTimeout (unsigned ms)
    Sets the timeout in millisecods after which the request is passed to the poller. More...

 setSocketWriteTimeout (unsigned ms)
    Sets the write timeout in millisecods after which the request is timed out. More...

 setKeepAliveMax (unsigned ms)
    Sets the maximum number of requests handled over a single connection. More...

 setSocketBufferSize (unsigned ms)
    Sets the size of the socket buffer. More...

 setMinCompressSize (unsigned s)
    Sets the minimum size of a request body for compression. More...

 setKeepAliveTimeout (unsigned s)
    Sets the keep alive timeout in milliseconds. More...

 setDefaultContentType (const std::string &s)

Setzt den Default-Typ der ausgelieferten Seite.
 
 setAccessLog (const std::string &accessLog)
 
 setMaxBackgroundTasks (unsigned n)




Es gibt für fast jede Set-Methode auch eine Get-Methode um auslegen zu können
welcher Wert aktuell gesetzt ist.