---
title: Controlador de ingresso na rede interna
titleSuffix: Azure Kubernetes Service
description: Aprenda a instalar e configurar um controlador de ingresso NGINX para uma rede interna e privada num cluster do Serviço Azure Kubernetes (AKS).
services: container-service
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: d6533434fc053427ae26a28879af18aed73f40aa
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82145430"
---
# <a name="create-an-ingress-controller-to-an-internal-virtual-network-in-azure-kubernetes-service-aks"></a>Criar um controlador de ingresso para uma rede virtual interna no Serviço Azure Kubernetes (AKS)

Um controlador de ingresso é uma peça de software que fornece procuração inversa, encaminhamento de tráfego configurável e rescisão de TLS para os serviços kubernetes. Os recursos de ingresso kubernetes são usados para configurar as regras e rotas de ingresso para serviços kubernetes individuais. Utilizando um controlador de ingresso e regras de ingresso, um único endereço IP pode ser usado para direcionar o tráfego para vários serviços em um cluster Kubernetes.

Este artigo mostra-lhe como implantar o [controlador de ingresso NGINX][nginx-ingress] num cluster do Serviço Azure Kubernetes (AKS). O controlador de ingresso está configurado numa rede virtual interna e privada e endereço IP. Não é permitido acesso externo. Duas aplicações são então executadas no cluster AKS, cada uma das quais é acessível ao longo do endereço IP único.

Também pode:

- [Criar um controlador básico de ingresso com conectividade de rede externa][aks-ingress-basic]
- [Ativar o add-on de encaminhamento de aplicações HTTP][aks-http-app-routing]
- [Crie um controlador de ingresso que utilize os seus próprios certificados TLS][aks-ingress-own-tls]
- Crie um controlador de ingresso que utilize Let's Encrypt para gerar automaticamente certificados TLS [com um endereço IP público dinâmico][aks-ingress-tls] ou com um endereço IP público [estático][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Antes de começar

Este artigo utiliza o [Helm 3][helm] para instalar o controlador de entrada NGINX, o cert-manager e uma aplicação web de amostra. Precisa de ter o Helm inicializado dentro do seu cluster AKS e usar uma conta de serviço para a Tiller. Para obter mais informações sobre configurar e utilizar o Helm, consulte [Instalar aplicações com o Helm no Serviço Azure Kubernetes (AKS)][use-helm].

Este artigo também requer que esteja a executar a versão Azure CLI 2.0.64 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Criar um controlador de ingresso

Por padrão, um controlador de ingresso NGINX é criado com uma atribuição dinâmica de endereços IP públicos. Um requisito comum de configuração é utilizar uma rede interna, privada e endereço IP. Esta abordagem permite-lhe restringir o acesso aos seus serviços a utilizadores internos, sem acesso externo.

Crie um ficheiro chamado *internal-ingress.yaml* utilizando o seguinte ficheiro manifesto exemplo. Este exemplo atribui *10.240.0.42* ao recurso *loadBalancerIP.* Forneça o seu próprio endereço IP interno para utilização com o controlador de entrada. Certifique-se de que este endereço IP ainda não está a ser utilizado dentro da sua rede virtual.

```yaml
controller:
  service:
    loadBalancerIP: 10.240.0.42
    annotations:
      service.beta.kubernetes.io/azure-load-balancer-internal: "true"
```

Agora implemente o gráfico *de nginx-ingresscom* Helm. Para utilizar o ficheiro manifesto criado no `-f internal-ingress.yaml` passo anterior, adicione o parâmetro. Para uma maior redundância, são implementadas duas réplicas dos controladores de entrada do NGINX com o parâmetro `--set controller.replicaCount`. Para se beneficiar totalmente da execução de réplicas do controlador de ingresso, certifique-se de que há mais de um nó no seu cluster AKS.

O controlador de entrada também tem de estar agendado num nó do Linux. Os nós do Windows Server (atualmente em pré-visualização no AKS) não devem executar o controlador de entrada. É especificado um seletor de nós com o parâmetro `--set nodeSelector` para indicar ao agendador do Kubernetes que execute o controlador de entrada do NGINX num nó baseado no Linux.

> [!TIP]
> O exemplo seguinte cria um espaço de nome Kubernetes para os recursos de ingresso *denominados ingress-basic*. Especifique um espaço de nome para o seu próprio ambiente, conforme necessário. Se o seu cluster AKS não `--set rbac.create=false` estiver ativado por RBAC, adicione aos comandos Helm.

> [!TIP]
> Se quiser ativar a [preservação ip][client-source-ip] de origem do cliente `--set controller.service.externalTrafficPolicy=Local` para pedidos de contentores no seu cluster, adicione ao comando de instalação helm. O IP de origem do cliente é armazenado no cabeçalho de pedido sob *X-Forwarded-For*. Ao utilizar um controlador de ingresso com a preservação IP de origem do cliente ativada, a passagem do TLS não funcionará.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Use Helm to deploy an NGINX ingress controller
helm install stable/nginx-ingress \
    --namespace ingress-basic \
    -f internal-ingress.yaml \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Quando o serviço de equilíbrio de carga Kubernetes é criado para o controlador de entrada NGINX, o seu endereço IP interno é atribuído, como mostra a seguinte saída exemplo:

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                              TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
alternating-coral-nginx-ingress-controller        LoadBalancer   10.0.97.109   10.240.0.42   80:31507/TCP,443:30707/TCP   1m
alternating-coral-nginx-ingress-default-backend   ClusterIP      10.0.134.66   <none>        80/TCP                       1m
```

Ainda não foram criadas regras de ingresso, pelo que a página padrão 404 do controlador de ingresso NGINX é exibida se navegar para o endereço IP interno. As regras de ingresso estão configuradas nos seguintes passos.

## <a name="run-demo-applications"></a>Executar aplicações de demonstração

Para ver o controlador de ingresso em ação, vamos executar duas aplicações de demonstração no seu cluster AKS. Neste exemplo, helm é usado para implementar dois casos de uma simples aplicação "Olá mundo".

Antes de poder instalar as tabelas Helm da amostra, adicione o repositório de amostras Azure ao seu ambiente Helm da seguinte forma:

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Crie a primeira aplicação de demonstração a partir de um gráfico Helm com o seguinte comando:

```console
helm install azure-samples/aks-helloworld --namespace ingress-basic
```

Agora instale uma segunda instância da aplicação de demonstração. Em segundo lugar, especifice um novo título para que as duas aplicações sejam visualmente distintas. Também especifica um nome de serviço único:

```console
helm install azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>Criar uma rota de ingresso

