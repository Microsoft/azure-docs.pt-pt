---
title: Utilize um controlador de ingresso de traefik personalizado e configure HTTPS
services: azure-dev-spaces
ms.date: 12/10/2019
ms.topic: conceptual
description: Saiba como configurar o Azure Dev Spaces para utilizar um controlador de ingresso de traefik personalizado e configurar HTTPS utilizando esse controlador de ingresso
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contentores, Helm, malha de serviço, encaminhamento de malha de serviço, kubectl, k8s
ms.openlocfilehash: fd11b3bbd3f90b75203084ff0753c1485d57a35b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80155434"
---
# <a name="use-a-custom-traefik-ingress-controller-and-configure-https"></a>Utilize um controlador de ingresso de traefik personalizado e configure HTTPS

Este artigo mostra-lhe como configurar o Azure Dev Spaces para utilizar um controlador de entrada de traefik personalizado. Este artigo também mostra como configurar esse controlador de ingresso personalizado para usar HTTPS.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma, pode criar uma [conta gratuita.][azure-account-create]
* A [CLI do Azure instalada][az-cli].
* [Cluster azure Kubernetes Service (AKS) com espaços Azure Dev ativados][qs-cli].
* [kubectl][kubectl] instalado.
* [Leme 3 instalado.][helm-installed]
* [Um domínio personalizado][custom-domain] com uma [Zona DNS.][dns-zone] Este artigo assume que o domínio personalizado e a Zona DNS estão no mesmo grupo de recursos que o seu cluster AKS, mas é possível usar um domínio personalizado e zona DNS em um grupo de recursos diferente.

## <a name="configure-a-custom-traefik-ingress-controller"></a>Configure um controlador de ingresso de traefik personalizado

Ligue-se ao seu cluster usando [kubectl,][kubectl]o cliente da linha de comando Kubernetes. Para configurar `kubectl` para se ligar ao cluster do Kubernetes, utilize o comando [az aks get-credentials][az-aks-get-credentials]. Este comando descarrega credenciais e confunde o ClI Kubernetes para usá-las.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKS
```

Para verificar a ligação ao cluster, utilize o comando [kubectl get][kubectl-get] para devolver uma lista de nós do cluster.

```console
kubectl get nodes
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.1
```

Adicione o [repositório oficial de Helm,][helm-stable-repo]que contém o gráfico helm do controlador de entrada traefik.

```console
helm repo add stable https://kubernetes-charts.storage.googleapis.com/
```

Crie um espaço de nome Kubernetes para o `helm`controlador de entrada de traefik e instale-o utilizando .

> [!NOTE]
> Se o seu cluster AKS não tiver RBAC ativado, remova o *rbac.enabled=verdadeiro* parâmetro.

```console
kubectl create ns traefik
helm install traefik stable/traefik --namespace traefik --set kubernetes.ingressClass=traefik --set rbac.enabled=true --set fullnameOverride=customtraefik --set kubernetes.ingressEndpoint.useDefaultPublishedService=true --version 1.85.0
```

> [!NOTE]
> O exemplo acima cria um ponto final público para o seu controlador de ingresso. Se precisar de utilizar um ponto final privado para o seu controlador de ingresso, adicione o *serviço .-set.anotações." serviço\\\\.beta\\.kubernetes .io/azure-load-balancer-internal"=verdadeiro* parâmetro para o comando de instalação do *leme.*
> ```console
> helm install traefik stable/traefik --namespace traefik --set kubernetes.ingressClass=traefik --set rbac.enabled=true --set fullnameOverride=customtraefik --set kubernetes.ingressEndpoint.useDefaultPublishedService=true --set service.annotations."service\.beta\.kubernetes\.io/azure-load-balancer-internal"=true --version 1.85.0
> ```
> Este ponto final privado está exposto dentro da rede virtual onde o cluster AKS é implantado.

Obtenha o endereço IP do serviço de controlador de ingresso traefik utilizando [kubectl get][kubectl-get].

```console
kubectl get svc -n traefik --watch
```

A saída da amostra mostra os endereços IP de todos os serviços no espaço de nome *traefik.*

```console
NAME      TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
traefik   LoadBalancer   10.0.205.78   <pending>     80:32484/TCP,443:30620/TCP   20s
...
traefik   LoadBalancer   10.0.205.78   MY_EXTERNAL_IP   80:32484/TCP,443:30620/TCP   60s
```

Adicione um registo *A* à sua zona DNS com o endereço IP externo do serviço traefik utilizando um registo de dns de [rede az .][az-network-dns-record-set-a-add-record]

```azurecli
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name *.traefik \
    --ipv4-address MY_EXTERNAL_IP
```

O exemplo acima adiciona um recorde *a um* registo a *MY_CUSTOM_DOMAIN* zona DNS.

Neste artigo, utiliza a aplicação de partilha de [bicicletas Azure Dev Spaces](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) para demonstrar a utilização dos Espaços Azure Dev. Clone a aplicação do GitHub e navegue para o seu diretório:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/charts
```

Abra [os valores.yaml][values-yaml] e faça as seguintes atualizações:
* Substitua todas as instâncias de *<REPLACE_ME_WITH_HOST_SUFFIX>* por *traefik. MY_CUSTOM_DOMAIN* usar o seu domínio para *MY_CUSTOM_DOMAIN*. 
* Substitua *kubernetes.io/ingress.class: traefik-azds # Dev Spaces-specific* com *kubernetes.io/ingress.class: traefik # Custom Ingress*. 

Abaixo está um exemplo `values.yaml` de um ficheiro atualizado:

```yaml
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

bikesharingweb:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
    hosts:
      - dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space

gateway:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
    hosts:
      - dev.gateway.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
```

Guarde as suas alterações e feche o ficheiro.

Crie o espaço *de dev* com a aplicação da amostra utilizando `azds space select`.

```console
azds space select -n dev -y
```

Implementar a aplicação da amostra utilizando `helm install`.

```console
helm install bikesharingsampleapp . --dependency-update --namespace dev --atomic
```

O exemplo acima implementa a aplicação da amostra para o espaço de nome de *v.*

Mostrar os URLs para aceder `azds list-uris`à aplicação da amostra utilizando .

```console
azds list-uris
```

A saída abaixo mostra o `azds list-uris`exemplo URLs de .

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/  Available
http://dev.gateway.traefik.MY_CUSTOM_DOMAIN/         Available
```

Navegue para o serviço *bikesharingweb* abrindo o URL público a partir do `azds list-uris` comando. No exemplo acima, o URL público para `http://dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/`o serviço *bikesharingweb* é .

> [!NOTE]
> Se vir uma página de erro em vez do serviço *bikesharingweb,* verifique se atualizou tanto a *anotação kubernetes.io/ingress.class* **como** o anfitrião no ficheiro *values.yaml.*

Utilize `azds space select` o comando para criar um espaço para crianças em *dev* e enumere os URLs para aceder ao espaço de dev infantil.

```console
azds space select -n dev/azureuser1 -y
azds list-uris
```

A saída abaixo mostra o `azds list-uris` exemplo URLs de acesso à aplicação da amostra no espaço de v de v infantil *azureuser1.*

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/  Available
http://azureuser1.s.dev.gateway.traefik.MY_CUSTOM_DOMAIN/         Available
```

Navegue para o serviço *bikesharingweb* no espaço *azureuser1* child `azds list-uris` dev, abrindo o URL público a partir do comando. No exemplo acima, o URL público para o serviço de *bikesharingweb* `http://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/`no espaço *azureuser1* child dev é .

## <a name="configure-the-traefik-ingress-controller-to-use-https"></a>Configure o controlador de ingresso traefik para usar HTTPS

Utilize o [cert-manager][cert-manager] para automatizar a gestão do certificado TLS ao configurar o seu controlador de entrada traefik para utilizar HTTPS. Utilize `helm` para instalar a ficha *certmanager.*

