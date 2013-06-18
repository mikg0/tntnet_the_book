
# Installtion #

Zunächst die schlechte Nachricht: Es gibt zur Zeit keinen unterstützten Weg
Tntnet unter Windows zu benutzen. Was aber nicht heißt, das das Projekt es
ablehnen würde einen Windows-Port zu intrigieren. Doch derzeit gibt es keine
Bemühungen dazu.

Nun die gute Nachricht: Die Installationen unter Linux ist sehr einfach.

Wer eine Distribution verwendet die fertige deb- oder rpm-Pakete anbietet, für
den ist die Installation meist in wenigen Minuten erledigt. Wie genau die Pakete
heißen die gebraucht werden und wie sie installiert werden hängt von der
jeweiligen Distribution ab. Hier zu ist die jeweilige Dokumentation und Tools
zu konsultieren.

Für diejenigen deren Distribution keine passenden Pakete mitbringt oder die
nicht aktuell genug sind, folgt nun die Instruktion wie man aus den original
Sourcen installiert.

## Abhängigkeiten ##

Zunächst müssen die Abhängigkeiten erfüllt sein. Will man die Volle
Entwickler-Suite aus Tntnet, cxxtools und ein Tntdb mit vollen Funktionsumfang
installieren braucht man:

* gcc-g++
* automake
* autoconf
* libtool
* postgresql-devel
* zlib-devel
* openssl-devel

Und gegebenenfalls noch:

* MySQL
* PostgreSQL
* SQLite

Die meisten Linux-Distributionen haben hier für passende Pakete.

## Die Sourcen beschaffen ##

Das Projekt ist mit seinen Source Code unter
[https://github.com/maekitalo](https://github.com/maekitalo) zu finden.

Git bietet die Möglichkeit über die GUI ein Zip-File herunter zu laden. Wer
aber regelmäßig seine Installation aktualisieren will um von den neusten
Änderungen zu profitieren, sollte den Code direkt mit Git auschecken.

Zunächst brauchen wir eine Installation von cxxtools. Auf diese C++-Lib setzt
Tntnet und Tntdb auf.

 # Hier zu holen wird uns den Code wie folgt:
 git clone https://github.com/maekitalo/cxxtools.git

 # Wechseln in das neue Verzeichnis
 cd cxxtools

 # Dort rufen wir ein Bash-Script auf:
 ./autogen.sh

 # Nach dem Durchlauf wurden neue Dateien generiert, von denen wir diese aufrufen:
 ./configure

 # Möglicherweise kommt es zu Fehlermeldungen, das Libs oder Programme fehlen.
 # Diese müssen sie nachinstallieren bis der Aufruf fehlerfrei durchläuft.
 # Danach erfolgt die eigentliche Übersetzung der Sourcen mit dem Befehl:
 make

 # Wenn der Befehl erfolgreich durchgelaufen ist, was einige Minuten dauern kann,
 # kann die eigentliche Installation erfolgen. Diese kann nur mit privilegierten
 # Rechten geschehen:
 sudo make install


Nun können Tntnet und Tntdb installiert werden. Die Schritte sind fast identisch
deshalb hier nur noch ein mal in Kurzform:


 git clone https://github.com/maekitalo/tntdb.git
 cd tntdb
 ./autogen.sh
 ./configure
 make
 sudo make install

und

 git clone https://github.com/maekitalo/tntnet.git
 cd tntdb
 ./autogen.sh
 ./configure
 make
 sudo make install

Nun sind alle nötigen Pakete installiert. Da die Software aber am Paket-Manager
vorbei installiert wurde, ist dem System der Pfad zu den Objekt-Dateien noch
nicht bekannt. Das wird geändert durch die Befehle:

 echo "/usr/local/lib/" > /etc/ld.so.conf.d/tntdb.conf
 ldconfig


## updates ##

Wenn man später seine Installation aktualisieren möchte, wechselt in in das
git-Verzeichnis und ruf den Befehl auf:

 git pull

statt clone und wieder holt das bisherige wie gehabt.

