---
title: Crie um controlador de ingresso HTTP com um endereço IP estático no Serviço Azure Kubernetes (AKS)
description: Aprenda a instalar e configurar um controlador de ingresso NGINX com um endereço IP público estático num cluster do Serviço Azure Kubernetes (AKS).
services: container-service
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: 10422595b85c71020225df694778e6b8ae7e0185
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78191355"
---
# <a name="create-an-ingress-controller-with-a-static-public-ip-address-in-azure-kubernetes-service-aks"></a>Crie um controlador de ingresso com um endereço IP público estático no Serviço Azure Kubernetes (AKS)

Um controlador de ingresso é uma peça de software que fornece procuração inversa, encaminhamento de tráfego configurável e rescisão de TLS para os serviços kubernetes. Os recursos de ingresso kubernetes são usados para configurar as regras e rotas de ingresso para serviços kubernetes individuais. Utilizando um controlador de ingresso e regras de ingresso, um único endereço IP pode ser usado para direcionar o tráfego para vários serviços em um cluster Kubernetes.

Este artigo mostra-lhe como implantar o [controlador de ingresso NGINX][nginx-ingress] num cluster do Serviço Azure Kubernetes (AKS). O controlador de ingresso é configurado com um endereço IP público estático. O projeto [cert-manager][cert-manager] é usado para gerar e configurar automaticamente os certificados [Let's Encrypt.][lets-encrypt] Finalmente, duas aplicações são executadas no cluster AKS, cada uma das quais é acessível num único endereço IP.

Também pode:

- [Criar um controlador básico de ingresso com conectividade de rede externa][aks-ingress-basic]
- [Ativar o add-on de encaminhamento de aplicações HTTP][aks-http-app-routing]
- [Crie um controlador de ingresso que utilize os seus próprios certificados TLS][aks-ingress-own-tls]
- [Crie um controlador de ingresso que utilize Let's Encrypt para gerar automaticamente certificados TLS com um endereço IP público dinâmico][aks-ingress-tls]

## <a name="before-you-begin"></a>Antes de começar

Este artigo assume que você tem um aglomerado AKS existente. Se precisar de um cluster AKS, consulte o quickstart AKS [utilizando o Azure CLI][aks-quickstart-cli] ou [utilizando o portal Azure][aks-quickstart-portal].

Este artigo utiliza o Helm para instalar o controlador de ingresso NGINX, o cert-manager e uma aplicação web de amostra. Certifique-se de que está a usar o último lançamento do Helm. Para obter instruções de atualização, consulte o [Helm instalar docs][helm-install]. Para obter mais informações sobre configurar e utilizar o Helm, consulte [Instalar aplicações com o Helm no Serviço Azure Kubernetes (AKS)][use-helm].

Este artigo também requer que esteja a executar a versão Azure CLI 2.0.64 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Criar um controlador de ingresso

Por padrão, um controlador de ingresso NGINX é criado com uma nova atribuição de endereçoip público. Este endereço IP público é apenas estático para o tempo de vida do controlador de ingresso, e perde-se se o controlador for eliminado e recriado. Um requisito comum de configuração é fornecer ao controlador de ingresso NGINX um endereço IP público estático existente. O endereço IP público estático permanece se o controlador de entrada for eliminado. Esta abordagem permite-lhe utilizar os registos DNS existentes e configurações de rede de forma consistente ao longo do ciclo de vida das suas aplicações.

Se precisar de criar um endereço IP público estático, primeiro obtenha o nome do grupo de recursos do cluster AKS com o comando [az aks show:][az-aks-show]

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

Em seguida, crie um endereço IP público com o método de atribuição *estática* utilizando o comando de criação de [rede az public-ip.][az-network-public-ip-create] O exemplo seguinte cria um endereço IP público chamado *myAKSPublicIP* no grupo de recursos de cluster AKS obtido na fase anterior:

```azurecli-interactive
az network public-ip create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --sku Standard --allocation-method static --query publicIp.ipAddress -o tsv
```

Agora implemente o gráfico *de nginx-ingresscom* Helm. Adicione o parâmetro `--set controller.service.loadBalancerIP` e especifique o seu próprio endereço IP público criado no passo anterior. Para um maior despedimento, duas réplicas dos controladores de entrada NGINX são implantadas com o parâmetro `--set controller.replicaCount`. Para se beneficiar totalmente da execução de réplicas do controlador de ingresso, certifique-se de que há mais de um nó no seu cluster AKS.

O controlador de ingresso também precisa de ser programado num nó Linux. Os nós do Windows Server (atualmente em pré-visualização no AKS) não devem executar o controlador de entrada. É especificado um seletor de nó utilizando o parâmetro de `--set nodeSelector` para dizer ao programador kubernetes para executar o controlador de entrada NGINX num nó baseado em Linux.

> [!TIP]
> O exemplo seguinte cria um espaço de nome Kubernetes para os recursos de ingresso *denominados ingress-basic*. Especifique um espaço de nome para o seu próprio ambiente, conforme necessário. Se o seu cluster AKS não estiver ativado, adicione `--set rbac.create=false` aos comandos Helm.

> [!TIP]
> Se quiser ativar a [preservação ip][client-source-ip] de origem do cliente para pedidos de contentores no seu cluster, adicione `--set controller.service.externalTrafficPolicy=Local` ao comando de instalação helm. O IP de origem do cliente é armazenado no cabeçalho de pedido sob *X-Forwarded-For*. Ao utilizar um controlador de ingresso com a preservação IP de origem do cliente ativada, a passagem do SSL não funcionará.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Use Helm to deploy an NGINX ingress controller
helm install nginx-ingress stable/nginx-ingress \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.service.loadBalancerIP="40.121.63.72"
```

Quando o serviço de equilíbrio de carga Kubernetes é criado para o controlador de entrada NGINX, o seu endereço IP estático é atribuído, como mostra a seguinte saída exemplo:

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                        TYPE           CLUSTER-IP    EXTERNAL-IP    PORT(S)                      AGE
nginx-ingress-controller                    LoadBalancer   10.0.232.56   40.121.63.72   80:31978/TCP,443:32037/TCP   3m
nginx-ingress-default-backend               ClusterIP      10.0.95.248   <none>         80/TCP                       3m
```

Ainda não foram criadas regras de ingresso, pelo que a página padrão 404 do controlador de ingresso NGINX é exibida se navegar para o endereço IP público. As regras de ingresso estão configuradas nos seguintes passos.

## <a name="configure-a-dns-name"></a>Configurar um nome DNS

Para que os certificados HTTPS funcionem corretamente, configure um FQDN para o endereço IP do controlador de entrada. Atualize o seguinte script com o endereço IP do seu controlador de ingresso e um nome único que gostaria de utilizar para o FQDN:

```azurecli-interactive
#!/bin/bash

# Public IP address of your ingress controller
IP="40.121.63.72"

# Name to associate with public IP address
DNSNAME="demo-aks-ingress"

# Get the resource-id of the public ip
PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)

# Update public ip address with DNS name
az network public-ip update --ids $PUBLICIPID --dns-name $DNSNAME
```

O controlador de entrada está agora acessível através do FQDN.

## <a name="install-cert-manager"></a>Instalar cert-manager

O controlador de entrada NGINX suporta a rescisão de TLS. Existem várias formas de recuperar e configurar certificados para HTTPS. Este artigo demonstra a [utilização do cert-manager][cert-manager], que fornece automaticamente a geração de [certificados lets encrypt][lets-encrypt] e a funcionalidade de gestão.

> [!NOTE]
> Este artigo usa o ambiente `staging` para Let's Encrypt. Nas implementações de produção, utilize `letsencrypt-prod` e `https://acme-v02.api.letsencrypt.org/directory` nas definições de recursos e na instalação do gráfico Helm.

Para instalar o controlador cert-manager num cluster ativado pelo RBAC, utilize o seguinte comando `helm install`:

```console
# Install the CustomResourceDefinition resources separately
kubectl apply --validate=false -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.13/deploy/manifests/00-crds.yaml

# Label the cert-manager namespace to disable resource validation
kubectl label namespace ingress-basic cert-manager.io/disable-validation=true

# Add the Jetstack Helm repository
helm repo add jetstack https://charts.jetstack.io

# Update your local Helm chart repository cache
helm repo update

# Install the cert-manager Helm chart
helm install \
  cert-manager \
  --namespace ingress-basic \
  --version v0.13.0 \
  jetstack/cert-manager
```

Para obter mais informações sobre a configuração do cert-manager, consulte o [projeto cert-manager][cert-manager].

## <a name="create-a-ca-cluster-issuer"></a>Criar um emitente de cluster CA

Antes de os certificados poderem ser emitidos, o cert-manager requer um recurso [Emitente][cert-manager-issuer] ou [ClusterIssuer.][cert-manager-cluster-issuer] Estes recursos Kubernetes são idênticos na funcionalidade, no entanto `Issuer` trabalha num único espaço de nome, e `ClusterIssuer` funciona em todos os espaços de nome. Para mais informações, consulte a documentação do [emitente do gestor da CERT.][cert-manager-issuer]

Crie um emitente de cluster, como `cluster-issuer.yaml`, utilizando o seguinte manifesto de exemplo. Atualize o endereço de e-mail com um endereço válido da sua organização:

```yaml
apiVersion: cert-manager.io/v1alpha2
kind: ClusterIssuer
metadata:
  name: letsencrypt-staging
spec:
  acme:
    server: https://acme-staging-v02.api.letsencrypt.org/directory
    email: user@contoso.com
    privateKeySecretRef:
      name: letsencrypt-staging
    solvers:
    - http01:
        ingress:
          class: nginx
```

Para criar o emitente, use o comando `kubectl apply -f cluster-issuer.yaml`.

```
$ kubectl apply -f cluster-issuer.yaml --namespace ingress-basic

clusterissuer.cert-manager.io/letsencrypt-staging created
```

## <a name="run-demo-applications"></a>Executar aplicações de demonstração

Um controlador de ingresso e uma solução de gestão de certificados foram configurados. Agora vamos executar duas aplicações de demonstração no seu cluster AKS. Neste exemplo, helm é usado para implementar dois casos de uma simples aplicação "Olá mundo".

Antes de poder instalar as tabelas Helm da amostra, adicione o repositório de amostras Azure ao seu ambiente Helm da seguinte forma:

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Crie a primeira aplicação de demonstração a partir de um gráfico Helm com o seguinte comando:

```console
helm install aks-helloworld azure-samples/aks-helloworld --namespace ingress-basic
```

Agora instale uma segunda instância da aplicação de demonstração. Em segundo lugar, especifice um novo título para que as duas aplicações sejam visualmente distintas. Também especifica um nome de serviço único:

```console
helm install aks-helloworld-2 azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>Criar uma rota de ingresso

Ambas as aplicações estão agora a funcionar no seu cluster Kubernetes, no entanto estão configuradas com um serviço de tipo `ClusterIP`. Como tal, as aplicações não são acessíveis a partir da internet. Para torná-los disponíveis publicamente, crie um recurso de entrada Kubernetes. O recurso de ingresso configura as regras que encaminham o tráfego para uma das duas aplicações.

No exemplo seguinte, o tráfego para o endereço `https://demo-aks-ingress.eastus.cloudapp.azure.com/` é encaminhado para o serviço denominado `aks-helloworld`. O tráfego para o endereço `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` é encaminhado para o serviço de `ingress-demo`. Atualize os *anfitriões* e *o anfitrião* do nome DNS que criou num passo anterior.

Crie um ficheiro chamado `hello-world-ingress.yaml` e copie no seguinte exemplo YAML.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    cert-manager.io/cluster-issuer: letsencrypt-staging
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

Crie o recurso ingresso utilizando o comando `kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic`.

```
$ kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic

ingress.extensions/hello-world-ingress created
```

## <a name="create-a-certificate-object"></a>Criar um objeto de certificado

Em seguida, deve ser criado um recurso de certificado. O recurso do certificado define o certificado X.509 desejado. Para mais informações, consulte [os certificados do cert-manager][cert-manager-certificates].

O cert-manager provavelmente criou automaticamente um objeto de certificado para você usando ingress-shim, que é automaticamente implantado com cert-manager desde v0.2.2. Para mais informações, consulte a [documentação ingress-shim][ingress-shim].

Para verificar se o certificado foi criado com sucesso, utilize o comando `kubectl describe certificate tls-secret --namespace ingress-basic`.

Se o certificado tiver sido emitido, verá a saída semelhante à seguinte:
```
Type    Reason          Age   From          Message
----    ------          ----  ----          -------
  Normal  CreateOrder     11m   cert-manager  Created new ACME order, attempting validation...
  Normal  DomainVerified  10m   cert-manager  Domain "demo-aks-ingress.eastus.cloudapp.azure.com" verified with "http-01" validation
  Normal  IssueCert       10m   cert-manager  Issuing certificate...
  Normal  CertObtained    10m   cert-manager  Obtained certificate from ACME server
  Normal  CertIssued      10m   cert-manager  Certificate issued successfully
```

Se precisar de criar um recurso adicional de certificado, pode fazê-lo com o seguinte manifesto de exemplo. Atualize os *dnsNames* e *domínios* para o nome DNS que criou num passo anterior. Se utilizar um controlador de entrada interna, especifique o nome DNS interno para o seu serviço.

```yaml
apiVersion: cert-manager.io/v1alpha2
kind: Certificate
metadata:
  name: tls-secret
  namespace: ingress-basic
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

Para criar o recurso do certificado, utilize o comando `kubectl apply -f certificates.yaml`.

```
$ kubectl apply -f certificates.yaml

certificate.cert-manager.io/tls-secret created
```

## <a name="test-the-ingress-configuration"></a>Testar a configuração da entrada

Abra um navegador web para o FQDN do seu controlador de entrada Kubernetes, como *https://demo-aks-ingress.eastus.cloudapp.azure.com* .

Como estes exemplos usam `letsencrypt-staging`, o certificado SSL emitido não é confiado pelo navegador. Aceite o aviso para continuar na sua aplicação. As informações do certificado mostram que este certificado *Falso LE Intermediate X1* é emitido pela Let's Encrypt. Este certificado falso indica `cert-manager` processou corretamente o pedido e recebeu um certificado do prestador:

![Vamos encriptar o certificado de encenação](media/ingress/staging-certificate.png)

Quando muda, deixe-se encriptar para usar `prod` em vez de `staging`, é utilizado um certificado de confiança emitido pela Let's Encrypt, como mostra o seguinte exemplo:

![Vamos encriptar certificado](media/ingress/certificate.png)

A aplicação de demonstração é mostrada no navegador web:

![Exemplo de aplicação um](media/ingress/app-one.png)

Adicione agora o caminho */hello-world-two* para o FQDN, como *https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two* . A segunda aplicação de demonstração com o título personalizado é mostrada:

![Exemplo de aplicação dois](media/ingress/app-two.png)

## <a name="clean-up-resources"></a>Limpar recursos

Este artigo usou o Helm para instalar os componentes de entrada, certificados e aplicações de amostragem. Quando se implanta um gráfico Helm, são criados vários recursos kubernetes. Estes recursos incluem cápsulas, implantações e serviços. Para limpar estes recursos, pode eliminar todo o espaço de nome da amostra ou os recursos individuais.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Eliminar o espaço de nome da amostra e todos os recursos

Para eliminar todo o espaço de nome da amostra, utilize o comando `kubectl delete` e especifique o seu nome de espaço de nome. Todos os recursos no espaço de nome são eliminados.

```console
kubectl delete namespace ingress-basic
```

Em seguida, remova o repo Helm para a app aks olá mundo:

```console
helm repo remove azure-samples
```

### <a name="delete-resources-individually"></a>Eliminar recursos individualmente

Em alternativa, uma abordagem mais granular é eliminar os recursos individuais criados. Primeiro, remova os recursos do certificado:

```console
kubectl delete -f certificates.yaml
kubectl delete -f cluster-issuer.yaml
```

Agora lista o Helm lança com o comando `helm list`. Procure gráficos *chamados nginx-ingress,* *cert-manager*, e *aks-helloworld,* como mostra a seguinte saída exemplo:

```
$ helm list --all-namespaces

NAME                    NAMESPACE       REVISION        UPDATED                        STATUS          CHART                   APP VERSION
aks-helloworld          ingress-basic   1               2020-01-11 15:02:21.51172346   deployed        aks-helloworld-0.1.0
aks-helloworld-2        ingress-basic   1               2020-01-11 15:03:10.533465598  deployed        aks-helloworld-0.1.0
nginx-ingress           ingress-basic   1               2020-01-11 14:51:03.454165006  deployed        nginx-ingress-1.28.2    0.26.2
cert-manager            ingress-basic    1               2020-01-06 21:19:03.866212286  deployed        cert-manager-v0.13.0    v0.13.0
```

Elimine os lançamentos com o comando `helm uninstall`. O exemplo seguinte elimina a implementação de ingressos NGINX, gestor de certificados e as duas aplicações aks olá world.

```
$ helm uninstall aks-helloworld aks-helloworld-2 nginx-ingress cert-manager -n ingress-basic

release "aks-helloworld" deleted
release "aks-helloworld-2" deleted
release "nginx-ingress" deleted
release "cert-manager" deleted
```

Em seguida, remova o repo Helm para a app aks olá world:

```console
helm repo remove azure-samples
```

Apague o espaço de nome em si. Utilize o comando `kubectl delete` e especifique o seu nome de espaço de nome:

```console
kubectl delete namespace ingress-basic
```

Por fim, remova o endereço IP público estático criado para o controlador de ingresso. Forneça o seu *MC_* nome de grupo de recursos de cluster obtido no primeiro passo deste artigo, tais como *MC_myResourceGroup_myAKSCluster_eastus:*

```azurecli-interactive
az network public-ip delete --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP
```

## <a name="next-steps"></a>Passos seguintes

Este artigo incluía alguns componentes externos para a AKS. Para saber mais sobre estes componentes, consulte as seguintes páginas do projeto:

- [Helm CLI][helm-cli]
- [Controlador de ingresso NGINX][nginx-ingress]
- [cert-manager][cert-manager]

Também pode:

- [Criar um controlador básico de ingresso com conectividade de rede externa][aks-ingress-basic]
- [Ativar o add-on de encaminhamento de aplicações HTTP][aks-http-app-routing]
- [Criar um controlador de ingresso que utilize uma rede interna, privada e endereço IP][aks-ingress-internal]
- [Crie um controlador de ingresso que utilize os seus próprios certificados TLS][aks-ingress-own-tls]
- [Crie um controlador de ingresso com um IP público dinâmico e configure Vamos encriptar para gerar automaticamente certificados TLS][aks-ingress-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm
[ingress-shim]: https://docs.cert-manager.io/en/latest/tasks/issuing-certificates/ingress-shim.html

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[client-source-ip]: concepts-network.md#ingress-controllers
[install-azure-cli]: /cli/azure/install-azure-cli
