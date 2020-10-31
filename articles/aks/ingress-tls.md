---
title: Criar entrada com TLS automático
titleSuffix: Azure Kubernetes Service
description: Aprenda a instalar e configurar um controlador de entrada NGINX que utiliza o Let's Encrypt para a geração automática de certificados TLS num cluster Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: 5032880ddc5d23f824adec28aee85c652bad29d2
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93129665"
---
# <a name="create-an-https-ingress-controller-on-azure-kubernetes-service-aks"></a>Criar um controlador de entrada HTTPS no Serviço Azure Kubernetes (AKS)

Um controlador de entrada é uma peça de software que fornece procuração inversa, encaminhamento de tráfego configurável e rescisão de TLS para serviços Kubernetes. Os recursos de entrada de Kubernetes são usados para configurar as regras e rotas de entrada para serviços individuais de Kubernetes. Utilizando um controlador de entrada e regras de entrada, um único endereço IP pode ser usado para encaminhar o tráfego para vários serviços num cluster kubernetes.

Este artigo mostra-lhe como implantar o [controlador de entrada NGINX][nginx-ingress] num cluster Azure Kubernetes Service (AKS). O projeto [cert-manager][cert-manager] é usado para gerar e configurar automaticamente os certificados [Let's Encrypt.][lets-encrypt] Finalmente, duas aplicações são executadas no cluster AKS, cada uma das quais é acessível sobre um único endereço IP.

Também pode:

- [Criar um controlador básico de entrada com conectividade de rede externa][aks-ingress-basic]
- [Ativar o addon de encaminhamento de aplicações HTTP][aks-http-app-routing]
- [Crie um controlador ingress que utilize uma rede interna, privada e endereço IP][aks-ingress-internal]
- [Crie um controlador de entrada que use os seus próprios certificados TLS][aks-ingress-own-tls]
- [Crie um controlador ingress que usa o Let's Encrypt para gerar automaticamente certificados TLS com um endereço IP público estático][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que você tem um cluster AKS existente. Se precisar de um cluster AKS, consulte o quickstart AKS [utilizando o Azure CLI][aks-quickstart-cli] ou [utilizando o portal Azure][aks-quickstart-portal].

Este artigo também assume que você tem [um domínio personalizado][custom-domain] com uma Zona [DNS][dns-zone] no mesmo grupo de recursos que o seu cluster AKS.

Este artigo utiliza [o Helm 3][helm] para instalar o controlador de entrada NGINX e o gestor de certificados. Certifique-se de que está a usar o mais recente lançamento do Helm e tenha acesso aos *repositórios de ingresss-nginx* e *jetstack* Helm. Para obter instruções de atualização, consulte os [docs de instalação helm][helm-install]. Para obter mais informações sobre a configuração e utilização do Helm, consulte [instalar aplicações com Helm in Azure Kubernetes Service (AKS)][use-helm].

Este artigo também requer que esteja a executar a versão Azure CLI 2.0.64 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Criar um controlador de entrada

Para criar o controlador de entrada, utilize o `helm` comando para instalar a entrada de *nginx* . Para uma maior redundância, são implementadas duas réplicas dos controladores de entrada do NGINX com o parâmetro `--set controller.replicaCount`. Para beneficiar totalmente da execução de réplicas do controlador de entrada, certifique-se de que há mais de um nó no seu cluster AKS.

O controlador de entrada também tem de estar agendado num nó do Linux. Os nós do Windows Server não devem executar o controlador de entrada. É especificado um seletor de nós com o parâmetro `--set nodeSelector` para indicar ao agendador do Kubernetes que execute o controlador de entrada do NGINX num nó baseado no Linux.

> [!TIP]
> O exemplo a seguir cria um espaço de nome Kubernetes para os recursos *ingressos denominados ingress-basic* . Especifique um espaço de nome para o seu próprio ambiente, conforme necessário.

> [!TIP]
> Se pretender permitir a [preservação ip da fonte do cliente][client-source-ip] para pedidos a contentores no seu cluster, adicione ao comando de `--set controller.service.externalTrafficPolicy=Local` instalação Helm. A FONTE DO CLIENTE IP é armazenada no cabeçalho de pedido sob *X-Forwarded-For* . Ao utilizar um controlador de entrada com a preservação IP de fonte do cliente ativada, o passe do TLS não funcionará.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the ingress-nginx repository
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx

# Use Helm to deploy an NGINX ingress controller
helm install nginx-ingress ingress-nginx/ingress-nginx \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Durante a instalação, é criado um endereço IP público Azure para o controlador de entrada. Este endereço IP público é estático para o tempo de vida do controlador de entrada. Se eliminar o controlador de entrada, perde-se a atribuição do endereço IP público. Se então criar um controlador adicional de entrada, será atribuído um novo endereço IP público. Se desejar reter a utilização do endereço IP público, pode, em vez disso, [criar um controlador de entrada com um endereço IP público estático][aks-ingress-static-tls].

Para obter o endereço IP público, use o `kubectl get service` comando. Demora alguns minutos para que o endereço IP seja atribuído ao serviço.

```
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

Ainda não foram criadas regras ingressas. Se navegar no endereço IP público, a página padrão 404 do controlador NGINX é apresentada.

## <a name="add-an-a-record-to-your-dns-zone"></a>Adicione um registo A à sua zona DE DNS

Adicione um registo *A* à sua zona DE DNS com o endereço IP externo do serviço NGINX utilizando o [registo de dns da rede Az][az-network-dns-record-set-a-add-record].

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

## <a name="install-cert-manager"></a>Instalar o cert-manager

O controlador de entrada do NGINX suporta a terminação de TLS. Existem várias formas de obter e configurar certificados para HTTPS. Este artigo demonstra a utilização [de cert-manager,][cert-manager]que fornece automaticamente [Lets Encrypt de][lets-encrypt] geração e funcionalidade de gestão.

Para instalar o controlador cert-manager:

```console
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
  --version v0.16.1 \
  --set installCRDs=true \
  --set nodeSelector."beta\.kubernetes\.io/os"=linux \
  jetstack/cert-manager
```

Para obter mais informações sobre a configuração do gestor de cert, consulte o [projeto cert-manager][cert-manager].

## <a name="create-a-ca-cluster-issuer"></a>Criar um emitente de cluster ca

Antes de os certificados poderem ser emitidos, o cert-manager requer um [recurso Emitente][cert-manager-issuer] ou [ClusterIssuer.][cert-manager-cluster-issuer] Estes recursos kubernetes são idênticos na funcionalidade, no entanto `Issuer` funciona num espaço de nome único, e funciona em todos os espaços de `ClusterIssuer` nome. Para mais informações, consulte a documentação do [emitente cert-manager.][cert-manager-issuer]

Crie um emitente de cluster, `cluster-issuer.yaml` como, usando o manifesto de exemplo a seguir. Atualizar o endereço de e-mail com um endereço válido da sua organização:

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
          podTemplate:
            spec:
              nodeSelector:
                "kubernetes.io/os": linux
```

Para criar o emitente, use o `kubectl apply` comando.

```console
kubectl apply -f cluster-issuer.yaml
```

## <a name="run-demo-applications"></a>Executar aplicações de demonstração

Foi configurado um controlador de entrada e uma solução de gestão de certificados. Agora vamos executar duas aplicações de demonstração no seu cluster AKS. Neste exemplo, Helm é usado para implementar dois casos de uma simples aplicação *hello world.*

Para ver o controlador de entrada em ação, execute duas aplicações de demonstração no seu cluster AKS. Neste exemplo, você usa `kubectl apply` para implementar dois casos de uma simples aplicação *Hello world.*

Crie um ficheiro *aks-helloworld-one.yaml* e copie no seguinte exemplo YAML:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aks-helloworld-one
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-helloworld-one
  template:
    metadata:
      labels:
        app: aks-helloworld-one
    spec:
      containers:
      - name: aks-helloworld-one
        image: mcr.microsoft.com/azuredocs/aks-helloworld:v1
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "Welcome to Azure Kubernetes Service (AKS)"
---
apiVersion: v1
kind: Service
metadata:
  name: aks-helloworld-one
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld-one
```

Crie um ficheiro *aks-helloworld-two.yaml* e copie no seguinte exemplo YAML:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aks-helloworld-two
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-helloworld-two
  template:
    metadata:
      labels:
        app: aks-helloworld-two
    spec:
      containers:
      - name: aks-helloworld-two
        image: mcr.microsoft.com/azuredocs/aks-helloworld:v1
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "AKS Ingress Demo"
---
apiVersion: v1
kind: Service
metadata:
  name: aks-helloworld-two
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld-two
```

Executar as duas aplicações de demonstração `kubectl apply` utilizando:

```console
kubectl apply -f aks-helloworld-one.yaml --namespace ingress-basic
kubectl apply -f aks-helloworld-two.yaml --namespace ingress-basic
```

## <a name="create-an-ingress-route"></a>Criar uma rota de entrada

Ambas as aplicações estão agora a ser em execução no seu cluster Kubernetes. No entanto, estão configurados com um serviço de tipo `ClusterIP` e não estão acessíveis a partir da internet. Para torná-los disponíveis ao público, crie um recurso de entrada kubernetes. O recurso ingressa configura as regras que encaminham o tráfego para uma das duas aplicações.

No exemplo seguinte, o tráfego para o endereço *olá-ingresss. MY_CUSTOM_DOMAIN* é encaminhado para o serviço *aks-helloworld.* Tráfego para o endereço *olá-mundo-ingresss. MY_CUSTOM_DOMAIN/olá-mundo-dois* é encaminhado para o serviço *aks-helloworld-two.* Tráfego para *olá-mundo-ingresss. MY_CUSTOM_DOMAIN/estática* é encaminhado para o serviço chamado *aks-helloworld* para ativos estáticos.

> [!NOTE]
> Se configurar um FQDN para o endereço IP do controlador de entrada em vez de um domínio personalizado, use o FQDN em vez de *hello-world-ingress. MY_CUSTOM_DOMAIN.* Por exemplo, se o seu FQDN for *demo-aks-ingress.eastus.cloudapp.azure.com,* substitua *a hello-world-ingress. MY_CUSTOM_DOMAIN* com *demo-aks-ingress.eastus.cloudapp.azure.com* em `hello-world-ingress.yaml` .

Crie um ficheiro com `hello-world-ingress.yaml` o nome de YAML abaixo. Atualize os *anfitriões* e *o anfitrião* para o nome DNS que criou num passo anterior.

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /$1
    nginx.ingress.kubernetes.io/use-regex: "true"
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
          serviceName: aks-helloworld-one
          servicePort: 80
        path: /hello-world-one(/|$)(.*)
      - backend:
          serviceName: aks-helloworld-two
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
      - backend:
          serviceName: aks-helloworld-one
          servicePort: 80
        path: /(.*)
---
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress-static
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /static/$2
    nginx.ingress.kubernetes.io/use-regex: "true"
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
          serviceName: aks-helloworld-one
          servicePort: 80
        path: /static(/|$)(.*)
```

Crie o recurso de entrada utilizando o `kubectl apply` comando.

```console
kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic
```

## <a name="verify-a-certificate-object-has-been-created"></a>Verifique se um objeto de certificado foi criado

Em seguida, deve ser criado um recurso de certificado. O recurso de certificado define o certificado X.509 pretendido. Para mais informações, consulte [os certificados de gestor de certificados.][cert-manager-certificates] O gestor de certificados criou automaticamente um objeto de certificado para si usando o ingress-shim, que é automaticamente implantado com o cert-manager desde v0.2.2. Para mais informações, consulte a [documentação ingress-shim][ingress-shim].

Para verificar se o certificado foi criado com sucesso, utilize o `kubectl get certificate --namespace ingress-basic` comando e verifique SE *PRONTO* é *Verdadeiro* , o que pode demorar vários minutos.

```
$ kubectl get certificate --namespace ingress-basic

NAME         READY   SECRET       AGE
tls-secret   True    tls-secret   11m
```

## <a name="test-the-ingress-configuration"></a>Testar a configuração de entrada

Abra um navegador web para *hello-world-ingress. MY_CUSTOM_DOMAIN* do seu controlador de entrada kubernetes. Note que é redirecionado para a utilização do HTTPS e o certificado é fidedigno e a aplicação de demonstração é mostrada no navegador web. Adicione o caminho */olá-mundo-dois* e note que a segunda aplicação de demonstração com o título personalizado é mostrado.

## <a name="clean-up-resources"></a>Limpar recursos

Este artigo usou helm para instalar os componentes, certificados e aplicações de amostra. Quando se implementa um gráfico Helm, são criados vários recursos kubernetes. Estes recursos incluem cápsulas, implantações e serviços. Para limpar estes recursos, pode eliminar todo o espaço de nome da amostra ou os recursos individuais.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Eliminar o espaço de nome da amostra e todos os recursos

Para eliminar todo o espaço de nome da amostra, use o `kubectl delete` comando e especifique o nome do seu nome de espaço. Todos os recursos no espaço de nomes são eliminados.

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>Eliminar recursos individualmente

Em alternativa, uma abordagem mais granular consiste em eliminar os recursos individuais criados. Primeiro, remova os recursos do emitente do cluster:

```console
kubectl delete -f cluster-issuer.yaml --namespace ingress-basic
```

Lista os lançamentos do Leme com o `helm list` comando. Procure gráficos nomeados *nginx* e *cert-manager,* como mostrado na saída do exemplo seguinte:

```
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
cert-manager            ingress-basic   1               2020-01-15 10:23:36.515514 -0600 CST    deployed        cert-manager-v0.13.0    v0.13.0    
nginx                   ingress-basic   1               2020-01-15 10:09:45.982693 -0600 CST    deployed        nginx-ingress-1.29.1    0.27.0  
```

Desinstale os desbloqueios com o `helm uninstall` comando. O exemplo a seguir desinstala as implementações de entrada e cert-manager NGINX.

```
$ helm uninstall cert-manager nginx --namespace ingress-basic

release "cert-manager" uninstalled
release "nginx" uninstalled
```

Em seguida, remova as duas aplicações da amostra:

```console
kubectl delete -f aks-helloworld-one.yaml --namespace ingress-basic
kubectl delete -f aks-helloworld-two.yaml --namespace ingress-basic
```

Remova a rota de entrada que direcionou o tráfego para as aplicações da amostra:

```console
kubectl delete -f hello-world-ingress.yaml --namespace ingress-basic
```

Finalmente, pode apagar o espaço de nome em si. Utilize o `kubectl delete` comando e especifique o nome do seu nome de espaço de nome:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Passos seguintes

Este artigo incluiu alguns componentes externos para a AKS. Para saber mais sobre estes componentes, consulte as seguintes páginas do projeto:

- [Helm CLI][helm-cli]
- [Controlador de entrada NGINX][nginx-ingress]
- [cert-manager][cert-manager]

Também pode:

- [Criar um controlador básico de entrada com conectividade de rede externa][aks-ingress-basic]
- [Ativar o addon de encaminhamento de aplicações HTTP][aks-http-app-routing]
- [Crie um controlador ingress que utilize uma rede interna, privada e endereço IP][aks-ingress-internal]
- [Crie um controlador de entrada que use os seus próprios certificados TLS][aks-ingress-own-tls]
- [Crie um controlador ingress que usa o Let's Encrypt para gerar automaticamente certificados TLS com um endereço IP público estático][aks-ingress-static-tls]

<!-- LINKS - external -->
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/a?view=azure-cli-latest#az-network-dns-record-set-a-add-record
[custom-domain]: ../app-service/manage-custom-dns-buy-domain.md#buy-the-domain
[dns-zone]: ../dns/dns-getstarted-cli.md
[helm]: https://helm.sh/
[helm-cli]: ./kubernetes-helm.md
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
