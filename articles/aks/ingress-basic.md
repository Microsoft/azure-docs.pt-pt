---
title: Criar um controlador de ingresso
titleSuffix: Azure Kubernetes Service
description: Aprenda a instalar e configurar um controlador de ingresso NGINX básico num cluster do Serviço Azure Kubernetes (AKS).
services: container-service
ms.topic: article
ms.date: 12/20/2019
ms.openlocfilehash: f6e07dde68f2f2ce0ccfbb7858fd1d217b993a62
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82101489"
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

Este artigo utiliza o [Helm 3][helm] para instalar o controlador de entrada NGINX e uma aplicação web de amostra.

Este artigo também requer que esteja a executar a versão Azure CLI 2.0.64 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Criar um controlador de ingresso

Para criar o controlador de ingresso, utilize o Helm para instalar *a nginx-ingress.* Para uma maior redundância, são implementadas duas réplicas dos controladores de entrada do NGINX com o parâmetro `--set controller.replicaCount`. Para se beneficiar totalmente da execução de réplicas do controlador de ingresso, certifique-se de que há mais de um nó no seu cluster AKS.

O controlador de entrada também tem de estar agendado num nó do Linux. Os nós do Windows Server (atualmente em pré-visualização no AKS) não devem executar o controlador de entrada. É especificado um seletor de nós com o parâmetro `--set nodeSelector` para indicar ao agendador do Kubernetes que execute o controlador de entrada do NGINX num nó baseado no Linux.

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

Para ver o controlador de ingresso em ação, vamos executar duas aplicações de demonstração no seu cluster AKS. Neste exemplo, Helm é usado para implementar dois casos de uma aplicação simples *do mundo Hello.*

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
helm install aks-helloworld-two azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="aks-helloworld-two"
```

## <a name="create-an-ingress-route"></a>Criar uma rota de ingresso

Ambas as aplicações estão agora a funcionar no seu cluster Kubernetes. Para encaminhar o tráfego para cada aplicação, crie um recurso de entrada Kubernetes. O recurso de ingresso configura as regras que encaminham o tráfego para uma das duas aplicações.

No exemplo seguinte, o tráfego para *EXTERNAL_IP* `aks-helloworld`é encaminhado para o serviço denominado . O tráfego para *EXTERNAL_IP/olá-mundo-dois* `aks-helloworld-two` é encaminhado para o serviço. O tráfego para *EXTERNAL_IP/estática* é `aks-helloworld` encaminhado para o serviço nomeado para ativos estáticos.

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
          serviceName: aks-helloworld
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

Em seguida, remova o repo Helm para a app aks olá mundo:

```console
helm repo remove azure-samples
```

### <a name="delete-resources-individually"></a>Eliminar recursos individualmente

Em alternativa, uma abordagem mais granular é eliminar os recursos individuais criados. Lista o Helm liberta `helm list` com o comando. Procure gráficos *chamados nginx-ingress* e *aks-helloworld,* como mostra a seguinte saída exemplo:

```
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
aks-helloworld          ingress-basic   1               2020-01-06 19:57:00.131576 -0600 CST    deployed        aks-helloworld-0.1.0               
aks-helloworld-two      ingress-basic   1               2020-01-06 19:57:10.971365 -0600 CST    deployed        aks-helloworld-0.1.0               
nginx-ingress           ingress-basic   1               2020-01-06 19:55:46.358275 -0600 CST    deployed        nginx-ingress-1.27.1    0.26.1  
```

Elimine os lançamentos com o `helm delete` comando. O exemplo seguinte elimina a implementação de ingresso NGINX, e as duas aplicações aks olá world.

```
$ helm delete aks-helloworld aks-helloworld-two nginx-ingress --namespace ingress-basic

release "aks-helloworld" uninstalled
release "aks-helloworld-two" uninstalled
release "nginx-ingress" uninstalled
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
