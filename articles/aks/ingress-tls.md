---
title: Criar uma entrada HTTPS com o cluster Azure Kubernetes Service (AKS)
description: Aprenda a instalar e configurar um controlador de ingresso NGINX que utiliza Let's Encrypt para a geração automática de certificados TLS num cluster do Serviço Azure Kubernetes (AKS).
services: container-service
ms.topic: article
ms.date: 01/29/2020
ms.openlocfilehash: 6f497ee3edd5ee831c091a5a50629df81673acea
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78191321"
---
# <a name="create-an-https-ingress-controller-on-azure-kubernetes-service-aks"></a>Criar um controlador de ingresso HTTPS no Serviço Azure Kubernetes (AKS)

Um controlador de ingresso é uma peça de software que fornece procuração inversa, encaminhamento de tráfego configurável e rescisão de TLS para os serviços kubernetes. Os recursos de ingresso kubernetes são usados para configurar as regras e rotas de ingresso para serviços kubernetes individuais. Utilizando um controlador de ingresso e regras de ingresso, um único endereço IP pode ser usado para direcionar o tráfego para vários serviços em um cluster Kubernetes.

Este artigo mostra-lhe como implantar o [controlador de ingresso NGINX][nginx-ingress] num cluster do Serviço Azure Kubernetes (AKS). O projeto [cert-manager][cert-manager] é usado para gerar e configurar automaticamente os certificados [Let's Encrypt.][lets-encrypt] Finalmente, duas aplicações são executadas no cluster AKS, cada uma das quais é acessível num único endereço IP.

Também pode:

- [Criar um controlador básico de ingresso com conectividade de rede externa][aks-ingress-basic]
- [Ativar o add-on de encaminhamento de aplicações HTTP][aks-http-app-routing]
- [Criar um controlador de ingresso que utilize uma rede interna, privada e endereço IP][aks-ingress-internal]
- [Crie um controlador de ingresso que utilize os seus próprios certificados TLS][aks-ingress-own-tls]
- [Crie um controlador de ingresso que utilize Let's Encrypt para gerar automaticamente certificados TLS com um endereço IP público estático][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Antes de começar

Este artigo assume que você tem um aglomerado AKS existente. Se precisar de um cluster AKS, consulte o quickstart AKS [utilizando o Azure CLI][aks-quickstart-cli] ou [utilizando o portal Azure][aks-quickstart-portal].

Este artigo também assume que você tem [um domínio personalizado][custom-domain] com uma Zona [DNS][dns-zone] no mesmo grupo de recursos que o seu cluster AKS.

Este artigo utiliza o Helm para instalar o controlador de ingresso NGINX, o cert-manager e uma aplicação web de amostra. Certifique-se de que está a usar o último lançamento do Helm. Para obter instruções de atualização, consulte o [Helm instalar docs][helm-install]. Para obter mais informações sobre configurar e utilizar o Helm, consulte [Instalar aplicações com o Helm no Serviço Azure Kubernetes (AKS)][use-helm].

Este artigo também requer que esteja a executar a versão Azure CLI 2.0.64 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Criar um controlador de ingresso

Para criar o controlador de entrada, utilize o comando `helm` para instalar *nginx-ingress*. Para um maior despedimento, duas réplicas dos controladores de entrada NGINX são implantadas com o parâmetro `--set controller.replicaCount`. Para se beneficiar totalmente da execução de réplicas do controlador de ingresso, certifique-se de que há mais de um nó no seu cluster AKS.

O controlador de ingresso também precisa de ser programado num nó Linux. Os nós do Windows Server (atualmente em pré-visualização no AKS) não devem executar o controlador de entrada. É especificado um seletor de nó utilizando o parâmetro de `--set nodeSelector` para dizer ao programador kubernetes para executar o controlador de entrada NGINX num nó baseado em Linux.

> [!TIP]
> O exemplo seguinte cria um espaço de nome Kubernetes para os recursos de ingresso *denominados ingress-basic*. Especifique um espaço de nome para o seu próprio ambiente, conforme necessário.

> [!TIP]
> Se quiser ativar a [preservação ip][client-source-ip] de origem do cliente para pedidos de contentores no seu cluster, adicione `--set controller.service.externalTrafficPolicy=Local` ao comando de instalação helm. O IP de origem do cliente é armazenado no cabeçalho de pedido sob *X-Forwarded-For*. Ao utilizar um controlador de ingresso com a preservação IP de origem do cliente ativada, a passagem do SSL não funcionará.

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

Durante a instalação, é criado um endereço IP público Azure para o controlador de ingresso. Este endereço IP público é estático para o tempo de vida do controlador de ingresso. Se eliminar o controlador de ingresso, a atribuição de endereçoip público perde-se. Se criar um controlador adicional de ingresso, é atribuído um novo endereço IP público. Se desejar manter a utilização do endereço IP público, pode, em vez disso, criar um controlador de [ingresso com um endereço IP público estático][aks-ingress-static-tls].

Para obter o endereço IP público, use o comando `kubectl get service`. Leva alguns minutos para que o endereço IP seja atribuído ao serviço.

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                             TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
nginx-ingress-controller                         LoadBalancer   10.0.182.160   MY_EXTERNAL_IP  80:30920/TCP,443:30426/TCP   20m
nginx-ingress-default-backend                    ClusterIP      10.0.255.77    <none>          80/TCP                       20m
```

Ainda não foram criadas regras de ingresso. Se navegar no endereço IP público, é apresentada a página padrão 404 do controlador de entrada NGINX.

## <a name="add-an-a-record-to-your-dns-zone"></a>Adicione um disco A à sua zona DNS

Adicione um registo *A* à sua zona DNS com o endereço IP externo do serviço NGINX utilizando um registo de dns de [rede az .][az-network-dns-record-set-a-add-record]

```console
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name * \
    --ipv4-address MY_EXTERNAL_IP
```

> [!NOTE]
> Opcionalmente, pode configurar um FQDN para o endereço IP do controlador de entrada em vez de um domínio personalizado. Note que esta amostra é para uma concha bash.
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

## <a name="install-cert-manager"></a>Instalar cert-manager

O controlador de entrada NGINX suporta a rescisão de TLS. Existem várias formas de recuperar e configurar certificados para HTTPS. Este artigo demonstra a [utilização do cert-manager][cert-manager], que fornece automaticamente a geração de [certificados lets encrypt][lets-encrypt] e a funcionalidade de gestão.

Para instalar o controlador cert-manager:

```console
# Install the CustomResourceDefinition resources separately
kubectl apply --validate=false -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.13/deploy/manifests/00-crds.yaml

# Label the ingress-basic namespace to disable resource validation
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

Para criar o emitente, use o comando `kubectl apply`.

```console
kubectl apply -f cluster-issuer.yaml --namespace ingress-basic
```

## <a name="run-demo-applications"></a>Executar aplicações de demonstração

Um controlador de ingresso e uma solução de gestão de certificados foram configurados. Agora vamos executar duas aplicações de demonstração no seu cluster AKS. Neste exemplo, Helm é usado para implementar dois casos de uma aplicação simples *do mundo Hello.*

Antes de poder instalar as tabelas Helm da amostra, adicione o repositório de amostras Azure ao seu ambiente Helm.

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Crie uma aplicação de demonstração chamada *aks-helloworld* usando o gráfico de helm ofe-amostras/aks-helloworld.

```console
helm install aks-helloworld azure-samples/aks-helloworld --namespace ingress-basic
```

Crie uma segunda instância da aplicação de demonstração chamada *aks-helloworld-two*. Especifique um novo título e um nome de serviço único para que as duas aplicações sejam visualmente distintas usando *--set*.

```console
helm install aks-helloworld-two azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="aks-helloworld-two"
```

## <a name="create-an-ingress-route"></a>Criar uma rota de ingresso

Ambas as aplicações estão agora a funcionar no seu cluster Kubernetes. No entanto, estão configurados com um serviço de tipo `ClusterIP` e não são acessíveis a partir da internet. Para torná-los disponíveis publicamente, crie um recurso de entrada Kubernetes. O recurso de ingresso configura as regras que encaminham o tráfego para uma das duas aplicações.

No exemplo seguinte, o tráfego para o endereço *olá-ingresso mundial. MY_CUSTOM_DOMAIN* é encaminhado para o serviço *aks-helloworld.* Tráfego para o endereço *olá-world-ingress. MY_CUSTOM_DOMAIN/hello-world-two* é encaminhado para o serviço *aks-helloworld-two.* Tráfego para *hello-world-ingress. MY_CUSTOM_DOMAIN/estática* é encaminhado para o serviço chamado *aks-helloworld* para ativos estáticos.

Crie um ficheiro chamado `hello-world-ingress.yaml` usando o exemplo YAML. Atualize os *anfitriões* e *o anfitrião* do nome DNS que criou num passo anterior.

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

Crie o recurso ingresso utilizando o comando `kubectl apply`.

```console
kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic
```

## <a name="verify-a-certificate-object-has-been-created"></a>Verifique se foi criado um objeto de certificado

Em seguida, deve ser criado um recurso de certificado. O recurso do certificado define o certificado X.509 desejado. Para mais informações, consulte [os certificados do cert-manager][cert-manager-certificates]. O cert-manager criou automaticamente um objeto de certificado para si usando ingress-shim, que é automaticamente implantado com cert-manager desde v0.2.2. Para mais informações, consulte a [documentação ingress-shim][ingress-shim].

Para verificar se o certificado foi criado com sucesso, utilize o comando `kubectl get certificate --namespace ingress-basic` e verifique se o *READY* é *Verdadeiro,* que pode demorar vários minutos.

```
$ kubectl get certificate --namespace ingress-basic

NAME         READY   SECRET       AGE
tls-secret   True    tls-secret   11m
```

## <a name="test-the-ingress-configuration"></a>Testar a configuração da entrada

Abra um navegador web para *hello-world-ingress. MY_CUSTOM_DOMAIN* do seu controlador de ingresso Kubernetes. Note que é redirecionado para usar HTTPS e o certificado é de confiança e a aplicação de demonstração é mostrada no navegador web. Adicione o caminho */hello-world-two* e note que a segunda aplicação de demonstração com o título personalizado é mostrada.

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

Em alternativa, uma abordagem mais granular é eliminar os recursos individuais criados. Primeiro, remova os recursos emitentes do cluster:

```console
kubectl delete -f cluster-issuer.yaml --namespace ingress-basic
```

Lista o Helm liberta com o comando `helm list`. Procure gráficos *chamados nginx-ingress* e *aks-helloworld,* como mostra a seguinte saída exemplo:

```
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
aks-helloworld          ingress-basic   1               2020-01-15 10:24:32.054871 -0600 CST    deployed        aks-helloworld-0.1.0               
aks-helloworld-two      ingress-basic   1               2020-01-15 10:24:37.671667 -0600 CST    deployed        aks-helloworld-0.1.0               
cert-manager            ingress-basic   1               2020-01-15 10:23:36.515514 -0600 CST    deployed        cert-manager-v0.13.0    v0.13.0    
nginx                   ingress-basic   1               2020-01-15 10:09:45.982693 -0600 CST    deployed        nginx-ingress-1.29.1    0.27.0  
```

Elimine os lançamentos com o comando `helm delete`. O exemplo seguinte elimina a implementação de ingresso NGINX, e as duas aplicações aks olá world.

```
$ helm uninstall aks-helloworld aks-helloworld-two cert-manager nginx --namespace ingress-basic

release "aks-helloworld" uninstalled
release "aks-helloworld-two" uninstalled
release "cert-manager" uninstalled
release "nginx" uninstalled
```

Em seguida, remova o repo Helm para a app aks olá world:

```console
helm repo remove azure-samples
```

Remova a rota de ingresso que direcionou o tráfego para as aplicações da amostra:

```console
kubectl delete -f hello-world-ingress.yaml --namespace ingress-basic
```

Finalmente, pode eliminar o espaço de nome si mesmo. Utilize o comando `kubectl delete` e especifique o seu nome de espaço de nome:

```console
kubectl delete namespace ingress-basic
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
- [Crie um controlador de ingresso que utilize Let's Encrypt para gerar automaticamente certificados TLS com um endereço IP público estático][aks-ingress-static-tls]

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
