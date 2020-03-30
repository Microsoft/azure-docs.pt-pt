---
title: Interagir com os contentores do Windows
services: azure-dev-spaces
ms.date: 01/16/2020
ms.topic: conceptual
description: Saiba como executar espaços Azure Dev num aglomerado existente com contentores Windows
keywords: Espaços Azure Dev, Espaços Dev, Docker, Kubernetes, Azure, AKS, Serviço Azure Kubernetes, contentores, contentores, contentores windows
ms.openlocfilehash: 0b3f221c9e62343a02ba8742e4cf988c7cf26c12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240491"
---
# <a name="interact-with-windows-containers-using-azure-dev-spaces"></a>Interaja com recipientes Windows usando espaços Azure Dev

Você pode ativar espaços Azure Dev em espaços de nome kubernetes novos e existentes. A Azure Dev Spaces funcionará e serviços de instrumentos que funcionam em contentores Linux. Esses serviços também podem interagir com aplicações que funcionam em contentores Windows no mesmo espaço de nome. Este artigo mostra-lhe como usar a Dev Spaces para executar serviços num espaço com contentores Windows existentes. Neste momento, não é possível depurar ou prender aos recipientes windows com espaços Azure Dev.

## <a name="set-up-your-cluster"></a>Configurar o seu cluster

Este artigo assume que já tem um cluster com piscinas de nó linux e Windows. Se precisar de criar um cluster com piscinas de nó linux e Windows, pode seguir as instruções [aqui][windows-container-cli].

Ligue-se ao seu cluster usando [kubectl,][kubectl]o cliente da linha de comando Kubernetes. Para configurar `kubectl` para se ligar ao cluster do Kubernetes, utilize o comando [az aks get-credentials][az-aks-get-credentials]. Este comando descarrega credenciais e confunde o ClI Kubernetes para usá-las.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Para verificar a ligação ao cluster, utilize o comando [kubectl get][kubectl-get] para devolver uma lista de nós do cluster.

```azurecli-interactive
kubectl get nodes
```

A saída de exemplo que se segue mostra um cluster com um nó Windows e Linux. Certifique-se de que o estado está *pronto* para cada nó antes de prosseguir.

```console
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmss000000   Ready    agent   13m    v1.14.8
aks-nodepool1-12345678-vmss000001   Ready    agent   13m    v1.14.8
aksnpwin000000                      Ready    agent   108s   v1.14.8
```

Aplique uma [mancha][using-taints] nos seus nódosos windows. A mancha nos seus nódosos Windows impede que a Dev Spaces aperte os recipientes Linux para funcionar nos seus nódos windows. O seguinte comando de exemplo de comando aplica uma mancha ao nó do Windows *aksnpwin987654* do exemplo anterior.

```azurecli-interactive
kubectl taint node aksnpwin987654 sku=win-node:NoSchedule
```

> [!IMPORTANT]
> Quando aplica uma mancha a um nó, tem de configurar uma tolerância correspondente no modelo de implementação do seu serviço para executar o seu serviço nesse nó. A aplicação da amostra já está configurada com uma [tolerância correspondente][sample-application-toleration-example] à mancha configurada no comando anterior.

## <a name="run-your-windows-service"></a>Executar o seu serviço Windows

Execute o seu serviço Windows no seu cluster AKS e verifique se está em estado de *Execução.* Este artigo utiliza uma [aplicação de amostra][sample-application] para demonstrar um serviço Windows e Linux em execução no seu cluster.

