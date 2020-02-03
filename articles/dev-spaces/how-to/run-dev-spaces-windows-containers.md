---
title: Interagir com os contentores do Windows
services: azure-dev-spaces
ms.date: 01/16/2020
ms.topic: conceptual
description: Saiba como executar Azure Dev Spaces em um cluster existente com contêineres do Windows
keywords: Azure Dev Spaces, espaços de desenvolvimento, Docker, kubernetes, Azure, AKS, serviço kubernetes do Azure, contêineres, contêineres do Windows
ms.openlocfilehash: 882cdaa8a7b01578b2f04cf939aa14fe4aee7e2e
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720372"
---
# <a name="interact-with-windows-containers-using-azure-dev-spaces"></a>Interagir com contêineres do Windows usando Azure Dev Spaces

Você pode habilitar Azure Dev Spaces em namespaces kubernetes novos e existentes. Azure Dev Spaces será executado e instrumentar serviços que são executados em contêineres do Linux. Esses serviços também podem interagir com aplicativos executados em contêineres do Windows no mesmo namespace. Este artigo mostra como usar espaços de desenvolvimento para executar serviços em um namespace com contêineres do Windows existentes. Neste momento, você não pode depurar ou anexar a contêineres do Windows com Azure Dev Spaces.

## <a name="set-up-your-cluster"></a>Configurar o cluster

Este artigo pressupõe que você já tem um cluster com pools de nós do Linux e do Windows. Se precisar de criar um cluster com piscinas de nó linux e Windows, pode seguir as instruções [aqui][windows-container-cli].

Ligue-se ao seu cluster usando [kubectl,][kubectl]o cliente da linha de comando Kubernetes. Para configurar `kubectl` para se ligar ao seu cluster Kubernetes, use o comando [az aks get-credentials.][az-aks-get-credentials] Esse comando baixa as credenciais e configura a CLI do kubernetes para usá-las.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Para verificar a ligação ao cluster, utilize o comando [kubectl get][kubectl-get] para devolver uma lista de nós do cluster.

```azurecli-interactive
kubectl get nodes
```

A saída de exemplo a seguir mostra um cluster com um nó do Windows e do Linux. Certifique-se de que o estado está *pronto* para cada nó antes de prosseguir.

```console
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmss000000   Ready    agent   13m    v1.14.8
aks-nodepool1-12345678-vmss000001   Ready    agent   13m    v1.14.8
aksnpwin000000                      Ready    agent   108s   v1.14.8
```

Aplique uma [mancha][using-taints] nos seus nódosos windows. Os seus nós do Windows impedem espaços de desenvolvimento do agendamento de contêineres do Linux para serem executados em seus nós do Windows. O seguinte comando de exemplo de comando aplica uma mancha ao nó do Windows *aksnpwin987654* do exemplo anterior.

```azurecli-interactive
kubectl taint node aksnpwin987654 sku=win-node:NoSchedule
```

> [!IMPORTANT]
> Ao aplicar um salve a um nó, você deve configurar um toleration correspondente no modelo de implantação do serviço para executar o serviço nesse nó. A aplicação da amostra já está configurada com uma [tolerância correspondente][sample-application-toleration-example] à mancha configurada no comando anterior.

## <a name="run-your-windows-service"></a>Executar o serviço do Windows

Execute o seu serviço Windows no seu cluster AKS e verifique se está em estado de *Execução.* Este artigo utiliza uma [aplicação de amostra][sample-application] para demonstrar um serviço Windows e Linux em execução no seu cluster.

Clone a aplicação da amostra do GitHub e navegue no diretório `dev-spaces/samples/existingWindowsBackend/mywebapi-windows`:

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/existingWindowsBackend/mywebapi-windows
```

A aplicação da amostra utiliza o [Helm 3][helm-installed] para executar o serviço Windows no seu cluster. Navegue para o diretório `charts` e use helm executar o serviço Windows:

```console
cd charts/
kubectl create ns dev
helm install windows-service . --namespace dev
```

O comando acima utiliza o Helm para executar o seu serviço Windows no espaço de nome de *v.* Se não tiver um espaço *com*nome sinuoso, será criado.

Utilize o comando `kubectl get pods` para verificar se o seu serviço Windows está a funcionar no seu cluster. 

```console
$ kubectl get pods --namespace dev --watch
NAME                     READY   STATUS              RESTARTS   AGE
myapi-4b9667d123-1a2b3   0/1     ContainerCreating   0          47s
...
myapi-4b9667d123-1a2b3   1/1     Running             0          98s
```

## <a name="enable-azure-dev-spaces"></a>Habilitar Azure Dev Spaces

Habilite espaços de desenvolvimento no mesmo namespace usado para executar o serviço do Windows. O seguinte comando permite espaços Dev no espaço de nome de *v:*

```console
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster --space dev --yes
```

## <a name="update-your-windows-service-for-dev-spaces"></a>Atualizar seu serviço do Windows para espaços de desenvolvimento

Quando você habilita espaços de desenvolvimento em um namespace existente com contêineres que já estão em execução, por padrão, os espaços de desenvolvimento tentarão criar e instrumentar novos contêineres que são executados nesse namespace. Os espaços de desenvolvimento também tentarão criar e instrumentar novos contêineres criados para o serviço já em execução no namespace. Para evitar que a Dev Spaces ainstrumente um recipiente em funcionamento no seu espaço de nome, adicione o cabeçalho *sem procuração* ao `deployment.yaml`.

Adicione `azds.io/no-proxy: "true"` ao ficheiro `existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  ...
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    ...
  template:
    metadata:
      labels:
        app.kubernetes.io/name: {{ include "mywebapi.name" . }}
        app.kubernetes.io/instance: {{ .Release.Name }}
        azds.io/no-proxy: "true"
```

Utilize `helm list` para listar a implementação para o seu serviço Windows:

```cmd
$ helm list --namespace dev
NAME              REVISION  UPDATED                     STATUS      CHART           APP VERSION NAMESPACE
windows-service 1           Wed Jul 24 15:45:59 2019    DEPLOYED    mywebapi-0.1.0  1.0         dev  
```

No exemplo acima, o nome da sua implementação é *serviço windows- service*. Atualize o seu serviço Windows com a nova configuração utilizando `helm upgrade`:

```cmd
helm upgrade windows-service . --namespace dev
```

Uma vez que atualizou o seu `deployment.yaml`, a Dev Spaces não tentará instrumentalmente o seu serviço.

## <a name="run-your-linux-application-with-azure-dev-spaces"></a>Execute seu aplicativo Linux com o Azure Dev Spaces

Navegue para o diretório `webfrontend` e use os comandos `azds prep` e `azds up` para executar a sua aplicação Linux no seu cluster.

```console
cd ../../webfrontend-linux/
azds prep --public
azds up
```

O comando `azds prep --public` gera o gráfico Helm e os Ficheiros Docker para a sua aplicação.

> [!TIP]
> O [gráfico Dockerfile e Helm](../how-dev-spaces-works.md#prepare-your-code) para o seu projeto é usado pela Azure Dev Spaces para construir e executar o seu código, mas pode modificar estes ficheiros se quiser alterar a forma como o projeto é construído e executado.

O comando `azds up` executa o seu serviço no espaço de nome.

```console
$ azds up
Using dev space 'dev' with target 'myAKSCluster'
Synchronizing files...4s
Installing Helm chart...11s
Waiting for container image build...6s
Building container image...
Step 1/12 : FROM mcr.microsoft.com/dotnet/core/sdk:2.2
...
Step 12/12 : ENTRYPOINT ["/bin/bash", "/entrypoint.sh"]
Built container image in 36s
Waiting for container...2s
Service 'webfrontend' port 'http' is available at http://dev.webfrontend.abcdef0123.eus.azds.io/
Service 'webfrontend' port 80 (http) is available via port forwarding at http://localhost:57648
```

Você pode ver o serviço em execução abrindo a URL pública, que é exibida na saída do comando azds up. Neste exemplo, o URL público é `http://dev.webfrontend.abcdef0123.eus.azds.io/`. Navegue para o serviço num browser e clique em *About* at the top. Verifique se vê uma mensagem do serviço *mywebapi* contendo a versão do Windows que o recipiente está a utilizar.

![Aplicativo de exemplo mostrando a versão do Windows do mywebapi](../media/run-dev-spaces-windows-containers/sample-app.png)

## <a name="next-steps"></a>Passos Seguintes

Saiba como Azure Dev Spaces ajuda a desenvolver aplicativos mais complexos em vários contêineres e como você pode simplificar o desenvolvimento colaborativo trabalhando com diferentes versões ou branches do seu código em espaços diferentes.

> [!div class="nextstepaction"]
> [Desenvolvimento de equipa em Espaços Azure Dev][team-development-qs]

[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-installed]: https://helm.sh/docs/intro/install/
[sample-application]: https://github.com/Azure/dev-spaces/tree/master/samples/existingWindowsBackend
[sample-application-toleration-example]: https://github.com/Azure/dev-spaces/blob/master/samples/existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml#L24-L27
[team-development-qs]: ../quickstart-team-development.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[team-development]: ../team-development-netcore.md
[using-taints]: ../../aks/use-multiple-node-pools.md#schedule-pods-using-taints-and-tolerations
[windows-container-cli]: ../../aks/windows-container-cli.md
