# The Hitchhikers Guide To Magento
###### von Fabian Blechschmidt
### Vorwort
`Per Anhalter durch die Galaxis` von `Douglas Adams` ist ein tolles Buch und da ich plane, diesen Magento Guide unterwegs zu schreiben, wann immer ich Zeit habe, ist das erstmal der Arbeitstitel.

#### Wording (Überschrift ändern und an einen passenderen Ort verschieben)
[Mit Dank an](https://twitter.com/VinaiKopp/status/307773382785642496) [Vinai Kopp](https://twitter.com/VinaiKopp/status/307773580190552064) übernehme/führe ich folgendes Wording ein:
- /app/etc/modules ist das Modulregister.
- In Modul/etc/config.xml ist die Modulkonfiguration.
- Alles unter app/etc und app/etc/modules zusammen ist die Basiskonfiguration

## Verzeichnisaufbau
Wie jede moderne Webapplikation heutzutage, trennt Magento zwischen verschiedenen Arten von Dateien. Intuitiv ist es an einigen Stellen sicher nicht und ob es eine sinnvolle Trennung ist darüber lässt sich auch streiten, unabhängig davon schauen wir sie uns jetzt an.

    .
    ├── LICENSE.html
    ├── LICENSE.txt
    ├── LICENSE_AFL.txt
    ├── RELEASE_NOTES.txt
    ├── app
    │   ├── Mage.php
    │   ├── code
    │   │   ├── community
    │   │   ├── core
    │   │   └── local
    │   ├── design
    │   │   ├── adminhtml
    │   │   ├── frontend
    │   │   └── install
    │   ├── etc
    │   │   ├── config.xml
    │   │   ├── local.xml
    │   │   ├── local.xml.additional
    │   │   ├── local.xml.template
    │   │   └── modules
    │   └── locale
    │       ├── en_US
    ├── cron.php
    ├── cron.sh
    ├── downloader
    ├── errors
    ├── favicon.ico
    ├── index.php
    ├── js
    ├── lib
    │   ├── 3Dsecure
    │   ├── LinLibertineFont
    │   ├── Mage
    │   ├── PEAR
    │   ├── Varien
    │   ├── Zend
    │   ├── flex
    │   ├── googlecheckout
    │   └── phpseclib
    ├── mage
    ├── media
    │   ├── catalog
    │   ├── customer
    │   ├── downloadable
    │   ├── export
    │   ├── import
    │   └── xmlconnect
    ├── shell
    ├── skin
    │   ├── adminhtml
    │   ├── frontend
    │   └── install
    └── var
    ├── cache
    ├── import
    ├── locks
    └── log
    
### app
Im Ordner ’app’ finde sich alle Dateien, die von PHP geparst oder ausgelesen werden. Dazu gehören alle PHP-Klassen, Templatedateien, Konfigurationsdateien, Übersetzungen
#### app/etc
Fangen wir mit der "leichten" Kost an. In ’app/etc’ liegt die Basis-Konfigurationsdatei ’config.xml’ und die persönliche Konfigurationsdatei für und: ’local.xml’. Die ’config.xml’ passen wir am Besten einfach in Ruhe, Änderungen kommen in die ’local.xml’. Daneben liegt noch eine ’local.xml.template’. In dieser Datei sind allerlei mögliche Konfigurationen und Parameter aufgeführt, die in Magento möglich sind. 

**Achtung:** Magento lädt ALLE Dateien mit der Endung xml, die in diesem Ordner liegen und baut daraus eine Konfiguration. Dateien wie z.B. ’local.live.xml’ oder ’local.stage.xml’ haben schon zu komischem Verhalten geführt ;-) Nennt sie also besser ’local.xml.stage’, dann werden sie ignoriert.

#### app/code
In ’app/code’ liegen die verschiedenen Module von Magento, mehr zu deren Aufbau später. Für den Anfang schauen wir uns nur die drei Ordner an:

- core
- community
- local

