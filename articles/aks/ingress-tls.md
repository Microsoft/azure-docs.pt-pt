---
title: Criar uma entrada HTTPS com o cluster AKS (serviço kubernetes do Azure)
description: Saiba como instalar e configurar um controlador de entrada NGINX que usa vamos criptografar para a geração automática de certificado TLS em um cluster AKS (serviço de kubernetes do Azure).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/24/2019
ms.author: mlearned
ms.openlocfilehash: 30f25ad9152bc722b54a834ef0ed037ac1666014
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "67615298"
---
# <a name="create-an-https-ingress-controller-on-azure-kubernetes-service-aks"></a>Criar um controlador de entrada HTTPS no serviço kubernetes do Azure (AKS)

Um controlador de entrada é uma parte do software que fornece proxy reverso, roteamento de tráfego configurável e terminação de TLS para serviços Kubernetess. Os recursos de entrada do kubernetes são usados para configurar as regras de entrada e rotas para serviços individuais do kubernetes. Usando um controlador de entrada e regras de entrada, um único endereço IP pode ser usado para rotear o tráfego para vários serviços em um cluster kubernetes.

Este artigo mostra como implantar o controlador de [entrada Nginx][nginx-ingress] em um cluster do AKS (serviço kubernetes do Azure). O projeto [CERT-Manager][cert-manager] é usado para gerar e configurar automaticamente [][lets-encrypt] os certificados criptografados. Por fim, dois aplicativos são executados no cluster AKS, cada um deles acessível por um único endereço IP.

Também pode:

- [Criar um controlador de entrada básico com conectividade de rede externa][aks-ingress-basic]
- [Habilitar o complemento de roteamento de aplicativo HTTP][aks-http-app-routing]
- [Criar um controlador de entrada que usa uma rede interna e privada e um endereço IP][aks-ingress-internal]
- [Criar um controlador de entrada que usa seus próprios certificados TLS][aks-ingress-own-tls]
- [Criar um controlador de entrada que usa vamos criptografar para gerar automaticamente certificados TLS com um endereço IP público estático][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que você tenha um cluster AKS existente. Se você precisar de um cluster AKS, consulte o guia de início rápido do AKS [usando o CLI do Azure][aks-quickstart-cli] ou [usando o portal do Azure][aks-quickstart-portal].

Este artigo usa o Helm para instalar o controlador de entrada do NGINX, o CERT-Manager e um aplicativo Web de exemplo. Você precisa ter o Helm inicializado em seu cluster AKS e usar uma conta de serviço para o gaveta. Verifique se você está usando a versão mais recente do Helm. Para obter instruções de atualização, consulte o [Helm instalar documentos][helm-install]. Para obter mais informações sobre como configurar e usar o Helm, consulte [instalar aplicativos com Helm no serviço kubernetes do Azure (AKs)][use-helm].

Este artigo também requer que você esteja executando o CLI do Azure versão 2.0.64 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Criar um controlador de entrada

Para criar o controlador de entrada, use `Helm` para instalar o *Nginx-Ingres*. Para redundância adicional, duas réplicas dos controladores de entrada do Nginx são implantadas com `--set controller.replicaCount` o parâmetro. Para se beneficiar totalmente da execução de réplicas do controlador de entrada, verifique se há mais de um nó no cluster AKS.

O controlador de entrada também precisa ser agendado em um nó do Linux. Os nós do Windows Server (atualmente em visualização no AKS) não devem executar o controlador de entrada. Um seletor de nó é especificado `--set nodeSelector` usando o parâmetro para instruir o Agendador kubernetes a executar o controlador de entrada Nginx em um nó baseado em Linux.

> [!TIP]
> O exemplo a seguir cria um namespace kubernetes para os recursos de entrada chamados *ingress-Basic*. Especifique um namespace para seu próprio ambiente, conforme necessário. Se o cluster AKs não estiver habilitado para RBAC, `--set rbac.create=false` adicione aos comandos Helm.

> [!TIP]
> Se você quiser habilitar a [preservação de IP de origem do cliente][client-source-ip] para solicitações a contêineres em seu `--set controller.service.externalTrafficPolicy=Local` cluster, adicione ao comando Helm install. O IP de origem do cliente é armazenado no cabeçalho da solicitação em *X-forwardd-for*. Ao usar um controlador de entrada com preservação de IP de origem do cliente habilitada, a passagem SSL não funcionará.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Use Helm to deploy an NGINX ingress controller
helm install stable/nginx-ingress \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Durante a instalação, um endereço IP público do Azure é criado para o controlador de entrada. Esse endereço IP público é estático para o período de vida útil do controlador de entrada. Se você excluir o controlador de entrada, a atribuição de endereço IP público será perdida. Se você criar um controlador de entrada adicional, um novo endereço IP público será atribuído. Se você quiser manter o uso do endereço IP público, poderá [criar um controlador de entrada com um endereço IP público estático][aks-ingress-static-tls].

Para obter o endereço IP público, use o `kubectl get service` comando. Leva alguns minutos para que o endereço IP seja atribuído ao serviço.

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                             TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
billowing-kitten-nginx-ingress-controller        LoadBalancer   10.0.182.160   51.145.155.210  80:30920/TCP,443:30426/TCP   20m
billowing-kitten-nginx-ingress-default-backend   ClusterIP      10.0.255.77    <none>          80/TCP                       20m
```

Nenhuma regra de entrada foi criada ainda. Se você navegar até o endereço IP público, a página padrão 404 do controlador de entrada do NGINX será exibida.

## <a name="configure-a-dns-name"></a>Configurar um nome DNS

Para que os certificados HTTPS funcionem corretamente, configure um FQDN para o endereço IP do controlador de entrada. Atualize o script a seguir com o endereço IP do controlador de entrada e um nome exclusivo que você deseja usar para o FQDN:

```azurecli-interactive
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

O controlador de entrada agora está acessível por meio do FQDN.

## <a name="install-cert-manager"></a>Instalar o CERT-Manager

O controlador de entrada NGINX dá suporte à terminação de TLS. Há várias maneiras de recuperar e configurar certificados para HTTPS. Este artigo demonstra como usar o [CERT-Manager][cert-manager], que fornece a funcionalidade de gerenciamento e geração [de certificados][lets-encrypt] automáticos.

> [!NOTE]
> Este artigo usa o `staging` ambiente para criptografar. Em implantações de produção `letsencrypt-prod` , `https://acme-v02.api.letsencrypt.org/directory` use e nas definições de recurso e ao instalar o gráfico Helm.

Para instalar o controlador CERT-Manager em um cluster habilitado para RBAC, use o seguinte `helm install` comando:

```console
# Install the CustomResourceDefinition resources separately
kubectl apply -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.8/deploy/manifests/00-crds.yaml

# Create the namespace for cert-manager
kubectl create namespace cert-manager

# Label the cert-manager namespace to disable resource validation
kubectl label namespace cert-manager certmanager.k8s.io/disable-validation=true

# Add the Jetstack Helm repository
helm repo add jetstack https://charts.jetstack.io

# Update your local Helm chart repository cache
helm repo update

# Install the cert-manager Helm chart
helm install \
  --name cert-manager \
  --namespace cert-manager \
  --version v0.8.0 \
  jetstack/cert-manager
```

Para obter mais informações sobre a configuração do Gerenciador de certificados, consulte o [projeto CERT-Manager][cert-manager].

## <a name="create-a-ca-cluster-issuer"></a>Criar um emissor de cluster de autoridade de certificação

Antes que os certificados possam ser emitidos, o CERT-Manager requer um recurso de [emissor][cert-manager-issuer] ou [ClusterIssuer][cert-manager-cluster-issuer] . Esses recursos kubernetes são idênticos na funcionalidade, `Issuer` no entanto, funcionam em um `ClusterIssuer` único namespace e funcionam em todos os namespaces. Para obter mais informações, consulte a documentação do [emissor do Gerenciador de certificados][cert-manager-issuer] .

Crie um emissor de cluster, como `cluster-issuer.yaml`, usando o manifesto de exemplo a seguir. Atualize o endereço de email com um endereço válido da sua organização:

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: ClusterIssuer
metadata:
  name: letsencrypt-staging
  namespace: ingress-basic
spec:
  acme:
    server: https://acme-staging-v02.api.letsencrypt.org/directory
    email: user@contoso.com
    privateKeySecretRef:
      name: letsencrypt-staging
    http01: {}
```

Para criar o emissor, use o `kubectl apply -f cluster-issuer.yaml` comando.

```
$ kubectl apply -f cluster-issuer.yaml

clusterissuer.certmanager.k8s.io/letsencrypt-staging created
```

## <a name="run-demo-applications"></a>Executar aplicativos de demonstração

Um controlador de entrada e uma solução de gerenciamento de certificados foram configurados. Agora, vamos executar dois aplicativos de demonstração em seu cluster AKS. Neste exemplo, Helm é usado para implantar duas instâncias de um aplicativo "Hello World" simples.

Antes de instalar os gráficos de exemplo do Helm, adicione o repositório de exemplos do Azure ao seu ambiente Helm da seguinte maneira:

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Crie o primeiro aplicativo de demonstração de um gráfico do Helm com o seguinte comando:

```console
helm install azure-samples/aks-helloworld --namespace ingress-basic
```

Agora, instale uma segunda instância do aplicativo de demonstração. Para a segunda instância, você especifica um novo título para que os dois aplicativos sejam visualmente distintos. Você também especifica um nome de serviço exclusivo:

```console
helm install azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>Criar uma rota de entrada

Os dois aplicativos agora estão em execução no cluster kubernetes, no entanto, eles são configurados com um serviço do tipo `ClusterIP`. Assim, os aplicativos não são acessíveis pela Internet. Para torná-los publicamente disponíveis, crie um recurso de entrada do kubernetes. O recurso de entrada configura as regras que roteiam o tráfego para um dos dois aplicativos.

No exemplo a seguir, o tráfego para o `https://demo-aks-ingress.eastus.cloudapp.azure.com/` endereço é roteado para o `aks-helloworld`serviço chamado. O tráfego para o `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` endereço é roteado `ingress-demo` para o serviço. Atualize os *hosts* e o *host* para o nome DNS que você criou em uma etapa anterior.

Crie um arquivo chamado `hello-world-ingress.yaml` e copie no exemplo a seguir YAML.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    certmanager.k8s.io/cluster-issuer: letsencrypt-staging
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  tls:
  - hosts:
    - demo-aks-ingress.eastus.cloudapp.azure.com
    secretName: tls-secret
  rules:
  - host: demo-aks-ingress.eastus.cloudapp.azure.com
    http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /(.*)
      - backend:
          serviceName: ingress-demo
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
```

Crie o recurso de entrada usando o `kubectl apply -f hello-world-ingress.yaml` comando.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="create-a-certificate-object"></a>Criar um objeto de certificado

Em seguida, um recurso de certificado deve ser criado. O recurso de certificado define o certificado X. 509 desejado. Para obter mais informações, consulte [certificados do Gerenciador de][cert-manager-certificates]certificados.

O CERT-Manager provavelmente criou automaticamente um objeto de certificado para você usando o Shim de entrada, que é implantado automaticamente com o CERT-Manager desde o v 0.2.2. Para obter mais informações, consulte a [documentação de Shim da entrada][ingress-shim].

Para verificar se o certificado foi criado com êxito, use o `kubectl describe certificate tls-secret --namespace ingress-basic` comando.

Se o certificado foi emitido, você verá uma saída semelhante à seguinte:
```
Type    Reason          Age   From          Message
----    ------          ----  ----          -------
  Normal  CreateOrder     11m   cert-manager  Created new ACME order, attempting validation...
  Normal  DomainVerified  10m   cert-manager  Domain "demo-aks-ingress.eastus.cloudapp.azure.com" verified with "http-01" validation
  Normal  IssueCert       10m   cert-manager  Issuing certificate...
  Normal  CertObtained    10m   cert-manager  Obtained certificate from ACME server
  Normal  CertIssued      10m   cert-manager  Certificate issued successfully
```

Se você precisar criar um recurso de certificado adicional, poderá fazer isso com o manifesto de exemplo a seguir. Atualize o *dnsNames* e os *domínios* para o nome DNS que você criou em uma etapa anterior. Se você usar um controlador de entrada somente interno, especifique o nome DNS interno para seu serviço.

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: Certificate
metadata:
  name: tls-secret
  namespace: ingress-basic
spec:
  secretName: tls-secret-staging
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

Para criar o recurso de certificado, use `kubectl apply -f certificates.yaml` o comando.

```
$ kubectl apply -f certificates.yaml

certificate.certmanager.k8s.io/tls-secret-staging created
```

## <a name="test-the-ingress-configuration"></a>Testar a configuração de entrada

Abra um navegador da Web para o FQDN do controlador de entrada do kubernetes, como *https://demo-aks-ingress.eastus.cloudapp.azure.com* .

Como esses exemplos usam `letsencrypt-staging`, o certificado SSL emitido não é confiável para o navegador. Aceite a solicitação de aviso para continuar em seu aplicativo. As informações de certificado mostram que esse certificado *falso Le intermediário X1* é emitido por criptografar. Esse certificado falso indica `cert-manager` que a solicitação foi processada corretamente e recebeu um certificado do provedor:

![Vamos criptografar o certificado de preparo](media/ingress/staging-certificate.png)

Quando você altera, vamos criptografar para `prod` uso em `staging`vez de, um certificado confiável emitido por vamos criptografar é usado, conforme mostrado no exemplo a seguir:

![Vamos criptografar o certificado](media/ingress/certificate.png)

O aplicativo de demonstração é mostrado no navegador da Web:

![Exemplo de aplicativo One](media/ingress/app-one.png)

Agora, adicione o caminho */Hello-World-Two* ao FQDN, *https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two* como. O segundo aplicativo de demonstração com o título personalizado é mostrado:

![Exemplo de aplicativo dois](media/ingress/app-two.png)

## <a name="clean-up-resources"></a>Limpar recursos

Este artigo usou o Helm para instalar os componentes de entrada, os certificados e os aplicativos de exemplo. Quando você implanta um gráfico do Helm, vários recursos do kubernetes são criados. Esses recursos incluem pods, implantações e serviços. Para limpar esses recursos, você pode excluir o namespace de exemplo inteiro ou os recursos individuais.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Excluir o namespace de exemplo e todos os recursos

Para excluir o namespace de exemplo inteiro, use `kubectl delete` o comando e especifique o nome do namespace. Todos os recursos no namespace são excluídos.

```console
kubectl delete namespace ingress-basic
```

Em seguida, remova o repositório Helm para o aplicativo Hello World do AKS:

```console
helm repo remove azure-samples
```

### <a name="delete-resources-individually"></a>Excluir recursos individualmente

Como alternativa, uma abordagem mais granular é excluir os recursos individuais criados. Primeiro, remova os recursos de certificado:

```console
kubectl delete -f certificates.yaml
kubectl delete -f cluster-issuer.yaml
```

Agora, liste as versões Helm `helm list` com o comando. Procure gráficos chamados *Nginx-ingress*, *CERT-Manager*e *AKs-HelloWorld*, conforme mostrado na seguinte saída de exemplo:

```
$ helm list

NAME                    REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
billowing-kitten        1           Wed Mar  6 19:37:43 2019    DEPLOYED    nginx-ingress-1.3.1     0.22.0      kube-system
loitering-waterbuffalo  1           Wed Mar  6 20:25:01 2019    DEPLOYED    cert-manager-v0.6.6     v0.6.2      kube-system
flabby-deer             1           Wed Mar  6 20:27:54 2019    DEPLOYED    aks-helloworld-0.1.0                default
linting-echidna         1           Wed Mar  6 20:27:59 2019    DEPLOYED    aks-helloworld-0.1.0                default
```

Exclua as versões com `helm delete` o comando. O exemplo a seguir exclui a implantação de entrada do NGINX, o Gerenciador de certificados e os dois exemplos de aplicativos do Hello World do AKS.

```
$ helm delete billowing-kitten loitering-waterbuffalo flabby-deer linting-echidna

release "billowing-kitten" deleted
release "loitering-waterbuffalo" deleted
release "flabby-deer" deleted
release "linting-echidna" deleted
```

Em seguida, remova o repositório Helm para o aplicativo Hello World do AKS:

```console
helm repo remove azure-samples
```

Exclua o namespace em si. Use o `kubectl delete` comando e especifique o nome do namespace:

```console
kubectl delete namespace ingress-basic
```

Por fim, remova a rota de entrada que direcionou o tráfego para os aplicativos de exemplo:

```console
kubectl delete -f hello-world-ingress.yaml
```

## <a name="next-steps"></a>Passos Seguintes

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