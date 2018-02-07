**Table of Contents**  *generated with [DocToc](http://doctoc.herokuapp.com/)*

- [1. OSINT Reconnaissance](#)
		- [Verwendete Tools](#)
		- [Zielwahl](#)
		- [The Harvester - E-Mail Recon](#)
		- [Inspy - Linkedin Recon](#)
		- [Maltego - OSINT Graphing](#)
		- [Leaks](#)
		- [Abschluss](#)
- [2. Active Information Gathering - Nmap](#)
		- [Prozessablauf](#)
		- [Kommandos](#)
- [3. Patch](#)
		- [System:](#)
		- [Software:](#)
		- [Vorbereitungen](#)
		- [Debugging mit IDA-Pro einrichten](#)
		- [Vergleichfsfunktion(en) finden](#)
		- [Patch des Programmes](#)
- [4. Keygen](#)
		- [Vorbereitung und Setup](#)
		- [Analyse](#)
		- [Skript und Proof of Concept](#)


# 1. OSINT Reconnaissance

In der ersten Aufgabe sollen mittels verschiedener OSINT-Werkzeuge Informationen über ein beliebiges Unternehmen gesammelt werden und bereits stattgefundene Leaks aufgestöbert werden.

OSINT steht dabei für Open Source Intelligence. Das Open Source im Titel bezieht sich allerdings nicht auf Open Source im Sinne von open source Software, sondern auf offene und legale Informationsquellen. Das Sammeln von Daten von allen möglichen legalen Quellen wie beispielsweise der offiziellen Website des Unternehmens, Auftritten auf diversen Social Media Plattformen und so weiter wird davon abgedeckt.

### Verwendete Tools
Die hier gezeigten Reconnaissance-Arbeiten werden auf einem MacBook Pro durchgeführt. Auf dem Computer werden je nach Aufgabe verschiedene virtuelle Maschinen vewendet.

Für diesen Punkt kommt eine spezielle Linux Maschine namens Buscador (span.: "Suchender") zum Einsatz, welche mit einem vorinstallierten Bundle von gängigen OSINT Tools daherkommt und kostenlos im Netz verfügbar ist.

Zusätzlich kommt noch meine übliche 64 Bit Kali-VM zum Einsatz, da diese einfach besser funktioniert als die 32 Bit VM, die für spätere Aufgaben benötigt wird.

Auf Seite der Software werden die folgenden Tools verwendet:

* TheHarvester
* Inspy
* Maltego

### Zielwahl

Für jede Informationssammelaufgabe muss als Erstes ein Ziel festgelegt werden. Für diese Arbeit wurde das deutsche Softwareunternehmen ATOSS Software AG ausgewählt.

### The Harvester - E-Mail Recon

Als erstes OSINT Tool kommt nun TheHarvester zum Einsatz. Diese Utility ist als Kommandozeilen-Tool auf Kali installiert und als Tool mit graphischer Nutzeroberfläche auf dem Buscador. In diesem Fall habe ich das Tool aber mittels Homebrew auf meinem Mac installiert, da mir dort die Handhabe am einfachsten fällt.

The Harvester durchsucht gängige Suchmaschinen, aber auch Jobnetzwerke, wie beispielsweiese Linkedin und sogar PGP Schlüsselverzeichnisse nach e-Mail-Adressen bestimmter Domains.

Dazu wird theharvester mit ebendiesem Kommando auf der Kommandozeile aufgerufen. Der "-d"-Schalter erlaubt, die Zieldomain festzulegen. Mit dem "-l"-Schalter wird die maximale Anzahl der Ausgaben festgelegt und mit dem "-b"-Schalter wird ausgewählt, welche Quellen durchsucht werden sollen.

Ich mache es mir einfach und durchsuche sämtliche verfügbare Quellen nach meiner Domain:

```
Daves-MBP:~ Dave$ theharvester -d atoss.com -l 500 -b all

*******************************************************************
*                                                                 *
* | |_| |__   ___    /\  /\__ _ _ ____   _____  ___| |_ ___ _ __  *
* | __| '_ \ / _ \  / /_/ / _` | '__\ \ / / _ \/ __| __/ _ \ '__| *
* | |_| | | |  __/ / __  / (_| | |   \ V /  __/\__ \ ||  __/ |    *
*  \__|_| |_|\___| \/ /_/ \__,_|_|    \_/ \___||___/\__\___|_|    *
*                                                                 *
* TheHarvester Ver. 2.7                                           *
* Coded by Christian Martorella                                   *
* Edge-Security Research                                          *
* cmartorella@edge-security.com                                   *
*******************************************************************


Full harvest..
[-] Searching in Google..
	Searching 0 results...
	Searching 100 results...
	Searching 200 results...
	Searching 300 results...
	Searching 400 results...
	Searching 500 results...
[-] Searching in PGP Key server..
[-] Searching in Bing..
	Searching 50 results...
	Searching 100 results...
	Searching 150 results...
	Searching 200 results...
	Searching 250 results...
	Searching 300 results...
	Searching 350 results...
	Searching 400 results...
	Searching 450 results...
	Searching 500 results...
[-] Searching in Exalead..
	Searching 50 results...
	Searching 100 results...
	Searching 150 results...
	Searching 200 results...
	Searching 250 results...
	Searching 300 results...
	Searching 350 results...
	Searching 400 results...
	Searching 450 results...
	Searching 500 results...
	Searching 550 results...


[+] Emails found:
------------------
Christian.Braumann@atoss.com
benjamin.gernhardt@atoss.com
pixel-151799923124589-web-@atoss.com

[+] Hosts found in search engines:
------------------------------------
[-] Resolving hostnames IPs... 
212.75.60.116:shellretailnl.atoss.com
195.30.124.217:www.atoss.com
[+] Virtual hosts:
==================
195.30.124.217	www.atoss.com

```

Die Ergebnisse sind nicht unbedingt reichhaltig, aber immerhin konnte ich drei Mailadressen, die mit der Firma verbunden sind aufstöbern, zwei davon dürften zu Mitarbeitern gehören. Außerdem wurden drei Hosts mit IP-Adressen gefunden.

Das nächste verwendete Tool wird gezielt auf Linkedin zugreifen, daher interessiert mich, welche Treffer theharvester für Linkedin liefert:

```
Daves-MBP:~ Dave$ theharvester -d atoss.com -b linkedin

*******************************************************************
*                                                                 *
* | |_| |__   ___    /\  /\__ _ _ ____   _____  ___| |_ ___ _ __  *
* | __| '_ \ / _ \  / /_/ / _` | '__\ \ / / _ \/ __| __/ _ \ '__| *
* | |_| | | |  __/ / __  / (_| | |   \ V /  __/\__ \ ||  __/ |    *
*  \__|_| |_|\___| \/ /_/ \__,_|_|    \_/ \___||___/\__\___|_|    *
*                                                                 *
* TheHarvester Ver. 2.7                                           *
* Coded by Christian Martorella                                   *
* Edge-Security Research                                          *
* cmartorella@edge-security.com                                   *
*******************************************************************


[-] Searching in Linkedin..
	Searching 100 results..
Users from Linkedin:
====================
Kathrin Pamp
Alexander von Fritsch
Oliver Henecka
Steffen Winkler
Dorle Schwake
Christian Maier
Nina Bruch
Clara Emich - Sales Specialist
Marion Altschach
Ginesh Koottakara
Corina Oravitan
Ioana Medesan
Mihaela Andreea Nicolicescu
Alexander Grams
Johannes Matuschek
Nina Seidl
Jackie Sesselmann
Diana Tipei
Mahmoud Awwad
Vivien Bremer
Dirk Vanovsek - Senior Software Developer
Andreas Obereder
Nadine Wisnagrotzky
Julia Sabine Franke
Lavinia Golu
Tim Kuntze
Sebastian Velcelean Pirva

```

### Inspy - Linkedin Recon

Inspy ist eine kleine Utility über die ich kürzlich auf der Website hackingtutorials.org gestolpert bin. Inspy sucht gezielt nach Mitarbeiternamen und deren Jobfunktionen und greift dabei nur auf Linkedin zu.

Ich installiere Inspy mit folgendem Kommando:

```
apt update && apt -y install inspy
```

Inspy bietet zwei Modi, EmpSpy und TechSpy. Leider funktioniert nur noch EmpSpy, also der Mitarbeiter-Spion, seit Linkedin upgedated wurde.

Der EmpSpy-Modus benötigt ein Argument und zwar eine Wordlist, welche die Jobtitel angibt, nach denen gesucht werden soll. Wir finden diese Listen unter:

```
/usr/share/inspy/wordlists/
```
Hier sieht man die verfügbaren Listen:

![alt text](https://i.imgur.com/t8YJaHI.png "Logo Title Text XXX")

Da das Verzeichnis nun bekannt ist wird dieses für unser Suchkommando benutzt:

```
root@kali:~# inspy --empspy /usr/share/inspy/wordlists/title-list-large.txt "ATOSS Software AG"

InSpy 2.0.3

2018-02-07 06:23:51 94 Employees identified
2018-02-07 06:23:51 Elke Jaeger Director Marketing bei ATOSS Software AG
2018-02-07 06:23:51 Marius Pepu Senior UI/UX Software Engineer at ATOSS Software A
2018-02-07 06:23:51 Simina Mursa Marketing bei ATOSS Software AG
2018-02-07 06:23:51 Ardelian Diana QA Lead la ATOSS Software AG
2018-02-07 06:23:51 Karoline Wenzel Working student Sales international at ATOSS Softw
2018-02-07 06:23:51 Denise Hiebl HR Business Partner bei ATOSS Software AG
2018-02-07 06:23:51 Alexandra Sari Software Test Engineer at ATOSS Software AG
2018-02-07 06:23:51 Adrian Stefanescu Senior java developer la ATOSS Software AG
2018-02-07 06:23:51 Wolfgang Horner, PMP® Senior IT-Support-Spezialist bei ATOSS Software AG
2018-02-07 06:23:51 Jeannine Elsäßer Account Manager Retail bei ATOSS Software AG
2018-02-07 06:23:51 Robert Curetean Software Test Engineer at ATOSS Software AG
2018-02-07 06:23:51 Andrei Celovschi Junior Software Engineer at ATOSS Software AG
2018-02-07 06:23:51 Peter Van Ditmarsch Sales Executive at ATOSS Software AG
2018-02-07 06:23:51 Doris Hausen Senior Product Manager User Interface Design bei A
2018-02-07 06:23:51 Cornel Mamaliga Product Manager at ATOSS Software AG
2018-02-07 06:23:51 Stefanie Kremp Marketing Referent bei ATOSS Software AG
2018-02-07 06:23:51 Tim Kuntze Marketing Campaign Manager at ATOSS Software AG
2018-02-07 06:23:51 Claas Voigt Product Manager at ATOSS Software AG
2018-02-07 06:23:51 Matthias Granget Account-Manager bei ATOSS Software AG
2018-02-07 06:23:51 Axel Leeck Consultant Professional Services bei ATOSS Softwar
2018-02-07 06:23:51 Ali Osman Karbuz IT Consultant bei ATOSS Software AG
2018-02-07 06:23:51 Andrea Bojin Marketing Specialist la ATOSS Software AG
2018-02-07 06:23:51 Christian Walther Marketing Referent bei ATOSS Software AG
2018-02-07 06:23:51 Mahmoud Awwad Product Manager bei ATOSS Software AG
2018-02-07 06:23:51 Konstantin Klewe Manager Professional Services Switzerland bei ATOS
2018-02-07 06:23:51 Liviu Marunceac Software Test Engineer at ATOSS Software AG
2018-02-07 06:23:51 Eberhard Walcher Retired at ATOSS Software AG
2018-02-07 06:23:51 Veronika Kohlert Head of Online Marketing & Marketing Operations be
2018-02-07 06:23:51 Andrei Costache Senior Software Engineer at ATOSS Software AG
2018-02-07 06:23:51 Steffen Winkler Head of Product Marketing Management | ATOSS Softw
2018-02-07 06:23:51 Ciprian Stanescu software engineer la ATOSS Software AG
2018-02-07 06:23:51 Mihaela Andreea Nicolicescu Senior IT Recruiter at ATOSS Software AG
2018-02-07 06:23:51 Michael Kral Executive Account Manager bei ATOSS Software AG
2018-02-07 06:23:51 Alexander Auberger Marketing Assistant bei ATOSS Software AG
2018-02-07 06:23:51 Ginesh Koottakara Senior Account Manager bei ATOSS Software AG
2018-02-07 06:23:51 Ionut Ciprian Costan Mobile Application Developer at ATOSS Software AG
2018-02-07 06:23:51 Sebastian Velcelean Pirva Software Support Speacialist at ATOSS Software AG
2018-02-07 06:23:51 Andrei Suceava Senior Software Developer at ATOSS Software AG
2018-02-07 06:23:51 Ileana Lup Product Manager at ATOSS Software AG
2018-02-07 06:23:51 Oliver Henecka Strategy & Process Consultant at ATOSS Software AG
2018-02-07 06:23:51 Crivineanu Oana - Florentina Senior Java Developer & Support Specialist Reporti
2018-02-07 06:23:51 Stefan Handra Group Lead and Supervisor at ATOSS Software AG
2018-02-07 06:23:51 Kai Seidelmann Director Finance / Kfm. Leiter bei ATOSS Software 
2018-02-07 06:23:51 Kathrin Pamp Senior Account und Alliances Manager bei ATOSS Sof
2018-02-07 06:23:51 Konstantin Leitz Associate Account Manager - ATOSS Software AG
2018-02-07 06:23:51 Mircea Alexandru Dumitru Mobile Application Developer at ATOSS Software AG
2018-02-07 06:23:51 Thomas Bauer Consultant/Projektmanager bei ATOSS Software AG
2018-02-07 06:23:51 Andreas Koller Director Product Development bei ATOSS Software AG
2018-02-07 06:23:51 Florian Forster Business Development / Consultant to the CEO at AT
2018-02-07 06:23:51 Marc Hornschuh Senior Account Manager & Pre-Sales Consultant bei 
2018-02-07 06:23:51 Cedric Surmann Working Student Product Marketing Management bei A
2018-02-07 06:23:51 Stefan Schneider Key Account Manager at ATOSS Software AG
2018-02-07 06:23:51 Sasha Märki Consultant Professional Services bei ATOSS Softwar
2018-02-07 06:23:51 Mihai Novac Software Engineer at ATOSS Software AG
2018-02-07 06:23:51 Vlad Nicu Software Developer at ATOSS Software AG
2018-02-07 06:23:51 Octavian Bunaciu software/developer at ATOSS Software AG
2018-02-07 06:23:51 Alexander von Fritsch Managing Director Global SMB Sales & Alliances at 
2018-02-07 06:23:51 Stefan Werner Director international Alliances bei ATOSS Softwar
2018-02-07 06:23:51 Martin Reithel Senior Consultant Professional Services bei ATOSS 
2018-02-07 06:23:51 Ece Gurler System Software Engineer at ATOSS Software AG
2018-02-07 06:23:51 Ioana Medesan Senior Software Developer at ATOSS Software AG
2018-02-07 06:23:51 Nina Seidl Office Manager bei ATOSS Software AG
2018-02-07 06:23:51 Valentina Morar software developer at ATOSS Software AG
2018-02-07 06:23:51 Livia Elena VLADUT Software Developer la ATOSS Software AG
2018-02-07 06:23:51 Cristian-Nicolae BABĂU Software engineer at ATOSS Software AG
2018-02-07 06:23:51 Freek van Meijel Consultant workforce management bij ATOSS Software
2018-02-07 06:23:51 Clarisa Handra Junior Recruiter at ATOSS Software AG
2018-02-07 06:23:51 Alexandra Guliman Senior IT  Recruiter@ATOSS Software AG
2018-02-07 06:23:51 Corina Oravitan software developer at ATOSS Software AG
2018-02-07 06:23:51 Nina Bruch ATOSS Software AG
2018-02-07 06:23:51 Rainier van 't Woud Account Manager at ATOSS Software AG
2018-02-07 06:23:51 Stefan Ciontea Software Engineer at ATOSS Software AG
2018-02-07 06:23:51 Alexandra Dunga Software Test Engineer at ATOSS Software AG
2018-02-07 06:23:51 Florin Laudat Software developer la ATOSS Software AG
2018-02-07 06:23:51 Hans Dr. Höll Director Sales bei ATOSS Software AG
2018-02-07 06:23:51 Christof Leiber Vorstand bei ATOSS Software AG
2018-02-07 06:23:51 Reinhold Geissinger Senior Consultant Professional Services bei ATOSS 
2018-02-07 06:23:51 Tanja Baier Account Manager bei ATOSS Software AG
2018-02-07 06:23:51 Diana Riță Software Test Engineer at ATOSS Software AG
2018-02-07 06:23:51 sergiu andrei teodor at ATOSS Software AG
2018-02-07 06:23:51 Laurenţiu Răscol Product Manager @ ATOSS Software AG
2018-02-07 06:23:51 Nicole Maier Marketing Campaign Manager bei ATOSS Software AG
2018-02-07 06:23:51 Andreas Geistert Account Manager bei ATOSS Software AG
2018-02-07 06:23:51 Denys Syroporshnev Senior Software Ingenieur bei ATOSS Software AG
2018-02-07 06:23:51 Cristina Stana Marketing Referentin bei SC Atoss Software AG
2018-02-07 06:23:51 Anda Raletchi Marketing Specialist at ATOSS Software AG
2018-02-07 06:23:51 Raluca Popescu Product Manager at ATOSS Software AG
2018-02-07 06:23:51 Marian-Cosminel Dima Software Developer at ATOSS Software AG
2018-02-07 06:23:51 Lorenz Pöhlmann Executive Account Manager bei ATOSS Software AG
2018-02-07 06:23:51 Heiko Bräunig IT Senior Consultant bei ATOSS Software AG
2018-02-07 06:23:51 Mihai Neag Software Developer at ATOSS Software AG
2018-02-07 06:23:51 Hannes Herzog Senior Consultant CSS bei ATOSS Software AG
2018-02-07 06:23:51 Branimir Galic Technical Product Manager at ATOSS Software AG
2018-02-07 06:23:51 Markus Zieglmeier Principal Consultant bei ATOSS Software AG
Completed in 56.4s
```

Wir finden hier jede Menge Treffer. Einige davon sind nicht mehr bei ATOSS gelistet, was daran liegen kann, dass es sich um frührere Mitarbeiter handelt, welche auf ihrer Linkedin-Page noch einen Verweis auf den vormaligen Arbeitgeber haben.

Interessant wäre es gewesen hier einen Vergleich zu den beiden Herren zu ziehen, deren e-Mail-Adressen schon bekannt sind:

```
Christian.Braumann@atoss.com
benjamin.gernhardt@atoss.com
```
Die Funktion der Herren zu kennen, wäre für einen späteren Social Engineering Angriff (der natürlich rein hypothetisch ist) von Vorteil. Leider tauchen sie in der von Inspy ausgegebenen Liste nicht auf.

Andererseits kann man hier das Pferd auch von der anderen Seite aufzäumen, denn aufgrund des eindeutigen e-Mail Namensschemas sollte sich die e-Mail-Adresse von jedem der Inspy-Treffer leicht herleiten lassen. Hier stellt eher die zweifelhafte Aktualität der Treffer ein mögliches Hindernis dar.

### Maltego - OSINT Graphing

Das nächste Tool, das ich mir ansehe, ist Maltego, dessen Community Edition in Kali Linux bereits vorinstalliert ist. Dieses Progamm ist speziell für OSINT Zwecke konzipiert worden und implementiert einiges der Funtionalität von zB TheHarvester. Das Besondere an Maltego ist die Herangehensweise, da es dem Nutzer eine schnelle und einfache Erstellung von Baumgraphen zur Veranschaulichung der Suchergebnisse bietet. Dazu wird einfach ein Projekt für eine bestimmte Recon, in diesem Fall wieder "ATOSS", angelegt und dann kann auch schon eine automatisierte Suche nach ersten Ergebnissen durchgeführt werden.

Maltego stellt dazu sogenannte Maschinen zur Verfügung welche ein vorgefertigtes Set an Skripten gegen eine Domäne ausführen um Informationen zu erhalten. Selbstverständlich können Skripten auch manuell gestartet werden. Die Graphen können dabei um Elemente wie Personen, Standorte uvm. erweitert werden. So kann eine Investigation gleich der eines Detektives über eine bestimmte Firma oder Privatpersonen durchgeführt werden.

Auf dem folgenden Screenshot ist die Auswahl dieser Maschinen, sowie die allgemeine Nutzeroberfläche von Maltego zu sehen:

![alt text](https://i.imgur.com/LGNa2vu.png "Logo Title Text XXX")

Natürlich muss der Domainname noch angegeben werden:

![alt text](https://i.imgur.com/WDAaCAr.png "Logo Title Text XXX")

Tiefergehende Operationen von Maltego können bereits in die rechtliche Grauzone gehen, daher wird die eher oberflächliche "Company Stalker"-Maschine ausgewählt:

![alt text](https://i.imgur.com/lfvRX7v.png "Logo Title Text XXX")

Die "Transforms" genannten Skripten werden wie gesagt automatisch ausgeführt. Der folgende Graph ist das Ergebnis:

![alt text](https://i.imgur.com/U6iqLsN.png "Logo Title Text XXX")

Hier sehen wir verschiedene Online-Quellen, hauptsächlich Dokumente, welche von der Atoss-Seite angeboten werden. Personen die dabei namentlich erwähnt werden, werden gleich als solche auf dem Graphen ausgewiesen.

Im rechten Teil des Graphen finden wir wieder die auch von TheHarvester gefundenen Mail-Adressen vom PGP-Server. Ein Check mit dem E-Mail Tool des Harvesters zeigt jedoch, dass diese Adressen nicht mehr existieren:

![alt text](https://i.imgur.com/jFR9TBa.png "Logo Title Text XXX")

![alt text](https://i.imgur.com/7k0hSBd.png "Logo Title Text XXX")

Die Analyse zuvor reicht mir noch nicht ganz, daher führe ich nun eine Website-Analyse direkt für die Website von Atoss.com durch. Hier wird ein Spider auf die Website angewandt:

![alt text](https://i.imgur.com/xKwliR2.png "Logo Title Text XXX")

Hier macht sich Maltego interessante Suchmaschinen wie beispielsweise Built With zu nutze. Built with gibt Aufschluss darüber, mit welchen Technologien eine Website erstellt wurde. Für Webangriffe können dies nützliche erste Informationen sein. Zudem wird angezeigt, welche anderen Seiten auf der Website verlinkt sind. Auch der zuständige DNS-Server wird ausgewiesen. Dies ist mein Stichwort für die nächste Suche.

Ich beginne von vorne, doch dieses Mal mit der Footprinting 1 Maschine. Nach Durchlauf der Skripten wird das folgende Ergebnis geliefert:

![alt text](https://i.imgur.com/NwWsx3r.png "Logo Title Text XXX")

Diese Suche liefert mir nun diverse DNS-Server welche mit der Domain verknüpft sind, sowie den FTP, den Webserver, deren jeweilige IP-Adressen, die Größe der Subnetze und noch mehr. 

Hier wird es mir langsam zu heikel und ich beende meine Suche mit Maltego. Allerdings bin ich begeistert, wie einfach es einem dieses Tool macht, umfangreiche Recherchen durchzuführen.

### Leaks

Nun haben wir zweifelsohne bereits einige interessante Informationen zutage gefördert, doch das eigentliche Ziel, Leaks über das Unternehmen herauszufinden, ist nicht erfüllt. Ich bin nicht überrascht, das ich weiß, dass Atoss über recht hohe Sicherheitsstandards verfügt. Aber es muss doch zumindest irgendetwas geben...

Ich versuche es mit der Website haveibeenpwned.com. Diese Website greift auf bekannte Data-Leaks zu und kann ausgeben, ob eine E-Mail-Adresse Teil eines Dataleaks war. Ich versuche es mit der Info-Adresse von Atoss:

![alt text](https://i.imgur.com/q1JgScn.png "Logo Title Text XXX")

Nun, immerhin. Die Info-Mail-Adresse von Atoss, die wir zuvor aufgestöbert haben, taucht auch zwei Spam-Listen auf. Nicht unbedingt besonders nützliche Informationen. Ich versuche ein paar der E-Mail-Adressen durch, die ich mir aus dem Inspy-Fund ableiten kann. Alles ist sauber. Immerhin gibt es noch einen Treffer mit einer Spam-Liste:

![alt text](https://i.imgur.com/TYqw96Y.png "Logo Title Text XXX")

Diese Adresse gehört einem Vorstandsmitglied der Firma und ist deshalb auf einer Spam-Liste gelandet. Wirklich interessant wäre für mich allerdings nur ein Fund, wie etwa dieser hier:

![alt text](https://i.imgur.com/XpM6noX.png "Logo Title Text XXX")

Ich habe exemplarisch eine alte Adresse von mir selbst verwendet. Diese taucht in 2 großen Dataleaks der Vergangenheit auf. Die Dumps dieser Leaks kann man mit etwas Ausdauer oft schnell im Internet aufstöbern und über Torrent runterladen. Mit etwas Glück kann man zu der E-Mail Adresse einen Passworthash oder gar ein Passwort im Klartext finden. Und oft sind diese immer noch aktuell...

So ein Fund wäre natürlich besonders interessant, wenn es um einen Firmenaccount geht.

Würde ich jetzt mit Atoss tatsächlich Böses im Schilde führen, würde ich meine Suche vermutlich auf einige der höherrangigen Mitarbeiter aus dem Inspy-Fund konzentrieren und zu versuchen über deren private Webprofile an weitere Informationen zu gelangen. Angriffsvektoren gäbe es hier unzählige.

### Abschluss

Leider habe ich diesen ersten Teil erst als letzten begonnen um zuerst die technischeren Aufgaben in den Griff zu bekommen, daher war meine Zeit für die OSINT-Recherchen äußerst begrenzt. Doch es ist ein äußerst interessantes Gebiet und ich bin fasziniert bei den Angebot an frei verfügbaren Tools. Mit Maltego werde ich mich in Zukunft definitiv noch näher beschäftigen. 


# 2. Active Information Gathering - Nmap

In dieser Aufgabe werden aktiv Informationen über die Domäne scanme.nmap.org gesammelt. Dies erfolgt mittels der Commandline-Utilty nmap, welche alle Arten von Portscans ermöglicht.

### Prozessablauf

Dabei sieht der Prozessablauf folgendermaßen aus:

1. Detect Live Hosts & Ports
2. Service Enumeration
3. OS Detection

Zunächst wollen wir also sämtliche Live Hosts des Zieles ausfindig machen, sowie die geöffneten Ports. Anschließend sollen die laufenden Services augezählt werden und schlussendlich soll eruiert werden, welches Betriebssystem auf der jeweiligen Zielmaschine läuft.

### Kommandos

Zunächst wird das wahrscheinlich meistgenützte nmap Kommando "nmap -sP target/[subnet]" ausgeführt (alternativ kann dasselbe Kommando mit dem -sn Schalter ausgeführt werden). Dadurch, dass ein Subnet angegeben wird, listet nmap sämtliche Live Hosts eines Subnetzes für das Target:

```
nmap -sP scanme.nmap.org/24

Starting Nmap 7.12 ( https://nmap.org ) at 2018-01-30 17:18 CET
Nmap scan report for li982-11.members.linode.com (45.33.32.11)
Host is up (0.39s latency).
Nmap scan report for li982-12.members.linode.com (45.33.32.12)
Host is up (0.38s latency).
Nmap scan report for li982-16.members.linode.com (45.33.32.16)
Host is up (0.39s latency).
Nmap scan report for li982-17.members.linode.com (45.33.32.17)
Host is up (0.39s latency).
Nmap scan report for li982-24.members.linode.com (45.33.32.24)
Host is up (0.39s latency).
Nmap scan report for li982-25.members.linode.com (45.33.32.25)
Host is up (0.39s latency).
Nmap scan report for li982-27.members.linode.com (45.33.32.27)
Host is up (0.39s latency).
Nmap scan report for li982-28.members.linode.com (45.33.32.28)
Host is up (0.40s latency).
Nmap scan report for li982-30.members.linode.com (45.33.32.30)
Host is up (0.39s latency).
Nmap scan report for li982-32.members.linode.com (45.33.32.32)
Host is up (0.40s latency).
Nmap scan report for li982-42.members.linode.com (45.33.32.42)
Host is up (0.40s latency).
Nmap scan report for li982-43.members.linode.com (45.33.32.43)
Host is up (0.40s latency).
Nmap scan report for li982-45.members.linode.com (45.33.32.45)
Host is up (0.38s latency).
Nmap scan report for sjpar.com (45.33.32.47)
Host is up (0.39s latency).
Nmap scan report for li982-48.members.linode.com (45.33.32.48)
Host is up (0.40s latency).
Nmap scan report for li982-59.members.linode.com (45.33.32.59)
Host is up (0.39s latency).
Nmap scan report for li982-62.members.linode.com (45.33.32.62)
Host is up (0.40s latency).
Nmap scan report for mail.lovershot.info (45.33.32.73)
Host is up (0.39s latency).
Nmap scan report for li982-74.members.linode.com (45.33.32.74)
Host is up (0.40s latency).
Nmap scan report for li982-75.members.linode.com (45.33.32.75)
Host is up (0.40s latency).
Nmap scan report for li982-88.members.linode.com (45.33.32.88)
Host is up (0.40s latency).
Nmap scan report for li982-90.members.linode.com (45.33.32.90)
Host is up (0.40s latency).
Nmap scan report for li982-91.members.linode.com (45.33.32.91)
Host is up (0.57s latency).
Nmap scan report for li982-92.members.linode.com (45.33.32.92)
Host is up (0.57s latency).
Nmap scan report for li982-94.members.linode.com (45.33.32.94)
Host is up (0.57s latency).
Nmap scan report for li982-95.members.linode.com (45.33.32.95)
Host is up (0.57s latency).
Nmap scan report for li982-96.members.linode.com (45.33.32.96)
Host is up (0.57s latency).
Nmap scan report for li982-98.members.linode.com (45.33.32.98)
Host is up (0.57s latency).
Nmap scan report for li982-99.members.linode.com (45.33.32.99)
Host is up (0.57s latency).
Nmap scan report for li982-100.members.linode.com (45.33.32.100)
Host is up (0.57s latency).
Nmap scan report for promo.trailerpark.com (45.33.32.101)
Host is up (0.40s latency).
Nmap scan report for li982-102.members.linode.com (45.33.32.102)
Host is up (0.40s latency).
Nmap scan report for mail.fluid-code.com (45.33.32.103)
Host is up (0.19s latency).
Nmap scan report for li982-104.members.linode.com (45.33.32.104)
Host is up (0.19s latency).
Nmap scan report for li982-105.members.linode.com (45.33.32.105)
Host is up (0.19s latency).
Nmap scan report for li982-106.members.linode.com (45.33.32.106)
Host is up (0.20s latency).
Nmap scan report for li982-107.members.linode.com (45.33.32.107)
Host is up (0.20s latency).
Nmap scan report for li982-108.members.linode.com (45.33.32.108)
Host is up (0.20s latency).
Nmap scan report for li982-110.members.linode.com (45.33.32.110)
Host is up (0.20s latency).
Nmap scan report for li982-111.members.linode.com (45.33.32.111)
Host is up (0.20s latency).
Nmap scan report for gndevinfo.pjtsu.com (45.33.32.112)
Host is up (0.20s latency).
Nmap scan report for li982-113.members.linode.com (45.33.32.113)
Host is up (0.20s latency).
Nmap scan report for li982-117.members.linode.com (45.33.32.117)
Host is up (0.20s latency).
Nmap scan report for li982-118.members.linode.com (45.33.32.118)
Host is up (0.20s latency).
Nmap scan report for li982-119.members.linode.com (45.33.32.119)
Host is up (0.20s latency).
Nmap scan report for server.purplecowwebsites.com (45.33.32.120)
Host is up (0.20s latency).
Nmap scan report for li982-121.members.linode.com (45.33.32.121)
Host is up (0.20s latency).
Nmap scan report for cloud.mjitec.com (45.33.32.122)
Host is up (0.20s latency).
Nmap scan report for li982-123.members.linode.com (45.33.32.123)
Host is up (0.20s latency).
Nmap scan report for li982-124.members.linode.com (45.33.32.124)
Host is up (0.20s latency).
Nmap scan report for li982-125.members.linode.com (45.33.32.125)
Host is up (0.20s latency).
Nmap scan report for li982-126.members.linode.com (45.33.32.126)
Host is up (0.20s latency).
Nmap scan report for li982-127.members.linode.com (45.33.32.127)
Host is up (0.20s latency).
Nmap scan report for li982-128.members.linode.com (45.33.32.128)
Host is up (0.20s latency).
Nmap scan report for li982-130.members.linode.com (45.33.32.130)
Host is up (0.20s latency).
Nmap scan report for loyalcougars.com (45.33.32.132)
Host is up (0.20s latency).
Nmap scan report for li982-133.members.linode.com (45.33.32.133)
Host is up (0.20s latency).
Nmap scan report for li982-136.members.linode.com (45.33.32.136)
Host is up (0.20s latency).
Nmap scan report for li982-137.members.linode.com (45.33.32.137)
Host is up (0.20s latency).
Nmap scan report for li982-138.members.linode.com (45.33.32.138)
Host is up (0.20s latency).
Nmap scan report for li982-139.members.linode.com (45.33.32.139)
Host is up (0.20s latency).
Nmap scan report for li982-140.members.linode.com (45.33.32.140)
Host is up (0.20s latency).
Nmap scan report for li982-141.members.linode.com (45.33.32.141)
Host is up (0.20s latency).
Nmap scan report for li982-142.members.linode.com (45.33.32.142)
Host is up (0.20s latency).
Nmap scan report for li982-144.members.linode.com (45.33.32.144)
Host is up (0.20s latency).
Nmap scan report for li982-145.members.linode.com (45.33.32.145)
Host is up (0.20s latency).
Nmap scan report for algicorp.net (45.33.32.147)
Host is up (0.20s latency).
Nmap scan report for li982-148.members.linode.com (45.33.32.148)
Host is up (0.20s latency).
Nmap scan report for li982-149.members.linode.com (45.33.32.149)
Host is up (0.20s latency).
Nmap scan report for li982-150.members.linode.com (45.33.32.150)
Host is up (0.20s latency).
Nmap scan report for li982-151.members.linode.com (45.33.32.151)
Host is up (0.20s latency).
Nmap scan report for li982-153.members.linode.com (45.33.32.153)
Host is up (0.40s latency).
Nmap scan report for li982-154.members.linode.com (45.33.32.154)
Host is up (0.40s latency).
Nmap scan report for li982-155.members.linode.com (45.33.32.155)
Host is up (0.20s latency).
Nmap scan report for scanme.nmap.org (45.33.32.156)
Host is up (0.20s latency).
Other addresses for scanme.nmap.org (not scanned): 2600:3c01::f03c:91ff:fe18:bb2f
Nmap scan report for li982-157.members.linode.com (45.33.32.157)
Host is up (0.20s latency).
Nmap scan report for li982-158.members.linode.com (45.33.32.158)
Host is up (0.20s latency).
Nmap scan report for li982-160.members.linode.com (45.33.32.160)
Host is up (0.20s latency).
Nmap scan report for li982-162.members.linode.com (45.33.32.162)
Host is up (0.20s latency).
Nmap scan report for tls1903.thrivelife.com (45.33.32.163)
Host is up (0.20s latency).
Nmap scan report for li982-164.members.linode.com (45.33.32.164)
Host is up (0.20s latency).
Nmap scan report for snow.fromagique.com (45.33.32.165)
Host is up (0.20s latency).
Nmap scan report for li982-168.members.linode.com (45.33.32.168)
Host is up (0.20s latency).
Nmap scan report for li982-170.members.linode.com (45.33.32.170)
Host is up (0.20s latency).
Nmap scan report for li982-171.members.linode.com (45.33.32.171)
Host is up (0.20s latency).
Nmap scan report for li982-172.members.linode.com (45.33.32.172)
Host is up (0.20s latency).
Nmap scan report for li982-173.members.linode.com (45.33.32.173)
Host is up (0.20s latency).
Nmap scan report for box.shopjessicabuurman.com (45.33.32.174)
Host is up (0.20s latency).
Nmap scan report for li982-175.members.linode.com (45.33.32.175)
Host is up (0.20s latency).
Nmap scan report for li982-177.members.linode.com (45.33.32.177)
Host is up (0.20s latency).
Nmap scan report for li982-179.members.linode.com (45.33.32.179)
Host is up (0.22s latency).
Nmap scan report for li982-180.members.linode.com (45.33.32.180)
Host is up (0.40s latency).
Nmap scan report for li982-184.members.linode.com (45.33.32.184)
Host is up (0.40s latency).
Nmap scan report for li982-187.members.linode.com (45.33.32.187)
Host is up (0.39s latency).
Nmap scan report for li982-188.members.linode.com (45.33.32.188)
Host is up (0.39s latency).
Nmap scan report for li982-205.members.linode.com (45.33.32.205)
Host is up (0.40s latency).
Nmap scan report for kqq.gumleaf.org (45.33.32.206)
Host is up (0.40s latency).
Nmap scan report for li982-207.members.linode.com (45.33.32.207)
Host is up (0.40s latency).
Nmap scan report for li982-209.members.linode.com (45.33.32.209)
Host is up (0.39s latency).
Nmap scan report for li982-211.members.linode.com (45.33.32.211)
Host is up (0.39s latency).
Nmap scan report for li982-213.members.linode.com (45.33.32.213)
Host is up (0.38s latency).
Nmap scan report for li982-214.members.linode.com (45.33.32.214)
Host is up (0.39s latency).
Nmap scan report for li982-215.members.linode.com (45.33.32.215)
Host is up (0.39s latency).
Nmap scan report for li982-216.members.linode.com (45.33.32.216)
Host is up (0.39s latency).
Nmap scan report for li982-217.members.linode.com (45.33.32.217)
Host is up (0.40s latency).
Nmap scan report for ruby1.upspringhosting.net (45.33.32.220)
Host is up (0.40s latency).
Nmap scan report for li982-221.members.linode.com (45.33.32.221)
Host is up (0.38s latency).
Nmap scan report for li982-222.members.linode.com (45.33.32.222)
Host is up (0.38s latency).
Nmap scan report for altswagsvr.altswag.com (45.33.32.223)
Host is up (0.38s latency).
Nmap scan report for li982-224.members.linode.com (45.33.32.224)
Host is up (0.38s latency).
Nmap scan report for li982-225.members.linode.com (45.33.32.225)
Host is up (0.39s latency).
Nmap scan report for li982-226.members.linode.com (45.33.32.226)
Host is up (0.39s latency).
Nmap scan report for li982-227.members.linode.com (45.33.32.227)
Host is up (0.39s latency).
Nmap scan report for li982-228.members.linode.com (45.33.32.228)
Host is up (0.39s latency).
Nmap scan report for li982-232.members.linode.com (45.33.32.232)
Host is up (0.38s latency).
Nmap scan report for bare-blaster-2.bemsblaster.com (45.33.32.234)
Host is up (0.39s latency).
Nmap scan report for li982-235.members.linode.com (45.33.32.235)
Host is up (0.40s latency).
Nmap scan report for li982-236.members.linode.com (45.33.32.236)
Host is up (0.38s latency).
Nmap scan report for li982-239.members.linode.com (45.33.32.239)
Host is up (0.38s latency).
Nmap scan report for li982-240.members.linode.com (45.33.32.240)
Host is up (0.39s latency).
Nmap scan report for li982-241.members.linode.com (45.33.32.241)
Host is up (0.38s latency).
Nmap scan report for li982-242.members.linode.com (45.33.32.242)
Host is up (0.39s latency).
Nmap scan report for li982-243.members.linode.com (45.33.32.243)
Host is up (0.39s latency).
Nmap scan report for li982-245.members.linode.com (45.33.32.245)
Host is up (0.39s latency).
Nmap scan report for li982-247.members.linode.com (45.33.32.247)
Host is up (0.39s latency).
Nmap scan report for li982-248.members.linode.com (45.33.32.248)
Host is up (0.39s latency).
Nmap scan report for li982-249.members.linode.com (45.33.32.249)
Host is up (0.39s latency).
Nmap scan report for li982-250.members.linode.com (45.33.32.250)
Host is up (0.39s latency).
Nmap scan report for li982-251.members.linode.com (45.33.32.251)
Host is up (0.39s latency).
Nmap scan report for li982-252.members.linode.com (45.33.32.252)
Host is up (0.39s latency).
Nmap done: 256 IP addresses (130 hosts up) scanned in 21.58 seconds
```
Nmap hat alle 256 Adressen des Subnetzes gescannt und gibt aus, dass 130 Hosts online sind. Auch Adressen der einzelnen Hosts werden angegeben.

Der hier mit aufgeführte Host 45.33.32.156 ist der Host, dessen Ip-Adresse der Doman scanme.nmap.org entspricht. Wird das obige Kommando erneut ohne Angabe des Subnetzes ausgeführt, wird nur dieser Host ausgegeben. Die weiteren Scans sollen für diesen Host erfolgen:

```
nmap -sP scanme.nmap.org

Starting Nmap 7.12 ( https://nmap.org ) at 2018-01-30 15:16 CET
Nmap scan report for scanme.nmap.org (45.33.32.156)
Host is up (0.15s latency).
Other addresses for scanme.nmap.org (not scanned): 2600:3c01::f03c:91ff:fe18:bb2f
Nmap done: 1 IP address (1 host up) scanned in 0.31 seconds
```

Nun sollen die geöffneten Ports des Hosts gefunden werden. Mit dem Kommando "nmap -p 1-65535 -sV -sS -T4 scanme.nmap.org" wird dabei ein Scan auf sämtliche Ports (Schalter -p, Ports 1 - 65535) des Zielhosts ausgeührt. Der -sV Schalter Information über den Service bzw. die Version des Services auf dem jeweiligen Port zu erhalten.

Das Kommando erfordert Root-Privilegien.

```
nmap -p 1-65535 -sV -sS -T4 scanme.nmap.org

Starting Nmap 7.12 ( https://nmap.org ) at 2018-01-30 15:18 CET
Nmap scan report for scanme.nmap.org (45.33.32.156)
Host is up (0.15s latency).
Other addresses for scanme.nmap.org (not scanned): 2600:3c01::f03c:91ff:fe18:bb2f
Not shown: 65530 closed ports
PORT      STATE    SERVICE    VERSION
22/tcp    open     ssh        OpenSSH 6.6.1p1 Ubuntu 2ubuntu2.10 (Ubuntu Linux; protocol 2.0)
80/tcp    open     http       Apache httpd 2.4.7 ((Ubuntu))
515/tcp   filtered printer
9929/tcp  open     nping-echo Nping echo
31337/tcp open     tcpwrapped
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 364.14 seconds
```

Wie sich dem Output entnehmen lässt, sind die Ports 22 und 80 für SSH respektive HTTP Verbindungen geöffent. Weiters sind die Ports 9929 und 31337 mit den Services Nping-echo bzw. tcpwrapped offen.

```
nmap -v scanme.nmap.org

Starting Nmap 7.12 ( https://nmap.org ) at 2018-01-30 15:43 CET
Initiating Ping Scan at 15:43
Scanning scanme.nmap.org (45.33.32.156) [4 ports]
Completed Ping Scan at 15:43, 0.17s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 15:43
Completed Parallel DNS resolution of 1 host. at 15:43, 0.01s elapsed
Initiating SYN Stealth Scan at 15:43
Scanning scanme.nmap.org (45.33.32.156) [1000 ports]
Discovered open port 22/tcp on 45.33.32.156
Discovered open port 80/tcp on 45.33.32.156
Discovered open port 9929/tcp on 45.33.32.156
Discovered open port 31337/tcp on 45.33.32.156
Completed SYN Stealth Scan at 15:43, 6.90s elapsed (1000 total ports)
Nmap scan report for scanme.nmap.org (45.33.32.156)
Host is up (0.15s latency).
Other addresses for scanme.nmap.org (not scanned): 2600:3c01::f03c:91ff:fe18:bb2f
Not shown: 995 closed ports
PORT      STATE    SERVICE
22/tcp    open     ssh
80/tcp    open     http
515/tcp   filtered printer
9929/tcp  open     nping-echo
31337/tcp open     Elite

Read data files from: /usr/local/bin/../share/nmap
Nmap done: 1 IP address (1 host up) scanned in 7.17 seconds
```
```
nmap -sS -O scanme.nmap.org/24
```

Wir gehen über zur OS-Detection, also zur Erkennung des Betriebssystemes auf dem Host. Dazu wird das Kommando "sudo nmap -O scanme.nmap.org" verwendet.


```
sudo nmap -O scanme.nmap.org

Starting Nmap 7.12 ( https://nmap.org ) at 2018-01-30 21:11 CET
Nmap scan report for scanme.nmap.org (45.33.32.156)
Host is up (0.32s latency).
Other addresses for scanme.nmap.org (not scanned): 2600:3c01::f03c:91ff:fe18:bb2f
Not shown: 995 closed ports
PORT      STATE    SERVICE
22/tcp    open     ssh
25/tcp    filtered smtp
80/tcp    open     http
9929/tcp  open     nping-echo
31337/tcp open     Elite
Aggressive OS guesses: Linux 4.0 (93%), Linux 3.11 - 4.1 (92%), Linux 2.6.32 (92%), Linux 2.6.32 or 3.10 (91%), Linux 3.12 (91%), Linux 2.6.32 - 2.6.35 (90%), Linux 3.13 (90%), Linux 2.6.32 - 2.6.39 (90%), Linux 3.10 - 4.1 (89%), Linux 3.2 - 3.8 (89%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 12 hops

OS detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 48.39 seconds
```

Der Scan kann keine Auskunft mit 100%-iger Sicherheit geben jedoch dürfte auf dem Host jedenfalls ein Linux-Betriebssystem laufen.

# 3. Patch

In dieser Aufgabe soll ein Programm so gepatcht werden, dass bei der Abfrage des Nutzernamens und des Passworts jede beliebige Kombination zu einem erfolgreichen Login führt.

### System:
* MacOS Host-System
* Kali Linux (32bit - Offsec Distribution)
* Windows XP (32bit, SP3)

### Software:
* IDA Pro Debugger

### Vorbereitungen

Zunächst wird die Binary "Passcheck" in die Kali-VM überspielt. Mit dem Befehl 
```
chmod +x passcheck
```

wird die Datei ausführbar gemacht und kann anschließend mit 
```
./passcheck aaaaaaaa bbbbbbbb
```

gestartet werden. "aaaaaaaa" und "bbbbbbbb" sind beliebig angenommene Eingabeparameter und stellen den Nutzernamen und das Passwort dar.


![alt text](https://i.imgur.com/OQjzymO.png "Logo Title Text XXX")

Diese Kombination von Nutzernamen und Passwort wird vom Programm nicht als richtig anerkannt, daher erfolgt die Ausgabe, die in der obigen Abbildunng zu sehen ist.

### Debugging mit IDA-Pro einrichten

Um das Programm batchen zu können, muss die Vergleichsfunktion, welche die Eingabewerte auf ihre Richtigkeit überprüft, gefunden und ausgehebelt werden. Da der Source Code des Programms nicht vorliegt, muss das Binary File selbst debuggt werden.

Für den Debugging-Prozess wurde der Debugger IDA Pro ausgewählt. Dieser läuft unter Windows. Bei "passcheck" handelt es sich allerdings um eine Linux-Binary. Praktischerweise bietet IDA Pro eine Funktion, welche die Installation eines Remote-Servers auf einer Linuxmaschine erlaubt, womit in einem Zusammenspiel aus der Windows- und der Kali-VM die Software auch mit IDA Pro problemlos debuggt werden kann.

Dazu wird der das Linux-Server File, dass mit IDA Pro mitgeliefert wird, in die Kali VM übertragen, dort ausführbar gemacht und gestartet.

![alt text](https://i.imgur.com/1uBMjt5.png "Logo Title Text XXX")

Nach dem Import einiger Library-Dateien in die Windows VM, welche von IDA verlangt werden, kann die Verbindung bereits hergestellt werden.

Im IDA Debugger wird die Verbindung zum Remote-Host eingerichtet, außerdem müssen Nutzername und Passwort ebenfalls als Argumente mitgeliefert werden:

![alt text](https://i.imgur.com/BwJNCE5.png "Logo Title Text XXX")


Ein erster Testdurchlauf mit dem IDA-Debugger zeigt, dass der Debug-Vorgang erfolgreich durchgeführt werden kann. Da in diesem Fall keine Breakpoints gesetzt wurden, läuft das Programm einmal normal durch und wird danach beendet:

![alt text](https://i.imgur.com/ubefyZP.png "Logo Title Text XXX")

### Vergleichfsfunktion(en) finden

Da Ausgabestrings des Programms immer einen guten ersten Anhaltspunkt bei der Suche nach spezifischer Funktionalität bieten wird zunächst nach dem String "Cookies", der einen Substring der Ausgabe nach erfolglosem Login darstellt, durchgeführt. Mit "Alt + T" wird die Stringsuche aufgerufen:

![alt text](https://i.imgur.com/FGHNs58.png "Logo Title Text XXX")

Diese liefert uns auch gleich einen Treffer:

![alt text](https://i.imgur.com/3zlTpJR.png "Logo Title Text XXX")

Bei kurzer Inspektion der darüberliegenden Instruktionen zeigt sich, dass tatsächlich ein Vergleich (Assembler Instruktion: "cmp") zweier Low Byte Register (AL und DL) stattfindet:

![alt text](https://i.imgur.com/31x4njl.png "Logo Title Text XXX") 

Eines dieser Register enthält den erwarteten Wert aus dem intern generierten Passwort (der Prozess der Passwort-Generierung wird in Aufgabe 4 näher beleuchtet und daher hier nicht weiter besprochen). Das zweite Register enthält den Input Wert. Wenn diese beiden Werte nicht übereinstimmen, dann wird das Zero-Flag auf 0 bleiben, was als einem falschen Ergebnis einer Wahrheitswertabfrage enspricht. Es werden Breakpoints bei der "cmp" sowie der "jz" Instruktion gesetzt und das Programm wird einmal durchlaufen:

![alt text](https://i.imgur.com/4kLuVeF.png "Logo Title Text XXX")

Ein Blick auf das Zero-Flag zeigt, dass sich die obige Annahme bestätigt, der Wert ist nach dem Vergleich weiterhin auf null:

![alt text](https://i.imgur.com/jSFTl9E.png "Logo Title Text XXX")

Bevor die Lösung implementiert wird, wird zunächst noch nach weiteren Vorkommnissen einer Comparefunktion im Zusammenhang mit dem String gesucht. Immerhin ist es gut möglich, dass mehr als eine Vergleichsabfrage im Programm erfolgt. Dazu erfolgt ein Klick auf den "Cookies"-String und die Auswahl der Funktion "Jump to xref to operand...", welche eine Quersuche nach dem String durchführt.

![alt text](https://i.imgur.com/1B8Uies.png "Logo Title Text XXX")

Die Suche zeigt, dass der String in der Tat an einer weiteren Stelle auftaucht und auch hier gibt es wieder eine zugehörige Compare-Funktion:

![alt text](https://i.imgur.com/Ys1KyMD.png "Logo Title Text XXX")

![alt text](https://i.imgur.com/Ci50JaW.png "Logo Title Text XXX")

Nun wurden alle Vorkommnise der Vergleichsfunktion gefunden und der Patch kann erfolgen.

### Patch des Programmes

Die einfachste Lösung, um das Programm so zu patchen, dass jeder Inputwert akzeptiert wird, ist also einfach diesen Vergleich auszuheben. IDA-Pro bietet die Möglichkeit direkt zu patchen. Dazu wird die erste Vergleichsinstruktion markiert und anschließend das Menü "Edit -> Patch program: -> Assemble..." ausgewählt: 

![alt text](https://i.imgur.com/pEF4kEC.png "Logo Title Text XXX")

Im folgenden Bearbeitungsfenster wird die Instruktion noch einmal angezeigt und kann verändert werden:

![alt text](https://i.imgur.com/p8Mvpfc.png "Logo Title Text XXX")

Es wird lediglich das zweite Vergleichsregister auf das erste geändert. Dadurch ist gewährleistet, dass der Vergleich immer auf einen positiven Wahrheitswert evaluieren wird:

![alt text](https://i.imgur.com/Vcl8w1M.png "Logo Title Text XXX")

Dieselbe Änderung wird analog bei der zweiten Vergleichsstelle durchgeführt und anschließend wird ein Patchfile des Programms mit "Edit -> Patch program: -> Apply patches to input file..." erzeugt.

![alt text](https://i.imgur.com/ipO78yt.png "Logo Title Text XXX")

In diesem Setup kann das Originalfile auf der Kali-VM nicht direkt vom IDA-Debugger aus überschrieben werden, da die beiden VMs über keinen geteilten Ordner verfügen. Daher wird die neue Datei mittels WinScp auf die Kali-VM übertragen um dort die alte Datei zu überschreiben:

![alt text](https://i.imgur.com/Rs7LubP.png "Logo Title Text XXX")

![alt text](https://i.imgur.com/2LHSFPZ.png "Logo Title Text XXX")

Ein erneuter Durchlauf des Programms auf der Kali VM, zeigt, dass der Patch erfolgreich war:

![alt text](https://i.imgur.com/HwzG9pm.png "Logo Title Text XXX")

Ein erneuter Test mit zufällig gewählten Parametern auf der Kommandozeile zeigt, dass in der Tat jeder Eingabewert akzeptiert wird:

![alt text](https://i.imgur.com/MFNzlRv.png "Logo Title Text XXX")


# 4. Keygen

In der vierten Aufgabe soll nun für die ursprüngliche (also ungepatchte) Applikation aus Aufgabe Nr. 3 ein Keygenerator programmiert werden, welcher zu jedem beliebigen Nutzernamen das passende Passwort erzeugt.

Dazu muss der Sourcecode genauer analysiert werden um herauszufinden, wie das Passwort in der ursprünglichen Applikation generiert wird.

### Vorbereitung und Setup

Die Analyse erfolgt mit demselben Setup, dass bereits für Punkt 3 verwendet wurde. IDA Pro debuggt das File erneut mit Hilfe der Remote Kali-VM.

Nachdem die ursprünglichen "Passcheck"-Dateien auf beiden Maschinen wiederhergestellt wurden und ein Testdurchlauf des Debug Prozesses stattgefunden hat, kann die Analyse auch schon beginnen.

Wenn der genaue Erstellprozess des Passworts im Programm gefunden wurde, dann gilt es diesen mit einer beliebigen Programmiersprache abzubilden und ein Programm zu schreiben, welches als Input lediglich den Nutzernamen nimmt und das dazu passende Passwort für "Passcheck" erzeugt.

### Analyse

Zunächst wird erneut mittels Stringsuche der Code nahe des ersten "Cookie" strings aufgerufen:

![alt text](https://i.imgur.com/UOFNil5.png "Logo Title Text XXX")

Nachdem ich in dem Hausübungsszenario immer noch verkatert bin versuche ich als ökonmisch denkender Möchtegernhacker mir die Codeanalyse so einfach wie nur möglich zu machen, und nutze daher IDA's Funktion zur Erzeugung von Pseudo C-Code.

Dazu muss einfach nur die Tabulator Taste in der aktuellen Ansicht gedrückt werden und schon zeigt der Debugger C-Code an, welcher aus den Assembler Instruktionen abgeleitet wurde:

![alt text](https://i.imgur.com/lWJTdkK.png "Logo Title Text XXX")

IDA-Pro bietet hier nun auch die Möglichkeit Variablennamen zu verändern. Zwecks besserem Verständnis werden ein paar der Variablennamen verändert und das Endergebnis wird dann in einen herkömmlichen Texteditor kopiert und analysiert:

~~~c
int __cdecl sub_439747(char *input_string, int a2)
{
  int result; // eax@17
  int v3; // [sp+Dh] [bp-1Bh]@1
  int v4; // [sp+11h] [bp-17h]@1
  __int16 v5; // [sp+15h] [bp-13h]@1
  char v6; // [sp+17h] [bp-11h]@1
  signed __int32 len_input; // [sp+18h] [bp-10h]@1
  signed __int32 i; // [sp+1Ch] [bp-Ch]@1

  v3 = 0;
  v4 = 0;
  v5 = 0;
  v6 = 0;
  len_input = strlen(input_string);
  for ( i = 0; i < len_input && i <= 8; ++i )
  {
    if ( i ) //wird in der ersten runde nicht ausgeführt, da i = 0
      *(genPW + i) = (*(genPW + i - 1) + 4 * input_string[i]) % 93 + 33;
    else
      LOBYTE(v3) = (*input_string + input_string[1]) % 93 + 33;
      // *input_string gibt den ersten char des input strings
      // Bei Eingabe evaluiert der Ausdruck in der Klammer auf 130, danach folgen die % 93 + 33 Operationen.
    if ( *(genPW + i) != *(_BYTE *)(i + a2) )
    {
      cmp_flag = 0;
      puts("Sorry, no cookies for skriptkiddies :)\nWette verloren, Du zahlst!");
      break;
    }
    cmp_flag = 1;
  }
  while ( 1 )
  {
    result = (unsigned __int8)cmp_flag;
    if ( cmp_flag != 1 )
      break;
    if ( i <= 9 )
      *(genPW + i) = (*(genPW + i - 1) + ((i + 65) << (10 - i))) % 93 + 33;
    if ( *(genPW + i) == *(_BYTE *)(i + a2) )
    {
      if ( i == 9 )
        return sub_4396A8();
    }
    else
    {
      cmp_flag = 0;
      puts("Sorry, no cookies for skriptkiddies :)\nWette verloren, Du zahlst!");
    }
    ++i;
  }
  return result;
}
~~~

Ein Blick auf diesen Pseudocode macht einige Dinge deutlich klarer. Beispielsweise habe ich in Aufgabe Nr. 3 noch nicht ganz verstanden, wieso der Compare zweimal stattfindet. Dies ist jetzt klar. Doch ich beginne besser von vorne.

Der gesamte oben abgebildete Code stellt eine Funktion dar und zwar die Funktion die gleichzeitig das Passwort generiert und den Eingabestring gegen dieses abprüft. Als Eingabeparameter erhält die Funktion den Eingabestring sowie eine Memoryadresse (int a2).

In der Funktion werden zwei Schleifen Durchlaufen. Die erste Schleife läuft entweder über 8 Durchgänge oder bis zur Länge des eingegebenen Nutzernamens. Im ersten Durchgang wird immer der Code im Else-Block ausgeführt (In C gibt es keine Boolean-Werte. Stattdessen werden Integer verwendet. Alles außer 0 evaluiert auf positiv. Daher wird für die Kondition einfach der Counter verwendet, der im ersten Durchgang auf 0 steht).
In diesem ersten Durchgang werden die Dezimalrepräsentationen der ersten beiden Zeichen des Nutzernamens addiert, anschließend wird der Restwert einer Division durch 93 mittels des Modulo-Operators berechnet und dann werden noch 33 dazu addiert.

Diese beiden zunächst eher willkürlich wirkenden Zahlen erfüllen klare Funktionen:

Die Modulo 93 Operation dient dazu, den generierten Zahlenwert innerhalb der 128 möglichen Stellen des ASCII Codes zu halten.

Die dazuaddierten 33 stellen danach sicher, dass das Resultat ein druckbares Zeichen repräsentiert, da die Zeichen 0 - 33 in ASCII nicht druckbare Zeichen, wie etwa ein Leerschritt oder Tabulator sind. 

Das Ergebnis wird in der Integer Variable v3 gespeichert, auf welche noch das Lowbyte-Makro angewandt wird, welche meinem Verständnis nach dazu führen sollte, dass nur das Lowbyte des Integers übernommen wird. Dies würde aber erst relevant wenn der Integer mehr als 1 Byte in Anspruch nehmen würde, also größer als 255 in Dezimal sei. Durch die Modulo Begrenzung kommt das aber nicht zu stande. Der letztendliche Schlüsselgenerator hat auch ohne diese Operation optimal funktioniert. Zu Lernzwecken habe ich die Lowbyte Funktion dann aber doch noch in Python abgebildet.

Ab dem zweiten Durchgang wird dann der Code unter dem ersten IF-Statement ausgeführt. Hier wird nun die zuletzt generierte des neuen Passworts herangezogen und mit dem vierfachen der aktuellen Position des Eingabestrings (in Dezimal) addiert. Auf dies wird dann erneut der Modulo 93 sowie die Addition um 33 angewandt.

Auf diese Art werden die Stellen 2 - 7 des Passwortes generiert (sofern die Nutzername die entsprechende Länge aufweist, ansonsten erfolgt bereits früher der Sprung in die zweite Schleife).

Am Ende der Schleife wird Zeichen für Zeichen überprüft, ob das generierte Zeichen mit dem entsprechenden Zeichen des Input-Strings übereinstimmt. Wenn dies nicht der fall ist, wird die Compare-Flag auf einen negativen werd gesetzt und die Schleife wird gebrochen.

In der zweiten Schleife nun werden die restlichen Zeichen erzeugt, da das Passwort eine feste Länge von 10 Zeichen hat. Hier erklärt sich nun auch der zweite Vergleich im Quellcode. Wenn bereits der erste Vergleich ein negatives Ergebnis geliert hat, wird diese Schleife gleich zu Beginn gebrochen. Ansonsten werden die restlichen Zeichen erzeugt indem erneut der Dezimalwert des Zeichens der letzten Runde des Passworts herangezogen wird. Dieser wird anschließend mit dem um 65 erhöhten Counter, auf den eine Linksbitverschiebung des von 10 dezimierten Counters angewandt wurde addiert. Diese Linksbitverschiebung multipliziert die Zahl um den Faktor 2 und zwar für jede der Stellen. So würde beispielsweise ein Verschiebung 2 << 2 die Zahl 8 ergeben (Binär: 2 -> 10, 8 -> 1000). Die 1 wurde binär um 2 Stellen nach links verschoben.

### Skript und Proof of Concept

Am Ende wurde das folgende Python-Skript generiert um die Passworterstellung aus dem Originalcode abzubilden.

~~~python
uname = ""

'''
This represents the LOWBYTE macro
'''
def bytes(integer):
    return divmod(integer, 0x100)

'''
Prompt for the desired Username. 
The target program will always default to a negative response 
when the username is not at least 2 characters long.
'''
while len(uname) < 2:
    print("Welcome, please enter a username with at least 2 characters")
    uname = input("Please enter the username: ")

#Fixed values:
'''These values help to ensure that the generated password 
will stay within the range of printable ASCII characters'''

first_printable = 33
bounds = 93

output_pw = ''

i = 0
''' The following code just translates the password 
creation from the original program to python code'''
while i < 8 and i < len(uname):
    if i > 0:
        high, low = bytes((ord(output_pw[i-1]) + 4 * ord(uname[i])) 
                          % bounds + first_printable)
        output_pw += chr(low)
        i += 1
    else:
        high, low = bytes((ord(uname[i]) + ord(uname[i+1])) 
                          % bounds + first_printable)
        output_pw += chr(low)
        i += 1

while i < 10:
    high, low = bytes((ord(output_pw[i-1]) + ((i + 65) << (10 - i))) 
                      % bounds + first_printable)
    output_pw += chr(low)
    i += 1

print("Your password is: {}".format(output_pw))
~~~

Ein Testdurchlauf zeigt, dass das Skript funktioniert. Zuerst wird das neue Programm auf der Kommandozeile gestartet und ein beliebiger Nutzername als Input übergeben.

![alt text](https://i.imgur.com/HAWAxFk.png "Logo Title Text XXX")

Anschließend wird das Urprogramm erneut in der Kali-VM ausgeführt. Derselbe Nutzername und das generierte Passwort führen zu einem erfolgreichen Login:

![alt text](https://i.imgur.com/6R8VUto.png "Logo Title Text XXX")

