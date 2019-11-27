---
title: Usar um controlador de entrada do traefik personalizado e configurar o HTTPS
services: azure-dev-spaces
ms.date: 08/13/2019
ms.topic: conceptual
description: Saiba como configurar Azure Dev Spaces para usar um controlador de entrada do traefik personalizado e configurar o HTTPS usando esse controlador de entrada
keywords: Docker, kubernetes, Azure, AKS, serviço kubernetes do Azure, contêineres, Helm, malha de serviço, roteamento de malha de serviço, kubectl, K8S
ms.openlocfilehash: 8ddaa7b3e982cb85428a7faef20b59525a175778
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482530"
---
# <a name="use-a-custom-traefik-ingress-controller-and-configure-https"></a>Usar um controlador de entrada do traefik personalizado e configurar o HTTPS

Este artigo mostra como configurar Azure Dev Spaces para usar um controlador de entrada do traefik personalizado. Este artigo também mostra como configurar esse controlador de entrada personalizado para usar HTTPS.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma, poderá [criar uma conta gratuita][azure-account-create].
* A [CLI do Azure instalada][az-cli].
* [Cluster do serviço de kubernetes do Azure (AKs) com Azure dev Spaces habilitado][qs-cli].
* [kubectl][kubectl] instalado.
* [Helm 2,13-2,16 instalado][helm-installed].
* [Um domínio personalizado][custom-domain] com uma [zona DNS][dns-zone] no mesmo grupo de recursos que o cluster AKs.

## <a name="configure-a-custom-traefik-ingress-controller"></a>Configurar um controlador de entrada do traefik personalizado

Conecte-se ao cluster usando o [kubectl][kubectl], o cliente de linha de comando do kubernetes. Para configurar `kubectl` para se conectar ao cluster kubernetes, use o comando [AZ AKs Get-Credentials][az-aks-get-credentials] . Esse comando baixa as credenciais e configura a CLI do kubernetes para usá-las.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKS
```

Para verificar a ligação ao cluster, utilize o comando [kubectl get][kubectl-get] para devolver uma lista de nós do cluster.

```console
$ kubectl get nodes
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.1
```

Crie um namespace kubernetes para o controlador de entrada do traefik e instale-o usando `helm`.

```console
kubectl create ns traefik
helm init --wait
helm install stable/traefik --name traefik --namespace traefik --set kubernetes.ingressClass=traefik --set kubernetes.ingressEndpoint.useDefaultPublishedService=true
```

Obtenha o endereço IP do serviço do controlador de entrada traefik usando o [kubectl Get][kubectl-get].

```console
kubectl get svc -n traefik --watch
```

A saída de exemplo mostra os endereços IP para todos os serviços no espaço de nome *traefik* .

```console
NAME      TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
traefik   LoadBalancer   10.0.205.78   <pending>     80:32484/TCP,443:30620/TCP   20s
...
traefik   LoadBalancer   10.0.205.78   MY_EXTERNAL_IP   80:32484/TCP,443:30620/TCP   60s
```

Adicione um *registro a* à zona DNS com o endereço IP externo do serviço traefik usando [AZ Network DNS Record-set a Add-Record][az-network-dns-record-set-a-add-record].

```console
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name *.traefik \
    --ipv4-address MY_EXTERNAL_IP
```

O exemplo acima adiciona um *registro a* à zona DNS *MY_CUSTOM_DOMAIN* .

Neste artigo, você usa o [aplicativo de exemplo de compartilhamento de bicicletas Azure dev Spaces](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) para demonstrar o uso de Azure dev Spaces. Clone o aplicativo do GitHub e navegue até seu diretório:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/charts
```

Abra [Values. YAML][values-yaml] e substitua todas as instâncias de *< REPLACE_ME_WITH_HOST_SUFFIX >* com *traefik. MY_CUSTOM_DOMAIN* usando seu domínio para *MY_CUSTOM_DOMAIN*. Substitua também *kubernetes.Io/ingress.class: traefik-azds # dev Spaces – specific* com *kubernetes.Io/ingress.class: Traefik # entrada personalizada*. Abaixo está um exemplo de um arquivo de `values.yaml` atualizado:

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

Salve as alterações e feche o arquivo.

Implante o aplicativo de exemplo usando `helm install`.

```console
helm install -n bikesharing . --dep-up --namespace dev --atomic
```

O exemplo acima implanta o aplicativo de exemplo no namespace do *dev* .

Selecione o espaço de *desenvolvimento* com seu aplicativo de exemplo usando `azds space select` e exiba as URLs para acessar o aplicativo de exemplo usando `azds list-uris`.

```console
azds space select -n dev
azds list-uris
```

A saída abaixo mostra as URLs de exemplo de `azds list-uris`.

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/  Available
http://dev.gateway.traefik.MY_CUSTOM_DOMAIN/         Available
```

Navegue até o serviço *bikesharingweb* abrindo a URL pública do comando `azds list-uris`. No exemplo acima, a URL pública para o serviço *bikesharingweb* é `http://dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/`.

Use o comando `azds space select` para criar um espaço filho em *dev* e listar as URLs para acessar o espaço de desenvolvimento filho.

```console
azds space select -n dev/azureuser1 -y
azds list-uris
```

A saída abaixo mostra as URLs de exemplo de `azds list-uris` para acessar o aplicativo de exemplo no espaço de desenvolvimento de *azureuser1* filho.

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/  Available
http://azureuser1.s.dev.gateway.traefik.MY_CUSTOM_DOMAIN/         Available
```

Navegue até o serviço *bikesharingweb* no espaço de desenvolvimento filho do *AZUREUSER1* abrindo a URL pública do comando `azds list-uris`. No exemplo acima, a URL pública para o serviço *bikesharingweb* no espaço de desenvolvimento filho *azureuser1* é `http://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/`.

## <a name="configure-the-traefik-ingress-controller-to-use-https"></a>Configurar o controlador de entrada traefik para usar HTTPS

Crie um arquivo de `dev-spaces/samples/BikeSharingApp/traefik-values.yaml` semelhante ao exemplo abaixo. Atualize o valor de *email* com seu próprio email, que é usado para gerar o certificado com o que está criptografado.

```yaml
fullnameOverride: traefik
replicas: 1
cpuLimit: 400m
memoryRequest: 200Mi
memoryLimit: 500Mi
externalTrafficPolicy: Local
kubernetes:
  ingressClass: traefik
  ingressEndpoint:
    useDefaultPublishedService: true
dashboard:
  enabled: false
debug:
  enabled: false
accessLogs:
  enabled: true
  fields:
    defaultMode: keep
    headers:
      defaultMode: keep
      names:
        Authorization: redact
acme:
  enabled: true
  email: "someone@example.com"
  staging: false
  challengeType: tls-alpn-01
ssl:
  enabled: true
  enforced: true
  permanentRedirect: true
  tlsMinVersion: VersionTLS12
  cipherSuites:
    - TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
    - TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
    - TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
    - TLS_RSA_WITH_AES_128_GCM_SHA256
    - TLS_RSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
```

Atualize seu serviço *traefik* usando `helm repo update` e incluindo o arquivo *traefik-Values. YAML* que você criou.

```console
cd ..
helm upgrade traefik stable/traefik --namespace traefik --values traefik-values.yaml
```

O comando acima executa uma nova versão do serviço traefik usando os valores de *traefik-Values. YAML* e remove o serviço anterior. O serviço traefik também cria um certificado TLS usando vamos criptografar e começa a redirecionar o tráfego da Web para usar HTTPS.

> [!NOTE]
> Pode levar alguns minutos para que a nova versão do serviço traefik seja iniciada. Você pode verificar o progresso usando `kubectl get pods --namespace traefik --watch`.

Navegue até o aplicativo de exemplo no espaço filho *dev/azureuser1* e observe que você é redirecionado para usar HTTPS. Observe também que a página é carregada, mas o navegador mostra alguns erros. Abrir o console do navegador mostra o erro relacionado a uma página HTTPS tentando carregar recursos HTTP. Por exemplo:

```console
Mixed Content: The page at 'https://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/devsignin' was loaded over HTTPS, but requested an insecure resource 'http://azureuser1.s.dev.gateway.traefik.MY_CUSTOM_DOMAIN/api/user/allUsers'. This request has been blocked; the content must be served over HTTPS.
```

Para corrigir esse erro, atualize [BikeSharingWeb/azds. YAML][azds-yaml] para usar *traefik* para *kubernetes.Io/ingress.Class* e seu domínio personalizado para *$ (hostSuffix)* . Por exemplo:

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

Atualize [BikeSharingWeb/Package. JSON][package-json] com uma dependência para o pacote de *URL* .

```json
{
...
    "react-responsive": "^6.0.1",
    "universal-cookie": "^3.0.7",
    "url": "0.11.0"
  },
...
```

Atualize o método *getApiHostAsync* em [BikeSharingWeb/Pages/Helpers. js][helpers-js] para usar https:

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

Navegue até o diretório `BikeSharingWeb` e use `azds up` para executar o serviço BikeSharingWeb atualizado.

```console
cd BikeSharingWeb/
azds up
```

Navegue até o aplicativo de exemplo no espaço filho *dev/azureuser1* e observe que você é redirecionado para usar HTTPS sem erros.

## <a name="next-steps"></a>Passos Seguintes

Saiba como Azure Dev Spaces ajuda a desenvolver aplicativos mais complexos em vários contêineres e como você pode simplificar o desenvolvimento colaborativo trabalhando com diferentes versões ou branches do seu código em espaços diferentes.

> [!div class="nextstepaction"]
> [Desenvolvimento em equipe no Azure Dev Spaces][team-development-qs]


[az-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/a?view=azure-cli-latest#az-network-dns-record-set-a-add-record
[custom-domain]: ../../app-service/manage-custom-dns-buy-domain.md#buy-the-domain
[dns-zone]: ../../dns/dns-getstarted-cli.md
[qs-cli]: ../quickstart-cli.md
[team-development-qs]: ../quickstart-team-development.md

[azds-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/azds.yaml
[azure-account-create]: https://azure.microsoft.com/free
[helm-installed]: https://v2.helm.sh/docs/using_helm/#installing-helm
[helpers-js]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/pages/helpers.js#L7
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[package-json]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/package.json
[values-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml