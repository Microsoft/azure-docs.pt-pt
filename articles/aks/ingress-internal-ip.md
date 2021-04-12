---
title: Controlador ingress na rede interna
titleSuffix: Azure Kubernetes Service
description: Aprenda a instalar e configurar um controlador de entrada NGINX para uma rede interna privada num cluster Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 03/16/2021
ms.openlocfilehash: 3201f510db9970b7db548ee6a3348fa68d278248
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104601470"
---
# <a name="create-an-ingress-controller-to-an-internal-virtual-network-in-azure-kubernetes-service-aks"></a>Criar um controlador de entrada para uma rede virtual interna no Serviço Azure Kubernetes (AKS)

Um controlador de entrada é uma peça de software que fornece procuração inversa, encaminhamento de tráfego configurável e rescisão de TLS para serviços Kubernetes. Os recursos de entrada de Kubernetes são usados para configurar as regras e rotas de entrada para serviços individuais de Kubernetes. Utilizando um controlador de entrada e regras de entrada, um único endereço IP pode ser usado para encaminhar o tráfego para vários serviços num cluster kubernetes.

Este artigo mostra-lhe como implantar o [controlador de entrada NGINX][nginx-ingress] num cluster Azure Kubernetes Service (AKS). O controlador de entrada está configurado numa rede virtual interna e privada e endereço IP. Não é permitido acesso externo. Duas aplicações são então executadas no cluster AKS, cada uma das quais está acessível sobre o único endereço IP.

Também pode:

- [Criar um controlador básico de entrada com conectividade de rede externa][aks-ingress-basic]
- [Ativar o addon de encaminhamento de aplicações HTTP][aks-http-app-routing]
- [Crie um controlador de entrada que use os seus próprios certificados TLS][aks-ingress-own-tls]
- Crie um controlador ingress que utilize o Let's Encrypt para gerar automaticamente certificados TLS [com um endereço IP público dinâmico][aks-ingress-tls] ou com um endereço IP público [estático][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Antes de começar

Este artigo utiliza [o Helm 3][helm] para instalar o controlador de entrada NGINX. Certifique-se de que está a utilizar a mais recente versão do Helm e tenha acesso ao repositório de Helm *ingresss-nginx.* Para obter mais informações sobre a configuração e utilização do Helm, consulte [instalar aplicações com Helm in Azure Kubernetes Service (AKS)][use-helm].

Este artigo também requer que esteja a executar a versão Azure CLI 2.0.64 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Criar um controlador de entrada

Por predefinição, um controlador de entrada NGINX é criado com uma atribuição dinâmica de endereço IP público. Um requisito comum de configuração é usar uma rede interna, privada e endereço IP. Esta abordagem permite restringir o acesso aos seus serviços a utilizadores internos, sem acesso externo.

Crie um ficheiro chamado *internal-ingress.yaml* utilizando o seguinte ficheiro manifesto exemplo. Este exemplo atribui *10.240.0.42* ao recurso *loadBalancerIP.* Forneça o seu próprio endereço IP interno para utilização com o controlador de entrada. Certifique-se de que este endereço IP ainda não está a ser utilizado na sua rede virtual. Além disso, se estiver a utilizar uma rede virtual e uma sub-rede existentes, tem de configurar o seu cluster AKS com as permissões corretas para gerir a rede virtual e a sub-rede. Consulte [a rede kubenet com as suas próprias gamas de endereços IP no Serviço Azure Kubernetes (AKS)][aks-configure-kubenet-networking] ou [na rede Configure Azure CNI no Serviço Azure Kubernetes (AKS)][aks-configure-advanced-networking] para obter mais informações.

```yaml
controller:
  service:
    loadBalancerIP: 10.240.0.42
    annotations:
      service.beta.kubernetes.io/azure-load-balancer-internal: "true"
```

Agora, implante a tabela *de entrada de nginx* com Helm. Para utilizar o ficheiro manifesto criado no passo anterior, adicione o `-f internal-ingress.yaml` parâmetro. Para uma maior redundância, são implementadas duas réplicas dos controladores de entrada do NGINX com o parâmetro `--set controller.replicaCount`. Para beneficiar totalmente da execução de réplicas do controlador de entrada, certifique-se de que há mais de um nó no seu cluster AKS.

O controlador de entrada também tem de estar agendado num nó do Linux. Os nós do Windows Server não devem executar o controlador de entrada. É especificado um seletor de nós com o parâmetro `--set nodeSelector` para indicar ao agendador do Kubernetes que execute o controlador de entrada do NGINX num nó baseado no Linux.

> [!TIP]
> O exemplo a seguir cria um espaço de nome Kubernetes para os recursos *ingressos denominados ingress-basic*. Especifique um espaço de nome para o seu próprio ambiente, conforme necessário. Se o seu cluster AKS não estiver ativado por Kubernetes RBAC, adicione `--set rbac.create=false` aos comandos Helm.

> [!TIP]
> Se pretender permitir a [preservação ip da fonte do cliente][client-source-ip] para pedidos a contentores no seu cluster, adicione ao comando de `--set controller.service.externalTrafficPolicy=Local` instalação Helm. A FONTE DO CLIENTE IP é armazenada no cabeçalho de pedido sob *X-Forwarded-For*. Ao utilizar um controlador de entrada com a preservação IP de fonte do cliente ativada, o passe do TLS não funcionará.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the ingress-nginx repository
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx

# Use Helm to deploy an NGINX ingress controller
helm install nginx-ingress ingress-nginx/ingress-nginx \
    --namespace ingress-basic \
    -f internal-ingress.yaml \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.admissionWebhooks.patch.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Quando o serviço de balançador de carga Kubernetes é criado para o controlador de entrada NGINX, o seu endereço IP interno é atribuído. Para obter o endereço IP público, use o `kubectl get service` comando.

```console
kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller
```

Demora alguns minutos para que o endereço IP seja atribuído ao serviço, como mostra a seguinte saída de exemplo:

```
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

Ainda não foram criadas regras de entrada, pelo que a página padrão 404 do controlador NGINX é apresentada se navegar para o endereço IP interno. As regras de ingress são configuradas nos seguintes passos.

## <a name="run-demo-applications"></a>Executar aplicações de demonstração

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

Ambas as aplicações estão agora a ser em execução no seu cluster Kubernetes. Para encaminhar o tráfego para cada aplicação, crie um recurso de entrada kubernetes. O recurso ingressa configura as regras que encaminham o tráfego para uma das duas aplicações.

No exemplo seguinte, o tráfego para o endereço `http://10.240.0.42/` é encaminhado para o serviço denominado `aks-helloworld` . O tráfego para o endereço `http://10.240.0.42/hello-world-two` é encaminhado para o `ingress-demo` serviço.

Crie um ficheiro nomeado `hello-world-ingress.yaml` e copie no seguinte exemplo YAML.

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

Crie o recurso de entrada utilizando o `kubectl apply -f hello-world-ingress.yaml` comando.

```console
kubectl apply -f hello-world-ingress.yaml
```

A saída de exemplo a seguir mostra que o recurso de entrada é criado.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-controller"></a>Teste o controlador de entrada

Para testar as rotas do controlador de entrada, consulte as duas aplicações com um cliente web. Se necessário, pode testar rapidamente esta funcionalidade apenas interna a partir de uma cápsula no cluster AKS. Crie uma cápsula de teste e anexe-lhe uma sessão terminal:

```console
kubectl run -it --rm aks-ingress-test --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 --namespace ingress-basic
```

Instale `curl` na cápsula `apt-get` utilizando:

```console
apt-get update && apt-get install -y curl
```

Agora aceda ao endereço do seu controlador de entrada Kubernetes `curl` utilizando, por exemplo, *http://10.240.0.42* . Forneça o seu próprio endereço IP interno especificado quando implementou o controlador de entrada no primeiro passo deste artigo.

```console
curl -L http://10.240.0.42
```

Não foi fornecido nenhum caminho adicional com o endereço, pelo que o controlador de entrada não tem padrão para a */* rota. A primeira aplicação de demonstração é devolvida, como mostra a seguinte saída de exemplo condensado:

```
$ curl -L http://10.240.0.42

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Welcome to Azure Kubernetes Service (AKS)</title>
[...]
```

Adicione *agora /olá-mundo-dois* caminho ao endereço, como *http://10.240.0.42/hello-world-two* . A segunda aplicação de demonstração com o título personalizado é devolvida, como mostra a seguinte saída de exemplo condensado:

```
$ curl -L -k http://10.240.0.42/hello-world-two

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>AKS Ingress Demo</title>
[...]
```

## <a name="clean-up-resources"></a>Limpar os recursos

Este artigo usou o Helm para instalar os componentes de entrada. Quando se implementa um gráfico Helm, são criados vários recursos kubernetes. Estes recursos incluem cápsulas, implantações e serviços. Para limpar estes recursos, pode eliminar todo o espaço de nome da amostra ou os recursos individuais.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Eliminar o espaço de nome da amostra e todos os recursos

Para eliminar todo o espaço de nome da amostra, use o `kubectl delete` comando e especifique o nome do seu nome de espaço. Todos os recursos no espaço de nomes são eliminados.

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>Eliminar recursos individualmente

Em alternativa, uma abordagem mais granular consiste em eliminar os recursos individuais criados. Lista os lançamentos do Leme com o `helm list` comando. 

```console
helm list --namespace ingress-basic
```

Procure gráficos *nomeados nginx-ingress* e *aks-helloworld,* como mostrado na saída do exemplo seguinte:

```
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
nginx-ingress           ingress-basic   1               2020-01-06 19:55:46.358275 -0600 CST    deployed        nginx-ingress-1.27.1    0.26.1  
```

Desinstale os desbloqueios com o `helm uninstall` comando.

```console
helm uninstall nginx-ingress --namespace ingress-basic
```

O exemplo a seguir desinstala a implantação de entrada NGINX.

```
$ helm uninstall nginx-ingress --namespace ingress-basic

release "nginx-ingress" uninstalled
```

Em seguida, remova as duas aplicações da amostra:

```console
kubectl delete -f aks-helloworld.yaml --namespace ingress-basic
kubectl delete -f ingress-demo.yaml --namespace ingress-basic
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

- [Criar um controlador básico de entrada com conectividade de rede externa][aks-ingress-basic]
- [Ativar o addon de encaminhamento de aplicações HTTP][aks-http-app-routing]
- [Criar um controlador de entrada com um IP público dinâmico e configurar Let's Encrypt para gerar automaticamente certificados TLS][aks-ingress-tls]
- [Crie um controlador de entrada com um endereço IP público estático e configuure Let's Encrypt para gerar automaticamente certificados TLS][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-cli]: ./kubernetes-helm.md
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[client-source-ip]: concepts-network.md#ingress-controllers
[aks-configure-kubenet-networking]: configure-kubenet.md
[aks-configure-advanced-networking]: configure-azure-cni.md