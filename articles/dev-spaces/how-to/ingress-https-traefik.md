---
title: Use um controlador de entrada de traefik personalizado e configuure HTTPS
services: azure-dev-spaces
ms.date: 12/10/2019
ms.topic: conceptual
description: Aprenda a configurar a Azure Dev Spaces para usar um controlador de entrada de traefik personalizado e configuure HTTPS usando esse controlador ingresss
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contentores, Helm, malha de serviço, encaminhamento de malha de serviço, kubectl, k8s
ms.custom: devx-track-js
ms.openlocfilehash: a04b46297d4eef6403f580206795bb492dd82516
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374034"
---
# <a name="use-a-custom-traefik-ingress-controller-and-configure-https"></a>Use um controlador de entrada de traefik personalizado e configuure HTTPS

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

Este artigo mostra-lhe como configurar a Azure Dev Spaces para usar um controlador de entrada de traefik personalizado. Este artigo também mostra como configurar o controlador de entrada personalizado para usar HTTPS.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma, pode criar uma [conta gratuita.][azure-account-create]
* A [CLI do Azure instalada][az-cli].
* [Agrupamento de Serviços Azure Kubernetes (AKS) com espaços Azure Dev habilitados] [qs-cli].
* [kubectl][kubectl] instalado.
* [Leme 3 instalado][helm-installed].
* [Um domínio personalizado][custom-domain] com uma [Zona DNS][dns-zone]. Este artigo assume que o domínio personalizado e a Zona DNS estão no mesmo grupo de recursos que o seu cluster AKS, mas é possível usar um domínio personalizado e a Zona DNS num grupo de recursos diferente.

## <a name="configure-a-custom-traefik-ingress-controller"></a>Configure um controlador de entrada de traefik personalizado

Ligue-se ao seu cluster utilizando [kubectl,][kubectl]o cliente da linha de comando Kubernetes. Para configurar `kubectl` para se ligar ao cluster do Kubernetes, utilize o comando [az aks get-credentials][az-aks-get-credentials]. Este comando descarrega credenciais e configura o CLI de Kubernetes para usá-las.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKS
```

Para verificar a ligação ao cluster, utilize o comando [kubectl get][kubectl-get] para devolver uma lista de nós do cluster.

```console
kubectl get nodes
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.1
```

Adicione o [repositório oficial de Helm estável,][helm-stable-repo]que contém o gráfico helm controlador de entrada de traefik.

```console
helm repo add stable https://charts.helm.sh/stable
```

Crie um espaço de nome Kubernetes para o controlador de entrada de traefik e instale-o utilizando `helm` .

> [!NOTE]
> Se o seu cluster AKS não tiver o RBAC de Kubernetes ativado, remova o parâmetro *rbac.ativado=verdadeiro.*

```console
kubectl create ns traefik
helm install traefik stable/traefik --namespace traefik --set kubernetes.ingressClass=traefik --set rbac.enabled=true --set fullnameOverride=customtraefik --set kubernetes.ingressEndpoint.useDefaultPublishedService=true --version 1.85.0
```

> [!NOTE]
> O exemplo acima cria um ponto final público para o seu controlador de entrada. Se precisar de utilizar um ponto final privado para o seu controlador de entrada, adicione o *serviço de serviço .anotações." serviço \\ .beta \\ .kubernetes \\ .io/azure-load-balancer-internal"=parâmetro verdadeiro* para o comando de instalação do *leme.*
> ```console
> helm install traefik stable/traefik --namespace traefik --set kubernetes.ingressClass=traefik --set rbac.enabled=true --set fullnameOverride=customtraefik --set kubernetes.ingressEndpoint.useDefaultPublishedService=true --set service.annotations."service\.beta\.kubernetes\.io/azure-load-balancer-internal"=true --version 1.85.0
> ```
> Este ponto final privado está exposto dentro da rede virtual onde o cluster AKS é implantado.

Obtenha o endereço IP do serviço de controlador de entrada de traefik usando [o kubectl get][kubectl-get].

```console
kubectl get svc -n traefik --watch
```

A saída da amostra mostra os endereços IP para todos os serviços no espaço de nome *traefik.*

```console
NAME      TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
traefik   LoadBalancer   10.0.205.78   <pending>     80:32484/TCP,443:30620/TCP   20s
...
traefik   LoadBalancer   10.0.205.78   MY_EXTERNAL_IP   80:32484/TCP,443:30620/TCP   60s
```

Adicione um registo *A* à sua zona de DNS com o endereço IP externo do serviço traefik utilizando [o registo de dns da rede Az][az-network-dns-record-set-a-add-record].

```azurecli
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name *.traefik \
    --ipv4-address MY_EXTERNAL_IP
