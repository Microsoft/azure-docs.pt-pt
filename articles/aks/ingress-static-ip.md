---
title: Utilize controlador de ingresso com IP estático
titleSuffix: Azure Kubernetes Service
description: Aprenda a instalar e configurar um controlador de ingresso NGINX com um endereço IP público estático num cluster do Serviço Azure Kubernetes (AKS).
services: container-service
ms.topic: article
ms.date: 04/27/2020
ms.openlocfilehash: a44a41806af30479f06ec4daba936c7aa71ef5d7
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82561918"
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

Este artigo utiliza o [Helm 3][helm] para instalar o controlador de entrada NGINX e o cert-manager. Certifique-se de que está a usar o último lançamento do Helm. Para obter instruções de atualização, consulte o [Helm instalar docs][helm-install]. Para obter mais informações sobre configurar e utilizar o Helm, consulte [Instalar aplicações com o Helm no Serviço Azure Kubernetes (AKS)][use-helm].

Este artigo também requer que esteja a executar a versão Azure CLI 2.0.64 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli-install].

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

Agora implemente o gráfico *de nginx-ingresscom* Helm. Para uma maior redundância, são implementadas duas réplicas dos controladores de entrada do NGINX com o parâmetro `--set controller.replicaCount`. Para se beneficiar totalmente da execução de réplicas do controlador de ingresso, certifique-se de que há mais de um nó no seu cluster AKS.

Deve passar dois parâmetros adicionais para a libertação helm para que o controlador de entrada seja informado tanto do endereço IP estático do equilibrista de carga a atribuir ao serviço do controlador de entrada, como da etiqueta de nome DNS aplicada ao recurso de endereço IP público. Para que os certificados HTTPS funcionem corretamente, é utilizada uma etiqueta de nome DNS para configurar um FQDN para o endereço IP do controlador de entrada.

1. Adicione `--set controller.service.loadBalancerIP` o parâmetro. Especifique o seu próprio endereço IP público que foi criado no passo anterior.
1. Adicione `--set controller.service.annotations."service\.beta\.kubernetes\.io/azure-dns-label-name"` o parâmetro. Especifique uma etiqueta de nome DNS a aplicar ao endereço IP público que foi criado na etapa anterior.

O controlador de entrada também tem de estar agendado num nó do Linux. Os nós do Windows Server não devem executar o controlador de entrada. É especificado um seletor de nós com o parâmetro `--set nodeSelector` para indicar ao agendador do Kubernetes que execute o controlador de entrada do NGINX num nó baseado no Linux.

> [!TIP]
> O exemplo seguinte cria um espaço de nome Kubernetes para os recursos de ingresso *denominados ingress-basic*. Especifique um espaço de nome para o seu próprio ambiente, conforme necessário. Se o seu cluster AKS não `--set rbac.create=false` estiver ativado por RBAC, adicione aos comandos Helm.

> [!TIP]
> Se quiser ativar a [preservação ip][client-source-ip] de origem do cliente `--set controller.service.externalTrafficPolicy=Local` para pedidos de contentores no seu cluster, adicione ao comando de instalação helm. O IP de origem do cliente é armazenado no cabeçalho de pedido sob *X-Forwarded-For*. Ao utilizar um controlador de ingresso com a preservação IP de origem do cliente ativada, a passagem do TLS não funcionará.

Atualize o seguinte script com o **endereço IP** do seu controlador de ingresso e um **nome único** que gostaria de usar para o prefixo FQDN:

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Use Helm to deploy an NGINX ingress controller
helm install nginx-ingress stable/nginx-ingress \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.service.loadBalancerIP="STATIC_IP" \
    --set controller.service.annotations."service\.beta\.kubernetes\.io/azure-dns-label-name"="demo-aks-ingress"
```

Quando o serviço de equilíbrio de carga Kubernetes é criado para o controlador de entrada NGINX, o seu endereço IP estático é atribuído, como mostra a seguinte saída exemplo:

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                        TYPE           CLUSTER-IP    EXTERNAL-IP    PORT(S)                      AGE
nginx-ingress-controller                    LoadBalancer   10.0.232.56   STATIC_IP      80:31978/TCP,443:32037/TCP   3m
nginx-ingress-default-backend               ClusterIP      10.0.95.248   <none>         80/TCP                       3m
```

