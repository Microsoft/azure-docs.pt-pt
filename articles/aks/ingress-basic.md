---
title: Criar um controlador de entrada
titleSuffix: Azure Kubernetes Service
description: Aprenda a instalar e configurar um controlador básico de entrada NGINX num cluster Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: 9b51ee2767a9595f5732f558cfa25f5064944e49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93131195"
---
# <a name="create-an-ingress-controller-in-azure-kubernetes-service-aks"></a>Criar um controlador de entrada no Serviço Azure Kubernetes (AKS)

Um controlador de entrada é uma peça de software que fornece procuração inversa, encaminhamento de tráfego configurável e rescisão de TLS para serviços Kubernetes. Os recursos de entrada de Kubernetes são usados para configurar as regras e rotas de entrada para serviços individuais de Kubernetes. Utilizando um controlador de entrada e regras de entrada, um único endereço IP pode ser usado para encaminhar o tráfego para vários serviços num cluster kubernetes.

Este artigo mostra-lhe como implantar o [controlador de entrada NGINX][nginx-ingress] num cluster Azure Kubernetes Service (AKS). Duas aplicações são então executadas no cluster AKS, cada uma das quais está acessível sobre o único endereço IP.

Também pode:

- [Ativar o addon de encaminhamento de aplicações HTTP][aks-http-app-routing]
- [Crie um controlador ingress que utilize uma rede interna, privada e endereço IP][aks-ingress-internal]
- [Crie um controlador de entrada que use os seus próprios certificados TLS][aks-ingress-own-tls]
- Crie um controlador ingress que utilize o Let's Encrypt para gerar automaticamente certificados TLS [com um endereço IP público dinâmico][aks-ingress-tls] ou com um endereço IP público [estático][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Antes de começar

Este artigo utiliza [o Helm 3][helm] para instalar o controlador de entrada NGINX. Certifique-se de que está a utilizar a mais recente versão do Helm e tenha acesso ao repositório de Helm *ingresss-nginx.*

Este artigo também requer que esteja a executar a versão Azure CLI 2.0.64 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Criar um controlador de entrada

Para criar o controlador de entrada, utilize o Helm para instalar *a entrada de nginx*. Para uma maior redundância, são implementadas duas réplicas dos controladores de entrada do NGINX com o parâmetro `--set controller.replicaCount`. Para beneficiar totalmente da execução de réplicas do controlador de entrada, certifique-se de que há mais de um nó no seu cluster AKS.

O controlador de entrada também tem de estar agendado num nó do Linux. Os nós do Windows Server não devem executar o controlador de entrada. É especificado um seletor de nós com o parâmetro `--set nodeSelector` para indicar ao agendador do Kubernetes que execute o controlador de entrada do NGINX num nó baseado no Linux.

> [!TIP]
> O exemplo a seguir cria um espaço de nome Kubernetes para os recursos *ingressos denominados ingress-basic*. Especifique um espaço de nome para o seu próprio ambiente, conforme necessário.

> [!TIP]
> Se pretender permitir a [preservação ip da fonte do cliente][client-source-ip] para pedidos a contentores no seu cluster, adicione ao comando de `--set controller.service.externalTrafficPolicy=Local` instalação Helm. A FONTE DO CLIENTE IP é armazenada no cabeçalho de pedido sob *X-Forwarded-For*. Ao utilizar um controlador de entrada com a preservação IP de fonte do cliente ativada, a passagem SSL não funcionará.

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
    --set controller.admissionWebhooks.patch.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Quando o serviço de balançador de carga Kubernetes é criado para o controlador de entrada NGINX, é atribuído um endereço IP público dinâmico, como mostra a saída do exemplo seguinte:

```
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

Ainda não foram criadas regras de entrada, pelo que a página padrão 404 do controlador NGINX é apresentada se navegar para o endereço IP interno. As regras de ingress são configuradas nos seguintes passos.

## <a name="run-demo-applications"></a>Executar aplicações de demonstração

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

Ambas as aplicações estão agora a ser em execução no seu cluster Kubernetes. Para encaminhar o tráfego para cada aplicação, crie um recurso de entrada kubernetes. O recurso ingressa configura as regras que encaminham o tráfego para uma das duas aplicações.

No exemplo seguinte, o tráfego para *EXTERNAL_IP* é encaminhado para o serviço denominado `aks-helloworld-one` . O tráfego para *EXTERNAL_IP/olá-mundo-dois* é encaminhado para o `aks-helloworld-two` serviço. O tráfego para *EXTERNAL_IP/estática* é encaminhado para o serviço nomeado `aks-helloworld-one` para ativos estáticos.

Crie um ficheiro chamado *hello-world-ingress.yaml* e copie no seguinte exemplo YAML.

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    nginx.ingress.kubernetes.io/use-regex: "true"
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  rules:
  - http:
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
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    nginx.ingress.kubernetes.io/rewrite-target: /static/$2
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: aks-helloworld-one
          servicePort: 80
        path: /static(/|$)(.*)
```

Crie o recurso de entrada utilizando o `kubectl apply -f hello-world-ingress.yaml` comando.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
ingress.extensions/hello-world-ingress-static created
```

## <a name="test-the-ingress-controller"></a>Teste o controlador de entrada

Para testar as rotas do controlador de entrada, navegue pelas duas aplicações. Abra um navegador web para o endereço IP do seu controlador de entrada NGINX, como *EXTERNAL_IP*. A primeira aplicação de demonstração é apresentada no navegador web, como mostra o exemplo seguinte:

![Primeira aplicação a correr atrás do controlador de entrada](media/ingress-basic/app-one.png)

Adicione agora o caminho */olá-mundo-dois* ao endereço IP, como *EXTERNAL_IP/olá-mundo-dois*. É apresentada a segunda aplicação de demonstração com o título personalizado:

![Segunda aplicação a correr atrás do controlador de entrada](media/ingress-basic/app-two.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Este artigo usou o Helm para instalar os componentes de entrada e as aplicações de amostra. Quando se implementa um gráfico Helm, são criados vários recursos kubernetes. Estes recursos incluem cápsulas, implantações e serviços. Para limpar estes recursos, pode eliminar todo o espaço de nome da amostra ou os recursos individuais.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Eliminar o espaço de nome da amostra e todos os recursos

Para eliminar todo o espaço de nome da amostra, use o `kubectl delete` comando e especifique o nome do seu nome de espaço. Todos os recursos no espaço de nomes são eliminados.

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>Eliminar recursos individualmente

Em alternativa, uma abordagem mais granular consiste em eliminar os recursos individuais criados. Lista os lançamentos do Leme com o `helm list` comando. Procure gráficos *nomeados nginx-ingress* e *aks-helloworld,* como mostrado na saída do exemplo seguinte:

```
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
nginx-ingress           ingress-basic   1               2020-01-06 19:55:46.358275 -0600 CST    deployed        nginx-ingress-1.27.1    0.26.1  
```

Desinstale os desbloqueios com o `helm uninstall` comando. O exemplo a seguir desinstala a implantação de entrada NGINX.

```
$ helm uninstall nginx-ingress --namespace ingress-basic

release "nginx-ingress" uninstalled
```

Em seguida, remova as duas aplicações da amostra:

```console
kubectl delete -f aks-helloworld-one.yaml --namespace ingress-basic
kubectl delete -f aks-helloworld-two.yaml --namespace ingress-basic
```

Remova a rota de entrada que direcionou o tráfego para as aplicações da amostra:

```console
kubectl delete -f hello-world-ingress.yaml
```

Finalmente, pode apagar o espaço de nome em si. Utilize o `kubectl delete` comando e especifique o nome do seu nome de espaço de nome:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Passos seguintes

Este artigo incluiu alguns componentes externos para a AKS. Para saber mais sobre estes componentes, consulte as seguintes páginas do projeto:

- [Helm CLI][helm-cli]
- [Controlador de entrada NGINX][nginx-ingress]

Também pode:

- [Ativar o addon de encaminhamento de aplicações HTTP][aks-http-app-routing]
- [Crie um controlador ingress que utilize uma rede interna, privada e endereço IP][aks-ingress-internal]
- [Crie um controlador de entrada que use os seus próprios certificados TLS][aks-ingress-own-tls]
- Crie um controlador ingress que utilize o Let's Encrypt para gerar automaticamente certificados TLS [com um endereço IP público dinâmico][aks-ingress-tls] ou com um endereço IP público [estático][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-cli]: ./kubernetes-helm.md
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[client-source-ip]: concepts-network.md#ingress-controllers
