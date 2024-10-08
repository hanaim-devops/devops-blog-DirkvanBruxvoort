# Onderzoeksplan
Dit document beschrijft het onderzoeksproces voor het begrijpen en implementeren van de Kubernetes Horizontal Pod Autoscaler (HPA). Het doel is om de werking, voordelen, nadelen, en de verschillende configuratiemogelijkheden van HPA te onderzoeken. Dit onderzoek dient als basis voor een blogpost waarin hands-on configuraties en voorbeelden van HPA worden gepresenteerd.

## Doelstelling
Het doel van dit onderzoek is om inzicht te krijgen in de werking van Kubernetes HPA en hoe het kan worden geconfigureerd en geoptimaliseerd in een DevOps-omgeving. De bevindingen zullen worden verwerkt in een blogpost, gericht op DevOps- en Kubernetes-professionals.

## Hoofdvraag
Hoe kan Horizontal Pod Autoscaling (HPA) in Kubernetes de prestaties en efficiëntie van jouw applicaties optimaliseren?

## Deelvragen
- Hoe werkt HPA? (Literatuurstudie).
- Wat zijn de parameters om HPA in te stellen? (Literatuurstudie).
- Wat zijn de voordelen van HPA? (Community Research).
- Wat zijn de nadelen van HPA? (Community Research).
- Hoe past HPA in het DevOps landschap? (Literatuurstudie).
- Wat zijn de verschillende manieren om HPA in te stellen? (Literatuurstudie).
- Hoe implementateer je HPA? (Prototyping).

## Onderzoeksmethode (Hbo-I. n.d.)
Om deze vragen te beantwoorden, wordt gebruikgemaakt van een combinatie van kwalitatieve en kwantitatieve onderzoeksmethoden. Deze methoden zorgen voor een diepgaande analyse van de theorie, ondersteund door praktische experimenten en implementaties. We maken gebruik van de workshop- en showroom- en library-onderzoeksruimtes van de ICT research methods voor het onderzoek.

### Literatuurstudie
- Doel: Het verzamelen van theoretische kennis over HPA en de gerelateerde concepten in Kubernetes, zoals autoscaling, metrics en resource management.
- Bronnen: Wetenschappelijke artikelen, Kubernetes-documentatie, DevOps blogs en relevante vakliteratuur.
- Verwachte Uitkomst: Een duidelijk begrip van de theorie achter HPA, de mogelijkheden, beperkingen en best practices voor configuratie.

### Community Research
- Doel: Het verkrijgen van praktijkinzichten en ervaringen uit de Kubernetes-community door het bestuderen van forums, GitHub repositories, en blogposts van DevOps-experts.
- Bronnen: Stack Overflow, Kubernetes Slack-kanalen, GitHub-discussies, en andere relevante fora.
- Verwachte Uitkomst: Praktische inzichten en voorbeelden van HPA-implementaties in real-world Kubernetes-clusters.

### Prototyping
- Doel: Het testen van verschillende HPA-configuraties in een Minikube Kubernetes-cluster en het valideren van de theoretische bevindingen door hands-on experimenten.
- Methode: Implementeren van HPA op een testapplicatie in een Minikube-omgeving, gevolgd door load testing om de werking van HPA te evalueren.
- Verwachte Uitkomst: Concrete voorbeelden van HPA-configuraties, inclusief YAML-bestanden en meetresultaten van de prestatie-impact van HPA.

### Peer review
- Doel: Het verkrijgen van feedback op het onderzoek en de blogpost, en waar nodig verbeteren op basis van suggesties van peers.
- Methode: Peer review in kleine teams waarbij feedback wordt gegeven op onderzoeksvragen, methode en uitwerking.
- Verwachte Uitkomst: Verbeteringen in structuur, volledigheid en technische nauwkeurigheid van het onderzoek en de blogpost.

## Verwachte Uitkomsten
Aan het einde van het onderzoek wordt een blogpost opgeleverd die voldoet aan de volgende eisen:
- Theoretische basis: Een duidelijke uitleg van HPA, ondersteund door literatuur en community-bronnen.
- Hands-on configuratie: YAML-configuraties en voorbeeldcode voor het implementeren van HPA in een Kubernetes-cluster.
- Praktische voorbeelden: Resultaten van HPA-schaaltests.
- Feedback verwerkt: Verbeteringen op basis van peer reviews.

## Bronnen
Hbo-I. (n.d.). ICT Research Methods — Methods Pack for research in ICT. ICT Research Methods. https://ictresearchmethods.nl/

