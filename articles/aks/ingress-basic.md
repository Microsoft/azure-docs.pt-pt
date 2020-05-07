---
title: Criar um controlador de ingresso
titleSuffix: Azure Kubernetes Service
description: Aprenda a instalar e configurar um controlador de ingresso NGINX básico num cluster do Serviço Azure Kubernetes (AKS).
services: container-service
ms.topic: article
ms.date: 04/27/2020
ms.openlocfilehash: e5b3d1c94de8406c12222eea59beafd7277d646d
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82561969"
---
# <a name="create-an-ingress-controller-in-azure-kubernetes-service-aks"></a>Criar um controlador de ingresso no Serviço Azure Kubernetes (AKS)

Um controlador de ingresso é uma peça de software que fornece procuração inversa, encaminhamento de tráfego configurável e rescisão de TLS para os serviços kubernetes. Os recursos de ingresso kubernetes são usados para configurar as regras e rotas de ingresso para serviços kubernetes individuais. Utilizando um controlador de ingresso e regras de ingresso, um único endereço IP pode ser usado para direcionar o tráfego para vários serviços em um cluster Kubernetes.

Este artigo mostra-lhe como implantar o [controlador de ingresso NGINX][nginx-ingress] num cluster do Serviço Azure Kubernetes (AKS). Duas aplicações são então executadas no cluster AKS, cada uma das quais é acessível ao longo do endereço IP único.

Também pode:

- [Ativar o add-on de encaminhamento de aplicações HTTP][aks-http-app-routing]
- [Criar um controlador de ingresso que utilize uma rede interna, privada e endereço IP][aks-ingress-internal]
- [Crie um controlador de ingresso que utilize os seus próprios certificados TLS][aks-ingress-own-tls]
- Crie um controlador de ingresso que utilize Let's Encrypt para gerar automaticamente certificados TLS [com um endereço IP público dinâmico][aks-ingress-tls] ou com um endereço IP público [estático][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Antes de começar

Este artigo utiliza o [Helm 3][helm] para instalar o controlador de entrada NGINX.

Este artigo também requer que esteja a executar a versão Azure CLI 2.0.64 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Criar um controlador de ingresso

Para criar o controlador de ingresso, utilize o Helm para instalar *a nginx-ingress.* Para uma maior redundância, são implementadas duas réplicas dos controladores de entrada do NGINX com o parâmetro `--set controller.replicaCount`. Para se beneficiar totalmente da execução de réplicas do controlador de ingresso, certifique-se de que há mais de um nó no seu cluster AKS.

O controlador de entrada também tem de estar agendado num nó do Linux. Os nós do Windows Server não devem executar o controlador de entrada. É especificado um seletor de nós com o parâmetro `--set nodeSelector` para indicar ao agendador do Kubernetes que execute o controlador de entrada do NGINX num nó baseado no Linux.

> [!TIP]
> O exemplo seguinte cria um espaço de nome Kubernetes para os recursos de ingresso *denominados ingress-basic*. Especifique um espaço de nome para o seu próprio ambiente, conforme necessário.

> [!TIP]
> Se quiser ativar a [preservação ip][client-source-ip] de origem do cliente `--set controller.service.externalTrafficPolicy=Local` para pedidos de contentores no seu cluster, adicione ao comando de instalação helm. O IP de origem do cliente é armazenado no cabeçalho de pedido sob *X-Forwarded-For*. Ao utilizar um controlador de ingresso com a preservação IP de origem do cliente ativada, a passagem do SSL não funcionará.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the official stable repository
helm repo add stable https://kubernetes-charts.storage.googleapis.com/

# Use Helm to deploy an NGINX ingress controller
helm install nginx-ingress stable/nginx-ingress \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Quando o serviço de equilíbrio de carga Kubernetes é criado para o controlador de entrada NGINX, é atribuído um endereço IP público dinâmico, como mostra a seguinte saída exemplo:

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                             TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)                      AGE
nginx-ingress-controller         LoadBalancer   10.0.61.144    EXTERNAL_IP   80:30386/TCP,443:32276/TCP   6m2s
nginx-ingress-default-backend    ClusterIP      10.0.192.145   <none>        80/TCP                       6m2s
```

Ainda não foram criadas regras de ingresso, pelo que a página padrão 404 do controlador de ingresso NGINX é exibida se navegar para o endereço IP interno. As regras de ingresso estão configuradas nos seguintes passos.

## <a name="run-demo-applications"></a>Executar aplicações de demonstração

Para ver o controlador de ingresso em ação, execute duas aplicações de demonstração no seu cluster AKS. Neste exemplo, você `kubectl apply` usa para implementar duas instâncias de uma aplicação simples *do mundo Hello.*

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
  name: aks-helloworld-two
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld-two
```

Executar as duas aplicações `kubectl apply`de demonstração usando:

```console
kubectl apply -f aks-helloworld-one.yaml --namespace ingress-basic
kubectl apply -f aks-helloworld-two.yaml --namespace ingress-basic
```

## <a name="create-an-ingress-route"></a>Criar uma rota de ingresso

Ambas as aplicações estão agora a funcionar no seu cluster Kubernetes. Para encaminhar o tráfego para cada aplicação, crie um recurso de entrada Kubernetes. O recurso de ingresso configura as regras que encaminham o tráfego para uma das duas aplicações.

No exemplo seguinte, o tráfego para *EXTERNAL_IP* `aks-helloworld-one`é encaminhado para o serviço denominado . O tráfego para *EXTERNAL_IP/olá-mundo-dois* `aks-helloworld-two` é encaminhado para o serviço. O tráfego para *EXTERNAL_IP/estática* é `aks-helloworld-one` encaminhado para o serviço nomeado para ativos estáticos.

Crie um `hello-world-ingress.yaml` ficheiro nomeado e copie no seguinte exemplo YAML.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    nginx.ingress.kubernetes.io/rewrite-target: /$2
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: aks-helloworld-one
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

Crie o recurso `kubectl apply -f hello-world-ingress.yaml` de entrada utilizando o comando.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
ingress.extensions/hello-world-ingress-static created
```

## <a name="test-the-ingress-controller"></a>Teste o controlador de ingresso

Para testar as rotas para o controlador de ingresso, navegue para as duas aplicações. Abra um navegador web para o endereço IP do seu controlador de entrada NGINX, como *EXTERNAL_IP*. A primeira aplicação de demonstração é exibida no navegador web, como mostra o exemplo seguinte:

![Primeira aplicação a correr atrás do controlador de ingresso](media/ingress-basic/app-one.png)

Adicione agora o caminho */hello-world-two* para o endereço IP, como *EXTERNAL_IP/hello-world-two*. A segunda aplicação de demonstração com o título personalizado é exibida:

![Segunda aplicação a correr atrás do controlador de ingresso](media/ingress-basic/app-two.png)

## <a name="clean-up-resources"></a>Limpar recursos

Este artigo usou o Helm para instalar os componentes de ingresso e aplicações de amostragem. Quando se implanta um gráfico Helm, são criados vários recursos kubernetes. Estes recursos incluem cápsulas, implantações e serviços. Para limpar estes recursos, pode eliminar todo o espaço de nome da amostra ou os recursos individuais.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Eliminar o espaço de nome da amostra e todos os recursos

Para eliminar todo o espaço `kubectl delete` de nome da amostra, utilize o comando e especifique o nome do seu espaço de nome. Todos os recursos no espaço de nome são eliminados.

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>Eliminar recursos individualmente

Em alternativa, uma abordagem mais granular é eliminar os recursos individuais criados. Lista o Helm liberta `helm list` com o comando. Procure gráficos *chamados nginx-ingress* e *aks-helloworld,* como mostra a seguinte saída exemplo:

```
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
nginx-ingress           ingress-basic   1               2020-01-06 19:55:46.358275 -0600 CST    deployed        nginx-ingress-1.27.1    0.26.1  
```

Desinstale as `helm uninstall` versões com o comando. O exemplo seguinte desinstala a implementação de ingresso NGINX.

```
$ helm uninstall nginx-ingress --namespace ingress-basic

release "nginx-ingress" uninstalled
```

Em seguida, remova as duas aplicações da amostra:

```console
kubectl delete -f aks-helloworld-one.yaml --namespace ingress-basic
kubectl delete -f aks-helloworld-two.yaml --namespace ingress-basic
```

Remova a rota de ingresso que direcionou o tráfego para as aplicações da amostra:

```console
kubectl delete -f hello-world-ingress.yaml
```

Finalmente, pode eliminar o espaço de nome si mesmo. Utilize `kubectl delete` o comando e especifique o nome do seu espaço de nome:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Passos seguintes

Este artigo incluía alguns componentes externos para a AKS. Para saber mais sobre estes componentes, consulte as seguintes páginas do projeto:

- [Helm CLI][helm-cli]
- [Controlador de ingresso NGINX][nginx-ingress]

Também pode:

- [Ativar o add-on de encaminhamento de aplicações HTTP][aks-http-app-routing]
- [Criar um controlador de ingresso que utilize uma rede interna, privada e endereço IP][aks-ingress-internal]
- [Crie um controlador de ingresso que utilize os seus próprios certificados TLS][aks-ingress-own-tls]
- Crie um controlador de ingresso que utilize Let's Encrypt para gerar automaticamente certificados TLS [com um endereço IP público dinâmico][aks-ingress-tls] ou com um endereço IP público [estático][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
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