Die Benennung ist meiner Meinung nach gut verständlich und intuitiv, leider halten sich nicht alle Entwickler daran. Im Ordner ’app/code/core’ liegen alle Dateien die von Magento sind. Dieser Ordner ist für uns tabu. Es gibt KEINEN (lies: gar keinen) Grund irgendeine Datei zu ändern und in eine Versionsverwaltung einzuchecken oder gar auf in Livesystem zu spielen. Damit will ich sagen: Debuggen ist natürlich erlaubt. Ich bin allerdings inzwischen der Meinung das ein gut gesetzter Breakpoint für xDebug oft besser ist, als ein ’var_dump()’, ’echo’ oder ’die()’. Also kann man hier auf Änderungen verzichten. Aber es ist wuer Projekt und ihr müsst entwickeln, debuggt, wie es euch am Besten passt, funktionieren soll es am Ende :-).

In ’app/code/community’ liegen Module, die von der Magento-Community stammen. Früher hieß dass, sie kamen von magento-connect, aber heute kommen sie von github, via composer aus dem eigenen git oder svn. Wenn ihr ein Modul baut, dass später der Community zur Verfügung gestellt wird, packt es hier rein. Wenn die Entscheidung später kommt, ändert den Pfad in ’community’.

Und zuletzt ’app/code/local’. In dieses Verzeichnis kommen alle Exklusiventwicklungen rein.

## Konfiguration
Wie fängt man so ein Buch am Besten an? Ich dachte einfach wie ein neues Modul, da schreibe ich auch die Konfiguration zuerst.
### Modulkonfiguration (etc/modules/Namespace_ModuleName.xml)
Die wichtigste Konfigurationsdatei für ein Modul liegt in `app/etc/modules`, mehr dazu in den Hintergrundinfos. Sie heißt normalerweise `Firma_Modulbeschreibung.xml`, also z.B. `Sap_Importer.xml` wenn SAP ein Modul zum Import implementiert.

Magento lädt alle xml-Dateien in `app/etc/*`, das heißt insbesondere, dass es egal ist, wie die Datei heißt. Zum leichteren Wiederfinden und für die eigene Ordnung, ist aber dringend empfohlen, dem Muster `Namespace_ModuleName.xml` zu folgen. 

In dieser Datei kann man ein Modul aus- uns anstellen, den Codepool und Abhängigkeiten definieren. Wichtig noch zu wissen: Nach dem Laden, werden alle Dateien zu eine großen XML-Struktur verschmolzen.

Diese XML-Dateien hat folgenden Aufbau:

    <?xml version="1.0"?>
    <config>
        <modules>
            <Namespace_ModuleName>
                <active>true</active>
                <codePool>community</codePool>
                <depends>
                    <Mage_Cms />
                    <Mage_Catalog />
                </depends>
            </Namespace_ModuleName>
        </modules>
    </config>



#### Namespace_ModuleName
Der Name dieses Knoten ist der Name des Moduls. Dieser Name wird verwendet um die Dateien später in den Verzeichnissen zu finden.  Also in `app/code/(core|local|community)/Namespace/ModulName/`

**Wichtig** ist nur, dass der Name des Knotens eindeutig über alle Konfigurationsdateien ist.
**Wichtig** Achtet bei dem Namen des Knotens auf groß- und Kleinschreibung und den daraus resultierenden Ordnern. Auf einem Mac oder Windows ist (standardmäßig) `app/code/local/NameSpace/` das gleiche wie `app/code/local/Namespace/`, aber spätestens wenn man seine Extension auf das Linux vom Live-System einspielt, meckert das System und geht spontan (mit einem hässlichen 503) aus.
#### active
Dieser Knoten bestimmt, ob das Modul aktiv ist oder nicht.
##### Tipp
Wenn man ein Modul deaktivieren möchte, ohne die Konfigurationsdatei anzufassen, muss man genau diesen Knoten mit `false` überschreiben. Das Problem ist, dass Magento keine komplette Ordnung vorgibt für das Laden der Dateien. Das heißt, dass die XML-Dateien in folgender Reihenfolge geladen werden:

- Mage_All
- Mage_*
- *

Die Konsequenz daraus ist, dass man mit jedem Third-Party-Modul alle Core-Module deaktivieren kann, da diese nach den Core-Modulen geladen werden. Leider lässt sich das nicht 1:1 auf die anderen Third-Party-Module übertragen. Allerdings geben die meisten Dateisystem die Dateien nach dem Alphabet geordnet zurück (A-Za-z), d.h. eine Datei mit dem Namen `zzz_DeactivateModules.xml` und dem folgenden Inhalt, deaktiviert unser Modul wieder.

    <?xml version="1.0"?>
    <config>
        <modules>
            <Namespace_ModuleName>
                <active>false</active>
            </Namespace_ModuleName>
        </modules>
    </config>

