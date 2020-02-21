---
title: Utilize um controlador de ingresso NGINX personalizado e configure HTTPS
services: azure-dev-spaces
ms.date: 12/10/2019
ms.topic: conceptual
description: Saiba como configurar o Azure Dev Spaces para utilizar um controlador de ingresso NGINX personalizado e configurar HTTPS utilizando esse controlador de ingresso
keywords: Docker, kubernetes, Azure, AKS, serviço kubernetes do Azure, contêineres, Helm, malha de serviço, roteamento de malha de serviço, kubectl, K8S
ms.openlocfilehash: 39f17636779c4160867311af67ebc621b685f2d3
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77486207"
---
# <a name="use-a-custom-nginx-ingress-controller-and-configure-https"></a>Utilize um controlador de ingresso NGINX personalizado e configure HTTPS

Este artigo mostra-lhe como configurar o Azure Dev Spaces para utilizar um controlador de entrada NGINX personalizado. Este artigo também mostra como configurar esse controlador de ingresso personalizado para usar HTTPS.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma, poderá [criar uma conta gratuita][azure-account-create].
* A [CLI do Azure instalada][az-cli].
* [Cluster azure Kubernetes Service (AKS) com espaços Azure Dev ativados][qs-cli].
* [kubectl][kubectl] instalado.
* [Leme 3 instalado.][helm-installed]
* [Um domínio personalizado][custom-domain] com uma [Zona DNS][dns-zone] no mesmo grupo de recursos que o seu cluster AKS.

## <a name="configure-a-custom-nginx-ingress-controller"></a>Configure um controlador de ingresso NNGX personalizado

Ligue-se ao seu cluster usando [kubectl,][kubectl]o cliente da linha de comando Kubernetes. Para configurar `kubectl` para se ligar ao seu cluster Kubernetes, use o comando [az aks get-credentials.][az-aks-get-credentials] Este comando descarrega credenciais e confunde o ClI Kubernetes para usá-las.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKS
```

Para verificar a ligação ao cluster, utilize o comando [kubectl get][kubectl-get] para devolver uma lista de nós do cluster.

```console
$ kubectl get nodes
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.1
```

Adicione o [repositório oficial de helm estável][helm-stable-repo], que contém o gráfico helm do controlador de entrada NGINX.

```console
helm repo add stable https://kubernetes-charts.storage.googleapis.com/
```

Crie um espaço de nome Kubernetes para o controlador de entrada NGINX e instale-o utilizando `helm`.

```console
kubectl create ns nginx
helm install nginx stable/nginx-ingress --namespace nginx --version 1.27.0
```

> [!NOTE]
> O exemplo acima cria um ponto final público para o seu controlador de ingresso. Se precisar de utilizar um ponto final privado para o seu controlador de ingresso, adicione o *controlador .service.service.anotações.". serviço\\.beta\\.kubernetes\\.io/azure-load-balancer-internal"=verdadeiro* parâmetro para o comando de instalação do *leme.* Por exemplo:
> ```console
> helm install nginx stable/nginx-ingress --namespace nginx --set controller.service.annotations."service\.beta\.kubernetes\.io/azure-load-balancer-internal"=true --version 1.27.0
> ```
> Este ponto final privado está exposto dentro da rede virtual onde o cluster AKS é implantado.

Obtenha o endereço IP do serviço de controlador de ingresso NGINX utilizando [kubectl get][kubectl-get].

```console
kubectl get svc -n nginx --watch
```

A saída da amostra mostra os endereços IP de todos os serviços no espaço de nome *nginx.*

```console
NAME                                  TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                      AGE
nginx-nginx-ingress-controller        LoadBalancer   10.0.19.39     <pending>        80:31314/TCP,443:30521/TCP   10s
nginx-nginx-ingress-default-backend   ClusterIP      10.0.210.231   <none>           80/TCP                       10s
...
nginx-nginx-ingress-controller        LoadBalancer   10.0.19.39     MY_EXTERNAL_IP   80:31314/TCP,443:30521/TCP   26s
```

Adicione um registo *A* à sua zona DNS com o endereço IP externo do serviço NGINX utilizando um registo de dns de [rede az .][az-network-dns-record-set-a-add-record]

```console
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name *.nginx \
    --ipv4-address MY_EXTERNAL_IP