```

O exemplo acima adiciona um registo *A* à zona *de DNS MY_CUSTOM_DOMAIN.*

Neste artigo, você usa a [aplicação de bicicleta Azure Dev Spaces Partilhar](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) para demonstrar usando Azure Dev Spaces. Clone a aplicação do GitHub e navegue no seu diretório:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/charts
```

Abrir [valores.yaml][values-yaml] e fazer as seguintes atualizações:
* Substitua todas as instâncias de *<REPLACE_ME_WITH_HOST_SUFFIX>* por *traefik. MY_CUSTOM_DOMAIN* utilizar o seu domínio durante *MY_CUSTOM_DOMAIN*. 
* Substitua *kubernetes.io/ingress.class: traefik-azds # Dev Spaces-specific* com *kubernetes.io/ingress.class: traefik # Custom Ingress*. 

Abaixo está um exemplo de um `values.yaml` ficheiro atualizado:

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

Crie o *espaço dev* com a sua aplicação de amostra utilizando `azds space select` .

```console
azds space select -n dev -y
```

Implemente a aplicação da amostra utilizando `helm install` .

```console
helm install bikesharingsampleapp . --dependency-update --namespace dev --atomic
```

O exemplo acima implanta a aplicação da amostra no espaço de nome *dev.*

Mostrar os URLs para aceder à aplicação da amostra utilizando `azds list-uris` .

```console
azds list-uris
```

A saída abaixo mostra o exemplo URLs de `azds list-uris` .

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/  Available
http://dev.gateway.traefik.MY_CUSTOM_DOMAIN/         Available
```

Navegue para o serviço *de bikesharingweb* abrindo o URL público a partir do `azds list-uris` comando. No exemplo acima, o URL público para o serviço *de partilha de bicicletas* é `http://dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/` .

> [!NOTE]
> Se vir uma página de erro em vez do serviço *bikesharingweb,* verifique se atualizou **tanto** a *anotação kubernetes.io/ingress.class* como o anfitrião no ficheiro *values.yaml.*

Utilize o `azds space select` comando para criar um espaço para crianças sob *dev* e listar os URLs para aceder ao espaço dev infantil.

```console
azds space select -n dev/azureuser1 -y
azds list-uris
```

A saída abaixo mostra o exemplo URLs de `azds list-uris` aceder à aplicação da amostra no espaço dev dev para *crianças azureuser1.*

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/  Available
http://azureuser1.s.dev.gateway.traefik.MY_CUSTOM_DOMAIN/         Available
```

Navegue para o serviço *de partilha de bicicletasweb* no espaço *azureuser1* child dev, abrindo o URL público a partir do `azds list-uris` comando. No exemplo acima, o URL público para o serviço *de partilha de bicicletasweb* no espaço *azureuser1* child dev é `http://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/` .

## <a name="configure-the-traefik-ingress-controller-to-use-https"></a>Configure o controlador de entrada de traefik para usar HTTPS

Utilize [o cert-manager][cert-manager] para automatizar a gestão do certificado TLS ao configurar o controlador de entrada de traefik para utilizar HTTPS. Utilize `helm` para instalar o gráfico *certmanager.*

```console
kubectl apply --validate=false -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.12/deploy/manifests/00-crds.yaml --namespace traefik
kubectl label namespace traefik certmanager.k8s.io/disable-validation=true
helm repo add jetstack https://charts.jetstack.io
helm repo update
helm install cert-manager --namespace traefik --version v0.12.0 jetstack/cert-manager --set ingressShim.defaultIssuerName=letsencrypt --set ingressShim.defaultIssuerKind=ClusterIssuer
```

Crie um `letsencrypt-clusterissuer.yaml` ficheiro e atualize o campo de e-mail com o seu endereço de e-mail.

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
> Para testes, existe também um [servidor de preparação][letsencrypt-staging-issuer] que pode utilizar para o seu *ClusterIssuer*.

Utilizar `kubectl` para aplicar `letsencrypt-clusterissuer.yaml` .

