---
title: Instalar aplicações existentes com Helm em AKS
description: Saiba como utilizar a ferramenta de embalagem Helm para implantar contentores num cluster Azure Kubernetes Service (AKS)
services: container-service
author: zr-msft
ms.topic: article
ms.date: 12/07/2020
ms.author: zarhoads
ms.openlocfilehash: f12dffe0b538738a8f6dd00cd3d87d44da828f21
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96779172"
---
# <a name="install-existing-applications-with-helm-in-azure-kubernetes-service-aks"></a>Instalar aplicações existentes com Helm in Azure Kubernetes Service (AKS)

[Helm][helm] é uma ferramenta de embalagem de código aberto que o ajuda a instalar e gerir o ciclo de vida das aplicações Kubernetes. Semelhante aos gestores de pacotes Linux, como *APT* e *Yum,* o Helm é usado para gerir gráficos kubernetes, que são pacotes de recursos kubernetes pré-configurados.

Este artigo mostra-lhe como configurar e usar Helm num cluster Kubernetes em AKS.

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que você tem um cluster AKS existente. Se precisar de um cluster AKS, consulte o quickstart AKS [utilizando o Azure CLI][aks-quickstart-cli] ou [utilizando o portal Azure][aks-quickstart-portal].

Também precisa do Helm CLI instalado, que é o cliente que funciona no seu sistema de desenvolvimento. Permite-lhe iniciar, parar e gerir aplicações com o Helm. Se utilizar a Azure Cloud Shell, o Helm CLI já está instalado. Para obter instruções de instalação na sua plataforma local, consulte [a instalação do leme][helm-install].

> [!IMPORTANT]
> O leme destina-se a correr em nós Linux. Se tiver nós do Windows Server no seu cluster, deve certificar-se de que as cápsulas Helm estão programadas apenas para funcionar nos nós Linux. Também precisa de garantir que quaisquer gráficos helm que instale também estão programados para executar nos nós corretos. Os comandos deste artigo usam [seletores de nó][k8s-node-selector] para garantir que as cápsulas estão programadas para os nós corretos, mas nem todos os gráficos de Helm podem expor um seletor de nó. Também pode considerar a utilização de outras opções no seu cluster, como [manchas.][taints]

## <a name="verify-your-version-of-helm"></a>Verifique a sua versão do Helm

Utilize o `helm version` comando para verificar se tem o Helm 3 instalado:

```console
helm version
```

O exemplo a seguir mostra a versão Helm 3.0.0 instalada:

```console
$ helm version

version.BuildInfo{Version:"v3.0.0", GitCommit:"e29ce2a54e96cd02ccfce88bee4f58bb6e2a28b6", GitTreeState:"clean", GoVersion:"go1.13.4"}
```

## <a name="install-an-application-with-helm-v3"></a>Instale uma aplicação com Helm v3

### <a name="add-helm-repositories"></a>Adicionar repositórios helm

Use o comando [de repo de leme][helm-repo-add] para adicionar o *repositório ingress-nginx.*

```console
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
```

### <a name="find-helm-charts"></a>Encontre gráficos de Helm

Os gráficos de leme são usados para implantar aplicações num cluster Kubernetes. Para procurar gráficos Helm pré-criados, utilize o comando [de pesquisa de leme:][helm-search]

```console
helm search repo ingress-nginx
```

A seguinte saída de exemplo condensado mostra algumas das tabelas Helm disponíveis para utilização:

```console
$ helm search repo ingress-nginx

NAME                            CHART VERSION   APP VERSION     DESCRIPTION                                       
ingress-nginx/ingress-nginx     2.12.0          0.34.1          Ingress controller for Kubernetes using NGINX a...
```

Para atualizar a lista de gráficos, utilize o comando [de atualização do leme.][helm-repo-update]

```console
helm repo update
```

O exemplo a seguir mostra uma atualização de repouso bem sucedida:

```console
$ helm repo update

Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "ingress-nginx" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

### <a name="run-helm-charts"></a>Executar gráficos de leme

Para instalar gráficos com o Helm, utilize o comando [de instalação do leme][helm-install-command] e especifique um nome de desbloqueio e o nome do gráfico para instalar. Para ver a instalação de um gráfico Helm em ação, vamos instalar uma implementação nginx básica usando um gráfico Helm.

```console
helm install my-nginx-ingress ingress-nginx/ingress-nginx \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

A seguinte produção de exemplo condensado mostra o estado de implantação dos recursos Kubernetes criados pelo gráfico Helm:

```console
$ helm install my-nginx-ingress ingress-nginx/ingress-nginx \
>     --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
>     --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux

NAME: my-nginx-ingress
LAST DEPLOYED: Fri Nov 22 10:08:06 2019
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
The nginx-ingress controller has been installed.
It may take a few minutes for the LoadBalancer IP to be available.
You can watch the status by running 'kubectl --namespace default get services -o wide -w my-nginx-ingress-ingress-nginx-controller'
...
```

Utilize o `kubectl get services` comando para obter o IP *EXTERNO* do seu serviço.

```console
kubectl --namespace default get services -o wide -w my-nginx-ingress-ingress-nginx-controller
```

Por exemplo, o comando abaixo mostra o *IP EXTERNO* para o serviço *my-nginx-ingress-ingress-nginx-controller:*

```console
$ kubectl --namespace default get services -o wide -w my-nginx-ingress-ingress-nginx-controller

NAME                                        TYPE           CLUSTER-IP   EXTERNAL-IP      PORT(S)                      AGE   SELECTOR
my-nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.2.237   <EXTERNAL-IP>    80:31380/TCP,443:32239/TCP   72s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=my-nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

### <a name="list-releases"></a>Lançamentos de listas

Para ver uma lista de versões instaladas no seu cluster, utilize o `helm list` comando.

```console
helm list
```

O exemplo a seguir mostra a versão *my-nginx-ingress* implementada no passo anterior:

```console
$ helm list

NAME                NAMESPACE   REVISION    UPDATED                                 STATUS      CHART                   APP VERSION
my-nginx-ingress    default     1           2019-11-22 10:08:06.048477 -0600 CST    deployed    nginx-ingress-1.25.0    0.26.1 
```

### <a name="clean-up-resources"></a>Limpar os recursos

Quando se implementa um gráfico Helm, são criados vários recursos kubernetes. Estes recursos incluem cápsulas, implantações e serviços. Para limpar estes recursos, utilize o comando [de desinstalar][helm-cleanup] o leme e especifique o seu nome de libertação, tal como se encontra no `helm list` comando anterior.

```console
helm uninstall my-nginx-ingress
```

O exemplo a seguir mostra que o lançamento chamado *my-nginx-ingress* foi desinstalado:

```console
$ helm uninstall my-nginx-ingress

release "my-nginx-ingress" uninstalled
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a gestão das implementações da aplicação da Kubernetes com o Helm, consulte a documentação helm.

> [!div class="nextstepaction"]
> [Documentação do Helm][helm-documentation]

<!-- LINKS - external -->
[helm]: https://github.com/kubernetes/helm/
[helm-cleanup]: https://helm.sh/docs/intro/using_helm/#helm-uninstall-uninstalling-a-release
[helm-documentation]: https://helm.sh/docs/
[helm-install]: https://helm.sh/docs/intro/install/
[helm-install-command]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[helm-repo-add]: https://helm.sh/docs/intro/quickstart/#initialize-a-helm-chart-repository
[helm-search]: https://helm.sh/docs/intro/using_helm/#helm-search-finding-charts
[helm-repo-update]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
            
<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[taints]: operator-best-practices-advanced-scheduler.md