```

O exemplo acima adiciona um recorde *a um* registo a *MY_CUSTOM_DOMAIN* zona DNS.

Neste artigo, utiliza a aplicação de partilha de [bicicletas Azure Dev Spaces](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) para demonstrar a utilização dos Espaços Azure Dev. Clone a aplicação do GitHub e navegue para o seu diretório:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/charts
```

Abra [os valores.yaml][values-yaml] e substitua todas as instâncias de *<REPLACE_ME_WITH_HOST_SUFFIX>* por *nginx. MY_CUSTOM_DOMAIN* usar o seu domínio para *MY_CUSTOM_DOMAIN*. Substitua também *kubernetes.io/ingress.class: nginx-azds # Dev Spaces-specific* com *kubernetes.io/ingress.class: nginx # Custom Ingress*. Abaixo está um exemplo de um ficheiro `values.yaml` atualizado:

```yaml
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

bikesharingweb:
  ingress:
    annotations:
      kubernetes.io/ingress.class: nginx  # Custom Ingress
    hosts:
      - dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space

gateway:
  ingress:
    annotations:
      kubernetes.io/ingress.class: nginx  # Custom Ingress
    hosts:
      - dev.gateway.nginx.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
```

Guarde as suas alterações e feche o ficheiro.

Crie o espaço *de dev* com a sua aplicação de amostra utilizando `azds space select`.

```console
azds space select -n dev -y
```

Implemente a aplicação da amostra utilizando `helm install`.

```console
helm install bikesharingsampleapp . --dependency-update --namespace dev --atomic
```

O exemplo acima implementa a aplicação da amostra para o espaço de nome de *v.*

Mostrar os URLs para aceder à aplicação da amostra utilizando `azds list-uris`.

```console
azds list-uris
```

A saída abaixo mostra o exemplo URLs de `azds list-uris`.

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN/  Available
http://dev.gateway.nginx.MY_CUSTOM_DOMAIN/         Available
```

Navegue para o serviço *bikesharingweb* abrindo o URL público a partir do comando `azds list-uris`. No exemplo acima, o URL público para o serviço *bikesharingweb* é `http://dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN/`.

Utilize o comando `azds space select` para criar um espaço para crianças em *dev* e enumere os URLs para aceder ao espaço de dev infantil.

```console
azds space select -n dev/azureuser1 -y
azds list-uris
```

A saída abaixo mostra o exemplo URLs de `azds list-uris` para aceder à aplicação da amostra no espaço de v de *crianças azureuser1.*

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://azureuser1.s.dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN/  Available
http://azureuser1.s.dev.gateway.nginx.MY_CUSTOM_DOMAIN/         Available
```

Navegue para o serviço de *web bikesharing* no espaço *azureuser1* child dev, abrindo o URL público a partir do comando `azds list-uris`. No exemplo acima, o URL público para o serviço de *web bikesharing* no espaço *azureuser1* child dev é `http://azureuser1.s.dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN/`.

## <a name="configure-the-nginx-ingress-controller-to-use-https"></a>Configure o controlador de ingresso NGINX para utilizar HTTPS

Utilize o [cert-manager][cert-manager] para automatizar a gestão do certificado TLS ao configurar o seu controlador de entrada NGINX para utilizar HTTPS. Utilize `helm` para instalar a tabela *certmanager.*

```console
kubectl apply --validate=false -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.12/deploy/manifests/00-crds.yaml --namespace nginx
kubectl label namespace nginx certmanager.k8s.io/disable-validation=true
helm repo add jetstack https://charts.jetstack.io
helm repo update
helm install cert-manager --namespace nginx --version v0.12.0 jetstack/cert-manager --set ingressShim.defaultIssuerName=letsencrypt --set ingressShim.defaultIssuerKind=ClusterIssuer
```

Crie um ficheiro `letsencrypt-clusterissuer.yaml` e atualize o campo de e-mail com o seu endereço de e-mail.

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
            class: nginx
```

> [!NOTE]
> Para testar, existe também um servidor de [encenação][letsencrypt-staging-issuer] que pode utilizar para o seu *ClusterIssuer*.

Utilize `kubectl` para aplicar `letsencrypt-clusterissuer.yaml`.

```console
kubectl apply -f letsencrypt-clusterissuer.yaml --namespace nginx
```

Atualizar [valores.yaml][values-yaml] para incluir os detalhes para a utilização *de cert-manager* e HTTPS. Abaixo está um exemplo de um ficheiro `values.yaml` atualizado:

```yaml
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

bikesharingweb:
  ingress:
    annotations:
      kubernetes.io/ingress.class: nginx  # Custom Ingress
      cert-manager.io/cluster-issuer: letsencrypt
    hosts:
      - dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
    tls:
    - hosts:
      - dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN
      secretName: dev-bikesharingweb-secret

gateway:
  ingress:
    annotations:
      kubernetes.io/ingress.class: nginx  # Custom Ingress
      cert-manager.io/cluster-issuer: letsencrypt
    hosts:
      - dev.gateway.nginx.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
    tls:
    - hosts:
      - dev.gateway.nginx.MY_CUSTOM_DOMAIN
      secretName: dev-gateway-secret
```

Atualize a aplicação da amostra utilizando `helm`:

```console
helm upgrade bikesharing . --namespace dev --atomic
```

Navegue para a aplicação da amostra no espaço infantil *dev/azureuser1* e note que é redirecionado para usar HTTPS. Note também que a página carrega, mas o navegador mostra alguns erros. A abertura da consola do navegador mostra que o erro está relacionado com uma página HTTPS que tenta carregar recursos HTTP. Por exemplo:

```console
Mixed Content: The page at 'https://azureuser1.s.dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN/devsignin' was loaded over HTTPS, but requested an insecure resource 'http://azureuser1.s.dev.gateway.nginx.MY_CUSTOM_DOMAIN/api/user/allUsers'. This request has been blocked; the content must be served over HTTPS.
```

Para corrigir este erro, atualize [bikeSharingWeb/azds.yaml][azds-yaml] semelhante ao seguinte:

```yaml
...
    ingress:
      annotations:
        kubernetes.io/ingress.class: nginx
        cert-manager.io/cluster-issuer: letsencrypt
      hosts:
      # This expands to [space.s.][rootSpace.]bikesharingweb.<random suffix>.<region>.azds.io
      - $(spacePrefix)$(rootSpacePrefix)bikesharingweb.nginx.MY_CUSTOM_DOMAIN
      tls:
      - hosts:
        - $(spacePrefix)$(rootSpacePrefix)bikesharingweb.nginx.MY_CUSTOM_DOMAIN
        secretName: dev-bikesharingweb-secret
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

Atualize o método *getApiHostAsync* em [BikeSharingWeb/pages/helpers.js][helpers-js] para utilizar HTTPS:

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

Navegue para o diretório `BikeSharingWeb` e use `azds up` para executar o seu serviço *atualizado BikeSharingWeb.*

```console
cd ../BikeSharingWeb/
azds up
```

Navegue para a aplicação da amostra no espaço infantil *dev/azureuser1* e note que é redirecionado para utilizar HTTPS sem erros.

## <a name="next-steps"></a>Passos Seguintes

Saiba como o Azure Dev Spaces o ajuda a desenvolver aplicações mais complexas em vários recipientes e como pode simplificar o desenvolvimento colaborativo trabalhando com diferentes versões ou ramos do seu código em diferentes espaços.

> [!div class="nextstepaction"]
> [Desenvolvimento de equipa em Espaços Azure Dev][team-development-qs]


[az-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/a?view=azure-cli-latest#az-network-dns-record-set-a-add-record
[custom-domain]: ../../app-service/manage-custom-dns-buy-domain.md#buy-the-domain
[dns-zone]: ../../dns/dns-getstarted-cli.md
[qs-cli]: ../quickstart-cli.md
[team-development-qs]: ../quickstart-team-development.md

[azds-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/azds.yaml
[azure-account-create]: https://azure.microsoft.com/free
[cert-manager]: https://cert-manager.io/
[helm-installed]: https://helm.sh/docs/intro/install/
[helm-stable-repo]: https://helm.sh/docs/intro/quickstart/#initialize-a-helm-chart-repository
[helpers-js]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/pages/helpers.js#L7
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[letsencrypt-staging-issuer]: https://cert-manager.io/docs/configuration/acme/#creating-a-basic-acme-issuer
[package-json]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/package.json
[values-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml