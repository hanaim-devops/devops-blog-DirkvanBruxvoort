# Hoe HPA in Kubernetes jou kan helpen!

<img src="https://upload.wikimedia.org/wikipedia/commons/3/39/Kubernetes_logo_without_workmark.svg" width="250" align="right" alt="logo kubernetes" title="logo kubernetes">

*[Dirk van Bruxvoort, oktober 2024.](https://github.com/hanaim-devops/devops-blog-DirkvanBruxvoort)*
<hr/>

Op dit moment volg ik de minor DevOps aan de HAN. Voor deze minor leg ik in dit blogbericht leg ik uit hoe Horizontal Pod Autoscaling (HPA) in Kubernetes werkt. Kubernetes is een krachtig, open-source platform dat moderne apps beheert (Kubernetes, 18 februari 2024). Het automatiseert het draaien, schalen en beheren van apps die in containers draaien (zelfstandige pakketten die alles bevatten wat een app nodig heeft om te werken). Kubernetes functioneert als een slimme en ervaren manager voor apps, vergelijkbaar met hoe een restaurantmanager zijn personeel aanstuurt. Wanneer het drukker wordt in een restaurant, voegt de manager meer koks toe om alle bestellingen op tijd te verwerken. Kubernetes doet hetzelfde voor apps: het verdeelt automatisch het werk, voegt nieuwe "werknemers" (pods) toe als het drukker wordt, en lost problemen op zonder handmatige tussenkomst. Het zorgt ervoor dat apps geüpdatet worden zonder uitval en houdt alles veilig en georganiseerd. Horizontal Pod Autoscaling (HPA) is een functie van Kubernetes die automatisch meer of minder "werknemers" (pods) toevoegt, vergelijkbaar met het toevoegen van meer koks in een keuken bij drukte. HPA monitort de werklast van apps (zoals CPU- of geheugengebruik) en zorgt ervoor dat er altijd genoeg 'koks' aanwezig zijn om de 'keuken' soepel te laten draaien. Dit houdt de apps altijd krachtig, zelfs bij toenemende belasting. We bespreken ook andere manieren om te schalen en ervoor te zorgen dat apps altijd de benodigde resources hebben. <br>
Meer over Kubernetes lees je hier: 

[kubernetes.io](https://kubernetes.io/).

<br>
<br>

## Inhoudsopgave
<!-- TOC -->

- [Hoe HPA in Kubernetes jou kan helpen!](#hoe-hpa-in-kubernetes-jou-kan-helpen)
    - [Inhoudsopgave](#inhoudsopgave)
    - [Hoe werkt HPA?](#hoe-werkt-hpa)
        - [Metrics Server De Bewaker:](#metrics-server-de-bewaker)
        - [Horizontal Pod Autoscaler De Manager:](#horizontal-pod-autoscaler-de-manager)
        - [Deployment De Personeelsplanner:](#deployment-de-personeelsplanner)
        - [Pods De Koks:](#pods-de-koks)
    - [Wat zijn de parameters om HPA in te stellen?](#wat-zijn-de-parameters-om-hpa-in-te-stellen)
    - [Wat zijn de voordelen van HPA?](#wat-zijn-de-voordelen-van-hpa)
    - [Wat zijn de nadelen van HPA?](#wat-zijn-de-nadelen-van-hpa)
    - [Wat zijn de verschillende manieren om HPA in te stellen?](#wat-zijn-de-verschillende-manieren-om-hpa-in-te-stellen)
        - [CPU-gebaseerde autoscaling:](#cpu-gebaseerde-autoscaling)
        - [Aangepaste metrieken:](#aangepaste-metrieken)
        - [Geheugengebaseerde autoscaling:](#geheugengebaseerde-autoscaling)
        - [Schalen op meerdere metrieken:](#schalen-op-meerdere-metrieken)
        - [Aangepaste schaalregels:](#aangepaste-schaalregels)
        - [Pod Prioriteit en Preëmptie:](#pod-prioriteit-en-pre%C3%ABmptie)
    - [Voorbeeld implementatie](#voorbeeld-implementatie)
        - [Stap 1: Installeer de Metrics Server](#stap-1-installeer-de-metrics-server)
        - [Stap 2: Implementeer je Applicatie](#stap-2-implementeer-je-applicatie)
        - [Stap 3: Maak de Auto-Schaalregel HPA](#stap-3-maak-de-auto-schaalregel-hpa)
        - [Stap 4: Test de Auto-Schaalfunctie](#stap-4-test-de-auto-schaalfunctie)
        - [Stap 5: Bekijk je App bij Schaalvergroting](#stap-5-bekijk-je-app-bij-schaalvergroting)
    - [Resultaten](#resultaten)
    - [Conclusie](#conclusie)
    - [Bronnen](#bronnen)

<!-- /TOC -->
<!-- /TOC -->

<br>
<br>

## Hoe HPA werkt
![hoe-werkt-hpa](https://www.kubecost.com/images/hpa-overview.png)
*Afbeelding 1: Hoe hpa werkt uitgelegd (Kubecost, z.d.).* <br><br>

We breiden het restaurantvoorbeeld uit om de werking van Horizontal Pod Autoscaling (HPA) in Kubernetes uit te leggen. In een restaurant met een keuken (de server) waar koks (pods) maaltijden bereiden, probeert de manager altijd genoeg koks beschikbaar te hebben om klanten goed te bedienen, zonder onnodig kosten te maken door te veel koks in dienst te hebben.

### Metrics Server (De Bewaker):
De slimme bewaker monitort continu de drukte in de keuken. Deze bewaker meet de werklast van de koks, zoals CPU- en geheugengebruik, in plaats van alleen te kijken naar hoe vol de keuken is.

### Horizontal Pod Autoscaler (De Manager):
De manager ontvangt informatie van de bewaker (Metrics Server) en beslist of de keuken extra koks nodig heeft, op basis van de werkdruk. Bij een drukke keuken, waar koks het moeilijk hebben om alle bestellingen op tijd klaar te maken, kan de manager besluiten extra koks aan te nemen. Bij rustigere momenten kan de manager besluiten sommige koks weg te sturen.

### Deployment (De Personeelsplanner):
De personeelsplanner regelt het aantal koks in de keuken op basis van de instructies van de manager. De instructies van de manager (HPA) over het toevoegen of verwijderen van koks voert de personeelsplanner uit, zodat altijd het juiste aantal koks aanwezig is.

### Pods (De Koks):
De pods zijn de "werknemers" in de keuken die de maaltijden bereiden en zorgen dat alles soepel verloopt. De manager voegt extra pods (koks) toe wanneer dat nodig is, en verwijdert pods bij afnemende drukte.

In afbeelding 2 staat welke onderdelen er zijn binnen HPA en hoe deze met elkaar verbonden zijn.

<br>
<br>

## Parameters HPA 
Om HPA in te stellen, maak je een HPA-object aan met de volgende eigenschappen:
- Target CPU- of geheugenbelasting: Stel de CPU- of geheugenbelasting in die je wilt bereiken (Spectrocloud, n.d.).
- Min replicas: Bepaal het minimum aantal pods dat je nodig hebt (Spectrocloud, n.d.).
- Max replicas: Stel het maximum aantal pods in dat je wilt toestaan (Spectrocloud, n.d.).
- Scaling down policy: Definieer de regels die bepalen wanneer je kunt schalen naar beneden (Kubernetes, 18 februari 2024).
- Scaling up policy: Stel de regels in die bepalen wanneer je kunt schalen naar boven (Kubernetes, 18 februari 2024).

<br>
<br>

## Voordelen HPA
HPA (Horizontal Pod Autoscaler) biedt de volgende voordelen:
- Onbeperkte flexibiliteit: HPA maakt het mogelijk om automatisch op en neer te schalen op basis van de belasting van je applicaties. Dit zorgt voor een dynamische toewijzing van resources, waardoor je je Kubernetes-cluster effectief kunt beheren zonder handmatige ingrepen (InSpark, 20 oktober 2023).
- Optimalisatie van prestaties: Door het gebruik van HPA kun je je applicaties nauwkeurig en efficiënt schalen op basis van de vraag. Microservices kunnen individueel worden opgeschaald, wat leidt tot een optimale prestatie. Bovendien zorgt HPA ervoor dat Kubernetes constant de nodes monitort en automatisch reageert op eventuele problemen, waardoor de prestaties en beschikbaarheid van je applicaties altijd hoog blijven (InSpark, 20 oktober 2023).
- Sterkere focus op DevOps en business: Dankzij de automatisering van schaalbaarheid en de flexibiliteit die HPA biedt, kunnen DevOps-teams zich meer richten op het ontwikkelen van de applicaties en minder op infrastructuurbeheer. Dit leidt tot een snellere time-to-market en efficiëntere ontwikkeling van applicaties (InSpark, 20 oktober 2023).
- Kostenbesparing: HPA zorgt ervoor dat je alleen betaalt voor de resources die je daadwerkelijk nodig hebt. Dit resulteert in lagere kosten, omdat overprovisionering wordt vermeden en je Kubernetes-cluster optimaal wordt benut (InSpark, 20 oktober 2023).
- Tijdsbesparing door automatisering: Omdat het schalen van je pods automatisch wordt beheerd door HPA, hoef je niet langer handmatig in te grijpen om je resources aan te passen. Dit bespaart tijd en maakt het eenvoudiger om nieuwe functies en updates snel en schaalbaar uit te rollen (InSpark, 20 oktober 2023).
- Ondersteuning door een grote community: HPA is een belangrijk onderdeel van Kubernetes, dat op zijn beurt wordt ondersteund door een grote open-source community. Hierdoor kun je rekenen op voortdurende verbeteringen en uitgebreide ondersteuning vanuit de community (InSpark, 20 oktober 2023).

<br>
<br>

## Nadelen HPA
HPA (Horizontal Pod Autoscaler) kent de volgende nadelen:
- Je raakt het overzicht kwijt: Het gebruik van HPA in Kubernetes kan leiden tot een verlies van overzicht. Wanneer je probeert om alle workloads en applicaties tegelijk te moderniseren en te migreren, kan het complex zijn om alle containers en hun schaalacties te monitoren. Containers zijn namelijk dynamische eenheden die snel kunnen worden aangemaakt of verwijderd, waardoor je moeite kunt hebben om je infrastructuur volledig te overzien (InSpark, 20 oktober 2023).
- Je hebt geen eigen blauwdruk gemaakt: Hoewel HPA automatisch schaalt op basis van vooraf ingestelde voorwaarden, vereist het systeem dat je een gedetailleerde blauwdruk hebt van de gewenste staat van je applicaties. Dit betekent dat je exact moet definiëren hoe het systeem moet functioneren en welke acties moeten worden ondernomen bij schommelingen in de belasting. Zonder een nauwkeurige blauwdruk kan HPA niet effectief functioneren, wat kan leiden tot ongewenste resultaten of inefficiëntie (InSpark, 20 oktober 2023).
- Je beheerst de functionaliteiten niet: In Kubernetes zijn containers vaak beperkt tot één specifieke functionaliteit. Dit betekent dat je zeer zorgvuldig moet zijn in het beheren en scheiden van verschillende functionaliteiten. Het samenvoegen van meerdere functies in één container kan leiden tot problemen, zoals moeilijkheden bij het onderhoud en de controle. Een nauwe samenwerking tussen development en operations (DevOps) is noodzakelijk om alle functionaliteiten effectief te beheren (InSpark, 20 oktober 2023).
- Je creëert een kettingreactie: Door de strikte scheiding van functionaliteiten en het constante samenwerken van containers kan er een kettingreactie ontstaan wanneer er problemen optreden. Fouten in één container kunnen leiden tot het aanmaken van andere containers voor probleemoplossing, wat kan resulteren in een complexe keten van containers. Dit kan het debuggen bemoeilijken en leiden tot een onoverzichtelijke situatie waarin je voortdurend bezig bent met het oplossen van problemen en het bijwerken van containers volgens je blauwdruk (InSpark, 20 oktober 2023).

<br>
<br>

## HPA in het DevOps landschap
Hoe HPA in het DevOps landschap past leg ik uit aan de hand van het CDMM model.

### 🧑‍🤝‍🧑 Culture & Organization
- Multidisciplinair samenwerken: Teams gebruiken HPA om automatisch te schalen, waardoor ze verantwoordelijkheid delen voor de prestaties van applicaties. Dit bevordert directe samenwerking tussen development en operations, omdat operationele handelingen zoals schalen geen handmatige interventie meer vereisen.
- Agile werkwijzen: Door HPA passen teams hun applicaties direct aan op de veranderende vraag, wat hen in staat stelt snel te reageren op feedback en nieuwe vereisten, volledig in lijn met Agile.

### ⛪ Design & Architecture
- High Availability en microservices: Teams zetten HPA in om microservices zelfstandig te schalen, waardoor ze de beschikbaarheid en veerkracht van hun applicaties vergroten. Dit voorkomt dat een enkele service overbelast raakt en verhoogt de betrouwbaarheid.
- Autonomy over authority: Teams nemen zelf beslissingen over de schaalbaarheid van hun applicaties zonder tussenkomst van een centraal systeem. Door HPA te gebruiken, behouden ze volledige controle over hoe hun applicaties omgaan met wisselende belasting.

### 🏗️ Build & Deploy
- Automate all the things: Teams integreren HPA in hun CI/CD-pijplijn om schaalbaarheid volledig te automatiseren. Ze configureren de autoscaler zodat deze automatisch reageert op veranderende belasting, wat zorgt voor een naadloze implementatie zonder handmatige interventie.
- CI/CD integratie: Bij iedere nieuwe release zorgen teams ervoor dat HPA klaarstaat om de applicatie te schalen op basis van realtime belasting. Dit garandeert dat de applicatie optimaal presteert zonder dat er handmatig moet worden ingegrepen in de schaalbaarheid.

### 🧪 Test & Verification
- Bedrijfsmetrics en geautomatiseerd testen: Teams gebruiken HPA om belastingtests te doen op basis van CPU- en geheugenverbruik. Ze integreren deze gegevens in hun tests en passen de schaalbaarheid van de applicatie dynamisch aan op basis van testresultaten.
- Testpiramide en Continuous Verification: Teams gebruiken HPA om te valideren hoe hun applicaties schalen bij verschillende belastingniveaus. Ze testen continu de reacties van hun applicaties op schaalbare belasting, wat essentieel is bij stress- en prestatietesten.

### 📈 Information & Reporting
- Metrics vanuit productie: Teams monitoren de metrics die HPA levert om continu inzicht te krijgen in de prestaties van hun applicaties. Deze metrics helpen hen om realtime bij te sturen en de prestaties te optimaliseren.
- Visualisatie van resourcegebruik: Door tools zoals Prometheus en Grafana te gebruiken, visualiseren teams de schaalbaarheid en resourcegebruik van hun applicaties. Dit geeft hen waardevolle inzichten die de feedbackloops versterken en betere besluitvorming ondersteunen.
Teams gebruiken HPA om schaalbaarheid te automatiseren, samenwerking te verbeteren, en prestaties te optimaliseren. Dit past perfect binnen de kernwaarden van een DevOps-werkwijze binnen het CDMM.

<br>
<br>

## Manieren om HPA in te stellen

<br>

### CPU-gebaseerde autoscaling: 
Je stelt HPA meestal in op basis van CPU-gebruik. Definieer een target CPU-gebruikspercentage (bijv. 50%). Als dit percentage overschreden wordt, voegt Kubernetes meer pods toe (Kubernetes, 18 februari 2024).
```
yaml
Copy code
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: my-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: my-deployment
  minReplicas: 1
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      targetAverageUtilization: 50
```

<br>

### Aangepaste metrieken: 
Je configureert HPA ook op basis van aangepaste metrieken, zoals verzoeken per seconde of wachtrijlengte. Exporteer deze metrieken naar een monitoringssysteem zoals Prometheus en configureer ze vervolgens in de HPA-definitie (Kubernetes, 18 februari 2024).
```
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: custom-metrics-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: my-deployment
  minReplicas: 1
  maxReplicas: 10
  metrics:
  - type: Pods
    pods:
      metricName: custom_metric_name
      targetAverageValue: 100
```

<br>

### Geheugengebaseerde autoscaling: 
Stel HPA ook in op basis van geheugengebruik, wat nuttig is voor geheugenintensieve applicaties (Kubernetes, 18 februari 2024).
```
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: memory-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: my-deployment
  minReplicas: 1
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: memory
      targetAverageValue: 100Mi
```

<br>

### Schalen op meerdere metrieken: 
Je kunt HPA instellen om op meerdere metrieken tegelijk te schalen, zoals zowel CPU- als geheugengebruik (Kubernetes, 18 februari 2024).
```
metrics:
- type: Resource
  resource:
    name: cpu
    targetAverageUtilization: 50
- type: Resource
  resource:
    name: memory
    targetAverageValue: 100Mi
```

<br>

### Aangepaste schaalregels: 
Gebruik de behavior-sectie van HPA om aangepaste schaalregels in te stellen, zoals vertragingen of het geleidelijk toevoegen/verwijderen van pods (Kubernetes, 18 februari 2024).
```
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: custom-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: my-deployment
  minReplicas: 1
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      targetAverageUtilization: 50
  behavior:
    scaleDown:
      policies:
      - type: Pods
        value: 1
        periodSeconds: 300
    scaleUp:
      policies:
      - type: Pods
        value: 2
        periodSeconds: 60
```

<br>

### Pod Prioriteit en Preëmptie: 
Stel pod-prioriteit en preëmptie in om te bepalen welke pods als eerste schalen bij resource-schaarste, en geef prioriteit aan belangrijke workloads (Kubernetes, 19 februari 2024).
```
apiVersion: v1
kind: Pod
metadata:
  name: high-priority-pod
  labels:
    app: my-app
spec:
  priorityClassName: high-priority
  containers:
  - name: my-container
    image: my-image
    resources:
      requests:
        cpu: "0.2"
        memory: "256Mi"
---
apiVersion: v1
kind: Pod
metadata:
  name: low-priority-pod
  labels:
    app: my-app
spec:
  containers:
  - name: my-container
    image: my-image
    resources:
      requests:
        cpu: "0.1"
        memory: "128Mi"
```

<br>
<br>

## Voorbeeld implementatie
Hier lees je hoe je automatische schaalvergroting voor een app instelt (Medium, 15 juni 2024). We gaan ervan uit dat je al een Kubernetes-cluster en Minikube draaiende hebt.

<br>

### Stap 1: Installeer de Metrics Server
De Metrics Server monitort hoe jouw app computerbronnen gebruikt. Je hebt deze nodig om je app automatisch te laten schalen.

Installeer de Metrics Server met het volgende commando:

```
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

Controleer of de Metrics Server draait met:

```
kubectl get deployment metrics-server -n kube-system
```

<br>

### Stap 2: Implementeer je Applicatie
Stel een eenvoudige Nginx-server in als voorbeeldapplicatie. Maak een bestand voor de Nginx Deployment, sla het op en implementeer het. Maak de Nginx-app toegankelijk.

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: "100m"
            memory: "128Mi"
          limits:
            cpu: "250m"
            memory: "256Mi"
```
Sla dit bestand op als nginx-deployment.yaml. 

Implementeer Nginx met:

```
kubectl apply -f nginx-deployment.yaml
```

Controleer de implementatie met:

```
kubectl get deployment nginx-deployment
```

Maak de Nginx-app toegankelijk met:

```
kubectl expose deployment nginx-deployment --port=80 --target-port=80
```

<br>

### Stap 3: Maak de Auto-Schaalregel (HPA)
Voeg automatisch meer servers toe als de app dat nodig heeft. Maak een bestand voor de HPA. Open een teksteditor en kopieer dit:

```
apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  name: nginx-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: nginx-deployment
  minReplicas: 1
  maxReplicas: 10
  targetCPUUtilizationPercentage: 25
```

Sla dit bestand op als hpa.yaml. 

Maak de HPA aan met:

```
kubectl apply -f hpa.yaml
```

Controleer of de HPA is ingesteld met:

```
kubectl get hpa
```

<br>

### Stap 4: Test de Auto-Schaalfunctie
Om te zien of auto-schaalvergroting werkt, moet je de app harder laten werken. Maak een bestand voor een Load Generator. Open een teksteditor en kopieer dit:

```
apiVersion: v1
kind: Pod
metadata:
  name: load-generator
spec:
  containers:
  - name: busybox
    image: busybox
    command: ["/bin/sh"]
    args: ["-c", "while true; do wget -q -O- http://nginx-deployment.default.svc.cluster.local; done"]
    resources:
      requests:
        cpu: "3000m"  
        memory: "6400Mi"
      limits:
        cpu: "3000m"  
        memory: "6400Mi"
```
Sla dit bestand op als load-generator.yaml. Voer de Load Generator uit met:

```
kubectl apply -f load-generator.yaml
```

<br>

### Stap 5: Bekijk je App bij Schaalvergroting
Controleer hoe de HPA meer servers toevoegt wanneer dat nodig is. Controleer de status van de HPA met:

```
kubectl get hpa
```

Kubernetes verhoogt het aantal servers naarmate de app drukker wordt.

<br>
<br>

## Resultaten
Bij de implementatie van het HPA-object in een Kubernetes-cluster schaalt de controller automatisch de workload om de CPU-belasting binnen gewenste grenzen te houden.
Kubernetes past het aantal pods aan op basis van de belasting.

<br>
<br>

## Conclusie
Horizontal Pod Autoscaler (HPA) speelt een cruciale rol in het optimaliseren van je Kubernetes-omgeving door automatisch je applicaties op en neer te schalen op basis van de belasting. Deze functionaliteit biedt aanzienlijke voordelen, waaronder verbeterde prestaties, verhoogde beschikbaarheid en kostenbesparingen. Dankzij HPA kan je Kubernetes-cluster dynamisch reageren op veranderingen in de vraag zonder dat je handmatig hoeft in te grijpen. Met HPA zorg je ervoor dat je applicaties altijd over voldoende middelen beschikken om efficiënt te functioneren, zelfs tijdens piekuren. Door automatisch pods toe te voegen of te verwijderen op basis van metrics zoals CPU- of geheugengebruik, optimaliseer je de resource-allocatie en voorkom je overprovisionering. Dit vertaalt zich in lagere operationele kosten en een betere gebruikservaring voor je eindgebruikers. Bovendien maakt HPA je DevOps-processen soepeler door het management van resources te automatiseren, waardoor je team zich kan concentreren op het verbeteren en ontwikkelen van applicaties in plaats van het handmatig beheren van infrastructuur. De mogelijkheid om te schalen op meerdere metrieken en aangepaste schaalregels te configureren, geeft je de flexibiliteit om HPA af te stemmen op de specifieke behoeften van je applicaties. Hoewel HPA aanzienlijke voordelen biedt, zijn er ook uitdagingen, zoals het behouden van overzicht over een dynamische infrastructuur en het correct instellen van schaalparameters. Het is belangrijk om deze factoren zorgvuldig te overwegen en te plannen om het meeste uit HPA te halen. Door het implementeren van HPA in je Kubernetes-cluster, investeer je in een schaalbare en kosten-efficiënte toekomst voor je applicaties, waarbij je zowel prestaties als beschikbaarheid op peil houdt in een steeds veranderende omgeving.

<br>
<br>

## Bronnen
- *Kubecost. (z.d.). The Guide To Kubernetes HPA by Example. Geraadpleegd 12 september 2024 op https://www.kubecost.com/kubernetes-autoscaling/kubernetes-hpa/ .*
- *Kubernetes. (2024, 18 februari). Horizontal pod autoscaling. Geraadpleegd 12 september 2024 op https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/ .*
- *Medium. (2024, 15 juni). Introduction to Horizontal Pod Autoscaler (HPA) in Kubernetes with Example. Geraadpleegd 12 september 2024 op https://medium.com/@ravipatel.it/introduction-to-horizontal-pod-autoscaler-hpa-in-kubernetes-with-example-775babb88b6f .*
- *Kubernetes. (2024, 19 februari). Pod Priority and Preemption. Geraadpleegd 12 september 2024 op https://kubernetes.io/docs/concepts/scheduling-eviction/pod-priority-preemption/ .*
- *InSpark. (2023, 20 oktober). Kubernetes | Totale flexibiliteit met containers | InSpark. Geraadpleegd 12 september 2024 op https://www.inspark.nl/kubernetes/ .*
- *Spectrocloud. (2023, 9 oktober). Kubernetes autoschaling pattern. Geraadpleegd 12 september 2024 op https://www.spectrocloud.com/blog/kubernetes-autoscaling-patterns-hpa-vpa-and-keda .*