---
author: "Marlena Müller"
title: "Erste Erfahrungen mit Uberspace"
date: 2024-07-07T00:00:00+02:00
description: ""
tags: [
    "Uberspace", "Webhosting", "Erfahrungen", "Mailserver"
]
summary: "Meine ersten Erfahrungen mit Uberspace und dem Einrichten eines Mailservers und von statischen Webseiten."
---

Seit einigen Tagen habe ich einen Account bei [Uberspace](https://uberspace.de/). Uberspace ist ein Hosting-Anbieter, mit innovativen Konzept. Bei Uberspace gibt es ein "Pay what you want" Modell. Das bedeutet, dass man einen beliebigen Betrag ab 5€ pro Monat zahlen kann (theoretisch kann man auch ab 1€ zahlen, was jedoch nicht empfohlen wird). Uberspace empfiehlt 10€ pro Monat zu zahlen, damit die Kosten gedeckt sind und Uberspace weiter existieren kann. Damit Uberspace auch die geringen Beträge annehmen kann, gibt es ein "Solidaritätsmodell". Das bedeutet, dass diejenigen, die mehr zahlen, diejenigen unterstützen, die weniger zahlen.

Um Uberspace ausprobieren zu können, ist der 1. Monat kostenlos. Aktuell bin ich in dem Testmonat und habe noch nicht bezahlt. Falls mein Testmonat gut verläuft, werde ich Uberspace weiter nutzen und auch bezahlen :)

Uberspace bietet viele Funktionen. Für mich sind hauptsächlich der Mailserver und das statische Webhosting interessant.

## Mailserver