```console
kubectl apply --validate=false -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.12/deploy/manifests/00-crds.yaml --namespace traefik
kubectl label namespace traefik certmanager.k8s.io/disable-validation=true
helm repo add jetstack https://charts.jetstack.io
helm repo update
helm install cert-manager --namespace traefik --version v0.12.0 jetstack/cert-manager --set ingressShim.defaultIssuerName=letsencrypt --set ingressShim.defaultIssuerKind=ClusterIssuer
```

Crie `letsencrypt-clusterissuer.yaml` um ficheiro e atualize o campo de e-mail com o seu endereço de e-mail.

```yaml
apiVersion: cert-manager.io/v1alpha2
kind: ClusterIssuer
metadata:
  name: letsencrypt
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: MY_EMAIL_ADDRESS
    privateKeySecretRef:
      name: letsencrypt
    solvers:
      - http01:
          ingress:
            class: traefik
```

> [!NOTE]
> Para testar, existe também um servidor de [encenação][letsencrypt-staging-issuer] que pode utilizar para o seu *ClusterIssuer*.

Use `kubectl` para `letsencrypt-clusterissuer.yaml`aplicar .

```console
kubectl apply -f letsencrypt-clusterissuer.yaml --namespace traefik
```

Remova o *clusterRole* de *traefik* anterior e *clusterRoleBinding,* em `helm`seguida, atualize o traefik para utilizar HTTPS usando .

> [!NOTE]
> Se o seu cluster AKS não tiver RBAC ativado, remova o *rbac.enabled=verdadeiro* parâmetro.

```console
kubectl delete ClusterRole traefik
kubectl delete ClusterRoleBinding traefik
helm upgrade traefik stable/traefik --namespace traefik --set kubernetes.ingressClass=traefik --set rbac.enabled=true --set kubernetes.ingressEndpoint.useDefaultPublishedService=true --version 1.85.0 --set ssl.enabled=true --set ssl.enforced=true --set ssl.permanentRedirect=true
```

Obtenha o endereço IP atualizado do serviço de controlador de ingresso traefik utilizando [kubectl get][kubectl-get].

```console
kubectl get svc -n traefik --watch
```

A saída da amostra mostra os endereços IP de todos os serviços no espaço de nome *traefik.*

```console
NAME      TYPE           CLUSTER-IP    EXTERNAL-IP          PORT(S)                      AGE
traefik   LoadBalancer   10.0.205.78   <pending>            80:32484/TCP,443:30620/TCP   20s
...
traefik   LoadBalancer   10.0.205.78   MY_NEW_EXTERNAL_IP   80:32484/TCP,443:30620/TCP   60s
```

Adicione um registo *A* à sua zona DNS com o novo endereço IP externo do serviço traefik utilizando um registo de dns de [rede az][az-network-dns-record-set-a-add-record] e remova o anterior registo *A* utilizando um disco de dns de [rede az.][az-network-dns-record-set-a-remove-record]

```azurecli
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name *.traefik \
    --ipv4-address MY_NEW_EXTERNAL_IP

az network dns record-set a remove-record \
    --resource-group myResourceGroup \
    --zone-name  MY_CUSTOM_DOMAIN \
    --record-set-name *.traefik \
    --ipv4-address PREVIOUS_EXTERNAL_IP
```

O exemplo acima atualiza o registo *A* na zona *MY_CUSTOM_DOMAIN* DNS para utilizar *PREVIOUS_EXTERNAL_IP*.

Atualizar [valores.yaml][values-yaml] para incluir os detalhes para a utilização *de cert-manager* e HTTPS. Abaixo está um exemplo `values.yaml` de um ficheiro atualizado:

```yaml
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

bikesharingweb:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
      cert-manager.io/cluster-issuer: letsencrypt
    hosts:
      - dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
    tls:
    - hosts:
      - dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN
      secretName: dev-bikesharingweb-secret

gateway:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
      cert-manager.io/cluster-issuer: letsencrypt
    hosts:
      - dev.gateway.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
    tls:
    - hosts:
      - dev.gateway.traefik.MY_CUSTOM_DOMAIN
      secretName: dev-gateway-secret
```

