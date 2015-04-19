Super szybki kurs integracji open source – Camel & ServiceMix
============

Cel: super szybkie wprowadzenie w temat.

## Narzędzia ##

* Framework integracyjny [Apache Camel](http://camel.apache.org/)
* Lekkie rozwiązanie esb [ServiceMix](http://servicemix.apache.org/)
* Java 1.7, Maven 3, SoapUI 5 

## Przygotowanie ##

    $ git clone https://github.com/gniewkos/super-szybki-kurs-integracji-open-source.git
    $ cd super-szybki-kurs-integracji-open-source
  
##  Wygenerowanie uslugi z archetypu maven ##
    
    $ git checkout tags/challenge1
    
    mvn archetype:generate -DarchetypeArtifactId=camel-archetype-cxf-contract-first-blueprint -DarchetypeVersion=2.14.1 -DarchetypeGroupId=org.apache.camel.archetypes
    
     ..
    Define value for property 'groupId': : esb
    Define value for property 'artifactId': : report-service
    Define value for property 'version':  1.0-SNAPSHOT: :
    Define value for property 'package':  esb: :
    ..
     Y: :Y
     ..
     [INFO] BUILD SUCCESS
     
     cd report-service
     mvn install
    
## Uruchamienie ServiceMix ##

    $ cd ~/Programs/apache-servicemix-5.1.2/
    $ ./bin/servicemix

... i rozglądamy się po terenie

Sprawdza się metoda pracy z konsolą linux, działa TAB:)

    karaf@root> h `tab`
    head       headers    help       history
    karaf@root> help

`help` wyświetla listę wszystkich dostępnych poleceń.
Większość z nich udostępnia pomoc `--help`.

Polecenia wystarczające do przejścia warsztatu to (także w 90% standardowego użytkowania):

* podpowiedzi `tab`
* wyszukiwanie w historii poleceń `ctrl+r`
* niektóre przydatne linuxowe narzędzia gdzie numer jeden to `grep`
* `list` wyświetla listę paczek (w lokalnym dialekcie *bundli*), najcześciej używana komenda to: `list | grep '..'`
* `log:tail` wyświetla logi ServiceMix
* `install` instaluje nowe paczki
* `uninstall BUNDLE_ID` odinstalowuje paczki
* `start BUNDLE_ID` startuje paczki (działa też `install -s`)
* `stop BUNDLE_ID` zatrzymuje paczki 
* `dev:watch *` automatycznie odświeża paczki rozwojowe (w lokalnym dialekcie *SNAPSHOTS*)

Proponuje poeksperymentować trochę z poleceniami `list` i `log:tail` w połączeniu z `grep`.

    karaf@root> list
    ..
    karaf@root> log:tail
    ...
    ctrl+c
    
## Uruchomienie usługi

Uruchomienie uslugi opisane jest w pliku ReadMe.txt wygenerowanej uslugi. Na poczatek trzeba zainstalowac ponizsze paczki.

    features:install camel-jaxb
    features:install camel-cxf
    

ServiceMix jest domyślnie skonfigurowany w taki sposób, aby szukać paczek właśnie w `~/.m2/repository/`. Dzięki temu instalacja paczki na ServiceMix polega na wykonaniu polecenia.

    karaf@root> install -s mvn:esb/report-service/1.0-SNAPSHOT
    Bundle ID: 200

Sprawdzamy status usługi. `Active` = OK. 

    karaf@root> list
    ...
    20   Active  Created   80    A Camel CXF Blueprint Route (1.0.0.SNAPSHOT)

Sprawdzamy logi z uruchomienia usługi.

    karaf@root> log:tail
    ...
    ctrl+c

ServiceMix udostępnia (z wykorzystaniem biblioteki cxf) listę dostępnych usług po adresem:
    
    http://localhost:8181/cxf

Możemy wyświetlić WSDL usługi dodając parametr ?wsdl do adresu:
    
    http://localhost:8181/cxf/report/


Ustawiamy ServiceMix aby przyszłe zmiany usługi były odświeżane automatycznie po zainstalowaniu w lokalnym repozytorium (zmiany wszystkich paczek SNAPSHOTS).

    karaf@root> dev:watch *

Aby przetestować odświeżanie modyfikujemy nazwę usługi w pliku pom.xml

    <name>Report Service</name>
    
Przebudowujemy usługę i sprawdzamy konsolę ServiceMix (polecenie `list` wyświetli nową nazwę usługi).

    
## Porzadki  

    git reset –hard
    git clean –fd
    git checkout master
    