Wir können beliebig viele weitere Modulnamen in diese Datei packen.

#### codePool
Es gibt drei verschiedene Codepools:
- core
- community
- local

Die Codepools sind eigentlich selbsterklärend:
- In `core` liegen alle Module und zugehörigen Dateien von Magento. Das ist der Ordner in dem man **NIE NIE NIE** Dateien ändert.
- `community` ist für die Community-Module vorgesehen, also alles was man auf [magento-connect](http://www.magentocommerce.com/magento-connect), im weiten Netz oder auf [github](https://github.com/), z.B. [im Magento-Hackathon Repo](https://github.com/magento-hackathon/), findet.
- Und zuletzt: `local`. Dieser codePool ist für die Module vorgesehen, die nicht veröffentlich werden solle, also exklusiv für Shopbetreiber oder Kunden entwickelt werden.

Für die `local`-Module gilt: Man kann sie alle auf einmal deaktivieren, indem man in der local.xml den XML-Knoten `global/disable_local_modules/` auf `true` setzt.

Der codePool bestimmt widererwarten NICHT, wo die PHP-Dateien gesucht werden. Das übernimmt der Autoloader, der automatisch `local`, `community` und `core` durchsucht. Es werden aber alle modulabhängigen Konfigurationsdateien dort gesucht (adminhtml.xml, system.xml, api.xml, etc.), mehr dazu später.

**Tipp**: Es heißt codePool und nicht codepool!
#### depends
Innerhalb des Knotens depends kann man Abhängigkeiten zu anderen Modulen angeben. Diese Abhängigkeiten haben zwei Effekte:
1. Die Abhängigkeit wird hart geprüft, d.h. wenn das Modul, zu der eine Abhängigkeit besteht nicht existiert, fliegt ein Fehler. Der Fehler ist, mit Logins leicht zu identifizieren, ohne Blick in die Log-Dateien wird es schwierig.
2. Außerdem wird aufgrund der Dependancen, die Reihenfolge bestimmt, in der die Module geladen werden, die Rede ist hier von den `config.xml` Dateien der einzelnen Module.

Bei diesen Konfigurationsdateien ist das Überschreiben der einzelnen Werte einfacher als bei dem `active` Knoten der Modulkonfiguration, denn durch die Abhängigkeit hat man direkt die Kontroller darüber, in welcher Abfolge die Dateien geladen werden. Mehr dazu später.

#### Zusammenfassung

TBD

###### Hintergrundinfos
Das Laden aller Konfigurationsdateien findet man in `Mage_Core_Model_Config::_getDeclaredModuleFiles`

### TBD -> NAMEN FINDEN!! Modulkonfiguration (config.xml)
Jedes Modul besitzt eine `config.xml`. Diese befindet sich im `etc` Verzeichnis des Moduls.

Die XML-Datei hat verschiedene große Untergruppen:
- modules
- global
- frontend
- adminhtml
- admin
- crontab
- default
- stores
- websites
#### modules 
In `modules` gibt es einen Knoten mit dem Namen des Moduls. Es empfiehlt ich hier den gleichen Namen zu verwenden, wie der Dateiname und der Modulname in der `Namespace_ModuleName.xml`

In diesem Knoten wiederum wird die `version` dokumentiert. Diese ist später wichtig für Update-Skripte.

Beispiel:
    <config>
        <modules>
            <Namespace_ModuleName>
                <version>3.1.4</version>
            </Namespace_ModuleName>
        </modules>
    </config>
#### global
Im `global`-Knoten werden alle Daten gespeichert, die systemweit benötigt werden.
##### models
##### blocks
##### helpers
##### resources
##### resource
##### events
##### anderes
#### frontend
#### adminhtml
#### admin
#### crontab
#### default, stores, websites

#### Controller
#### Layout
#### Übersetzungen
#### Observer
Magento implementiert an etlichen stellen ein Event-Observer-Pattern. Damit kann man z.B. Optionen an Quote-Items ändern, Exceptions schmeißen, wenn Produkte nicht gespeichert werden dürfen oder um unter bestimmten Umständen neue Menüpunkte im Backend einzufügen.

Es steht und natürlich auch frei, eigene Events zu triggern. Das macht nicht soviel Sinn bei eigenen local Modulen, aber umso mehr bei community Modulen um anderen Entwicklern Möglichkeiten zum Ändern zu geben, ohne das Modul ändern zu müssen.

##### Observer in der Konfiguration
Es gibt drei Stellen um Observer in der Konfiguration zu registrieren, im frontend, adminhtml oder global Knoten. Wie man sich evtl. denken kann, werden die Observer nur aufgerufen, wenn sie im richtigen Knoten definiert sind, also für das Backend adminhtml, für das Frontend frontend. Die Observer die in global definiert sind, werden immer ausgeführt.

    <config>
        <frontend|adminhtml|global>
            <events>
                <customer_login>
                    <observers>
                        <namespace_module>
                            <type>singleton|model|disabled</type>
                            <class>namespace_module/observer</class>
                            <method>customerLogin</method>
                        </namespace_module>
                    </observers>
                </customer_login>
            </events>
        </frontend|adminhtml|global>
    </config>

Im `events`-Knoten der verschiedenen Bereiche wird ein Knoten mit dem Namen des Events angelegt. Eine Liste mit etlichen Events findet sich z.B. auf [magento.stackexchange.com](http://magento.stackexchange.com/questions/153/where-can-i-find-a-complete-list-of-magento-events/). Das richtige Event zu finden ist eine Sache für sich und wird folgenden Unterkapitel beschrieben.

Innerhalb des Knotens mit dem Event-Namen, gibt es einen Knoten `observers`. Darin wiederum findet sich dann eine Liste mit allen definierten Observern. Jeder Observer muss innerhalb des Events einen **eindeutigen Namen** haben. Es bietet sich hier, einfach den Namen des Moduls zu benutzen `namespace_module`. Üblicherweise gibt es für ein Event nicht mehr als einen Observer pro Modul, damit sind die Namen einmalig. Die "echte" Konfiguration für das Event findet sich in diesem Knoten: `type`, `class` und `method`.

`type` ist die Art und Weise wie die Observer-Klasse initialisiert wird. Zur Auswahl stehen hier: `model`, `singleton` und `disabled`. Mit Model wird die Methode `Mage::getModel()` genutzt, bei Singleton `Mage::getSingleton()`. Die Konsequenz eines Singleton ist, dass weniger Objekte instanziiert werden und das Daten, die innerhalb des Objekts gespeichert werden, bei anderen Events, die der Observer abhandelt (die auch als `singleton` geladen werden), zur Verfügung stehen (da es sich um das gleiche Objekt handelt).

`class`: Wie zu erkennen ist, wird innerhalb  hier der Klassenname und der Methodenname definiert. Der Klassenname kann sowohl in der gewohnten Magentoschreibweise `namespace_module/classname, als auch komplett angegeben werden. Es sollte hier die magentoschreibweise bevorzugt werden um ggf. Rewrites zu ermöglichen.

`method`: Beim Methodennamen scheiden sich die Geister. Hier gibt es zwei Best-Practices. Wichtig ist, dass sie **ohne** die abschließenden Klammern `customerLogin()` angegeben werden.

1. Man wählt den Methodenname wie den Eventnamen, also z.B. `customerLogin` für das Event `customer_login`
2. oder man benennt die Methode (wie üblich) nach dem was sie tut, also z.B. checkOptionsAttached()

##### Observer Implementierung

In der Konfiguration werden nur Modelname und Methodenname definiert. Dieses Objekt wird dann instanziiert und die Methode darauf aufgerufen. Eine Observerklasse hat keinerlei Bedingungen, d.h. Man muss weder ein Interface implementieren, noch eine Klasse erweitern.

Wer mehr dazu wissen will, findet die wichtige Methode hier:

    \Mage_Core_Model_App::dispatchEvent()

##### Das richtige Event finden

### system.xml
### adminhtml.xml
## Model, Resourcemodel und Collections
## Controller
## Blöcke und Layout

