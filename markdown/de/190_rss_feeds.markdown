# Wie man ein RSS feed implementieren kann #

RSS-Feeds sind eine praktische Sache. Mit Hilfe von RSS-Feeds können auf
bequeme Art und Weise Seitenänderungen von Benutzer beobachtet werden.
Entstanden sind Feed im Blogger-Umfeld. Aber nicht nur bei Blogs machen Feeds
Sinn, sondern auf allen Seiten wo sich Inhalte regelmäßig ändern und die
Benutzer davon erfahren sollten.

## Aufbau ##

Es gibt grundsätzlich zwei Arten von Feeds RSS und Atom. RSS ist etwas simpler
aufgebaut und soll deshalb hier dargestellt werden. Der Feed ist eine XML-Datei
die von dem Webserver in unserem Fall von Tntnet ausgeliefert wird. Das Gerüst
sieht so aus:

    <?xml version="1.0" encoding="UTF-8" ?>
    <rss version="2.0" >
        <channel>
            <title>Peruschim RSS-Feed</title>
            <link>http://peruschim.de</link>
            <description>
                Mit Peruschim kannst du gemeinsam mit anderen deine Bibelstudien machen.
            </description>
            <language>de-de</language>
            <copyright>Creative Commons Attribution-ShareAlike 3.0 Germany License.</copyright>
            <pubDate>Mon, 06 Sep 2009 16:20:00 +0000</pubDate>


            <item>
                <title>Eine erste Nachricht</title>
                <link>http://peruschim/erste_nachricht</link>
                <description>Hallo an Alle! Es gibt jetzt RSS Feed...</description>
                <guid>http://peruschim.de/id_1</guid>
                <pubDate>Mon, 06 Sep 2009 16:20:00 +0000</pubDate>
            </item>
        </channel>
    </rss>

Hier ist erst mal eine Sache sehr wichtig, die dem Beispiel nicht anzusehen
ist: Eine XML-Datei darf nicht mit einer leeren Zeile beginnen! Eine valide
XML-Datei muss mit mit dem <?xml-Tag beginnen! Das ist sehr wichtig wenn wird
die Tntnet-Tampate-Dateien (*.ecpp) verwenden. Die könnte dann so aussehen:

    <#
    Copyright (C) 2013  Olaf Radicke <briefkasten@olaf-rdicke.de>

    This program is free software: you can redistribute it and/or modify
    it under the terms of the GNU Affero General Public License as published by
    the Free Software Foundation, either version 3 of the License, or later
    version.

    This program is distributed in the hope that it will be useful,
    but WITHOUT ANY WARRANTY; without even the implied warranty of
    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
    GNU Affero General Public License for more details.

    You should have received a copy of the GNU Affero General Public License
    along with this program.  If not, see <http://www.gnu.org/licenses/>.
    #><%pre>
        #include <vector>
        #include <models/Config.h>
        #include <manager/RSSfeedManager.h>
        #include <models/RSSfeed.h>
        #include <time.h>
    </%pre><?xml version="1.0" encoding="UTF-8" ?>
    <rss version="2.0" >
        <channel>
    % RSSfeedManager feedManager;
            <title>Peruschim RSS-Feed</title>
            <link>http://<$ Config::it().domainName() $></link>
            <description>
                Mit Peruschim kannst du gemeinsam mit anderen deine Bibelstudien machen.
            </description>

            <language>de-de</language>
            <copyright>
                This work is licensed under a Creative Commons
                Attribution-ShareAlike 3.0 Germany License.
            </copyright>
            <pubDate><$ feedManager.getLastUpdate() $></pubDate>

    % std::vector<RSSfeed> feedList = feedManager.getFeeds( 3 );
    % for ( unsigned int i=0; i < feedList.size(); i++) {
            <item>
                <title><$ feedList[i].getTitle() $></title>
                <link>http://<$ Config::it().domainName() $>/<$ feedList[i].getLinkURL()  $></link>
                <description><$ feedList[i].getDescription()  $></description>
                <guid>http://<$ Config::it().domainName() $>/home/<$ feedList[i].getID() $></guid>
                <pubDate><$ feedManager.covertDate( feedList[i].getCreateTime() ) $></pubDate>
            </item>
    % }


        </channel>
    </rss>

Es wäre naheliegend  vor dem <%pre>-Tag einen Zeilenumbruch machen zu wollen.
Allerdings würde dadurch das XML-File mit einer leeren Zeile beginnen und wäre
damit dann nicht mehr valide.

