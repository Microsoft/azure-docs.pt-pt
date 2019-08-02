---
title: Criar um controlador de entrada para uma rede interna no serviço de kubernetes do Azure (AKS)
description: Saiba como instalar e configurar um controlador de entrada do NGINX para uma rede interna e privada em um cluster do AKS (serviço kubernetes do Azure).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/24/2019
ms.author: mlearned
ms.openlocfilehash: 935b96bd553c9ae73b55086483baa0ea7c4aeaa4
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "67615473"
---
# <a name="create-an-ingress-controller-to-an-internal-virtual-network-in-azure-kubernetes-service-aks"></a>Criar um controlador de entrada para uma rede virtual interna no serviço de kubernetes do Azure (AKS)

Um controlador de entrada é uma parte do software que fornece proxy reverso, roteamento de tráfego configurável e terminação de TLS para serviços Kubernetess. Os recursos de entrada do kubernetes são usados para configurar as regras de entrada e rotas para serviços individuais do kubernetes. Usando um controlador de entrada e regras de entrada, um único endereço IP pode ser usado para rotear o tráfego para vários serviços em um cluster kubernetes.

Este artigo mostra como implantar o controlador de [entrada Nginx][nginx-ingress] em um cluster do AKS (serviço kubernetes do Azure). O controlador de entrada é configurado em um endereço IP e uma rede virtual privada interna. Nenhum acesso externo é permitido. Dois aplicativos são então executados no cluster AKS, cada um deles acessível por meio de um único endereço IP.

Também pode:

- [Criar um controlador de entrada básico com conectividade de rede externa][aks-ingress-basic]
- [Habilitar o complemento de roteamento de aplicativo HTTP][aks-http-app-routing]
- [Criar um controlador de entrada que usa seus próprios certificados TLS][aks-ingress-own-tls]
- Criar um controlador de entrada que usa vamos criptografar para gerar automaticamente certificados TLS [com um endereço IP público dinâmico][aks-ingress-tls] ou [com um endereço IP público estático][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Antes de começar

Este artigo usa o Helm para instalar o controlador de entrada do NGINX, o CERT-Manager e um aplicativo Web de exemplo. Você precisa ter o Helm inicializado em seu cluster AKS e usar uma conta de serviço para o gaveta. Para obter mais informações sobre como configurar e usar o Helm, consulte [instalar aplicativos com Helm no serviço kubernetes do Azure (AKs)][use-helm].

Este artigo também requer que você esteja executando o CLI do Azure versão 2.0.64 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Criar um controlador de entrada

Por padrão, um controlador de entrada NGINX é criado com uma atribuição de endereço IP público dinâmico. Um requisito de configuração comum é usar uma rede interna e privada e um endereço IP. Essa abordagem permite restringir o acesso aos seus serviços a usuários internos, sem acesso externo.

Crie um arquivo chamado *Internal-ingress. YAML* usando o seguinte arquivo de manifesto de exemplo. Este exemplo atribui *10.240.0.42* ao recurso *loadBalancerIP* . Forneça seu próprio endereço IP interno para uso com o controlador de entrada. Certifique-se de que esse endereço IP ainda não esteja em uso em sua rede virtual.

```yaml
controller:
  service:
    loadBalancerIP: 10.240.0.42
    annotations:
      service.beta.kubernetes.io/azure-load-balancer-internal: "true"
```

Agora, implante o gráfico *Nginx-ingress* com Helm. Para usar o arquivo de manifesto criado na etapa anterior, adicione o `-f internal-ingress.yaml` parâmetro. Para redundância adicional, duas réplicas dos controladores de entrada do Nginx são implantadas com `--set controller.replicaCount` o parâmetro. Para se beneficiar totalmente da execução de réplicas do controlador de entrada, verifique se há mais de um nó no cluster AKS.

O controlador de entrada também precisa ser agendado em um nó do Linux. Os nós do Windows Server (atualmente em visualização no AKS) não devem executar o controlador de entrada. Um seletor de nó é especificado `--set nodeSelector` usando o parâmetro para instruir o Agendador kubernetes a executar o controlador de entrada Nginx em um nó baseado em Linux.

> [!TIP]
> O exemplo a seguir cria um namespace kubernetes para os recursos de entrada chamados *ingress-Basic*. Especifique um namespace para seu próprio ambiente, conforme necessário. Se o cluster AKs não estiver habilitado para RBAC, `--set rbac.create=false` adicione aos comandos Helm.

> [!TIP]
> Se você quiser habilitar a [preservação de IP de origem do cliente][client-source-ip] para solicitações a contêineres em seu `--set controller.service.externalTrafficPolicy=Local` cluster, adicione ao comando Helm install. O IP de origem do cliente é armazenado no cabeçalho da solicitação em *X-forwardd-for*. Ao usar um controlador de entrada com preservação de IP de origem do cliente habilitada, a passagem SSL não funcionará.

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

Quando o serviço de balanceador de carga do kubernetes é criado para o controlador de entrada do NGINX, seu endereço IP interno é atribuído, conforme mostrado na seguinte saída de exemplo:

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                              TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
alternating-coral-nginx-ingress-controller        LoadBalancer   10.0.97.109   10.240.0.42   80:31507/TCP,443:30707/TCP   1m
alternating-coral-nginx-ingress-default-backend   ClusterIP      10.0.134.66   <none>        80/TCP                       1m
```

Nenhuma regra de entrada foi criada ainda, portanto, a página padrão 404 do controlador de entrada do NGINX será exibida se você navegar até o endereço IP interno. As regras de entrada são configuradas nas etapas a seguir.

## <a name="run-demo-applications"></a>Executar aplicativos de demonstração

Para ver o controlador de entrada em ação, vamos executar dois aplicativos de demonstração em seu cluster AKS. Neste exemplo, Helm é usado para implantar duas instâncias de um aplicativo "Hello World" simples.

Antes de instalar os gráficos de exemplo do Helm, adicione o repositório de exemplos do Azure ao seu ambiente Helm da seguinte maneira:

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Crie o primeiro aplicativo de demonstração de um gráfico do Helm com o seguinte comando:

```console
helm install azure-samples/aks-helloworld --namespace ingress-basic
```

Agora, instale uma segunda instância do aplicativo de demonstração. Para a segunda instância, você especifica um novo título para que os dois aplicativos sejam visualmente distintos. Você também especifica um nome de serviço exclusivo:

```console
helm install azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>Criar uma rota de entrada

Os dois aplicativos agora estão em execução no cluster kubernetes. Para rotear o tráfego para cada aplicativo, crie um recurso de entrada do kubernetes. O recurso de entrada configura as regras que roteiam o tráfego para um dos dois aplicativos.

No exemplo a seguir, o tráfego para o `http://10.240.0.42/` endereço é roteado para o `aks-helloworld`serviço chamado. O tráfego para o `http://10.240.0.42/hello-world-two` endereço é roteado `ingress-demo` para o serviço.

Crie um arquivo chamado `hello-world-ingress.yaml` e copie no exemplo a seguir YAML.

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

Crie o recurso de entrada usando o `kubectl apply -f hello-world-ingress.yaml` comando.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-controller"></a>Testar o controlador de entrada

Para testar as rotas para o controlador de entrada, navegue até os dois aplicativos com um cliente Web. Se necessário, você pode testar rapidamente essa funcionalidade somente interno de um pod no cluster AKS. Crie um pod de teste e anexe uma sessão de terminal a ele:

```console
kubectl run -it --rm aks-ingress-test --image=debian --namespace ingress-basic
```

Instale `curl` no pod usando `apt-get`:

```console
apt-get update && apt-get install -y curl
```

Agora acesse o endereço do controlador de entrada do kubernetes `curl`usando, *http://10.240.0.42* como. Forneça seu próprio endereço IP interno especificado quando você implantou o controlador de entrada na primeira etapa deste artigo.

```console
curl -L http://10.240.0.42
```

Nenhum caminho adicional foi fornecido com o endereço, portanto, o controlador de entrada usa como padrão */* a rota. O primeiro aplicativo de demonstração é retornado, conforme mostrado na seguinte saída de exemplo condensada:

```
$ curl -L 10.240.0.42

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Welcome to Azure Kubernetes Service (AKS)</title>
[...]
```

Agora, adicione o caminho */Hello-World-Two* ao endereço, como *http://10.240.0.42/hello-world-two* . O segundo aplicativo de demonstração com o título personalizado é retornado, conforme mostrado na seguinte saída de exemplo condensada:

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

Este artigo usou o Helm para instalar os componentes de entrada e aplicativos de exemplo. Quando você implanta um gráfico do Helm, vários recursos do kubernetes são criados. Esses recursos incluem pods, implantações e serviços. Para limpar esses recursos, você pode excluir o namespace de exemplo inteiro ou os recursos individuais.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Excluir o namespace de exemplo e todos os recursos

Para excluir o namespace de exemplo inteiro, use `kubectl delete` o comando e especifique o nome do namespace. Todos os recursos no namespace são excluídos.

```console
kubectl delete namespace ingress-basic
```

Em seguida, remova o repositório Helm para o aplicativo Hello World do AKS:

```console
helm repo remove azure-samples
```

### <a name="delete-resources-individually"></a>Excluir recursos individualmente

Como alternativa, uma abordagem mais granular é excluir os recursos individuais criados. Liste as versões Helm com `helm list` o comando. Procure gráficos chamados *Nginx-ingress* e *AKs-HelloWorld*, conforme mostrado na seguinte saída de exemplo:

```
$ helm list

NAME                REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
kissing-ferret      1           Tue Oct 16 17:13:39 2018    DEPLOYED    nginx-ingress-0.22.1    0.15.0      kube-system
intended-lemur      1           Tue Oct 16 17:20:59 2018    DEPLOYED    aks-helloworld-0.1.0                default
pioneering-wombat   1           Tue Oct 16 17:21:05 2018    DEPLOYED    aks-helloworld-0.1.0                default
```

Exclua as versões com `helm delete` o comando. O exemplo a seguir exclui a implantação de entrada do NGINX e os dois aplicativos de exemplo do AKS Hello World.

```
$ helm delete kissing-ferret intended-lemur pioneering-wombat

release "kissing-ferret" deleted
release "intended-lemur" deleted
release "pioneering-wombat" deleted
```

Em seguida, remova o repositório Helm para o aplicativo Hello World do AKS:

```console
helm repo remove azure-samples
```

Remova a rota de entrada que direcionou o tráfego para os aplicativos de exemplo:

```console
kubectl delete -f hello-world-ingress.yaml
```

Por fim, você pode excluir o namespace em si. Use o `kubectl delete` comando e especifique o nome do namespace:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Passos Seguintes

Este artigo incluía alguns componentes externos no AKS. Para saber mais sobre esses componentes, consulte as seguintes páginas do projeto:

- [CLI do Helm][helm-cli]
- [Controlador de entrada do NGINX][nginx-ingress]

Também pode:

- [Criar um controlador de entrada básico com conectividade de rede externa][aks-ingress-basic]
- [Habilitar o complemento de roteamento de aplicativo HTTP][aks-http-app-routing]
- [Criar um controlador de entrada com um IP público dinâmico e configurar vamos criptografar para gerar automaticamente certificados TLS][aks-ingress-tls]
- [Criar um controlador de entrada com um endereço IP público estático e configurar vamos criptografar para gerar automaticamente certificados TLS][aks-ingress-static-tls]

<!-- LINKS - external -->
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