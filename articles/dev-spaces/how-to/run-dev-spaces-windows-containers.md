---
title: Interagir com os contentores do Windows
services: azure-dev-spaces
ms.date: 01/16/2020
ms.topic: conceptual
description: Saiba como executar espaços Azure Dev em um cluster existente com recipientes Windows
keywords: Espaços Azure Dev, Espaços Dev, Docker, Kubernetes, Azure, AKS, Serviço Azure Kubernetes, contentores, contentores Windows
ms.openlocfilehash: bbef5eafe44e38691327714c14c6a6026d45a3c7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777442"
---
# <a name="interact-with-windows-containers-using-azure-dev-spaces"></a>Interaja com recipientes Windows usando espaços Azure Dev

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

Você pode ativar Azure Dev Spaces em espaços de nomes novos e existentes kubernetes. A Azure Dev Spaces funcionará e serviços de instrumentos que funcionam em contentores Linux. Esses serviços também podem interagir com aplicações que funcionam em recipientes Windows no mesmo espaço de nome. Este artigo mostra-lhe como usar dev Spaces para executar serviços num espaço de nome com recipientes Windows existentes. Neste momento, não é possível depurar ou anexar aos recipientes do Windows com espaços Azure Dev.

## <a name="set-up-your-cluster"></a>Configurar o seu cluster

Este artigo pressupõe que já tem um cluster com piscinas de nó linux e Windows. Se precisar de criar um cluster com piscinas de nó linux e windows, pode seguir as instruções [aqui][windows-container-cli].

Ligue-se ao seu cluster utilizando [kubectl,][kubectl]o cliente da linha de comando Kubernetes. Para configurar `kubectl` para se ligar ao cluster do Kubernetes, utilize o comando [az aks get-credentials][az-aks-get-credentials]. Este comando descarrega credenciais e configura o CLI de Kubernetes para usá-las.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Para verificar a ligação ao cluster, utilize o comando [kubectl get][kubectl-get] para devolver uma lista de nós do cluster.

```azurecli-interactive
kubectl get nodes
```

A saída de exemplo a seguir mostra um cluster com um nó Windows e Linux. Certifique-se de que o estado está *pronto* para cada nó antes de prosseguir.

```console
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmss000000   Ready    agent   13m    v1.14.8
aks-nodepool1-12345678-vmss000001   Ready    agent   13m    v1.14.8
aksnpwin000000                      Ready    agent   108s   v1.14.8
```

Aplique uma [mancha][using-taints] nos nós do Windows. A mancha nos nós do Windows impede que os Espaços Dev marquem contentores Linux para funcionarem nos nós do Windows. O seguinte comando de exemplo de comando aplica uma mancha no nó do Windows *aksnpwin987654* do exemplo anterior.

```azurecli-interactive
kubectl taint node aksnpwin987654 sku=win-node:NoSchedule
```

> [!IMPORTANT]
> Quando aplicar uma mancha num nó, deve configurar uma tolerância correspondente no modelo de implantação do seu serviço para executar o seu serviço nesse nó. A aplicação da amostra já está configurada com uma [tolerância correspondente][sample-application-toleration-example] à mancha configurada no comando anterior.

## <a name="run-your-windows-service"></a>Executar o seu serviço Windows

Execute o seu serviço Windows no seu cluster AKS e verifique se está em *funcionamento.* Este artigo utiliza uma [aplicação de amostra][sample-application] para demonstrar um serviço Windows e Linux em execução no seu cluster.