Nun zu der Bedeutung der einzelnen Tags:

## <channel> ##

Um Schießt einen channel. Man kann auf seiner Seite durch aus mehrere channels
(Nachrichtenkanäle) haben. Nicht jeder Benutzer interessiert sich für allen
Nachrichten. So kann man Usern zu verschiedenen Themen unterschiedliche channels
anbieten.

## <pubDate> ##

Das ist das Datum an dem der Feed das letzte mal aktualisiert wurde.

## <item> ##

Umschließt einen News-Eintrag.

## <guid> ##

Ein Link zu einem Permanent Link über den eine News eindeutigen zugeordnet
werden kann.

## Datumsformat konvertieren ##

Eine Herausforderung der man sich gegenübersieht bei RSS Feeds ist das
Datumsformat. Es wird die Spezifikation von RFC 822 verwendet. Ein Format
was nicht so leicht automatisch zu generieren ist.

* Wochentag (engl. dreistellig)
* Komma
* Tag mit führender Null
* Monatsname (engl. dreistellig)
* Vierstelliges Jahr
* Stunde mit führender Null
* Doppelpunkt
* Minute
* Doppelpunkt
* Sekunde
* Zeitzone, entweder als Buchstabencode oder +/- Stunden

Höchstwahrscheinlich wird das Datum was man zum RSS Feed verarbeiten will
aus einer Datenbank stammen. Der Weg ist deshalb mit einigen Umwandlungen
verbunden. Nehmen wir das Datum das wir für <pubDate> brauchen. Wenn die
Feed alle in einer Datenbank gespeichert sind, brauchen wird das
Erstellungsdatum des zuletzt erstellten Feed um das Aktualisierungsdatum
des channel sinnvoll und korrekt zu setzen. Der Code dazu könnte so aussehen:


    #include <tntdb/cxxtools/datetime.h>


    # [...]

    std::string  RSSfeedManager::getLastUpdate(){
        cxxtools::DateTime cxxdt;

        tntdb::Statement st = this->conn.prepare( \
                       "SELECT \
                            createtime \
                        FROM rss_feeds \
                        WHERE createtime = (select max(createtime) from rss_feeds)");

        try {
            tntdb::Value value = st.selectValue( );
            value.get(cxxdt);
        } catch( tntdb::NotFound nfe )
        {
            cxxtools::DateTime init_cxxdt(
                1971,
                7,
                12,
                18,
                0,
                0
            );
            cxxdt = init_cxxdt;
        }
        return covertDate( cxxdt );
    }


Der Catch-Block fängt den Fall ab, das kein Datensatz gefunden wurde und setzt
den Wehrt auf weit in die Vergangenheit um dem Client zu sagen es gibt nichts.

Befor der Wert zurückgegeben wird, durchläuft der Type cxxtools::DateTime noch
eine Konvertierung zu dtd::string der das Datum im Zielformat RFC 822 beinhaltet.
Was die Funktion im Detail tut ist im folgendem Code-Listing zu sehen:


    std::string  RSSfeedManager::covertDate( cxxtools::DateTime cxxdt ){

        struct tm timeinfo;
        timeinfo.tm_year = ( cxxdt.year() - 1900 );
        timeinfo.tm_mon = cxxdt.month() - 1;
        timeinfo.tm_mday = cxxdt.day();
        timeinfo.tm_wday = 0;
        timeinfo.tm_hour = cxxdt.hour();
        timeinfo.tm_min = cxxdt.minute();
        timeinfo.tm_sec = cxxdt.second();
        timeinfo.tm_isdst = 0;
        // somewhat strange to use mktime to convert tm to time_t
        // and localtime_r back to tm but we need the day of week
        time_t t = mktime(&timeinfo);
        // localtime_r is the thread safe variant of localtime.
        localtime_r(&t, &timeinfo);
        char buffer[80];
        strftime(buffer, sizeof(buffer), "%a, %d %b %Y %T %z", &timeinfo);

        return std::string(buffer);
    }

Die Funktion konvertiert ihrerseits cxxtools::DateTime zu struct tm und von
struct tm zu time_t und mit Hilfe von strftime() zu std::string. Das sind
zugegeben eine ganze menge Klimmzüge. Der Funktionsaufruf localtime_r() sorgt
dafür, das man den korrekten Wochentag herausbekommt. Das "_r" am Ende des
Funktionsaufruf ist kein Verschreiber! Die spezielle Funktion muss benutzt
werden, da sie die "thread safe" Variante von localtime ist. 