Ambas as aplicações estão agora a funcionar no seu cluster Kubernetes. Para encaminhar o tráfego para cada aplicação, crie um recurso de entrada Kubernetes. O recurso de ingresso configura as regras que encaminham o tráfego para uma das duas aplicações.

No exemplo seguinte, o `http://10.240.0.42/` tráfego para o endereço `aks-helloworld`é encaminhado para o serviço denominado . O tráfego `http://10.240.0.42/hello-world-two` para o endereço `ingress-demo` é encaminhado para o serviço.

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
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  rules:
  - http:
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

Crie o recurso `kubectl apply -f hello-world-ingress.yaml` de entrada utilizando o comando.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-controller"></a>Teste o controlador de ingresso

Para testar as rotas para o controlador de ingresso, navegue para as duas aplicações com um cliente web. Se necessário, pode testar rapidamente esta funcionalidade interna a partir de uma cápsula no cluster AKS. Crie uma cápsula de ensaio e fixe-lhe uma sessão terminal:

```console
kubectl run -it --rm aks-ingress-test --image=debian --namespace ingress-basic
```

Instale `curl` na `apt-get`cápsula utilizando:

```console
apt-get update && apt-get install -y curl
```

Aceda agora ao endereço do seu `curl`controlador de *http://10.240.0.42*ingresso Kubernetes utilizando , como . Forneça o seu próprio endereço IP interno especificado quando implementou o controlador de ingresso no primeiro passo deste artigo.

```console
curl -L http://10.240.0.42
```

Não foi fornecido nenhum caminho adicional com o endereço, */* pelo que o controlador de entrada falha na rota. A primeira aplicação de demonstração é devolvida, como mostra a seguinte saída de exemplo condensado:

```
$ curl -L 10.240.0.42

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Welcome to Azure Kubernetes Service (AKS)</title>
[...]
```

Agora adicione */olá-mundo-dois* caminho para *http://10.240.0.42/hello-world-two*o endereço, tais como . A segunda aplicação de demonstração com o título personalizado é devolvida, como mostra a seguinte saída de exemplo condensado:

```
$ curl -L -k http://10.240.0.42/hello-world-two

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>AKS Ingress Demo</title>
[...]
```

## <a name="clean-up-resources"></a>Limpar recursos

Este artigo usou o Helm para instalar os componentes de ingresso e aplicações de amostragem. Quando se implanta um gráfico Helm, são criados vários recursos kubernetes. Estes recursos incluem cápsulas, implantações e serviços. Para limpar estes recursos, pode eliminar todo o espaço de nome da amostra ou os recursos individuais.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Eliminar o espaço de nome da amostra e todos os recursos

Para eliminar todo o espaço `kubectl delete` de nome da amostra, utilize o comando e especifique o nome do seu espaço de nome. Todos os recursos no espaço de nome são eliminados.

```console
kubectl delete namespace ingress-basic
```

Em seguida, remova o repo Helm para a app aks olá mundo:

```console
helm repo remove azure-samples
```

### <a name="delete-resources-individually"></a>Eliminar recursos individualmente

Em alternativa, uma abordagem mais granular é eliminar os recursos individuais criados. Lista o Helm liberta `helm list` com o comando. Procure gráficos *chamados nginx-ingress* e *aks-helloworld,* como mostra a seguinte saída exemplo:

```
$ helm list

NAME                REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
kissing-ferret      1           Tue Oct 16 17:13:39 2018    DEPLOYED    nginx-ingress-0.22.1    0.15.0      kube-system
intended-lemur      1           Tue Oct 16 17:20:59 2018    DEPLOYED    aks-helloworld-0.1.0                default
pioneering-wombat   1           Tue Oct 16 17:21:05 2018    DEPLOYED    aks-helloworld-0.1.0                default
```

Elimine os lançamentos com o `helm delete` comando. O exemplo seguinte elimina a implementação de ingresso NGINX, e as duas aplicações aks olá world.

```
$ helm delete kissing-ferret intended-lemur pioneering-wombat

release "kissing-ferret" deleted
release "intended-lemur" deleted
release "pioneering-wombat" deleted
```

Em seguida, remova o repo Helm para a app aks olá world:

```console
helm repo remove azure-samples
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

- [Criar um controlador básico de ingresso com conectividade de rede externa][aks-ingress-basic]
- [Ativar o add-on de encaminhamento de aplicações HTTP][aks-http-app-routing]
- [Crie um controlador de ingresso com um IP público dinâmico e configure Vamos encriptar para gerar automaticamente certificados TLS][aks-ingress-tls]
- [Crie um controlador de ingresso com um endereço IP público estático e configure Vamos encriptar para gerar automaticamente certificados TLS][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
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