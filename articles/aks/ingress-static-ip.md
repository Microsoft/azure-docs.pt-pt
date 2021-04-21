---
title: Use controlador de entrada com IP estático
titleSuffix: Azure Kubernetes Service
description: Aprenda a instalar e configurar um controlador de entrada NGINX com um endereço IP público estático num cluster Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: 8de0d25bc30d53f11ddaed5ab7b53ff992a5c88c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779836"
---
# <a name="create-an-ingress-controller-with-a-static-public-ip-address-in-azure-kubernetes-service-aks"></a>Criar um controlador de entrada com um endereço IP público estático no Azure Kubernetes Service (AKS)

Um controlador de entrada é uma peça de software que fornece procuração inversa, encaminhamento de tráfego configurável e rescisão de TLS para serviços Kubernetes. Os recursos de entrada de Kubernetes são usados para configurar as regras e rotas de entrada para serviços individuais de Kubernetes. Utilizando um controlador de entrada e regras de entrada, um único endereço IP pode ser usado para encaminhar o tráfego para vários serviços num cluster kubernetes.

Este artigo mostra-lhe como implantar o [controlador de entrada NGINX][nginx-ingress] num cluster Azure Kubernetes Service (AKS). O controlador de entrada está configurado com um endereço IP público estático. O projeto [cert-manager][cert-manager] é usado para gerar e configurar automaticamente os certificados [Let's Encrypt.][lets-encrypt] Finalmente, duas aplicações são executadas no cluster AKS, cada uma das quais é acessível sobre um único endereço IP.

Também pode:

- [Criar um controlador básico de entrada com conectividade de rede externa][aks-ingress-basic]
- [Ativar o addon de encaminhamento de aplicações HTTP][aks-http-app-routing]
- [Crie um controlador de entrada que use os seus próprios certificados TLS][aks-ingress-own-tls]
- [Crie um controlador ingress que usa o Let's Encrypt para gerar automaticamente certificados TLS com um endereço IP público dinâmico][aks-ingress-tls]

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que você tem um cluster AKS existente. Se precisar de um cluster AKS, consulte o quickstart AKS [utilizando o Azure CLI][aks-quickstart-cli] ou [utilizando o portal Azure][aks-quickstart-portal].

Este artigo utiliza [o Helm 3][helm] para instalar o controlador de entrada NGINX e o gestor de certificados. Certifique-se de que está a usar o mais recente lançamento do Helm e tenha acesso aos *repositórios de ingresss-nginx* e *jetstack* Helm. Para obter instruções de atualização, consulte os [docs de instalação helm][helm-install]. Para obter mais informações sobre a configuração e utilização do Helm, consulte [instalar aplicações com Helm in Azure Kubernetes Service (AKS)][use-helm].

Este artigo também requer que esteja a executar a versão Azure CLI 2.0.64 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Criar um controlador de entrada

Por predefinição, um controlador de entrada NGINX é criado com uma nova atribuição de endereço IP público. Este endereço IP público é apenas estático para o tempo de vida do controlador de entrada, e perde-se se o controlador for eliminado e recriado. Um requisito comum de configuração é fornecer ao controlador de entrada NGINX um endereço IP público estático existente. O endereço IP público estático permanece se o controlador de entrada for eliminado. Esta abordagem permite-lhe utilizar os registos DNS existentes e as configurações de rede de forma consistente ao longo do ciclo de vida das suas aplicações.

Se precisar de criar um endereço IP público estático, obtenha primeiro o nome do grupo de recursos do cluster AKS com o comando [az aks show:][az-aks-show]

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

Em seguida, crie um endereço IP público com o método de atribuição *estática* usando o comando [de criação pública de rede az.][az-network-public-ip-create] O exemplo a seguir cria um endereço IP público denominado *myAKSPublicIP* no grupo de recursos de cluster AKS obtido no passo anterior:

```azurecli-interactive
az network public-ip create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --sku Standard --allocation-method static --query publicIp.ipAddress -o tsv
```

> [!NOTE]
> Os comandos acima criam um endereço IP que será eliminado se eliminar o seu cluster AKS. Em alternativa, pode criar um endereço IP num grupo de recursos diferente que pode ser gerido separadamente do seu cluster AKS. Se criar um endereço IP num grupo de recursos diferente, certifique-se de que a identidade de cluster utilizada pelo cluster AKS tem permissões delegadas para o outro grupo de recursos, como o *Network Contributor*. Para obter mais informações, consulte [Utilize um endereço IP público estático e etiqueta DNS com o balançador de carga AKS][aks-static-ip].

Agora, implante a tabela *de entrada de nginx* com Helm. Para uma maior redundância, são implementadas duas réplicas dos controladores de entrada do NGINX com o parâmetro `--set controller.replicaCount`. Para beneficiar totalmente da execução de réplicas do controlador de entrada, certifique-se de que há mais de um nó no seu cluster AKS.

Deve passar dois parâmetros adicionais para a libertação helm para que o controlador de entrada seja informado tanto do endereço IP estático do esquilibrador de carga a atribuir ao serviço de controlador de entrada, como da etiqueta de nome DNS a ser aplicada ao recurso de endereço IP público. Para que os certificados HTTPS funcionem corretamente, é utilizada uma etiqueta de nome DNS para configurar um FQDN para o endereço IP do controlador de entrada.

1. Adicione o `--set controller.service.loadBalancerIP` parâmetro. Especifique o seu próprio endereço IP público que foi criado no passo anterior.
1. Adicione o `--set controller.service.annotations."service\.beta\.kubernetes\.io/azure-dns-label-name"` parâmetro. Especifique uma etiqueta de nome DNS a aplicar no endereço IP público criado no passo anterior.

O controlador de entrada também tem de estar agendado num nó do Linux. Os nós do Windows Server não devem executar o controlador de entrada. É especificado um seletor de nós com o parâmetro `--set nodeSelector` para indicar ao agendador do Kubernetes que execute o controlador de entrada do NGINX num nó baseado no Linux.

> [!TIP]
> O exemplo a seguir cria um espaço de nome Kubernetes para os recursos *ingressos denominados ingress-basic*. Especifique um espaço de nome para o seu próprio ambiente, conforme necessário. Se o seu cluster AKS não estiver ativado por Kubernetes RBAC, adicione `--set rbac.create=false` aos comandos Helm.

> [!TIP]
> Se pretender permitir a [preservação ip da fonte do cliente][client-source-ip] para pedidos a contentores no seu cluster, adicione ao comando de `--set controller.service.externalTrafficPolicy=Local` instalação Helm. A FONTE DO CLIENTE IP é armazenada no cabeçalho de pedido sob *X-Forwarded-For*. Ao utilizar um controlador de entrada com a preservação IP de fonte do cliente ativada, o passe do TLS não funcionará.

Atualize o seguinte script com o **endereço IP** do seu controlador de entrada e um **nome único** que gostaria de usar para o prefixo FQDN.

> [!IMPORTANT]
> Tem de atualizar *substituir STATIC_IP* e *DNS_LABEL* pelo seu próprio endereço IP e nome único ao executar o comando.

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
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.admissionWebhooks.patch.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.service.loadBalancerIP="STATIC_IP" \
    --set controller.service.annotations."service\.beta\.kubernetes\.io/azure-dns-label-name"="DNS_LABEL"
```

Quando o serviço de balançador de carga Kubernetes é criado para o controlador de entrada NGINX, o seu endereço IP estático é atribuído, como mostra a saída do exemplo seguinte:

```
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

Ainda não foram criadas regras de entrada, pelo que a página padrão 404 do controlador NGINX é apresentada se navegar para o endereço IP público. As regras de ingress são configuradas nos seguintes passos.

Pode verificar se a etiqueta de nome DNS foi aplicada consultando o FQDN no endereço IP público da seguinte forma:

```azurecli-interactive
az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query "[?ipAddress=='myAKSPublicIP'].[dnsSettings.fqdn]" -o tsv
```

O controlador de entrada está agora acessível através do endereço IP ou do FQDN.

## <a name="install-cert-manager"></a>Instalar o cert-manager

O controlador de entrada do NGINX suporta a terminação de TLS. Existem várias formas de obter e configurar certificados para HTTPS. Este artigo demonstra a utilização [de cert-manager,][cert-manager]que fornece automaticamente [Lets Encrypt de][lets-encrypt] geração e funcionalidade de gestão.

> [!NOTE]
> Este artigo usa o `staging` ambiente para Let's Encrypt. Nas implementações de produção, utilização `letsencrypt-prod` e `https://acme-v02.api.letsencrypt.org/directory` nas definições de recursos e na instalação do gráfico Helm.

Para instalar o controlador cert-manager num cluster ativado por CHC de Kubernetes, utilize o seguinte `helm install` comando:

```console
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
          podTemplate:
            spec:
              nodeSelector:
                "kubernetes.io/os": linux
```

Para criar o emitente, use o `kubectl apply` comando.

```
kubectl apply -f cluster-issuer.yaml --namespace ingress-basic
```

A saída deve ser semelhante a este exemplo:

```
clusterissuer.cert-manager.io/letsencrypt-staging created
```

## <a name="run-demo-applications"></a>Executar aplicações de demonstração

Foi configurado um controlador de entrada e uma solução de gestão de certificados. Agora vamos executar duas aplicações de demonstração no seu cluster AKS. Neste exemplo, Helm é usado para implementar dois casos de uma simples aplicação "Olá mundo".

Para ver o controlador de entrada em ação, execute duas aplicações de demonstração no seu cluster AKS. Neste exemplo, você usa `kubectl apply` para implementar dois casos de uma simples aplicação *Hello world.*

Crie um ficheiro *aks-helloworld.yaml* e copie no seguinte exemplo YAML:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aks-helloworld
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-helloworld
  template:
    metadata:
      labels:
        app: aks-helloworld
    spec:
      containers:
      - name: aks-helloworld
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
  name: aks-helloworld
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld
```

Crie um ficheiro *ingress-demo.yaml* e copie no seguinte exemplo YAML:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ingress-demo
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ingress-demo
  template:
    metadata:
      labels:
        app: ingress-demo
    spec:
      containers:
      - name: ingress-demo
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
  name: ingress-demo
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: ingress-demo
```

Executar as duas aplicações de demonstração `kubectl apply` utilizando:

```console
kubectl apply -f aks-helloworld.yaml --namespace ingress-basic
kubectl apply -f ingress-demo.yaml --namespace ingress-basic
```

## <a name="create-an-ingress-route"></a>Criar uma rota de entrada

Ambas as aplicações estão agora a ser executadas no seu cluster Kubernetes, no entanto estão configuradas com um serviço de tipo `ClusterIP` . Como tal, as aplicações não são acessíveis a partir da internet. Para torná-los disponíveis ao público, crie um recurso de entrada kubernetes. O recurso ingressa configura as regras que encaminham o tráfego para uma das duas aplicações.

No exemplo seguinte, o tráfego para o endereço `https://demo-aks-ingress.eastus.cloudapp.azure.com/` é encaminhado para o serviço denominado `aks-helloworld` . O tráfego para o endereço `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` é encaminhado para o `ingress-demo` serviço. Atualize os *anfitriões* e *o anfitrião* para o nome DNS que criou num passo anterior.

Crie um ficheiro nomeado `hello-world-ingress.yaml` e copie no seguinte exemplo YAML.

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    cert-manager.io/cluster-issuer: letsencrypt-staging
    nginx.ingress.kubernetes.io/rewrite-target: /$1
    nginx.ingress.kubernetes.io/use-regex: "true"
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
        path: /hello-world-one(/|$)(.*)
      - backend:
          serviceName: ingress-demo
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /(.*)
```

Crie o recurso de entrada utilizando o `kubectl apply` comando.

```
kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic
```

A saída deve ser semelhante a este exemplo:

```
ingress.extensions/hello-world-ingress created
```

## <a name="create-a-certificate-object"></a>Criar um objeto de certificado

Em seguida, deve ser criado um recurso de certificado. O recurso de certificado define o certificado X.509 pretendido. Para mais informações, consulte [os certificados de gestor de certificados.][cert-manager-certificates]

O gestor de certificados provavelmente criou automaticamente um objeto de certificado para si usando o ingress-shim, que é automaticamente implantado com cert-manager desde v0.2.2. Para mais informações, consulte a [documentação ingress-shim][ingress-shim].

Para verificar se o certificado foi criado com sucesso, utilize o `kubectl describe certificate tls-secret --namespace ingress-basic` comando.

Se o certificado for emitido, verá uma saída semelhante à seguinte:
```
Type    Reason          Age   From          Message
----    ------          ----  ----          -------
  Normal  CreateOrder     11m   cert-manager  Created new ACME order, attempting validation...
  Normal  DomainVerified  10m   cert-manager  Domain "demo-aks-ingress.eastus.cloudapp.azure.com" verified with "http-01" validation
  Normal  IssueCert       10m   cert-manager  Issuing certificate...
  Normal  CertObtained    10m   cert-manager  Obtained certificate from ACME server
  Normal  CertIssued      10m   cert-manager  Certificate issued successfully
```

Se precisar de criar um recurso adicional de certificado, pode fazê-lo com o seguinte manifesto de exemplo. Atualize os nomes e *domínios dns* para o nome DNS que criou num passo anterior.  Se utilizar um controlador interno de entrada, especifique o nome DNS interno para o seu serviço.

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

Para criar o recurso certificado, utilize o `kubectl apply` comando.

```
$ kubectl apply -f certificates.yaml

certificate.cert-manager.io/tls-secret created
```

## <a name="test-the-ingress-configuration"></a>Testar a configuração de entrada

Abra um navegador web para o FQDN do seu controlador de entrada Kubernetes, como *`https://demo-aks-ingress.eastus.cloudapp.azure.com`* .

Como estes exemplos `letsencrypt-staging` utilizam, o certificado TLS/SSL emitido não é fidedigno pelo navegador. Aceite o aviso de aviso para continuar a sua candidatura. As informações do certificado mostram que este certificado *Falso LE Intermediate X1* é emitido pela Let's Encrypt. Este certificado falso indica `cert-manager` que processou corretamente o pedido e recebeu um certificado do fornecedor:

![Vamos encriptar o certificado de encenação](media/ingress/staging-certificate.png)

Quando alterar O Cripto de Design de Let's Para usar `prod` em vez de , é utilizado um certificado de confiança emitido pela `staging` Let's Encrypt, como mostra o seguinte exemplo:

![Vamos encriptar certificado](media/ingress/certificate.png)

A aplicação de demonstração é mostrada no navegador web:

![Exemplo de aplicação um](media/ingress/app-one.png)

Adicione agora o caminho */olá-mundo-dois* ao FQDN, tal como *`https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two`* . A segunda aplicação de demonstração com o título personalizado é mostrada:

![Exemplo de aplicação dois](media/ingress/app-two.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Este artigo usou helm para instalar os componentes, certificados e aplicações de amostra. Quando se implementa um gráfico Helm, são criados vários recursos kubernetes. Estes recursos incluem cápsulas, implantações e serviços. Para limpar estes recursos, pode eliminar todo o espaço de nome da amostra ou os recursos individuais.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Eliminar o espaço de nome da amostra e todos os recursos

Para eliminar todo o espaço de nome da amostra, use o `kubectl delete` comando e especifique o nome do seu nome de espaço. Todos os recursos no espaço de nomes são eliminados.

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>Eliminar recursos individualmente

Em alternativa, uma abordagem mais granular consiste em eliminar os recursos individuais criados. Primeiro, remova os recursos do certificado:

```console
kubectl delete -f certificates.yaml
kubectl delete -f cluster-issuer.yaml
```

Agora, enumera os lançamentos do Helm com o `helm list` comando. Procure gráficos *nomeados nginx-ingress* e *cert-manager* como mostrado na saída do exemplo seguinte:

```
$ helm list --all-namespaces

NAME                    NAMESPACE       REVISION        UPDATED                        STATUS          CHART                   APP VERSION
nginx-ingress           ingress-basic   1               2020-01-11 14:51:03.454165006  deployed        nginx-ingress-1.28.2    0.26.2
cert-manager            ingress-basic   1               2020-01-06 21:19:03.866212286  deployed        cert-manager-v0.13.0    v0.13.0
```

Desinstale os desbloqueios com o `helm uninstall` comando. O exemplo a seguir desinstala as implementações de implementação e gestor de certificados de entrada NGINX.

```
$ helm uninstall nginx-ingress cert-manager -n ingress-basic

release "nginx-ingress" deleted
release "cert-manager" deleted
```

Em seguida, remova as duas aplicações da amostra:

```console
kubectl delete -f aks-helloworld.yaml --namespace ingress-basic
kubectl delete -f ingress-demo.yaml --namespace ingress-basic
```

Apague o espaço de nome em si. Utilize o `kubectl delete` comando e especifique o nome do seu nome de espaço de nome:

```console
kubectl delete namespace ingress-basic
```

Por fim, remova o endereço IP público estático criado para o controlador de entrada. Forneça *o* seu MC_ nome do grupo de recursos de cluster obtidos no primeiro passo deste artigo, como *MC_myResourceGroup_myAKSCluster_eastus:*

```azurecli-interactive
az network public-ip delete --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP
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
- [Criar um controlador de entrada com um IP público dinâmico e configurar Let's Encrypt para gerar automaticamente certificados TLS][aks-ingress-tls]

<!-- LINKS - external -->
[helm-cli]: ./kubernetes-helm.md
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm]: https://helm.sh/
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm
[ingress-shim]: https://docs.cert-manager.io/en/latest/tasks/issuing-certificates/ingress-shim.html

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az_network_public_ip_create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[client-source-ip]: concepts-network.md#ingress-controllers
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-static-ip]: static-ip.md