Clone a aplicação da amostra do `dev-spaces/samples/existingWindowsBackend/mywebapi-windows` GitHub e navegue no diretório:

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/existingWindowsBackend/mywebapi-windows
```

A aplicação da amostra utiliza o [Helm 3][helm-installed] para executar o serviço Windows no seu cluster. Navegue `charts` para o diretório e utilize o Helm executar o serviço Windows:

```console
cd charts/
kubectl create ns dev
helm install windows-service . --namespace dev
```

O comando acima utiliza o Helm para executar o seu serviço Windows no espaço de nome de *v.* Se não tiver um espaço *com*nome sinuoso, será criado.

Utilize `kubectl get pods` o comando para verificar se o seu serviço Windows está a funcionar no seu cluster. 

```console
$ kubectl get pods --namespace dev --watch
NAME                     READY   STATUS              RESTARTS   AGE
myapi-4b9667d123-1a2b3   0/1     ContainerCreating   0          47s
...
myapi-4b9667d123-1a2b3   1/1     Running             0          98s
```

## <a name="enable-azure-dev-spaces"></a>Ativar espaços Azure Dev

Ative dev Spaces no mesmo espaço de nome que usou para executar o seu serviço Windows. O seguinte comando permite espaços Dev no espaço de nome de *v:*

```console
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster --space dev --yes
```

## <a name="update-your-windows-service-for-dev-spaces"></a>Atualize o seu serviço Windows para Espaços Dev

Quando ativar a Dev Spaces num espaço de nome existente com contentores que já estão em funcionamento, por padrão, a Dev Spaces tentará instrumentar quaisquer novos recipientes que funcionam nesse espaço de nome. A Dev Spaces também tentará instrumentar quaisquer novos recipientes criados para o serviço já em funcionamento no espaço de nome. Para evitar que a Dev Spaces ainstrumente um recipiente em funcionamento no seu espaço de nome, adicione o cabeçalho *sem procuração* ao `deployment.yaml`.

Adicione `azds.io/no-proxy: "true"` ao `existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml` ficheiro:

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

Utilizar `helm list` para listar a implementação para o seu serviço Windows:

```cmd
$ helm list --namespace dev
NAME              REVISION  UPDATED                     STATUS      CHART           APP VERSION NAMESPACE
windows-service 1           Wed Jul 24 15:45:59 2019    DEPLOYED    mywebapi-0.1.0  1.0         dev  
```

No exemplo acima, o nome da sua implementação é *serviço windows- service*. Atualize o seu serviço `helm upgrade`Windows com a nova configuração utilizando:

```cmd
helm upgrade windows-service . --namespace dev
```

Uma vez `deployment.yaml`que atualizou o seu , a Dev Spaces não tentará instrumentalmente o seu serviço.

## <a name="run-your-linux-application-with-azure-dev-spaces"></a>Execute a sua aplicação Linux com espaços Azure Dev

Navegue `webfrontend` para o diretório e use os `azds prep` e `azds up` comandos para executar a sua aplicação Linux no seu cluster.

```console
cd ../../webfrontend-linux/
azds prep --enable-ingress
azds up
```

O `azds prep --enable-ingress` comando gera a tabela Helm e os Ficheiros Docker para a sua aplicação.

> [!TIP]
> O [gráfico Dockerfile e Helm](../how-dev-spaces-works-prep.md#prepare-your-code) para o seu projeto é usado pela Azure Dev Spaces para construir e executar o seu código, mas pode modificar estes ficheiros se quiser alterar a forma como o projeto é construído e executado.

O `azds up` comando executa o seu serviço no espaço de nome.

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

Pode ver o serviço a funcionar abrindo o URL público, que é exibido na saída a partir do comando azds up. Neste exemplo, o URL `http://dev.webfrontend.abcdef0123.eus.azds.io/`público é . Navegue para o serviço num browser e clique em *About* at the top. Verifique se vê uma mensagem do serviço *mywebapi* contendo a versão do Windows que o recipiente está a utilizar.

![App de amostra smostra a versão do Windows a partir de mywebapi](../media/run-dev-spaces-windows-containers/sample-app.png)

## <a name="next-steps"></a>Passos seguintes

Saiba como o Azure Dev Spaces o ajuda a desenvolver aplicações mais complexas em vários recipientes e como pode simplificar o desenvolvimento colaborativo trabalhando com diferentes versões ou ramos do seu código em diferentes espaços.

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
