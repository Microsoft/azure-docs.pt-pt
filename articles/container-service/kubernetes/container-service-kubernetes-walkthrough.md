---
title: (DEPRECIADO) Quickstart - Cluster Azure Kubernetes para Linux
description: Aprenda rapidamente a criar um cluster do Kubernetes para contentores do Linux no Azure Container Service com a CLI do Azure.
author: iainfoulds
ms.service: container-service
ms.topic: quickstart
ms.date: 02/26/2018
ms.author: iainfou
ms.custom: H1Hack27Feb2017, mvc, devcenter
ms.openlocfilehash: cfd0f8a9a3180b14b4da9dc61e252054fe06628c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "78274179"
---
# <a name="deprecated-deploy-kubernetes-cluster-for-linux-containers"></a>(DEPRECIADO) Implementar o cluster Kubernetes para contentores Linux

> [!TIP]
> Para a versão atualizada este quickstart que utiliza o Serviço Azure Kubernetes, consulte [Quickstart: Implemente um cluster azure Kubernetes Service (AKS).](../../aks/kubernetes-walkthrough.md)

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

Neste arranque rápido, um cluster Kubernetes é implantado usando o Azure CLI. Depois, é implementada e executada no cluster uma aplicação de vários contentores que consiste num front-end da Web e numa instância de Redis. Depois de concluída, a aplicação está acessível através da Internet. 

A aplicação de exemplo utilizada neste documento está escrita em Python. Os conceitos e os passos detalhados aqui podem ser utilizados para implementar qualquer imagem de contentor num cluster de Kubernetes. O código, o Dockerfile e os ficheiros de manifesto do Kubernetes pré-criados relacionados com este projeto estão disponíveis no [GitHub](https://github.com/Azure-Samples/azure-voting-app-redis.git).

![Imagem de navegação para o Azure Vote](media/container-service-kubernetes-walkthrough/azure-vote.png)

Este guia de introdução pressupõe um conhecimento básico dos conceitos do Kubernetes. Para obter informações detalhadas sobre o Kubernetes, consulte a [documentação do Kubernetes]( https://kubernetes.io/docs/home/).

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e usar a CLI localmente, este tópico requer a execução da versão 2.0.4 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az-group-create). Um grupo de recursos do Azure é um grupo lógico, no qual os recursos do Azure são implementados e geridos. 

O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *westeurope*.

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

Saída:

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup",
  "location": "westeurope",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-kubernetes-cluster"></a>Criar cluster do Kubernetes

Crie um cluster do Kubernetes no Azure Container Service com o comando [az acs create](/cli/azure/acs#az-acs-create). O exemplo seguinte cria um cluster com o nome *myK8sCluster* com um nó principal do Linux e três nós de agente do Linux.

```azurecli-interactive
az acs create --orchestrator-type kubernetes --resource-group myResourceGroup --name myK8sCluster --generate-ssh-keys
```

Em alguns casos, como numa versão de avaliação limitada, uma subscrição do Azure tem acesso limitado aos recursos do Azure. Caso a implementação falhe devido a um número limitado de núcleos disponíveis, reduza a contagem de agentes pré-definida ao adicionar `--agent-count 1` ao comando [az acs create](/cli/azure/acs#az-acs-create). 

Ao fim de vários minutos, o comando é concluído e devolve informações sobre o cluster no formato json. 

## <a name="connect-to-the-cluster"></a>Ligar ao cluster

Para gerir um cluster de Kubernetes, utilize [kubectl](https://kubernetes.io/docs/user-guide/kubectl/), o cliente de linha de comandos do Kubernetes. 

Se estiver a utilizar o Azure CloudShell, o kubectl já está instalado. Se pretender instalá-lo localmente, pode utilizar o comando [az acs kubernetes install-cli](/cli/azure/acs/kubernetes).

Para configurar kubectl para se ligar ao seu cluster do Kubernetes, execute o comando [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes). Este passo transfere credenciais e configura a CLI do Kubernetes para as utilizar.

```azurecli-interactive
az acs kubernetes get-credentials --resource-group=myResourceGroup --name=myK8sCluster
```

Para verificar a ligação ao cluster, utilize o comando [kubectl get](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) para devolver uma lista de nós do cluster.

```console
kubectl get nodes
```

Saída:

```output
NAME                    STATUS                     AGE       VERSION
k8s-agent-14ad53a1-0    Ready                      10m       v1.6.6
k8s-agent-14ad53a1-1    Ready                      10m       v1.6.6
k8s-agent-14ad53a1-2    Ready                      10m       v1.6.6
k8s-master-14ad53a1-0   Ready,SchedulingDisabled   10m       v1.6.6
```

## <a name="run-the-application"></a>Executar a aplicação

Um ficheiro de manifesto do Kubernetes define um estado pretendido para o cluster, incluindo que imagens de contentor devem estar em execução. Neste exemplo, é utilizado um manifesto para criar todos os objetos necessários para executar a aplicação Azure Vote. 

Crie um ficheiro com o nome `azure-vote.yml` e copie-o para o YAML seguinte. Se estiver a trabalhar no Azure Cloud Shell, este ficheiro pode ser criado através de vi ou Nano, como se estivesse a trabalhar num sistema físico ou virtual.

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      containers:
      - name: azure-vote-back
        image: redis
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      containers:
      - name: azure-vote-front
        image: microsoft/azure-vote-front:v1
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

Utilize o comando [kubectl create](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create) para executar a aplicação.

```console
kubectl create -f azure-vote.yml
```

Saída:

```output
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>Testar a aplicação

À medida que a aplicação é executada, é criado um [serviço do Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) que expõe o front-end da aplicação na Internet. Este processo pode demorar alguns minutos a concluir. 

Para monitorizar o progresso, utilize o comando [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) com o argumento `--watch`.

```console
kubectl get service azure-vote-front --watch
```

Inicialmente, o **EXTERNAL-IP** do serviço *azure-vote-front* aparece como *pendente*. Quando o endereço EXTERNAL-IP mudar de *pendente* para *Endereço IP*, utilize `CTRL-C` para parar o processo de observação do kubectl. 
  
```output
azure-vote-front   10.0.34.242   <pending>     80:30676/TCP   7s
azure-vote-front   10.0.34.242   52.179.23.131   80:30676/TCP   2m
```

Agora, pode navegar para o endereço IP externo para ver a aplicação Azure Vote.

![Imagem de navegação para o Azure Vote](media/container-service-kubernetes-walkthrough/azure-vote.png)  

## <a name="delete-cluster"></a>Eliminar o cluster
Quando o cluster já não for necessário, pode utilizar o comando [az group delete](/cli/azure/group#az-group-delete) para remover o grupo de recursos, o serviço de contentores e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="get-the-code"></a>Obter o código

Neste guia de introdução, foram utilizadas imagens de contentores pré-criadas para criar uma implementação de Kubernetes. O código da aplicação relacionado, o Dockerfile, e o ficheiro de manifesto do Kubernetes, estão disponíveis no GitHub.

[https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis.git)

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, implementou um cluster do Kubernetes e implementou uma aplicação de vários contentores no mesmo. 

Para saber mais sobre o Azure Container Service e ver um exemplo completo de código para implementação, avance para o tutorial dos clusters de Kubernetes.

> [!div class="nextstepaction"]
> [Gerir um cluster do ACS Kubernetes](./container-service-tutorial-kubernetes-prepare-app.md)