Atualize a `helm`aplicação da amostra utilizando:

```console
helm upgrade bikesharingsampleapp . --namespace dev --atomic
```

Navegue para a aplicação da amostra no espaço infantil *dev/azureuser1* e note que é redirecionado para usar HTTPS.

> [!IMPORTANT]
> Pode levar 30 minutos ou mais para que as alterações do DNS sejam concluídas e a sua aplicação de amostra sabote.

Note também que a página carrega, mas o navegador mostra alguns erros. A abertura da consola do navegador mostra que o erro está relacionado com uma página HTTPS que tenta carregar recursos HTTP. Por exemplo:

```console
Mixed Content: The page at 'https://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/devsignin' was loaded over HTTPS, but requested an insecure resource 'http://azureuser1.s.dev.gateway.traefik.MY_CUSTOM_DOMAIN/api/user/allUsers'. This request has been blocked; the content must be served over HTTPS.
```

Para corrigir este erro, atualize [bikeSharingWeb/azds.yaml][azds-yaml] para utilizar *traefik* para *kubernetes.io/ingress.class* e o seu domínio personalizado por *$(hostSuffix)*. Por exemplo:

```yaml
...
    ingress:
      annotations:
        kubernetes.io/ingress.class: traefik
      hosts:
      # This expands to [space.s.][rootSpace.]bikesharingweb.<random suffix>.<region>.azds.io
      - $(spacePrefix)$(rootSpacePrefix)bikesharingweb.traefik.MY_CUSTOM_DOMAIN
...
```

Atualizar [BikeSharingWeb/package.json][package-json] com uma dependência para o pacote *de url.*

```json
{
...
    "react-responsive": "^6.0.1",
    "universal-cookie": "^3.0.7",
    "url": "0.11.0"
  },
...
```

Atualize o método *getApiHostAsync* em [BikeSharingWeb/lib/helpers.js][helpers-js] para utilizar HTTPS:

```javascript
...
    getApiHostAsync: async function() {
        const apiRequest = await fetch('/api/host');
        const data = await apiRequest.json();
        
        var urlapi = require('url');
        var url = urlapi.parse(data.apiHost);

        console.log('apiHost: ' + "https://"+url.host);
        return "https://"+url.host;
    },
...
```

Navegue `BikeSharingWeb` para o `azds up` diretório e use para executar o seu serviço atualizado BikeSharingWeb.

```console
cd ../BikeSharingWeb/
azds up
```

Navegue para a aplicação da amostra no espaço infantil *dev/azureuser1* e note que é redirecionado para utilizar HTTPS sem erros.

## <a name="next-steps"></a>Passos seguintes

Saiba como o Azure Dev Spaces o ajuda a desenvolver aplicações mais complexas em vários recipientes e como pode simplificar o desenvolvimento colaborativo trabalhando com diferentes versões ou ramos do seu código em diferentes espaços.

> [!div class="nextstepaction"]
> [Desenvolvimento de equipa em Espaços Azure Dev][team-development-qs]


[az-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/a?view=azure-cli-latest#az-network-dns-record-set-a-add-record
[az-network-dns-record-set-a-remove-record]: /cli/azure/network/dns/record-set/a?view=azure-cli-latest#az-network-dns-record-set-a-remove-record
[custom-domain]: ../../app-service/manage-custom-dns-buy-domain.md#buy-the-domain
[dns-zone]: ../../dns/dns-getstarted-cli.md
[qs-cli]: ../quickstart-cli.md
[team-development-qs]: ../quickstart-team-development.md

[azds-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/azds.yaml
[azure-account-create]: https://azure.microsoft.com/free
[cert-manager]: https://cert-manager.io/
[helm-installed]: https://helm.sh/docs/intro/install/
[helm-stable-repo]: https://helm.sh/docs/intro/quickstart/#initialize-a-helm-chart-repository
[helpers-js]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/lib/helpers.js#L7
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[letsencrypt-staging-issuer]: https://cert-manager.io/docs/configuration/acme/#creating-a-basic-acme-issuer
[package-json]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/package.json
[values-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml