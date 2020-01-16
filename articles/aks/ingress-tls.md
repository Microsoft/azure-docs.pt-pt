---
title: Criar uma entrada HTTPS com o cluster AKS (serviço kubernetes do Azure)
description: Saiba como instalar e configurar um controlador de entrada NGINX que usa vamos criptografar para a geração automática de certificado TLS em um cluster AKS (serviço de kubernetes do Azure).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 01/15/2020
ms.author: mlearned
ms.openlocfilehash: 5df7a8a639c87f90df0d3d22f84c4e93ac81e3a2
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028304"
---
# <a name="create-an-https-ingress-controller-on-azure-kubernetes-service-aks"></a>Criar um controlador de entrada HTTPS no serviço kubernetes do Azure (AKS)

Um controlador de entrada é uma parte do software que fornece proxy reverso, roteamento de tráfego configurável e terminação de TLS para serviços Kubernetess. Os recursos de entrada do kubernetes são usados para configurar as regras de entrada e rotas para serviços individuais do kubernetes. Usando um controlador de entrada e regras de entrada, um único endereço IP pode ser usado para rotear o tráfego para vários serviços em um cluster kubernetes.

Este artigo mostra como implantar o controlador de [entrada Nginx][nginx-ingress] em um cluster do AKS (serviço kubernetes do Azure). O projeto [CERT-Manager][cert-manager] é usado para gerar e configurar automaticamente os certificados [criptografados][lets-encrypt] . Por fim, dois aplicativos são executados no cluster AKS, cada um deles acessível por um único endereço IP.

Também pode:

- [Criar um controlador de entrada básico com conectividade de rede externa][aks-ingress-basic]
- [Habilitar o complemento de roteamento de aplicativo HTTP][aks-http-app-routing]
- [Criar um controlador de entrada que usa uma rede interna e privada e um endereço IP][aks-ingress-internal]
- [Criar um controlador de entrada que usa seus próprios certificados TLS][aks-ingress-own-tls]
- [Criar um controlador de entrada que usa vamos criptografar para gerar automaticamente certificados TLS com um endereço IP público estático][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que você tenha um cluster AKS existente. Se você precisar de um cluster AKS, consulte o guia de início rápido do AKS [usando o CLI do Azure][aks-quickstart-cli] ou [usando o portal do Azure][aks-quickstart-portal].

Este artigo também pressupõe que você tenha [um domínio personalizado][custom-domain] com uma [zona DNS][dns-zone] no mesmo grupo de recursos que o cluster AKs.

Este artigo usa o Helm para instalar o controlador de entrada do NGINX, o CERT-Manager e um aplicativo Web de exemplo. Verifique se você está usando a versão mais recente do Helm. Para obter instruções de atualização, consulte o [Helm instalar documentos][helm-install]. Para obter mais informações sobre como configurar e usar o Helm, consulte [instalar aplicativos com Helm no serviço kubernetes do Azure (AKs)][use-helm].

Este artigo também requer que você esteja executando o CLI do Azure versão 2.0.64 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Criar um controlador de entrada

Para criar o controlador de entrada, use o comando `helm` para instalar o *Nginx-ingress*. Para redundância adicional, duas réplicas dos controladores de entrada do NGINX são implantadas com o parâmetro `--set controller.replicaCount`. Para se beneficiar totalmente da execução de réplicas do controlador de entrada, verifique se há mais de um nó no cluster AKS.

O controlador de entrada também precisa ser agendado em um nó do Linux. Os nós do Windows Server (atualmente em visualização no AKS) não devem executar o controlador de entrada. Um seletor de nó é especificado usando o parâmetro `--set nodeSelector` para instruir o Agendador kubernetes a executar o controlador de entrada NGINX em um nó baseado em Linux.

> [!TIP]
> O exemplo a seguir cria um namespace kubernetes para os recursos de entrada chamados *ingress-Basic*. Especifique um namespace para seu próprio ambiente, conforme necessário.

> [!TIP]
> Se você quiser habilitar a [preservação de IP de origem do cliente][client-source-ip] para solicitações a contêineres no cluster, adicione `--set controller.service.externalTrafficPolicy=Local` ao comando de instalação Helm. O IP de origem do cliente é armazenado no cabeçalho da solicitação em *X-forwardd-for*. Ao usar um controlador de entrada com preservação de IP de origem do cliente habilitada, a passagem SSL não funcionará.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the official stable repo
helm repo add stable https://kubernetes-charts.storage.googleapis.com/

# Use Helm to deploy an NGINX ingress controller
helm install nginx stable/nginx-ingress \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Durante a instalação, um endereço IP público do Azure é criado para o controlador de entrada. Esse endereço IP público é estático para o período de vida útil do controlador de entrada. Se você excluir o controlador de entrada, a atribuição de endereço IP público será perdida. Se você criar um controlador de entrada adicional, um novo endereço IP público será atribuído. Se você quiser manter o uso do endereço IP público, poderá [criar um controlador de entrada com um endereço IP público estático][aks-ingress-static-tls].

Para obter o endereço IP público, use o comando `kubectl get service`. Leva alguns minutos para que o endereço IP seja atribuído ao serviço.

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                             TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
billowing-kitten-nginx-ingress-controller        LoadBalancer   10.0.182.160   MY_EXTERNAL_IP  80:30920/TCP,443:30426/TCP   20m
billowing-kitten-nginx-ingress-default-backend   ClusterIP      10.0.255.77    <none>          80/TCP                       20m
```

Nenhuma regra de entrada foi criada ainda. Se você navegar até o endereço IP público, a página padrão 404 do controlador de entrada do NGINX será exibida.

## <a name="add-an-a-record-to-your-dns-zone"></a>Adicionar um registro a à zona DNS

Adicione um *registro a* à zona DNS com o endereço IP externo do serviço Nginx usando [AZ Network DNS Record-set a Add-Record][az-network-dns-record-set-a-add-record].

```console
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name * \
    --ipv4-address MY_EXTERNAL_IP
```

> [!NOTE]
> Opcionalmente, você pode configurar um FQDN para o endereço IP do controlador de entrada em vez de um domínio personalizado.
> 
> ```azurecli-interactive
> # Public IP address of your ingress controller
> IP="MY_EXTERNAL_IP"
> 
> # Name to associate with public IP address
> DNSNAME="demo-aks-ingress"
> 
> # Get the resource-id of the public ip
> PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)
> 
> # Update public ip address with DNS name
> az network public-ip update --ids $PUBLICIPID --dns-name $DNSNAME
> 
> # Display the FQDN
> az network public-ip show --ids $PUBLICIPID --query "[dnsSettings.fqdn]" --output tsv
> ```

## <a name="install-cert-manager"></a>Instalar o CERT-Manager

O controlador de entrada NGINX dá suporte à terminação de TLS. Há várias maneiras de recuperar e configurar certificados para HTTPS. Este artigo demonstra como usar o [CERT-Manager][cert-manager] [, que fornece][lets-encrypt] a funcionalidade de gerenciamento e geração de certificados automáticos.

Para instalar o controlador CERT-Manager:

```console
# Install the CustomResourceDefinition resources separately
kubectl apply --validate=false -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.12/deploy/manifests/00-crds.yaml --namespace ingress-basic

# Label the ingress-basic namespace to disable resource validation
kubectl label namespace ingress-basic certmanager.k8s.io/disable-validation=true

# Add the Jetstack Helm repository
helm repo add jetstack https://charts.jetstack.io

# Update your local Helm chart repository cache
helm repo update

# Install the cert-manager Helm chart
helm install cert-manager --namespace ingress-basic --version v0.12.0 jetstack/cert-manager --set ingressShim.defaultIssuerName=letsencrypt --set ingressShim.defaultIssuerKind=ClusterIssuer
```

Para obter mais informações sobre a configuração do Gerenciador de certificados, consulte o [projeto CERT-Manager][cert-manager].

## <a name="create-a-ca-cluster-issuer"></a>Criar um emissor de cluster de autoridade de certificação

Antes que os certificados possam ser emitidos, o CERT-Manager requer um recurso de [emissor][cert-manager-issuer] ou [ClusterIssuer][cert-manager-cluster-issuer] . Esses recursos kubernetes são idênticos na funcionalidade, no entanto `Issuer` funciona em um único namespace e `ClusterIssuer` funciona em todos os namespaces. Para obter mais informações, consulte a documentação do [emissor do Gerenciador de certificados][cert-manager-issuer] .

Crie um emissor de cluster, como `cluster-issuer.yaml`, usando o manifesto de exemplo a seguir. Atualize o endereço de email com um endereço válido da sua organização:

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

Para criar o emissor, use o comando `kubectl apply`.

```console
kubectl apply -f cluster-issuer.yaml --namespace ingress-basic
```

## <a name="run-demo-applications"></a>Executar aplicativos de demonstração

Um controlador de entrada e uma solução de gerenciamento de certificados foram configurados. Agora, vamos executar dois aplicativos de demonstração em seu cluster AKS. Neste exemplo, Helm é usado para implantar duas instâncias de um aplicativo *Hello World* simples.

Antes de instalar os gráficos de exemplo do Helm, adicione o repositório de exemplos do Azure ao seu ambiente Helm.

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Crie um aplicativo de demonstração chamado *AKs-HelloWorld* usando o gráfico *Azure-Samples/AKs-HelloWorld* Helm.

```console
helm install aks-helloworld azure-samples/aks-helloworld --namespace ingress-basic
```

Crie uma segunda instância do aplicativo de demonstração chamado *AKs-HelloWorld-Two*. Especifique um novo título e um nome de serviço exclusivo para que os dois aplicativos sejam visualmente distintos usando *--set*.

```console
helm install aks-helloworld-two azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="aks-helloworld-two"
```

## <a name="create-an-ingress-route"></a>Criar uma rota de entrada

Os dois aplicativos agora estão em execução no cluster kubernetes. No entanto, eles são configurados com um serviço do tipo `ClusterIP` e não são acessíveis pela Internet. Para torná-los publicamente disponíveis, crie um recurso de entrada do kubernetes. O recurso de entrada configura as regras que roteiam o tráfego para um dos dois aplicativos.

No exemplo a seguir, o tráfego para o endereço *Hello-World-ingress. MY_CUSTOM_DOMAIN* é roteado para o serviço *AKs-HelloWorld* . Tráfego para o endereço *Hello-World-ingress. MY_CUSTOM_DOMAIN/Hello-World-Two* é roteado para o serviço *AKs-HelloWorld-dois* . Tráfego para *Hello-World-Ingres. MY_CUSTOM_DOMAIN/static* é roteado para o serviço chamado *AKs-HelloWorld* para ativos estáticos.

Crie um arquivo chamado `hello-world-ingress.yaml` usando o exemplo abaixo YAML. Atualize os *hosts* e o *host* para o nome DNS que você criou em uma etapa anterior.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /$2
    cert-manager.io/cluster-issuer: letsencrypt
spec:
  tls:
  - hosts:
    - hello-world-ingress.MY_CUSTOM_DOMAIN
    secretName: tls-secret
  rules:
  - host: hello-world-ingress.MY_CUSTOM_DOMAIN
    http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /(.*)
      - backend:
          serviceName: aks-helloworld-two
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress-static
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /static/$2
    cert-manager.io/cluster-issuer: letsencrypt
spec:
  tls:
  - hosts:
    - hello-world-ingress.MY_CUSTOM_DOMAIN
    secretName: tls-secret
  rules:
  - host: hello-world-ingress.MY_CUSTOM_DOMAIN
    http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /static(/|$)(.*)
```

Crie o recurso de entrada usando o comando `kubectl apply`.

```console
kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic
```

## <a name="verify-a-certificate-object-has-been-created"></a>Verifique se um objeto de certificado foi criado

Em seguida, um recurso de certificado deve ser criado. O recurso de certificado define o certificado X. 509 desejado. Para obter mais informações, consulte [certificados do Gerenciador de][cert-manager-certificates]certificados. O Gerenciador de certificados criou automaticamente um objeto de certificado para você usando o Shim de entrada, que é implantado automaticamente com o CERT-Manager desde o v 0.2.2. Para obter mais informações, consulte a [documentação de Shim da entrada][ingress-shim].

Para verificar se o certificado foi criado com êxito, use o comando `kubectl get certificate --namespace ingress-basic` e verifique se *pronto* é *true*, o que pode levar vários minutos.

```
$ kubectl get certificate --namespace ingress-basic

NAME         READY   SECRET       AGE
tls-secret   True    tls-secret   11m
```

## <a name="test-the-ingress-configuration"></a>Testar a configuração de entrada

Abra um navegador da Web para *Hello-World-ingress. MY_CUSTOM_DOMAIN* do controlador de entrada do kubernetes. Observe que você está Redirecionado para usar HTTPS e o certificado é confiável e o aplicativo de demonstração é mostrado no navegador da Web. Adicione o caminho */Hello-World-Two* e observe que o segundo aplicativo de demonstração com o título personalizado é mostrado.

## <a name="clean-up-resources"></a>Limpar recursos

Este artigo usou o Helm para instalar os componentes de entrada, os certificados e os aplicativos de exemplo. Quando você implanta um gráfico do Helm, vários recursos do kubernetes são criados. Esses recursos incluem pods, implantações e serviços. Para limpar esses recursos, você pode excluir o namespace de exemplo inteiro ou os recursos individuais.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Excluir o namespace de exemplo e todos os recursos

Para excluir o namespace de exemplo inteiro, use o comando `kubectl delete` e especifique o nome do namespace. Todos os recursos no namespace são excluídos.

```console
kubectl delete namespace ingress-basic
```

Em seguida, remova o repositório Helm para o aplicativo Hello World do AKS:

```console
helm repo remove azure-samples
```

### <a name="delete-resources-individually"></a>Excluir recursos individualmente

Como alternativa, uma abordagem mais granular é excluir os recursos individuais criados. Primeiro, remova os recursos do emissor do cluster:

```console
kubectl delete -f cluster-issuer.yaml --namespace ingress-basic
```

Liste as versões Helm com o comando `helm list`. Procure gráficos chamados *Nginx-ingress* e *AKs-HelloWorld*, conforme mostrado na seguinte saída de exemplo:

```
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
aks-helloworld          ingress-basic   1               2020-01-15 10:24:32.054871 -0600 CST    deployed        aks-helloworld-0.1.0               
aks-helloworld-two      ingress-basic   1               2020-01-15 10:24:37.671667 -0600 CST    deployed        aks-helloworld-0.1.0               
cert-manager            ingress-basic   1               2020-01-15 10:23:36.515514 -0600 CST    deployed        cert-manager-v0.12.0    v0.12.0    
nginx                   ingress-basic   1               2020-01-15 10:09:45.982693 -0600 CST    deployed        nginx-ingress-1.29.1    0.27.0  
```

Exclua as versões com o comando `helm delete`. O exemplo a seguir exclui a implantação de entrada do NGINX e os dois aplicativos de exemplo do AKS Hello World.

```
$ helm delete aks-helloworld aks-helloworld-two cert-manager nginx --namespace ingress-basic

release "aks-helloworld" uninstalled
release "aks-helloworld-two" uninstalled
release "cert-manager" uninstalled
release "nginx" uninstalled
```

Em seguida, remova o repositório Helm para o aplicativo Hello World do AKS:

```console
helm repo remove azure-samples
```

Remova a rota de entrada que direcionou o tráfego para os aplicativos de exemplo:

```console
kubectl delete -f hello-world-ingress.yaml --namespace ingress-basic
```

Por fim, você pode excluir o namespace em si. Use o comando `kubectl delete` e especifique o nome do namespace:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Passos seguintes

Este artigo incluía alguns componentes externos no AKS. Para saber mais sobre esses componentes, consulte as seguintes páginas do projeto:

- [CLI do Helm][helm-cli]
- [Controlador de entrada do NGINX][nginx-ingress]
- [cert-manager][cert-manager]

Também pode:

- [Criar um controlador de entrada básico com conectividade de rede externa][aks-ingress-basic]
- [Habilitar o complemento de roteamento de aplicativo HTTP][aks-http-app-routing]
- [Criar um controlador de entrada que usa uma rede interna e privada e um endereço IP][aks-ingress-internal]
- [Criar um controlador de entrada que usa seus próprios certificados TLS][aks-ingress-own-tls]
- [Criar um controlador de entrada que usa vamos criptografar para gerar automaticamente certificados TLS com um endereço IP público estático][aks-ingress-static-tls]

<!-- LINKS - external -->
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/a?view=azure-cli-latest#az-network-dns-record-set-a-add-record
[custom-domain]: ../app-service/manage-custom-dns-buy-domain.md#buy-the-domain
[dns-zone]: ../dns/dns-getstarted-cli.md
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[ingress-shim]: https://docs.cert-manager.io/en/latest/tasks/issuing-certificates/ingress-shim.html
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
[aks-ingress-own-tls]: ingress-own-tls.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[client-source-ip]: concepts-network.md#ingress-controllers
[install-azure-cli]: /cli/azure/install-azure-cli