Ainda não foram criadas regras de ingresso, pelo que a página padrão 404 do controlador de ingresso NGINX é exibida se navegar para o endereço IP público. As regras de ingresso estão configuradas nos seguintes passos.

Pode verificar se a etiqueta de nome DNS foi aplicada consultando o FQDN no endereço IP público da seguinte forma:

```azurecli-interactive
az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query "[?name=='myAKSPublicIP'].[dnsSettings.fqdn]" -o tsv
```

O controlador de entrada está agora acessível através do endereço IP ou do FQDN.

## <a name="install-cert-manager"></a>Instalar o cert-manager

O controlador de entrada do NGINX suporta a terminação de TLS. Existem várias formas de obter e configurar certificados para HTTPS. Este artigo demonstra a [utilização do cert-manager][cert-manager], que fornece automaticamente a geração de [certificados lets encrypt][lets-encrypt] e a funcionalidade de gestão.

> [!NOTE]
> Este artigo `staging` usa o ambiente para Let's Encrypt. Nas implantações de `letsencrypt-prod` `https://acme-v02.api.letsencrypt.org/directory` produção, utilização e nas definições de recursos e na instalação do gráfico Helm.

Para instalar o controlador cert-manager num cluster ativado `helm install` pelo RBAC, utilize o seguinte comando:

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

Antes de os certificados poderem ser emitidos, o cert-manager requer um recurso [Emitente][cert-manager-issuer] ou [ClusterIssuer.][cert-manager-cluster-issuer] Estes recursos Kubernetes são idênticos `Issuer` na funcionalidade, no `ClusterIssuer` entanto funciona num único espaço de nome, e funciona em todos os espaços de nome. Para mais informações, consulte a documentação do [emitente do gestor da CERT.][cert-manager-issuer]

Criar um emitente `cluster-issuer.yaml`de cluster, como, por exemplo, usando o seguinte manifesto exemplo. Atualize o endereço de e-mail com um endereço válido da sua organização:

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

Para criar o emitente, use o `kubectl apply -f cluster-issuer.yaml` comando.

```
$ kubectl apply -f cluster-issuer.yaml --namespace ingress-basic

clusterissuer.cert-manager.io/letsencrypt-staging created
```

## <a name="run-demo-applications"></a>Executar aplicações de demonstração

Um controlador de ingresso e uma solução de gestão de certificados foram configurados. Agora vamos executar duas aplicações de demonstração no seu cluster AKS. Neste exemplo, helm é usado para implementar dois casos de uma simples aplicação "Olá mundo".

Para ver o controlador de ingresso em ação, execute duas aplicações de demonstração no seu cluster AKS. Neste exemplo, você `kubectl apply` usa para implementar duas instâncias de uma aplicação simples *do mundo Hello.*

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
        image: neilpeterson/aks-helloworld:v1
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
        image: neilpeterson/aks-helloworld:v1
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

Executar as duas aplicações `kubectl apply`de demonstração usando:

```console
kubectl apply -f aks-helloworld.yaml --namespace ingress-basic
kubectl apply -f ingress-demo.yaml --namespace ingress-basic
```

## <a name="create-an-ingress-route"></a>Criar uma rota de ingresso

Ambas as aplicações estão agora a funcionar no seu cluster Kubernetes, no entanto estão configuradas com um serviço de tipo. `ClusterIP` Como tal, as aplicações não são acessíveis a partir da internet. Para torná-los disponíveis publicamente, crie um recurso de entrada Kubernetes. O recurso de ingresso configura as regras que encaminham o tráfego para uma das duas aplicações.

No exemplo seguinte, o `https://demo-aks-ingress.eastus.cloudapp.azure.com/` tráfego para o endereço `aks-helloworld`é encaminhado para o serviço denominado . O tráfego `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` para o endereço `ingress-demo` é encaminhado para o serviço. Atualize os *anfitriões* e *o anfitrião* do nome DNS que criou num passo anterior.

Crie um `hello-world-ingress.yaml` ficheiro nomeado e copie no seguinte exemplo YAML.

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