Aktuell nutze ich [PurelyMail](https://purelymail.com/) als Mailprovider. PurelyMail ist ein sehr guter Mailprovider, der auch relativ günstig ist (10$/Jahr). Ich bin mit PurelyMail sehr zufrieden. Allerdings sitzt PurelyMail in den USA und ich möchte meine Mails lieber in Deutschland haben, insbesondere um eine bessere DSGVO-Konformität zu haben. Außerdem können US-Unternehmen gezwungen werden, Daten an die US-Regierung herauszugeben. Das möchte ich vermeiden. (Auch wenn ich nicht glaube, dass ich im Visier der US-Regierung bin :D)

Deshalb habe ich mich entschieden, einen Mailserver bei Uberspace einzurichten. Uberspace bietet einen Mailserver an, der sehr einfach einzurichten. Der Einrichtungsprozess ist sehr gut [dokumentiert](https://manual.uberspace.de/mail-access/). Daher werde ich hier nicht auf die Einrichtung eingehen.

Einzig zu beachten ist, dass die Mail-Befehle zur einrichtung etwas länger dauern (5-10 Sekunden). Das ist eigentlich kein Problem, hatte mich aber anfangs etwas irritiert.
Die Einrichtung von Mail-Domains und Accounts ist sehr einfach, jedoch muss dies über die Kommandozeile gemacht werden. Man muss kein Profi mit der Kommandozeile sein, die höchste Hürde ist die SSH-Verbindung. Das ist aber auch nicht schwer und sehr gut [dokumentiert](https://manual.uberspace.de/basics-ssh/).

Da ich vorher alle meine Mails bei PurelyMail hatte, musste ich alle Mails von PurelyMail zu Uberspace umziehen.
Hierfür habe ich mich an dem [Guide von Uberspace](https://lab.uberspace.de/howto_migrate-mails/) orientiert.

### Migration von PurelyMail zu Uberspace

Für den Umzug wird das Tool `imapsync` empfohlen. `imapsync` ist auf den "Asteroids" von Uberspace bereits installiert.

Für den Umzug von PurelyMail zu Uberspace habe ich folgenden Befehl verwendet:

:warning: **Achtung**: Ich nehme an, dass hier Uberspace in Version 7 verwendet wird. Wenn Uberspace Version 8 released wird, kann es sein, dass sich die Befehle ändern. Bitte informiere dich vorher.

```bash
imapsync \
    --logdir ~/logs/imapsync \
    --host1 imap.purelymail.com \
    --user1 <hier Mailadresse einfügen> \
    --ssl1 \
    --host2 localhost \
    --user2 <hier neue Mailadresse einfügen> \
    --ssl2
```

Zuerst hatte ich Probleme mit der Authentifizierung. Nach etwas rumprobieren, bin ich auf die Idee gekommen, ein Passwort zu verwenden, welches keine Sonderzeichen enthält. Das hat funktioniert. Danach habe ich einfach das Passwort wieder geändert.

Für die Migration von ~2000 Mails hat `imapsync` ca. 10 min gebraucht.

### Catch-All-Adresse

Uberspace bietet auch eine Catch-All-Adresse an. Das bedeutet, dass alle Mails, die nicht an eine spezifische Adresse gehen, an eine Adresse weitergeleitet werden.
Ich nutze dieses Feature, um für jeden Dienst eine eigene Mailadresse zu haben. So kann ich sehen, welcher Dienst meine Mailadresse weitergegeben hat, oder Dienste, die mich zuspammen, einfach blockieren.

Man muss Catch-All-Adressen über die Kommandozeile einrichten. Das ist ebenfalls in der [Dokumentation](https://manual.uberspace.de/mail-mailboxes/#catch-all-mailbox) beschrieben.

### Einschränkungen

Mail bei Uberspace hat einige Einschränkungen. Wenn man mehrere Domains einrichten möchte, sind die einzelnen Accounts auf alle Domains verteilt. Wenn man z.B. `example1.com` und `example2.com` hat und den account `info` einrichtet, dann erhält dieser Account Mails für `info@example1.com` und `info@example2.com`.
Für meinen Anwendungsfall ist das kein Problem, da ich alle Mails direkt in ein Postfach leite und nicht aufteile.

Was bei mir ein Problem war, dass für Subadressing nur das `+` Zeichen erlaubt ist. Vorher habe ich `_` verwendet. Das Problem kann einfach durch eine Sieve-Regel gelöst werden, ist jedoch etwas aufwändiger.

## Statisches Webhosting

Uberspace bietet auch gemangtes Webhosting an, um statische Webseiten zu hosten. 

Um das statische Webhosting zu nutzen, muss man nur die Webseite-Dateien in das Verzeichnis `/var/www/virtual/$USER/html` kopieren. `$USER` ist dabei der Benutzername. Die Webseite ist dann unter `https://$USER.uber.space` erreichbar.

Wenn man eigene Domains hinzufügen möchte, muss man nur die Domain in der Uberspace-Konsole hinzufügen und die DNS-Einträge anpassen (siehe [Dokumentation](https://manual.uberspace.de/web-domains/)). Die Webseite ist dann unter `https://$DOMAIN` erreichbar.
Möchte man, dass der Inhalt nur über eine Domain verfügbar ist, erstellt man den Ordner `/var/www/virtual/$USER/$DOMAIN/` und kopiert die Dateien dort hinein, anstatt in `/var/www/virtual/$USER/html`. Auf diesem Weg kann man auch mehrere Statische Webseiten auf einem Asteroiden hosten.
Uberspace rät hiervon aus Sicherheitsgründen ab. Wenn die Webseiten/Webanwendungen nicht voneinander getrennt sind, kann ein Angreifer auf eine Webseite auch auf die anderen Webseiten zugreifen, falls eine Sicherheitslücke in der zugrunge liegenden Software besteht. Da ich nur statische Webseiten hoste, ist das für mich kein Problem. Bei statischen Webseiten ist der einzige Angriffsvektor der Webserver selbst, welcher von Uberspace bereitgestellt wird und bei allen Asteroiden gleich ist. Also kann dann nur ein Angreifer auf alle Webseiten zugreifen, wenn er eine Sicherheitslücke in dem Webserver findet, dies kann er jedoch auch, wenn die Webseiten auf unterschiedlichen Asteroiden liegen.

## Fancy Sticker

Man kann von Uberspace auch Sticker bestellen. Ich mag Sticker, einer hat es schon auf meinen Laptop geschafft :D

![Uberspace Sticker](/images/uberspace/brief.jpeg)

![Uberspace Sticker auf Laptop](/images/uberspace/laptop.jpeg)

## Weiteres

Uberspace hat einen eigenen Charme. Die Dokumentation ist sehr gut ausgestaltet.

Weiterhin hat Uberspace eine eigene [Hausordnung](https://uberspace.de/de/about/houserules/). In dieser Hausordnung sieht man, dass Uberspace ein sehr offenes und tolerantes Unternehmen ist. Das gefällt mir sehr gut.

Außerdem kann man bei Uberspace einen Auftragsverarbeitungsvertrag (AVV) abschließen. Das ist sehr wichtig, falls man personenbezogene Daten verarbeitet. Das ist ein großer Pluspunkt für Uberspace.

Es gibt einige Community-Anleitungen in dem [Uberlab](https://lab.uberspace.de/). Diese Anleitungen sind von der Community für die Community. Beim überfliegen erwecken die Anleitungen einen sehr guten Eindruck, sie sind sehr ausführlich und gut geschrieben.

## Fazit

Mail funktioniert. Wenn einen die Einschränkungen nicht stören, ist Uberspace eine gute Alternative zu anderen Mailprovidern. Ob ich Mail dort langfristig nutzen werde, muss die Zeit zeigen. Wenn der Service weiter stabil und gut läuft, sehe ich keinen Grund zu einem anderen Mailprovider zu wechseln.

Das statische Webhosting funktioniert auch sehr gut. Ich bin sehr zufrieden.  Hier gilt das gleiche wie bei Mail: Wenn der Service stabil und gut bleibt, werde ich weitere Webseiten (z.B. diese) auf Uberspace hosten.
