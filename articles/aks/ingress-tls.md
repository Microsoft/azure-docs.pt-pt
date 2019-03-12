---
title: Criar uma entrada HTTPS com o cluster do Azure Kubernetes Service (AKS)
description: Saiba como instalar e configurar um controlador de entradas NGINX que utiliza a encriptar vamos para a geração automática de certificado TLS num cluster do Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 03/06/2019
ms.author: iainfou
ms.openlocfilehash: 879b3cabcab6f10d46904bd3a479568756d877b4
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57777809"
---
# <a name="create-an-https-ingress-controller-on-azure-kubernetes-service-aks"></a>Criar um controlador de entradas HTTPS no Azure Kubernetes Service (AKS)

Um controlador de entrada é uma parte do software que fornece o proxy inverso, encaminhamento de tráfego configurável e terminação de TLS para serviços do Kubernetes. Recursos de entrada do Kubernetes são utilizados para configurar as regras de entrada e as rotas para serviços individuais do Kubernetes. Utilizar um controlador de entrada e de regras de entrada, um único endereço IP pode servir-se para encaminhar o tráfego para vários serviços num cluster do Kubernetes.

Este artigo mostra-lhe como implementar o [controlador de entradas NGINX] [ nginx-ingress] num cluster do Azure Kubernetes Service (AKS). O [Gestor de cert] [ cert-manager] projeto é utilizado para gerar e configurar automaticamente [vamos encriptar] [ lets-encrypt] certificados. Por fim, duas aplicações são executadas no cluster do AKS, cada um deles está acessível através de um único endereço IP.

Também pode:

- [Criar um controlador de entrada básico com conectividade de rede externa][aks-ingress-basic]
- [Ativar o suplemento de encaminhamento de aplicação de HTTP][aks-http-app-routing]
- [Criar um controlador de entrada que utiliza uma rede interna, privada e o endereço IP][aks-ingress-internal]
- [Criar um controlador de entrada que utiliza os seus próprios certificados TLS][aks-ingress-own-tls]
- [Criar um controlador de entrada que utiliza a encriptar vamos para gerar automaticamente certificados TLS com um endereço IP público estático][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que tem um cluster do AKS existente. Se precisar de um cluster do AKS, consulte o guia de introdução do AKS [com a CLI do Azure] [ aks-quickstart-cli] ou [no portal do Azure][aks-quickstart-portal].

Este artigo utiliza o Helm para instalar o controlador de entrada do NGINX, o Gestor de certificado e um exemplo de aplicação web. Tem de ter o Helm inicializado no seu cluster do AKS e utilizar uma conta de serviço para Tiller. Certifique-se de que está a utilizar a última versão do Helm. Para obter instruções de atualização, consulte a [Helm instalar docs][helm-install]. Para obter mais informações sobre como configurar e utilizar o Helm, consulte [instalar aplicações com Helm no Azure Kubernetes Service (AKS)][use-helm].

Este artigo também requer a execução da versão 2.0.59 da CLI do Azure ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Criar um controlador de entrada

Para criar o controlador de entrada, utilize `Helm` para instalar *entrada de nginx*. Para maior redundância, as duas réplicas dos controladores de entrada de NGINX são implementadas com o `--set controller.replicaCount` parâmetro. Para beneficiar totalmente a execução de réplicas do controlador de entrada, certificar-se de que há mais de um nó no cluster do AKS.

> [!TIP]
> O exemplo seguinte instala o controlador de entrada no `kube-system` espaço de nomes. Pode especificar um espaço de nomes diferente para o seu próprio ambiente, se assim o desejar. Se o cluster do AKS não RBAC ativado, adicione `--set rbac.create=false` para os comandos.

```console
helm install stable/nginx-ingress --namespace kube-system --set controller.replicaCount=2
```

Durante a instalação, um endereço IP público do Azure é criado para o controlador de entrada. Este endereço IP público é estático para o tempo de vida do controlador de entrada. Se eliminar o controlador de entrada, a atribuição de endereços IP pública é perdida. Se, em seguida, criar um controlador de entrada adicionais, é atribuído um novo endereço IP público. Se pretender manter a utilização do endereço IP público, em vez disso, pode [criar um controlador de entrada com um endereço IP público estático][aks-ingress-static-tls].

Para obter o endereço IP público, utilize o `kubectl get service` comando. Demora alguns minutos para que o endereço IP a ser atribuída ao serviço.

```
$ kubectl get service -l app=nginx-ingress --namespace kube-system

NAME                                             TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
billowing-kitten-nginx-ingress-controller        LoadBalancer   10.0.182.160   51.145.155.210  80:30920/TCP,443:30426/TCP   20m
billowing-kitten-nginx-ingress-default-backend   ClusterIP      10.0.255.77    <none>          80/TCP                       20m
```

Não existem regras de entrada foram criadas ainda. Se navegar até o endereço IP público, é apresentada a página 404 do padrão do controlador de entrada NGINX.

## <a name="configure-a-dns-name"></a>Configurar um nome DNS

Para os certificados HTTPS funcionar corretamente, configure um FQDN para o endereço IP de controlador de entrada. Atualize o seguinte script com o endereço IP do seu controlador de entrada e um nome exclusivo que pretende utilizar para o FQDN:

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

O controlador de entrada está agora acessível através do FQDN.

## <a name="install-cert-manager"></a>Instalar o Gestor de certificado

O controlador de entrada do NGINX oferece suporte a terminação de TLS. Existem várias formas de obter e configurar certificados para HTTPS. Este artigo demonstra como utilizar [Gestor de cert][cert-manager], que fornece automática [permite encriptar] [ lets-encrypt] geração de certificados e funcionalidade de gestão.

> [!NOTE]
> Este artigo utiliza o `staging` ambiente para encriptar vamos. Nas implementações de produção, utilize `letsencrypt-prod` e `https://acme-v02.api.letsencrypt.org/directory` nas definições de recursos e ao instalar o gráfico do Helm.

Para instalar o controlador de Gestor de certificados num cluster habilitados no RBAC, utilize o seguinte `helm install` comando:

```console
kubectl label namespace kube-system certmanager.k8s.io/disable-validation=true

kubectl apply \
    -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.6/deploy/manifests/00-crds.yaml
    
helm install stable/cert-manager \
    --namespace kube-system \
    --set ingressShim.defaultIssuerName=letsencrypt-staging \
    --set ingressShim.defaultIssuerKind=ClusterIssuer \
    --version v0.6.6
```

> [!TIP]
> Se receber um erro como, por exemplo `Error: failed to download "stable/cert-manager"`, certifique-se de que tenha executou com êxito `helm repo update` para obter uma lista dos gráficos de Helm mais recente disponíveis.

Se o cluster não for RBAC ativada, em vez disso, utilize o seguinte comando:

```console
kubectl label namespace kube-system certmanager.k8s.io/disable-validation=true

kubectl apply \
    -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.6/deploy/manifests/00-crds.yaml
    
helm install stable/cert-manager \
    --namespace kube-system \
    --set ingressShim.defaultIssuerName=letsencrypt-staging \
    --set ingressShim.defaultIssuerKind=ClusterIssuer \
    --set rbac.create=false \
    --set serviceAccount.create=false \
    --version v0.6.6
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

Provavelmente-CERT-manager criado automaticamente um objeto de certificado para si com entrada-shim, que é implementada com o Gestor de cert desde v0.2.2. Para obter mais informações, consulte a [documentação de entrada shim][ingress-shim].

Para verificar se o certificado foi criado com êxito, utilize o `kubectl describe certificate tls-secret` comando. Se foi emitido um certificado, a saída na *eventos* resultado é semelhante ao seguinte exemplo:

```
Type    Reason          Age   From          Message
----    ------          ----  ----          -------
  Normal  CreateOrder     11m   cert-manager  Created new ACME order, attempting validation...
  Normal  DomainVerified  10m   cert-manager  Domain "demo-aks-ingress.eastus.cloudapp.azure.com" verified with "http-01" validation
  Normal  IssueCert       10m   cert-manager  Issuing certificate...
  Normal  CertObtained    10m   cert-manager  Obtained certificate from ACME server
  Normal  CertIssued      10m   cert-manager  Certificate issued successfully
```

Se precisar de criar um recurso de certificado, pode fazê-lo com o manifesto de exemplo seguinte. Atualização do *dnsNames* e *domínios* para o nome DNS que criou no passo anterior. Se usar um controlador de entrada apenas internos, especifique o nome DNS interno para o seu serviço.

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
billowing-kitten        1           Wed Mar  6 19:37:43 2019    DEPLOYED    nginx-ingress-1.3.1     0.22.0      kube-system
loitering-waterbuffalo  1           Wed Mar  6 20:25:01 2019    DEPLOYED    cert-manager-v0.6.6     v0.6.2      kube-system
flabby-deer             1           Wed Mar  6 20:27:54 2019    DEPLOYED    aks-helloworld-0.1.0                default
linting-echidna         1           Wed Mar  6 20:27:59 2019    DEPLOYED    aks-helloworld-0.1.0                default
```

Eliminar as versões com a `helm delete` comando. O exemplo seguinte elimina a implementação de entrada do NGINX, o Gestor de certificados e as dois exemplos AKS Olá mundo de aplicações.

```
$ helm delete billowing-kitten loitering-waterbuffalo flabby-deer linting-echidna

release "billowing-kitten" deleted
release "loitering-waterbuffalo" deleted
release "flabby-deer" deleted
release "linting-echidna" deleted
```

Em seguida, remova o repositório Helm para a aplicação hello world do AKS:

```console
helm repo remove azure-samples
```

Por fim, remova a rota de entrada que eram direcionadas para o tráfego para as aplicações de exemplo:

```console
kubectl delete -f hello-world-ingress.yaml
```

## <a name="next-steps"></a>Passos Seguintes

Este artigo inclui alguns componentes externos ao AKS. Para saber mais sobre estes componentes, consulte as seguintes páginas do projeto:

- [Helm CLI][helm-cli]
- [Controlador de entradas do NGINX][nginx-ingress]
- [cert-manager][cert-manager]

Também pode:

- [Criar um controlador de entrada básico com conectividade de rede externa][aks-ingress-basic]
- [Ativar o suplemento de encaminhamento de aplicação de HTTP][aks-http-app-routing]
- [Criar um controlador de entrada que utiliza uma rede interna, privada e o endereço IP][aks-ingress-internal]
- [Criar um controlador de entrada que utiliza os seus próprios certificados TLS][aks-ingress-own-tls]
- [Criar um controlador de entrada que utiliza a encriptar vamos para gerar automaticamente certificados TLS com um endereço IP público estático][aks-ingress-static-tls]

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
[install-azure-cli]: /cli/azure/install-azure-cli
