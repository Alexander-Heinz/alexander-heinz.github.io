---
title: "Wie viele Zigaretten sind eine Flasche Berliner Luft?"
date: 2025-11-16
tags: [data engineering, terraform, dashboarding, looker, google cloud, bigquery, cloud bucket]
header:
  image: "/images/berliner-luft.jpg"
excerpt: "Wie ich dank Data Engineering Berliner Luftqualität intuitiv sichtbar machte."
mathjax: "true"
---

# Berliner Luft: Luftqualitätsdaten automatisiert verarbeiten und visualisieren

Vor einiger Zeit habe ich ein kompaktes Data Engineering-Projekt gebaut um meine Skills in Data Engineering, API-Handling und Cloud-Infrastruktur zu vertiefen. Ziel war es, Luftqualitätsdaten automatisch aus einer API abzurufen, sauber zu speichern und anschließend auszuwerten – inklusive einer kleinen Visualisierung, die Luftqualität intuitiv begreifbar macht. 
Ich freue mich sehr, dass das Dashboard auf Google Looker Studio bis heute automatisiert und robust läuft und ich jederzeit die Luftqualität in meiner Nähe ansehen kann:

[Hier klicken, um das Dashboard zu sehen](https://lookerstudio.google.com/reporting/dd051443-68b7-4ff7-84bb-29031462c339)

[Hier geht's zum Github Repo](https://github.com/Alexander-Heinz/Berliner-Luft)

Im Folgenden möchte ich das Projekt kurz beschreiben, um meine Learnings daraus festzuhalten:

## Revisionssichere Datenspeicherung: API-Daten nachvollziehbar ablegen

Ein Kernaspekt war für mich, die Daten **revisionssicher** abzulegen. Das bedeutet: Ich wollte jederzeit nachvollziehen können, **welche Rohdaten** zu einem bestimmten Zeitpunkt aus der API kamen – ohne dass etwas überschrieben oder „verloren“ geht.

Dafür speichere ich alle JSON-Antworten der API im Google Cloud Storage mit einem zeitbasierten Pfad (Datentyp/Datum/Timestamp). Jede Anfrage ist somit dokumentiert und die entsprechenden Rohdaten aus der API-Response reproduzierbar.

## Datenvalidierung: Nur saubere Daten kommen weiter

Bevor etwas in BigQuery landet, validiere ich die Rohdaten gegen ein vorher definiertes Schema. Ich prüfe zum Beispiel:

- Sind alle erwarteten Felder vorhanden?
- Haben die Werte die richtigen Datentypen? ("Schema")
- Stimmen Einheiten und Struktur?

Das sorgt dafür, dass die Daten in meinem Data Warehouse nicht mit unerwarteten oder fehlerhaften Werten verfälscht wird.

## Terraform für die Infrastruktur: Alles als Code

Ein persönliches Highlight war, alles mit **Terraform** aufzubauen. Statt manuell Buckets oder Datasets anzulegen, definiere ich alle Ressourcen als Code:

- Cloud Storage Bucket  
- BigQuery Dataset & Tabelle  
- IAM-Rollen und Berechtigungen  

Das hat mehrere Vorteile:  
Die Infrastruktur ist **reproduzierbar**, **versionierbar**, und in wenigen Minuten neu aufsetzbar. Und ich habe gelernt, wie angenehm es ist, Cloud-Setups wirklich sauber und automatisiert zu verwalten.

## Automatisiertes Deployment mit `deploy.sh`

Für wiederholbare Deployments habe ich ein eigenes `deploy.sh` geschrieben, das die wichtigsten Schritte bündelt:  
Es prüft zunächst, ob alle Werkzeuge (`gcloud`, `gsutil`, `terraform`, `zip`) verfügbar sind und ob ich bei GCP eingeloggt bin. Anschließend wird der komplette Funktionscode aus `src/` neu gepackt, als ZIP erzeugt und automatisch in den vorgesehenen Cloud-Storage-Bucket hochgeladen. Danach wird die Terraform-Konfiguration ausgeführt, inklusive optionalem `terraform init` und anschließendem `terraform apply` mit den Projekt- und Regionsvariablen.

Das Skript ist im Grunde eine manuelle CI/CD-Pipeline zum Ausführen auf dem eigenen Rechner:  
ein definierter Ablauf, reproduzierbar und ohne interaktive Schritte.  
Der Unterschied zu einer „echten“ CI/CD-Pipeline (z. B. GitHub Actions oder Cloud Build) ist, dass alles lokal läuft und nicht durch Commits oder Pull Requests ausgelöst wird. Trotzdem erlaubt mir das Skript, die gesamte Infrastruktur und den Funktionscode mit einem einzigen Befehl konsistent auszurollen, ohne jeden Schritt einzeln bedienen zu müssen.


## Looker Studio: Luftqualität sichtbar gemacht

Zum Abschluss habe ich eine kleine Visualisierung in **Looker Studio** gebaut. Darin zeige ich:

- **Grenzwertüberschreitungen** (z. B. Feinstaub oder NO₂)  
- Eine anschauliche Übersetzung der Luftqualität in **„inhalierte Zigaretten pro Tag“** – basierend auf einer bekannten Faustformel, die viele Menschen besser greifen können als reine µg/m³-Werte.

Das Ergebnis ist ein Dashboard, das sowohl technisch Interessierten als auch Laien ein Gefühl dafür gibt, wie sich Luftqualität über die Zeit verändert.

---

Insgesamt war das Projekt ein schöner Mix aus API-Handling, Datenvalidierung, Infrastructure-as-Code und Dashboarding – und für mich ein super Lernerlebnis, das ich in zukünftige Projekte mitnehme.