```console
kubectl apply -f letsencrypt-clusterissuer.yaml --namespace traefik
```

Remova o *clusterrole* e  *clusterRoleBinding* anteriores, em seguida, atualize traefik para utilizar HTTPS utilizando `helm` .

> [!NOTE]
> Se o seu cluster AKS não tiver o RBAC de Kubernetes ativado, remova o parâmetro *rbac.ativado=verdadeiro.*

```console
kubectl delete ClusterRole traefik
kubectl delete ClusterRoleBinding traefik
helm upgrade traefik stable/traefik --namespace traefik --set kubernetes.ingressClass=traefik --set rbac.enabled=true --set kubernetes.ingressEndpoint.useDefaultPublishedService=true --version 1.85.0 --set ssl.enabled=true --set ssl.enforced=true --set ssl.permanentRedirect=true
```

Obtenha o endereço IP atualizado do serviço de controlador de ingresss traefik usando [o kubectl get][kubectl-get].

```console
kubectl get svc -n traefik --watch
```

A saída da amostra mostra os endereços IP para todos os serviços no espaço de nome *traefik.*

```console
NAME      TYPE           CLUSTER-IP    EXTERNAL-IP          PORT(S)                      AGE
traefik   LoadBalancer   10.0.205.78   <pending>            80:32484/TCP,443:30620/TCP   20s
...
traefik   LoadBalancer   10.0.205.78   MY_NEW_EXTERNAL_IP   80:32484/TCP,443:30620/TCP   60s
```

Adicione um registo *A* à sua zona de DNS com o novo endereço IP externo do serviço traefik utilizando o [registo de registo da rede Az e][az-network-dns-record-set-a-add-record] remova o registo *A* anterior utilizando o registo [de dns da rede Az, estabelecendo um registo de remoção][az-network-dns-record-set-a-remove-record].

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

O exemplo acima atualiza o registo *A* na zona *de DNS MY_CUSTOM_DOMAIN* para utilizar *PREVIOUS_EXTERNAL_IP*.

Atualizar [valores.yaml][values-yaml] para incluir os detalhes para a utilização *de cert-manager* e HTTPS. Abaixo está um exemplo de um `values.yaml` ficheiro atualizado:

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

Atualize a aplicação da amostra `helm` utilizando:

```console
helm upgrade bikesharingsampleapp . --namespace dev --atomic
```

Navegue para a aplicação da amostra no espaço *infantil dev/azureuser1* e note que é redirecionado para utilizar HTTPS.

> [!IMPORTANT]
> Pode levar 30 minutos ou mais para que as alterações ao DNS sejam completadas e a sua aplicação de amostra seja acessível.

Note também que a página carrega, mas o navegador mostra alguns erros. A abertura da consola do navegador mostra que o erro diz respeito a uma página HTTPS que tenta carregar recursos HTTP. Por exemplo:

```console
Mixed Content: The page at 'https://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/devsignin' was loaded over HTTPS, but requested an insecure resource 'http://azureuser1.s.dev.gateway.traefik.MY_CUSTOM_DOMAIN/api/user/allUsers'. This request has been blocked; the content must be served over HTTPS.
```

Para corrigir este erro, atualize [BikeSharingWeb/azds.yaml][azds-yaml] para utilizar *traefik* para *kubernetes.io/ingress.class* e o seu domínio personalizado para *$(hostSuffix)*. Por exemplo:

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

Atualizar [BikeSharingWeb/package.js][package-json] com uma dependência para o pacote *url.*

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

Navegue para o `BikeSharingWeb` diretório e use `azds up` para executar o seu serviço BikeSharingWeb atualizado.

```console
cd ../BikeSharingWeb/
azds up
```

Navegue para a aplicação da amostra no espaço *infantil dev/azureuser1* e note que é redirecionado para usar HTTPS sem erros.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o funcionamento da Azure Dev Spaces.

> [!div class="nextstepaction"]
> [Como funciona o Azure Dev Spaces](../how-dev-spaces-works.md)


[az-cli]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/a#az-network-dns-record-set-a-add-record
[az-network-dns-record-set-a-remove-record]: /cli/azure/network/dns/record-set/a#az-network-dns-record-set-a-remove-record
[custom-domain]: ../../app-service/manage-custom-dns-buy-domain.md#buy-an-app-service-domain
[dns-zone]: ../../dns/dns-getstarted-cli.md
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