Crie o recurso `kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic` de entrada utilizando o comando.

```
$ kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic

ingress.extensions/hello-world-ingress created
```

## <a name="create-a-certificate-object"></a>Criar um objeto de certificado

Em seguida, deve ser criado um recurso de certificado. O recurso do certificado define o certificado X.509 desejado. Para mais informações, consulte [os certificados do cert-manager][cert-manager-certificates].

O cert-manager provavelmente criou automaticamente um objeto de certificado para você usando ingress-shim, que é automaticamente implantado com cert-manager desde v0.2.2. Para mais informações, consulte a [documentação ingress-shim][ingress-shim].

Para verificar se o certificado foi `kubectl describe certificate tls-secret --namespace ingress-basic` criado com sucesso, utilize o comando.

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

Para criar o recurso `kubectl apply -f certificates.yaml` do certificado, utilize o comando.

```
$ kubectl apply -f certificates.yaml

certificate.cert-manager.io/tls-secret created
```

## <a name="test-the-ingress-configuration"></a>Testar a configuração da entrada

Abra um navegador web para o FQDN do seu *`https://demo-aks-ingress.eastus.cloudapp.azure.com`* controlador de entrada Kubernetes, como .

Como estes exemplos utilizam, `letsencrypt-staging`o certificado TLS/SSL emitido não é confiado pelo navegador. Aceite o aviso para continuar na sua aplicação. As informações do certificado mostram que este certificado *Falso LE Intermediate X1* é emitido pela Let's Encrypt. Este certificado `cert-manager` falso indica que processou o pedido corretamente e recebeu um certificado do fornecedor:

![Vamos encriptar o certificado de encenação](media/ingress/staging-certificate.png)

Quando muda, deixe-se `prod` encriptar `staging`para usar em vez de , um certificado de confiança emitido pela Let's Encrypt é usado, como mostra o seguinte exemplo:

![Vamos encriptar certificado](media/ingress/certificate.png)

A aplicação de demonstração é mostrada no navegador web:

![Exemplo de aplicação um](media/ingress/app-one.png)

Agora adicione o caminho */olá-mundo-dois* para o *`https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two`* FQDN, tais como . A segunda aplicação de demonstração com o título personalizado é mostrada:

![Exemplo de aplicação dois](media/ingress/app-two.png)

## <a name="clean-up-resources"></a>Limpar recursos

Este artigo usou o Helm para instalar os componentes de entrada, certificados e aplicações de amostragem. Quando se implanta um gráfico Helm, são criados vários recursos kubernetes. Estes recursos incluem cápsulas, implantações e serviços. Para limpar estes recursos, pode eliminar todo o espaço de nome da amostra ou os recursos individuais.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Eliminar o espaço de nome da amostra e todos os recursos

Para eliminar todo o espaço `kubectl delete` de nome da amostra, utilize o comando e especifique o nome do seu espaço de nome. Todos os recursos no espaço de nome são eliminados.

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>Eliminar recursos individualmente

Em alternativa, uma abordagem mais granular é eliminar os recursos individuais criados. Primeiro, remova os recursos do certificado:

```console
kubectl delete -f certificates.yaml
kubectl delete -f cluster-issuer.yaml
```

Agora lista o Helm `helm list` liberta com o comando. Procure gráficos *chamados nginx-ingress* e *cert-manager* como mostrado na seguinte saída exemplo:

```
$ helm list --all-namespaces

NAME                    NAMESPACE       REVISION        UPDATED                        STATUS          CHART                   APP VERSION
nginx-ingress           ingress-basic   1               2020-01-11 14:51:03.454165006  deployed        nginx-ingress-1.28.2    0.26.2
cert-manager            ingress-basic   1               2020-01-06 21:19:03.866212286  deployed        cert-manager-v0.13.0    v0.13.0
```

Desinstale as `helm uninstall` versões com o comando. O exemplo seguinte desinstala as implementações de implementação de ingressos NGINX e o gestor de certificados.

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

Apague o espaço de nome em si. Utilize `kubectl delete` o comando e especifique o nome do seu espaço de nome:

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
[helm]: https://helm.sh/
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
