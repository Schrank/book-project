# The Hitchhikers Guide To Magento
###### von Fabian Blechschmidt
### Vorwort
`Per Anhalter durch die Galaxis` von `Douglas Adams` ist ein tolles Buch und da ich plane, diesen Magento Guide unterwegs zu schreiben, wann immer ich Zeit habe, ist das erstmal der Arbeitstitel.

## Verzeichnisaufbau

## Konfiguration
Wie fängt man so ein Buch am Besten an? Ich dachte einfach wie ein neues Modul, da schreibe ich auch die Konfiguration zuerst.
### Modulkonfiguration (etc/modules/Namespace_ModuleName.xml)
Die wichtigste Konfigurationsdatei für ein Modul liegt in `app/etc/modules`, mehr dazu in den Hintergrundinfos. Sie heißt normalerweise `Firma_Modulbeschreibung.xml`, also z.B. `Sap_Importer.xml` wenn SAP ein Modul zum Import implementiert.

Magento lädt alle xml-Dateien in `app/etc/*`, das heißt insbesondere, dass es egal ist, wie die Datei heißt. Zum leichteren Wiederfinden und für die eigene Ordnung, ist aber dringend empfohlen, dem Muster `Namespace_ModuleName` zu folgen. 

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
Der Name des Moduls. Meiner Meinung nach, braucht man den Namen dieses XML-Knotens nie wieder. **Wichtig** ist nur, dass der Name des Knotens eindeutig über alle Konfigurationsdateien ist. Es empfiehlt sich dennoch, den Knoten ebenso zu benennen, wie die Datei selbst.
#### active
Dieser Knoten bestimmt, ob das Modul aktiv ist oder nicht.
##### Tipp
Wenn man ein Modul deaktivieren möchte, ohne die Konfigurationsdatei anzufassen, muss man genau diesen Knoten mit `false` überschreiben. Das Problem ist, dass Magento keine Ordnung vorgibt für das Laden der Dateien. Glücklicherweise geben die meisten Dateisystem die Dateien nach dem Alphabet geordnet zurück, d.h. eine Datei mit dem Namen `ZZZ_DeactivateModules.xml` und dem folgenden Inhalt, deaktiviert unser Modul wieder.

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
Im `global`-Knoten werden alle Daten gespeichert, die magentoweit benötigt werden.
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
### system.xml
### adminhtml.xml
## Model, Resourcemodel und Collections
## Controller
## Blöcke und Layout