Clone a aplicação da amostra do GitHub e navegue no `dev-spaces/samples/existingWindowsBackend/mywebapi-windows` diretório:

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/existingWindowsBackend/mywebapi-windows
```

A aplicação da amostra utiliza [o Helm 3][helm-installed] para executar o serviço Windows no seu cluster. Navegue para o `charts` diretório e use Helm executar o serviço Windows:

```console
cd charts/
kubectl create ns dev
helm install windows-service . --namespace dev
```

O comando acima utiliza o Helm para executar o seu serviço Windows no *espaço de nomes dev.* Se não tiver um espaço com nome chamado *dev,* será criado.

Utilize o `kubectl get pods` comando para verificar se o seu serviço Windows está a funcionar no seu cluster. 

```console
$ kubectl get pods --namespace dev --watch
NAME                     READY   STATUS              RESTARTS   AGE
myapi-4b9667d123-1a2b3   0/1     ContainerCreating   0          47s
...
myapi-4b9667d123-1a2b3   1/1     Running             0          98s
```

## <a name="enable-azure-dev-spaces"></a>Ativar espaços Azure Dev

Ative os Espaços Dev no mesmo espaço de identificação que usou para executar o seu serviço Windows. O seguinte comando permite espaços Dev no espaço de nome *dev:*

```console
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster --space dev --yes
```

## <a name="update-your-windows-service-for-dev-spaces"></a>Atualize o seu serviço Windows para Espaços Dev

Quando ativar os Espaços Dev num espaço de nome existente com recipientes que já estão em execução, por padrão, a Dev Spaces tentará instrumentar quaisquer novos contentores que corram nesse espaço de nome. A Dev Spaces também tentará instrumentá-lo para quaisquer novos contentores criados para o serviço que já estão a funcionar no espaço de nomes. Para evitar que o Dev Spaces instrumento um recipiente que funciona no seu espaço de nome, adicione o cabeçalho *sem procuração* ao `deployment.yaml` .

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

Utilize `helm list` para listar a implementação do seu serviço Windows:

```cmd
$ helm list --namespace dev
NAME             REVISION   UPDATED                    STATUS    CHART            APP VERSION    NAMESPACE
windows-service    1        Wed Jul 24 15:45:59 2019   DEPLOYED  mywebapi-0.1.0   1.0            dev
```

No exemplo acima, o nome da sua implantação é *serviço de janelas.* Atualize o seu serviço Windows com a nova configuração utilizando `helm upgrade` :

```cmd
helm upgrade windows-service . --namespace dev
```

Uma vez que atualizou o seu `deployment.yaml` , Dev Spaces não tentará instrumentá-lo.

## <a name="run-your-linux-application-with-azure-dev-spaces"></a>Execute a sua aplicação Linux com espaços Azure Dev

Navegue para o `webfrontend` diretório e use os `azds prep` comandos e `azds up` comandos para executar a sua aplicação Linux no seu cluster.

```console
cd ../../webfrontend-linux/
azds prep --enable-ingress
azds up
```

O `azds prep --enable-ingress` comando gera o gráfico Helm e Dockerfiles para a sua aplicação.

> [!TIP]
> O [gráfico Dockerfile e Helm](../how-dev-spaces-works-prep.md#prepare-your-code) para o seu projeto é usado pela Azure Dev Spaces para construir e executar o seu código, mas pode modificar estes ficheiros se quiser alterar a forma como o projeto é construído e executado.

O `azds up` comando executa o seu serviço no espaço de nomes.

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

Pode ver o serviço a funcionar abrindo o URL público, que é exibido na saída a partir do comando azds up. Neste exemplo, a URL pública é `http://dev.webfrontend.abcdef0123.eus.azds.io/` . Navegue para o serviço num browser e clique em *Cerca* no topo. Verifique se vê uma mensagem do serviço *mywebapi* que contém a versão do Windows que o recipiente está a usar.

![App de amostra mostrando versão do Windows do mywebapi](../media/run-dev-spaces-windows-containers/sample-app.png)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o funcionamento da Azure Dev Spaces.

> [!div class="nextstepaction"]
> [Como funciona o Azure Dev Spaces](../how-dev-spaces-works.md)

[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-installed]: https://helm.sh/docs/intro/install/
[sample-application]: https://github.com/Azure/dev-spaces/tree/master/samples/existingWindowsBackend
[sample-application-toleration-example]: https://github.com/Azure/dev-spaces/blob/master/samples/existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml#L24-L27
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[using-taints]: ../../aks/use-multiple-node-pools.md#setting-nodepool-taints
[windows-container-cli]: ../../aks/windows-container-cli.md
