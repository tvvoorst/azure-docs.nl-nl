---
title: Een inkomende HTTPS met Azure Kubernetes Service (AKS)-cluster maken
description: Informatie over het installeren en configureren van een NGINX-controller voor binnenkomend verkeer die gebruikmaakt van we gaan coderen voor het automatisch genereren van SSL-certificaat in een cluster Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 08/30/2018
ms.author: iainfou
ms.openlocfilehash: 87ea88ad84114c4059e9a461beedb656c1d66bf5
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2018
ms.locfileid: "44356650"
---
# <a name="create-an-https-ingress-controller-on-azure-kubernetes-service-aks"></a>Maak een HTTPS-controller voor binnenkomend verkeer op Azure Kubernetes Service (AKS)

Een controller voor binnenkomend verkeer is een stukje software die voorziet in omgekeerde proxy, configureerbare verkeersroutering en TLS-beëindiging voor Kubernetes-services. Kubernetes-resources voor inkomend verkeer worden gebruikt voor het configureren van de ingress regels en routes voor afzonderlijke Kubernetes-services. Met behulp van een controller voor binnenkomend verkeer en ingress regels, kan één IP-adres worden gebruikt voor het routeren van verkeer naar meerdere services in een Kubernetes-cluster.

In dit artikel leest u over het implementeren van de [controller voor binnenkomend verkeer van NGINX] [ nginx-ingress] in een cluster Azure Kubernetes Service (AKS). De [Certificaatbeheer] [ cert-manager] project wordt gebruikt om automatisch te genereren en configureer [laten versleutelen] [ lets-encrypt] certificaten. Ten slotte worden twee toepassingen uitgevoerd in de AKS-cluster, die toegankelijk via één IP-adres is.

U kunt ook het volgende doen:

- [Een eenvoudige ingangscontroller met verbinding met het externe netwerk maken][aks-ingress-basic]
- [De HTTP-aanvraag routering invoegtoepassing inschakelen][aks-http-app-routing]
- [Een controller voor binnenkomend verkeer die gebruikmaakt van een privé-interne netwerken en IP-adres maken][aks-ingress-internal]
- [Maken van een controller voor binnenkomend verkeer met een statisch openbaar IP-adres en we gaan coderen voor het automatisch genereren van TLS-certificaten configureren][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel maakt gebruik van Helm om de NGINX-controller voor binnenkomend verkeer, certificaat-manager en een voorbeeld-web-app te installeren. U moet hebben Helm geïnitialiseerd in uw AKS-cluster en het gebruik van een service-account voor Tiller. Zorg ervoor dat u de nieuwste versie van Helm. Zie voor upgrade-instructies, de [Helm installeren docs][helm-install]. Zie voor meer informatie over het configureren en het gebruik van Helm [installeren van toepassingen met Helm in Azure Kubernetes Service (AKS)][use-helm].

In dit artikel is ook vereist dat u de Azure CLI versie 2.0.41 worden uitgevoerd of hoger. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Maken van een controller voor binnenkomend verkeer

Gebruik voor het maken van de controller voor binnenkomend verkeer `Helm` voor het installeren van *nginx-inkomend*.

> [!TIP]
> Het volgende voorbeeld installeert de controller voor binnenkomend verkeer in de `kube-system` naamruimte. Indien gewenst, kunt u een andere naamruimte voor uw eigen omgeving. Als uw AKS-cluster niet RBAC ingeschakeld is, voegt u toe `--set rbac.create=false` voor de opdrachten.

```console
helm install stable/nginx-ingress --namespace kube-system
```

Tijdens de installatie van is een openbaar IP-adres van Azure gemaakt voor de controller voor binnenkomend verkeer. Dit openbare IP-adres wordt statisch voor de levensduur van de controller voor binnenkomend verkeer. Als u de controller voor binnenkomend verkeer verwijdert, wordt de toewijzing van het openbare IP-adres gaat verloren. Als u vervolgens een extra ingangscontroller maakt, wordt een nieuw openbaar IP-adres toegewezen. Als u het gebruik van het openbare IP-adres behouden wilt, kunt u in plaats daarvan [maken van een controller voor binnenkomend verkeer met een statisch openbaar IP-adres][aks-ingress-static-tls].

Als u het openbare IP-adres, gebruikt de `kubectl get service` opdracht. Het duurt een paar minuten voor het IP-adres moet worden toegewezen aan de service.

```
$ kubectl get service -l app=nginx-ingress --namespace kube-system

NAME                                       TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
eager-crab-nginx-ingress-controller        LoadBalancer   10.0.182.160   51.145.155.210  80:30920/TCP,443:30426/TCP   20m
eager-crab-nginx-ingress-default-backend   ClusterIP      10.0.255.77    <none>          80/TCP                       20m
```

Nog zijn geen binnenkomende regels gemaakt. Als u naar het openbare IP-adres bladert, wordt de NGINX-ingangscontroller standaard 404-pagina weergegeven.

## <a name="configure-a-dns-name"></a>Configureren van een DNS-naam

Voor de HTTPS-certificaten correct te laten werken, configureert u een FQDN-naam voor het IP-adres voor inkomend verkeer controller. Het volgende script bijwerken met het IP-adres van de controller voor binnenkomend verkeer en een unieke naam die u wilt gebruiken voor de FQDN-naam:

```console
#!/bin/bash

# Public IP address of your ingress controller
IP="51.145.155.210"

# Name to associate with public IP address
DNSNAME="demo-aks-ingress"

# Get the resource-id of the public ip
PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)

# Update public ip address with DNS name
az network public-ip update --ids $PUBLICIPID --dns-name $DNSNAME
```

De controller voor binnenkomend verkeer is nu toegankelijk zijn via de FQDN-naam.

## <a name="install-cert-manager"></a>Certificaat-manager installeren

De NGINX-controller voor binnenkomend verkeer biedt ondersteuning voor TLS-beëindiging. Er zijn verschillende manieren om te halen en certificaten configureren voor HTTPS. In dit artikel wordt gedemonstreerd hoe [Certificaatbeheer][cert-manager], waarmee u automatische [kunt versleutelen] [ lets-encrypt] certificaat genereren en Management-functionaliteit.

> [!NOTE]
> In dit artikel wordt de `staging` omgeving voor laten we versleutelen. Gebruik in productie-implementaties, `letsencrypt-prod` en `https://acme-v02.api.letsencrypt.org/directory` in de resourcedefinities en bij het installeren van de Helm-diagram.

Als u wilt de certificaat-manager-domeincontroller installeren in een cluster RBAC is ingeschakeld, gebruikt u de volgende `helm install` opdracht:

```console
helm install stable/cert-manager --set ingressShim.defaultIssuerName=letsencrypt-staging --set ingressShim.defaultIssuerKind=ClusterIssuer
```

Als uw cluster niet RBAC ingeschakeld is, gebruikt u in plaats daarvan de volgende opdracht uit:

```console
helm install stable/cert-manager \
  --set ingressShim.defaultIssuerName=letsencrypt-staging \
  --set ingressShim.defaultIssuerKind=ClusterIssuer \
  --set rbac.create=false \
  --set serviceAccount.create=false
```

Zie voor meer informatie over Certificaatbeheer configuratie, de [Certificaatbeheer project][cert-manager].

## <a name="create-a-ca-cluster-issuer"></a>Een uitgevende CA-cluster maken

Voordat u certificaten kunnen worden uitgegeven, certificaat-manager vereist een [verlener] [ cert-manager-issuer] of [ClusterIssuer] [ cert-manager-cluster-issuer] resource. Deze Kubernetes-resources zijn identiek in functionaliteit, maar `Issuer` werkt in één enkele naamruimte en `ClusterIssuer` werkt met alle naamruimten. Zie voor meer informatie de [Certificaatbeheer verlener] [ cert-manager-issuer] documentatie.

Maken van de verlener van een cluster, zoals `cluster-issuer.yaml`, met behulp van het volgende voorbeeld-manifest. Het e-mailadres bijwerken met een geldig adres van uw organisatie:

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: ClusterIssuer
metadata:
  name: letsencrypt-staging
spec:
  acme:
    server: https://acme-staging-v02.api.letsencrypt.org/directory
    email: user@contoso.com
    privateKeySecretRef:
      name: letsencrypt-staging
    http01: {}
```

Gebruik voor het maken van de uitgever van de `kubectl apply -f cluster-issuer.yaml` opdracht.

```
$ kubectl apply -f cluster-issuer.yaml

clusterissuer.certmanager.k8s.io/letsencrypt-staging created
```

## <a name="create-a-certificate-object"></a>Een certificaatobject maken

Vervolgens moet een certificaat-bron worden gemaakt. De certificaatresource definieert de gewenste X.509-certificaat. Zie voor meer informatie, [Certificaatbeheer certificaten][cert-manager-certificates].

Maak de certificaat-resource, zoals `certificates.yaml`, met het volgende voorbeeld-manifest. Update de *dnsNames* en *domeinen* aan de DNS-naam die u in de vorige stap hebt gemaakt. Als u een controller voor intern gebruik bestemde binnenkomend verkeer gebruikt, geeft u de interne DNS-naam voor uw service.

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: Certificate
metadata:
  name: tls-secret
spec:
  secretName: tls-secret
  dnsNames:
  - demo-aks-ingress.eastus.cloudapp.azure.com
  acme:
    config:
    - http01:
        ingressClass: nginx
      domains:
      - demo-aks-ingress.eastus.cloudapp.azure.com
  issuerRef:
    name: letsencrypt-staging
    kind: ClusterIssuer
```

Gebruik voor het maken van de certificaatresource de `kubectl apply -f certificates.yaml` opdracht.

```
$ kubectl apply -f certificates.yaml

certificate.certmanager.k8s.io/tls-secret created
```

## <a name="run-demo-applications"></a>Demotoepassingen uitvoeren

Een controller voor binnenkomend verkeer en een oplossing voor het beheer van certificaten zijn geconfigureerd. Nu gaan we twee run demo voor toepassingen in uw AKS-cluster. In dit voorbeeld Helm gebruikt om twee instanties van een eenvoudige 'Hallo wereld'-toepassing implementeren.

Voordat u het voorbeeld Helm-grafieken installeren kunt, de Azure-voorbeelden opslagplaats toevoegen aan uw omgeving Helm als volgt:

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

De eerste demo-toepassing maken van een Helm-diagram met de volgende opdracht:

```console
helm install azure-samples/aks-helloworld
```

Nu een tweede exemplaar van de demo-toepassing installeren. Voor de tweede instantie geeft u een nieuwe titel zodat de twee toepassingen, visueel verschilt. U ook opgeven een unieke naam:

```console
helm install azure-samples/aks-helloworld --set title="AKS Ingress Demo" --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>Een binnenkomende route maken

Beide toepassingen wordt nu uitgevoerd in uw Kubernetes-cluster, maar ze zijn geconfigureerd met een service van het type `ClusterIP`. Daarom zijn de toepassingen niet toegankelijk is vanaf internet. Om deze openbaar beschikbaar maken door een Kubernetes-ingress-resource te maken. De resource met inkomend verkeer configureert u de regels die verkeer naar een van de twee toepassingen routeren.

In het volgende voorbeeld wordt het verkeer naar het adres `https://demo-aks-ingress.eastus.cloudapp.azure.com/` wordt doorgestuurd naar de service met de naam `aks-helloworld`. Verkeer naar het adres `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` wordt doorgestuurd naar de `ingress-demo` service. Update de *hosts* en *host* aan de DNS-naam die u in de vorige stap hebt gemaakt.

Maak een bestand met de naam `hello-world-ingress.yaml` en kopieer het volgende voorbeeld YAML.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    certmanager.k8s.io/cluster-issuer: letsencrypt-staging
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  tls:
  - hosts:
    - demo-aks-ingress.eastus.cloudapp.azure.com
    secretName: tls-secret
  rules:
  - host: demo-aks-ingress.eastus.cloudapp.azure.com
    http:
      paths:
      - path: /
        backend:
          serviceName: aks-helloworld
          servicePort: 80
      - path: /hello-world-two
        backend:
          serviceName: ingress-demo
          servicePort: 80
```

Maakt de ingress resource via de `kubectl apply -f hello-world-ingress.yaml` opdracht.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-configuration"></a>De configuratie van de inkomende gegevens testen

Open een webbrowser naar de FQDN-naam van uw ingangscontroller Kubernetes zoals *https://demo-aks-ingress.eastus.cloudapp.azure.com*.

Zoals deze voorbeelden `letsencrypt-staging`, het verleende SSL-certificaat niet wordt vertrouwd door de browser. Accepteer de waarschuwing om door te gaan naar uw toepassing. Informatie over het certificaat wordt dit weergegeven *vals LE tussenliggende X1* certificaat is uitgegeven door laten versleutelen. Dit certificaat valse geeft `cert-manager` verwerkt de aanvraag goed en ontvangen van een certificaat van de provider:

![We gaan coderen staging-certificaat](media/ingress/staging-certificate.png)

Wanneer u wijzigen we versleutelen gebruiken `prod` in plaats van `staging`, een vertrouwd certificaat dat is uitgegeven door laten versleutelen wordt gebruikt, zoals wordt weergegeven in het volgende voorbeeld:

![Laten we gecodeerd certificaat](media/ingress/certificate.png)

De demo-toepassing wordt weergegeven in de webbrowser:

![Voorbeeld van de toepassing een](media/ingress/app-one.png)

Voeg nu de */hello-world-two* pad naar de FQDN-naam, zoals *https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two*. De tweede demo-toepassing met de aangepaste titel wordt weergegeven:

![Voorbeeld van de toepassing twee](media/ingress/app-two.png)

## <a name="next-steps"></a>Volgende stappen

In dit artikel bevat enkele externe onderdelen over AKS. Zie voor meer informatie over deze onderdelen, de volgende project-pagina's:

- [Helm CLI][helm-cli]
- [NGINX-controller voor binnenkomend verkeer][nginx-ingress]
- [certificaat-manager][cert-manager]

U kunt ook het volgende doen:

- [Een eenvoudige ingangscontroller met verbinding met het externe netwerk maken][aks-ingress-basic]
- [De HTTP-aanvraag routering invoegtoepassing inschakelen][aks-http-app-routing]
- [Een controller voor binnenkomend verkeer die gebruikmaakt van een privé-interne netwerken en IP-adres maken][aks-ingress-internal]
- [Maken van een controller voor binnenkomend verkeer met een statisch openbaar IP-adres en we gaan coderen voor het automatisch genereren van TLS-certificaten configureren][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm#install-helm-cli
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-http-app-routing]: http-application-routing.md
