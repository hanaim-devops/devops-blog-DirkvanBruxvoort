# Hoe HPA in Kubernetes jou kan helpen!

<img src="https://upload.wikimedia.org/wikipedia/commons/3/39/Kubernetes_logo_without_workmark.svg" width="250" align="right" alt="logo kubernetes" title="logo kubernetes">

*[Dirk van Bruxvoort, oktober 2024.](https://github.com/hanaim-devops/devops-blog-DirkvanBruxvoort)*
<hr/>

Op dit moment volg ik de minor DevOps aan de HAN. Voor deze minor leg ik in dit blogbericht leg ik uit hoe Horizontal Pod Autoscaling (HPA) in Kubernetes werkt. Kubernetes is een open-source platform dat apps beheert (CNCF, 2024). Het automatiseert het draaien, schalen en beheren van apps die in containers draaien (zelfstandige pakketten die alles bevatten wat een app nodig heeft om te werken). Kubernetes functioneert als een manager voor apps, vergelijkbaar met hoe een restaurantmanager zijn personeel aanstuurt. Wanneer het drukker is in een restaurant, voegt de manager meer koks toe om alle bestellingen op tijd te verwerken. Kubernetes doet hetzelfde voor apps: het verdeelt automatisch het werk, waarmee kubernetes er automatisch meer of minder pods laat zijn. En kubernetes lost problemen op zonder handmatige tussenkomst. Het zorgt ervoor dat apps geüpdatet zijn zonder uitval en houdt alles veilig en georganiseerd. <br>
Meer over Kubernetes lees je op: 

[kubernetes.io](https://kubernetes.io/).

<br>
<br>

## Inhoudsopgave
<!-- TOC -->

- [Hoe HPA in Kubernetes jou kan helpen!](#hoe-hpa-in-kubernetes-jou-kan-helpen)
    - [Inhoudsopgave](#inhoudsopgave)
    - [HPA uitgelegd](#hpa-uitgelegd)
    - [Hoe HPA werkt](#hoe-hpa-werkt)
        - [Metrics Server De Bewaker:](#metrics-server-de-bewaker)
        - [Horizontal Pod Autoscaler De Manager:](#horizontal-pod-autoscaler-de-manager)
        - [Deployment De Personeelsplanner:](#deployment-de-personeelsplanner)
        - [Pods De Koks:](#pods-de-koks)
    - [Parameters HPA](#parameters-hpa)
    - [Voordelen HPA](#voordelen-hpa)
    - [Nadelen HPA](#nadelen-hpa)
    - [HPA in het DevOps landschap](#hpa-in-het-devops-landschap)
        - [🧑‍🤝‍🧑 Culture & Organization](#%E2%80%8D%E2%80%8D-culture--organization)
        - [⛪ Design & Architecture](#-design--architecture)
        - [🏗️ Build & Deploy](#-build--deploy)
        - [🧪 Test & Verification](#%F0%9F%A7%AA-test--verification)
        - [📈 Information & Reporting](#-information--reporting)
    - [Manieren om HPA in te stellen](#manieren-om-hpa-in-te-stellen)
        - [CPU-gebaseerde autoscaling:](#cpu-gebaseerde-autoscaling)
        - [Aangepaste metrieken:](#aangepaste-metrieken)
        - [Geheugengebaseerde autoscaling:](#geheugengebaseerde-autoscaling)
        - [Schalen op meerdere metrieken:](#schalen-op-meerdere-metrieken)
        - [Aangepaste schaalregels:](#aangepaste-schaalregels)
        - [Pod Prioriteit en Preëmptie:](#pod-prioriteit-en-pre%C3%ABmptie)
    - [Voorbeeld implementatie](#voorbeeld-implementatie)
        - [Stap 1: Installeer de Metrics Server](#stap-1-installeer-de-metrics-server)
        - [Stap 2: Implementeer je Applicatie](#stap-2-implementeer-je-applicatie)
        - [Stap 3: Maak de Auto-Schaalregel](#stap-3-maak-de-auto-schaalregel)
        - [Stap 4: Test de Auto-Schaalfunctie](#stap-4-test-de-auto-schaalfunctie)
        - [Stap 5: Bekijk je App bij Schaalvergroting](#stap-5-bekijk-je-app-bij-schaalvergroting)
    - [Resultaten](#resultaten)
    - [Conclusie](#conclusie)
    - [Bronnen](#bronnen)

<!-- /TOC -->
<!-- /TOC -->

<br>
<br>

## HPA uitgelegd
![wat-is-hpa](https://www.kubecost.com/images/hpa-autoscaling.png)
*Afbeelding 1: Wat HPA is (Kubecost, z.d.).* <br><br>

Ik breid het voorbeeld van het restaurant uit. Horizontal Pod Autoscaling is een functie van Kubernetes die automatisch meer of minder "werknemers" (pods) toevoegt, vergelijkbaar met het toevoegen van meer koks in een keuken bij drukte. HPA monitort de werklast van apps (zoals CPU- of geheugengebruik) en zorgt ervoor dat er altijd genoeg 'koks' aanwezig zijn om de 'keuken' soepel te laten draaien. Dit houdt de apps altijd krachtig, zelfs bij toenemende belasting. Ik bespreek ook andere manieren om te schalen en ervoor te zorgen dat apps altijd de benodigde resources hebben in deze blog. In afbeelding 1 staat afgebeeld wat HPA is.



<br>
<br>

## Hoe HPA werkt
![hoe-werkt-hpa](https://www.kubecost.com/images/hpa-overview.png)
*Afbeelding 2: Hoe hpa werkt (Kubecost, z.d.).* <br><br>

Ik breid het restaurantvoorbeeld uit om de werking van Horizontal Pod Autoscaling in Kubernetes uit te leggen. In een restaurant met een keuken (de server) waar koks (pods) maaltijden bereiden, zorgt de manager ervoor dat er altijd genoeg koks beschikbaar zijn om klanten goed te bedienen, zonder onnodig kosten te maken en dus te veel koks in dienst te hebben.

### Metrics Server (De Bewaker):
De bewaker houdt continu in de gaten wat de drukte in de keuken is. Deze bewaker meet de werklast van de koks, zoals CPU- en geheugengebruik, in plaats van alleen te kijken naar hoe vol de keuken is.

### Horizontal Pod Autoscaler (De Manager):
De manager ontvangt informatie van de bewaker (Metrics Server) en beslist of de keuken extra koks nodig heeft, op basis van de werkdruk. Bij een drukke keuken, waar koks moeite hebben om alle bestellingen op tijd klaar te maken, kan de manager besluiten extra koks aan te nemen. Bij rustigere momenten kan de manager besluiten sommige koks weg te sturen.

### Deployment (De Personeelsplanner):
De personeelsplanner regelt het aantal koks in de keuken op basis van de instructies van de manager. De instructies van de manager over het toevoegen of verwijderen van koks voert de personeelsplanner uit, zodat altijd het juiste aantal koks aanwezig is.

### Pods (De Koks):
De pods zijn de "werknemers" in de keuken die de maaltijden bereiden en zorgen dat alles soepel verloopt. De manager voegt extra pods (koks) toe wanneer dat nodig is, en verwijdert pods bij afnemende drukte.

In afbeelding 2 staat welke onderdelen er zijn binnen HPA en hoe deze met elkaar verbonden zijn.

<br>
<br>

## Parameters HPA 
Om HPA in te stellen, maak je een HPA-object aan met de volgende eigenschappen:
- Target CPU- of geheugenbelasting: Stel de CPU- of geheugenbelasting in die je wilt bereiken (Princess Egbuna O, n.d.).
- Min replicas: Bepaal het minimum aantal pods dat je nodig hebt (Princess Egbuna O, n.d.).
- Max replicas: Stel het maximum aantal pods in dat je wilt toestaan (Princess Egbuna O, n.d.).
- Scaling down policy: Definieer de regels die bepalen wanneer je kunt schalen naar beneden (CNCF, 2024).
- Scaling up policy: Stel de regels in die bepalen wanneer je kunt schalen naar boven (CNCF, 2024).

<br>
<br>

## Voordelen HPA
HPA biedt de volgende voordelen:
- Flexibiliteit: HPA maakt het mogelijk om automatisch op en neer te schalen op basis van de belasting van je applicaties. Dit zorgt voor een toewijzing van resources, wat ervoor zorgt dat je je Kubernetes-cluster kunt beheren zonder handmatige ingrepen (InSpark, 2023).
- Optimalisatie van prestaties: Met het gebruik van HPA kun je je applicaties schalen op basis van de vraag. Je kunt microservices individueel opschalen, wat leidt tot prestatie. Bovendien zorgt HPA ervoor dat Kubernetes de nodes monitort en automatisch reageert op problemen, wat ervoor zorgt dat de prestaties en beschikbaarheid van je applicaties hoog blijven (InSpark, 2023).
- Focus op DevOps en business: Dankzij de automatisering van schaalbaarheid en de flexibiliteit die HPA biedt, kunnen DevOps-teams zich meer richten op het ontwikkelen van de applicaties en minder op infrastructuurbeheer. Dit leidt tot een time-to-market en ontwikkeling van applicaties (InSpark, 2023).
- Kostenbesparing: HPA zorgt ervoor dat je alleen betaalt voor de resources die je nodig hebt. Dit resulteert in kosten, omdat overprovisionering vermeden is en je Kubernetes-cluster is benut (InSpark, 2023).
- Tijdsbesparing met automatisering: Omdat HPA het schalen van pods beheert, hoef je niet langer handmatig in te grijpen om je resources aan te passen. Dit bespaart tijd en maakt het eenvoudiger om nieuwe functies en updates uit te rollen (InSpark, 2023).
- Ondersteuning met community: HPA is een onderdeel van Kubernetes, dat een open-source community ondersteunt. Je kunt rekenen op verbeteringen en ondersteuning vanuit de community (InSpark, 2023).


<br>
<br>

## Nadelen HPA
HPA kent de volgende nadelen:
- Je raakt het overzicht kwijt: Het gebruik van HPA in Kubernetes kan leiden tot een verlies van overzicht. Wanneer je probeert om alle workloads en applicaties tegelijk te moderniseren en te migreren, kan het complex zijn om alle containers en hun schaalacties te monitoren. Containers zijn eenheden die ontstaan of verdwijnen, wat ervoor zorgt dat je moeite kunt hebben om je infrastructuur te overzien (InSpark, 2023).
- Je hebt geen eigen blauwdruk gemaakt: Hoewel HPA automatisch schaalt op basis van vooraf ingestelde voorwaarden, vereist het systeem dat je een blauwdruk hebt van de gewenste staat van je applicaties. Dit betekent dat je moet definiëren hoe het systeem moet functioneren en welke acties moeten zijn ondernomen bij schommelingen in de belasting. Zonder een blauwdruk kan HPA niet functioneren, wat kan leiden tot resultaten of inefficiëntie (InSpark, 2023).
- Je beheerst de functionaliteiten niet: In Kubernetes zijn containers beperkt tot één functionaliteit. Dit betekent dat je zorgvuldig moet zijn in het beheren en scheiden van verschillende functionaliteiten. Het samenvoegen van meerdere functies in één container kan leiden tot problemen, zoals moeilijkheden bij het onderhoud en de controle. Een samenwerking tussen development en operations (DevOps) is noodzakelijk om alle functionaliteiten te beheren (InSpark, 2023).
- Je creëert een kettingreactie: De scheiding van functionaliteiten en het samenwerken van containers kan er een kettingreactie ontstaan wanneer er problemen optreden. Fouten in één container kunnen leiden tot nieuwe containers voor probleemoplossing, wat kan resulteren in een keten van containers. Dit kan het debuggen bemoeilijken en leiden tot een situatie waarin je bezig bent met het oplossen van problemen en het bijwerken van containers volgens je blauwdruk (InSpark, 2023).

<br>
<br>

## HPA in het DevOps landschap
Hoe HPA in het DevOps landschap past leg ik uit aan de hand van het CDMM model.

### 🧑‍🤝‍🧑 Culture & Organization
- Samenwerken: Teams gebruiken HPA om automatisch te schalen, ze delen verantwoordelijkheid voor de prestaties van applicaties. Dit bevordert samenwerking tussen development en operations, omdat handelingen zoals schalen geen interventie meer vereisen.
- Werkwijzen: teams passen met HPA hun applicaties aan op de veranderende vraag, wat hen in staat stelt te reageren op feedback en vereisten, in lijn met Agile.

### ⛪ Design & Architecture
- High Availability en microservices: Teams zetten HPA in om microservices te schalen, wat ervoor zorgt dat ze de beschikbaarheid en veerkracht van hun applicaties vergroten. Dit voorkomt dat een service overbelast raakt en verhoogt de betrouwbaarheid.
- Autonomy over authority: Teams nemen beslissingen over de schaalbaarheid van hun applicaties zonder tussenkomst van een systeem.  Met HPA behouden ze controle over hoe hun applicaties omgaan met belasting.

### 🏗️ Build & Deploy
- Automatiseren: Teams integreren HPA in hun CI/CD-pijplijn om schaalbaarheid te automatiseren. Ze configureren de autoscaler zodat deze reageert op belasting, wat zorgt voor een implementatie zonder interventie.
- CI/CD integratie: Bij iedere release zorgen teams ervoor dat HPA klaarstaat om de applicatie te schalen op basis van belasting. Dit garandeert dat de applicatie presteert zonder dat er moet zijn ingegrepen in de schaalbaarheid.

### 🧪 Test & Verification
- Metrics en testen: Teams gebruiken HPA om tests te doen op basis van CPU- en geheugenverbruik. Ze integreren deze gegevens in hun tests en passen de schaalbaarheid van de applicatie aan op basis van resultaten.
- Testpiramide en verificatie: Teams gebruiken HPA om te valideren hoe hun applicaties schalen bij belastingniveaus. Ze testen de reacties van hun applicaties op schaalbare belasting, wat belangrijk is bij stress- en prestatietesten.

### 📈 Information & Reporting
- Metrics vanuit productie: Teams monitoren de metrics die HPA levert om inzicht te krijgen in de prestaties van hun applicaties. Deze metrics helpen hen om bij te sturen en de prestaties te optimaliseren.
- Visualisatie van resourcegebruik: Met tools zoals Prometheus en Grafana te gebruiken, visualiseren teams de schaalbaarheid en resourcegebruik van hun applicaties. Dit geeft hen inzichten die de feedbackloops versterken en besluitvorming ondersteunen.

<br>
<br>

## Manieren om HPA in te stellen

<br>

### CPU-gebaseerde autoscaling: 
Je stelt HPA meestal in op basis van CPU-gebruik. Definieer een target CPU-gebruikspercentage (bijv. 50%). Als dit percentage overschreden is, voegt Kubernetes meer pods toe (CNCF, 2024).
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
Je configureert HPA ook op basis van aangepaste metrieken, zoals verzoeken per seconde of wachtrijlengte. Exporteer deze metrieken naar een monitoringssysteem zoals Prometheus en configureer ze vervolgens in de HPA-definitie (CNCF, 2024).
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
Stel HPA ook in op basis van geheugengebruik, wat nuttig is voor geheugenintensieve applicaties (CNCF, 2024).
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
Je kunt HPA instellen om op meerdere metrieken tegelijk te schalen, zoals zowel CPU- als geheugengebruik (CNCF, 2024).
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
Gebruik de behavior-sectie van HPA om aangepaste schaalregels in te stellen, zoals vertragingen of het geleidelijk toevoegen/verwijderen van pods (CNCF, 2024).
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
Stel pod-prioriteit en preëmptie in om te bepalen welke pods als eerste schalen bij resource-schaarste, en geef prioriteit aan belangrijke workloads (CNCF, 2024).
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
Hoe je automatische schaalvergroting voor een app instelt beschrijft Ravi Patel in zijn Medium artikel (Patel R, 2024). Ik ga ervan uit dat je al een Kubernetes-cluster en Minikube draaiende hebt.

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
Stel een Nginx-server in als voorbeeldapplicatie. Maak een bestand voor de Nginx Deployment, sla het op en implementeer het. Maak de Nginx-app toegankelijk.

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

### Stap 3: Maak de Auto-Schaalregel
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

Kubernetes verhoogt het aantal servers naarmate de app drukker is.

<br>
<br>

## Resultaten
Bij de implementatie van het HPA-object in een Kubernetes-cluster schaalt de controller automatisch de workload om de CPU-belasting binnen gewenste grenzen te houden.
Kubernetes past het aantal pods aan op basis van de belasting.

<br>
<br>

## Conclusie
De Horizontal Pod Autoscaler is een tool binnen Kubernetes voor organisaties die hun applicaties willen schalen op basis van de belasting. Dit kan waarde opleveren in scenario's waar pieken en dalen in gebruik voorkomen, zoals bij e-commerce platforms of apps met wisselende gebruikersaantallen.

Maar niet elke organisatie heeft die behoefte. Als je geen noodzaak hebt om automatisch op te schalen, kan Kubernetes, met zijn functionaliteiten, misschien te veel zijn. In dat geval zijn er manieren om je applicaties te beheren zonder de complexiteit van een Kubernetes-omgeving.

Het is dus belangrijk om te bepalen welke behoeften jouw organisatie heeft. Als schaling een vereiste is, biedt HPA binnen Kubernetes een oplossing. Zo niet, dan is het verstandig om te evalueren welke technologie het best past bij je doelen.

<br>
<br>

## Bronnen
- *Kubecost. (z.d.). The Guide To Kubernetes HPA by Example. Geraadpleegd 12 september 2024 op https://www.kubecost.com/kubernetes-autoscaling/kubernetes-hpa/ .*
- *CNCF. (2024, 18 februari). Horizontal pod autoscaling. Geraadpleegd 12 september 2024 op https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/ .*
- *Patel, R. (2024, 15 juni). Introduction to Horizontal Pod Autoscaler (HPA) in Kubernetes with Example. Geraadpleegd 12 september 2024 op https://medium.com/@ravipatel.it/introduction-to-horizontal-pod-autoscaler-hpa-in-kubernetes-with-example-775babb88b6f .*
- *CNCF. (2024, 19 februari). Pod Priority and Preemption. Geraadpleegd 12 september 2024 op https://kubernetes.io/docs/concepts/scheduling-eviction/pod-priority-preemption/ .*
- *InSpark. (2023, 20 oktober). Kubernetes | Totale flexibiliteit met containers | InSpark. Geraadpleegd 12 september 2024 op https://www.inspark.nl/kubernetes/ .*
- *Princess Egbuna, O. (2023, 9 oktober). Kubernetes autoschaling pattern. Geraadpleegd 12 september 2024 op https://www.spectrocloud.com/blog/kubernetes-autoscaling-patterns-hpa-vpa-and-keda .*