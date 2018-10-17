---
title: Criar um controlador de entrada HTTP com um endereço IP estático no Azure Kubernetes Service (AKS)
description: Saiba como instalar e configurar um controlador de entrada do NGINX com um endereço IP público estático num cluster do Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 08/30/2018
ms.author: iainfou
ms.openlocfilehash: 0ffa1541439890a0591b52c1fdbc717c7d5aa5ff
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2018
ms.locfileid: "49362562"
---
# <a name="create-an-ingress-controller-with-a-static-public-ip-address-in-azure-kubernetes-service-aks"></a>Criar um controlador de entrada com um endereço IP público estático no Azure Kubernetes Service (AKS)

Um controlador de entrada é uma parte do software que fornece o proxy inverso, encaminhamento de tráfego configurável e terminação de TLS para serviços do Kubernetes. Recursos de entrada do Kubernetes são utilizados para configurar as regras de entrada e as rotas para serviços individuais do Kubernetes. Utilizar um controlador de entrada e de regras de entrada, um único endereço IP pode servir-se para encaminhar o tráfego para vários serviços num cluster do Kubernetes.

Este artigo mostra-lhe como implementar o [controlador de entradas NGINX] [ nginx-ingress] num cluster do Azure Kubernetes Service (AKS). O controlador de entrada está configurado com um endereço IP público estático. O [Gestor de cert] [ cert-manager] projeto é utilizado para gerar e configurar automaticamente [vamos encriptar] [ lets-encrypt] certificados. Por fim, duas aplicações são executadas no cluster do AKS, cada um deles está acessível através de um único endereço IP.

Também pode:

- [Criar um controlador de entrada básico com conectividade de rede externa][aks-ingress-basic]
- [Ativar o suplemento de encaminhamento de aplicação de HTTP][aks-http-app-routing]
- [Criar um controlador de entrada que utiliza uma rede interna, privada e o endereço IP][aks-ingress-internal]
- [Criar um controlador de entrada com um IP público dinâmico e configurar a encriptar vamos para gerar automaticamente certificados TLS][aks-ingress-tls]

## <a name="before-you-begin"></a>Antes de começar

Este artigo utiliza o Helm para instalar o controlador de entrada do NGINX, o Gestor de certificado e um exemplo de aplicação web. Tem de ter o Helm inicializado no seu cluster do AKS e utilizar uma conta de serviço para Tiller. Certifique-se de que está a utilizar a última versão do Helm. Certifique-se de que está a utilizar a última versão do Helm. Para obter instruções de atualização, consulte a [Helm instalar docs][helm-install]. Para obter mais informações sobre como configurar e utilizar o Helm, consulte [instalar aplicações com Helm no Azure Kubernetes Service (AKS)][use-helm].

Este artigo também requer a execução da versão 2.0.41 da CLI do Azure ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Criar um controlador de entrada

Por predefinição, um controlador de entrada do NGINX é criado com uma nova atribuição de endereço IP pública. Este endereço IP público é estático único para o tempo de vida do controlador de entrada e se perdem se o controlador é eliminado e recriado. É um requisito de configuração comum fornecer o controlador de entrada do NGINX um endereço IP público de estático existente. O endereço IP público estático permanece se o controlador de entrada é eliminado. Esta abordagem permite-lhe utilizar registos DNS existentes e configurações de rede de forma consistente em todo o ciclo de vida das suas aplicações.

Se precisar de criar um endereço IP público estático, obtenha primeiro o nome do grupo de recursos do cluster AKS com o [show do az aks] [ az-aks-show] comando:

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

Em seguida, crie um endereço IP público com o *estático* através do método de alocação a [criar a rede de az public-ip] [ az-network-public-ip-create] comando. O exemplo seguinte cria um endereço IP público com o nome *myAKSPublicIP* no AKS cluster de grupo de recursos que obteve no passo anterior:

```azurecli
az network public-ip create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --allocation-method static
```

Implementar o *nginx entrada* gráfico com Helm. Adicionar o `--set controller.service.loadBalancerIP` parâmetro e especifique o seu próprio endereço IP público criado no passo anterior. Para maior redundância, as duas réplicas dos controladores de entrada de NGINX são implementadas com o `--set controller.replicaCount` parâmetro. Para beneficiar totalmente a execução de réplicas do controlador de entrada, certificar-se de que há mais de um nó no cluster do AKS.

> [!TIP]
> Os exemplos seguintes instalar o controlador de entrada e os certificados no `kube-system` espaço de nomes. Pode especificar um espaço de nomes diferente para o seu próprio ambiente, se assim o desejar. Além disso, se o cluster do AKS não for RBAC ativado, adicione `--set rbac.create=false` para os comandos.

```console
helm install stable/nginx-ingress \
    --namespace kube-system \
    --set controller.service.loadBalancerIP="40.121.63.72"  \
    --set controller.replicaCount=2
```

Quando o serviço de Balanceador de carga do Kubernetes é criado para o controlador de entrada do NGINX, o endereço IP estático é atribuído, conforme mostrado no seguinte exemplo:

```
$ kubectl get service -l app=nginx-ingress --namespace kube-system

NAME                                        TYPE           CLUSTER-IP    EXTERNAL-IP    PORT(S)                      AGE
dinky-panda-nginx-ingress-controller        LoadBalancer   10.0.232.56   40.121.63.72   80:31978/TCP,443:32037/TCP   3m
dinky-panda-nginx-ingress-default-backend   ClusterIP      10.0.95.248   <none>         80/TCP                       3m
```

Não existem regras de entrada foram criadas no entanto, para que a página 404 do controlador de entrada NGINX predefinida é apresentada se navegar até o endereço IP público. Regras de entrada estão configuradas nos passos seguintes.

## <a name="configure-a-dns-name"></a>Configurar um nome DNS

Para os certificados HTTPS funcionar corretamente, configure um FQDN para o endereço IP de controlador de entrada. Atualize o seguinte script com o endereço IP do seu controlador de entrada e um nome exclusivo que pretende utilizar para o FQDN:

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

O controlador de entrada está agora acessível através do FQDN.

## <a name="install-cert-manager"></a>Instalar o Gestor de certificado

O controlador de entrada do NGINX oferece suporte a terminação de TLS. Existem várias formas de obter e configurar certificados para HTTPS. Este artigo demonstra como utilizar [Gestor de cert][cert-manager], que fornece automática [permite encriptar] [ lets-encrypt] geração de certificados e funcionalidade de gestão.

> [!NOTE]
> Este artigo utiliza o `staging` ambiente para encriptar vamos. Nas implementações de produção, utilize `letsencrypt-prod` e `https://acme-v02.api.letsencrypt.org/directory` nas definições de recursos e ao instalar o gráfico do Helm.

Para instalar o controlador de Gestor de certificados num cluster habilitados no RBAC, utilize o seguinte `helm install` comando. Novamente, se assim o desejar, altere `--namespace` para algo diferente do *kube system*:

```console
helm install stable/cert-manager \
  --namespace kube-system \
  --set ingressShim.defaultIssuerName=letsencrypt-staging \
  --set ingressShim.defaultIssuerKind=ClusterIssuer
```

Se o cluster não for RBAC ativada, em vez disso, utilize o seguinte comando:

```console
helm install stable/cert-manager \
  --namespace kube-system \
  --set ingressShim.defaultIssuerName=letsencrypt-staging \
  --set ingressShim.defaultIssuerKind=ClusterIssuer \
  --set rbac.create=false \
  --set serviceAccount.create=false
```

Para obter mais informações sobre a configuração do Gestor de certificados, consulte a [cert-Gestor projeto][cert-manager].

## <a name="create-a-ca-cluster-issuer"></a>Criar um emissor de cluster de AC

Antes dos certificados podem ser emitidos, Gestor de certificados requer um [emissor] [ cert-manager-issuer] ou [ClusterIssuer] [ cert-manager-cluster-issuer] recursos. Esses recursos do Kubernetes são idênticos em funcionalidade, no entanto `Issuer` funciona num único espaço de nomes, e `ClusterIssuer` funciona em todos os espaços de nomes. Para obter mais informações, consulte a [cert-manager emissor] [ cert-manager-issuer] documentação.

Criar um emissor de cluster, tal como `cluster-issuer.yaml`, usando o manifesto de exemplo seguinte. Atualize o endereço de e-mail com um endereço válido da sua organização:

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

Para criar o emissor, utilize o `kubectl apply -f cluster-issuer.yaml` comando.

```
$ kubectl apply -f cluster-issuer.yaml

clusterissuer.certmanager.k8s.io/letsencrypt-staging created
```

## <a name="create-a-certificate-object"></a>Criar um objeto de certificado

Em seguida, um recurso de certificado tem de ser criado. O recurso de certificado define o certificado X.509 desejado. Para obter mais informações, consulte [cert-Gestor certificados][cert-manager-certificates].

Criar o recurso de certificado, tal como `certificates.yaml`, com o manifesto de exemplo seguinte. Atualização do *dnsNames* e *domínios* para o nome DNS que criou no passo anterior. Se usar um controlador de entrada apenas internos, especifique o nome DNS interno para o seu serviço.

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

Para criar o recurso de certificado, utilize o `kubectl apply -f certificates.yaml` comando.

```
$ kubectl apply -f certificates.yaml

certificate.certmanager.k8s.io/tls-secret created
```

## <a name="run-demo-applications"></a>Executar aplicações de demonstração

Um controlador de entrada e de uma solução de gestão de certificado foram configuradas. Agora vamos executadas dois demonstrar aplicativos no seu cluster do AKS. Neste exemplo, o Helm é utilizada para implementar duas instâncias de um aplicativo "Hello world" simples.

Antes de instalar os gráficos do Helm de exemplo, adicione o repositório de exemplos do Azure para o seu ambiente do Helm da seguinte forma:

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Crie a primeira aplicação de demonstração de um gráfico Helm com o seguinte comando:

```console
helm install azure-samples/aks-helloworld
```

Agora a instalar uma segunda instância do aplicativo de demonstração. Para a segunda instância, especifique um novo título para que as duas aplicações são visualmente distintas. Também especificar um nome de serviço único:

```console
helm install azure-samples/aks-helloworld --set title="AKS Ingress Demo" --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>Criar uma rota de entrada

Ambos os aplicativos agora estão em execução no seu cluster do Kubernetes, no entanto, estes são configurados com um serviço do tipo `ClusterIP`. Como tal, os aplicativos não estão acessíveis a partir da internet. Para que fiquem disponíveis publicamente, crie um recurso de entrada do Kubernetes. O recurso de entrada configura as regras que encaminham o tráfego para uma das duas aplicações.

No exemplo a seguir, o tráfego para o endereço `https://demo-aks-ingress.eastus.cloudapp.azure.com/` é encaminhado para o serviço com o nome `aks-helloworld`. O tráfego para o endereço `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` é encaminhado para o `ingress-demo` serviço. Atualização do *anfitriões* e *anfitrião* para o nome DNS que criou no passo anterior.

Crie um ficheiro denominado `hello-world-ingress.yaml` e copie o exemplo seguinte YAML.

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

Crie o recurso de entrada com o `kubectl apply -f hello-world-ingress.yaml` comando.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-configuration"></a>Testar a configuração de entrada

Abra um navegador da web para o FQDN do seu controlador de entrada do Kubernetes, tal como *https://demo-aks-ingress.eastus.cloudapp.azure.com*.

Conforme estes exemplos utilizam `letsencrypt-staging`, o certificado emitido de SSL não é considerado fidedigno pelo navegador. Aceite o pedido de aviso para continuar a sua aplicação. As informações do certificado mostram isso *falsificação LE X1 intermediários* certificado é emitido por vamos encriptar. Este certificado falso indica `cert-manager` processou o pedido corretamente e recebeu um certificado do fornecedor:

![Vamos encriptar o certificado de teste](media/ingress/staging-certificate.png)

Quando altera a encriptar vamos usar `prod` vez `staging`, é utilizado um certificado fidedigno emitido por vamos encriptar, conforme mostrado no exemplo a seguir:

![Vamos encriptar certificado](media/ingress/certificate.png)

A aplicação de demonstração é mostrada no navegador da web:

![Um exemplo de aplicativo](media/ingress/app-one.png)

Agora, adicione a */hello-world-two* caminho para o FQDN, tal como *https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two*. O segundo aplicativo de demonstração com o título personalizado é apresentado:

![Exemplo de aplicativo dois](media/ingress/app-two.png)

## <a name="clean-up-resources"></a>Limpar recursos

Este artigo usou o Helm para instalar os componentes de entrada, certificados e aplicações de exemplo. Quando implementa um gráfico Helm, um número de recursos do Kubernetes é criado. Estes recursos incluem pods, implementações e serviços. Para limpar, remova primeiro os recursos de certificado:

```console
kubectl delete -f certificates.yaml
kubectl delete -f cluster-issuer.yaml
```

Agora, lista as versões do Helm com a `helm list` comando. Procure gráficos chamados *entrada de nginx*, *Gestor de certificado*, e *aks-helloworld*, conforme mostrado no seguinte exemplo:

```
$ helm list

NAME                    REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
waxen-hamster           1           Tue Oct 16 17:44:28 2018    DEPLOYED    nginx-ingress-0.22.1    0.15.0      kube-system
alliterating-peacock    1           Tue Oct 16 18:03:11 2018    DEPLOYED    cert-manager-v0.3.4     v0.3.2      kube-system
mollified-armadillo     1           Tue Oct 16 18:04:53 2018    DEPLOYED    aks-helloworld-0.1.0                default
wondering-clam          1           Tue Oct 16 18:04:56 2018    DEPLOYED    aks-helloworld-0.1.0                default
```

Eliminar as versões com a `helm delete` comando. O exemplo seguinte elimina a implementação de entrada do NGINX, o Gestor de certificados e as dois exemplos AKS Olá mundo de aplicações.

```
$ helm delete waxen-hamster alliterating-peacock mollified-armadillo wondering-clam

release "billowing-kitten" deleted
release "loitering-waterbuffalo" deleted
release "flabby-deer" deleted
release "linting-echidna" deleted
```

Em seguida, remova o repositório Helm para a aplicação hello world do AKS:

```console
helm repo remove azure-samples
```

Remova a rota de entrada que eram direcionadas para o tráfego para as aplicações de exemplo:

```console
kubectl delete -f hello-world-ingress.yaml
```

Por fim, remova o endereço IP público estático, criado para o controlador de entrada. Fornecer seu *MC_* nome do grupo de recursos de cluster obtidos no primeiro passo deste artigo, tal como *MC_myResourceGroup_myAKSCluster_eastus*:

```azurecli
az network public-ip delete --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP
```

## <a name="next-steps"></a>Passos Seguintes

Este artigo inclui alguns componentes externos ao AKS. Para saber mais sobre estes componentes, consulte as seguintes páginas do projeto:

- [Helm CLI][helm-cli]
- [Controlador de entradas do NGINX][nginx-ingress]
- [Gestor de certificado][cert-manager]

Também pode:

- [Criar um controlador de entrada básico com conectividade de rede externa][aks-ingress-basic]
- [Ativar o suplemento de encaminhamento de aplicação de HTTP][aks-http-app-routing]
- [Criar um controlador de entrada que utiliza uma rede interna, privada e o endereço IP][aks-ingress-internal]
- [Criar um controlador de entrada com um IP público dinâmico e configurar a encriptar vamos para gerar automaticamente certificados TLS][aks-ingress-tls]

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
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-http-app-routing]: http-application-routing.md
