---
title: Início rápido - criar um cluster do Azure Kubernetes Service (AKS)
description: Saiba como criar um cluster de Kubernetes rapidamente, implementar uma aplicação e monitorizar o desempenho no Azure Kubernetes Service (AKS) com a CLI do Azure.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: quickstart
ms.date: 12/18/2018
ms.author: iainfou
ms.custom: H1Hack27Feb2017, mvc, devcenter
ms.openlocfilehash: 2c613a42278fb4064e5c1cdbff5ebd3b4000092c
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57444842"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-using-the-azure-cli"></a>Início rápido: Implementar um cluster de Azure Kubernetes Service (AKS) com a CLI do Azure

O Azure Kubernetes Service (AKS) é um serviço de Kubernetes gerido que permite-lhe implementar e gerir clusters rapidamente. Neste início rápido, vai implementar um cluster do AKS com a CLI do Azure. Uma aplicação de vários contentores que inclui um front-end da web e numa instância de Redis é executada no cluster. Em seguida, veja como monitorizar o estado de funcionamento do cluster e pods que execute a sua aplicação.

![Imagem de navegação para o Azure Vote](media/container-service-kubernetes-walkthrough/azure-vote.png)

Este guia de introdução parte do princípio de que possui conhecimentos básicos dos conceitos do Kubernetes. Para obter mais informações, consulte [Kubernetes principais conceitos para o Azure Kubernetes Service (AKS)][kubernetes-concepts].

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este início rápido requer a execução da versão 2.0.52 da CLI do Azure ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli-install].

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um grupo lógico, no qual os recursos do Azure são implementados e geridos. Quando cria um grupo de recursos, é-lhe pedido que especifique uma localização. Esta localização é onde os metadados de grupo de recursos são armazenados, também é onde executar a seus recursos no Azure se não especificar outra região durante a criação de recursos. Criar um grupo de recursos utilizando o [criar grupo az] [ az-group-create] comando.

O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

O resultado de exemplo seguinte mostra o grupo de recursos criado com êxito:

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-aks-cluster"></a>Criar um cluster do AKS

Utilize o comando [az aks create][az-aks-create] para criar um cluster AKS. O exemplo seguinte cria um cluster com o nome *myAKSCluster* com um nó. O Azure Monitor para contentores também é ativado com o parâmetro *--enable-addons monitoring*.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --enable-addons monitoring \
    --generate-ssh-keys
```

Após alguns minutos, o comando é concluído e devolve o formato JSON informações sobre o cluster.

## <a name="connect-to-the-cluster"></a>Ligar ao cluster

Para gerir um cluster de Kubernetes, utilize [kubectl][kubectl], o cliente de linha de comandos do Kubernetes. Se utilizar o Azure Cloud Shell, `kubectl` já está instalado. Para instalar `kubectl` localmente, utilize o [az aks install-cli] [ az-aks-install-cli] comando:

```azurecli
az aks install-cli
```

Para configurar `kubectl` para se ligar ao cluster do Kubernetes, utilize o comando [az aks get-credentials][az-aks-get-credentials]. Este comando transfere credenciais e configura a CLI do Kubernetes para utilizá-los.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Para verificar a ligação ao cluster, utilize o comando [kubectl get][kubectl-get] para devolver uma lista de nós do cluster.

```azurecli-interactive
kubectl get nodes
```

A saída de exemplo seguinte mostra o nó único criado nos passos anteriores. Certifique-se de que o estado do nó é *pronto*:

```
NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.9.11
```

## <a name="run-the-application"></a>Executar a aplicação

Um arquivo de manifesto do Kubernetes define um estado pretendido para o cluster, como o contentor de imagens para executar. Neste início rápido, é utilizado um manifesto para criar todos os objetos necessários para executar a aplicação Azure Vote. Esse manifesto inclui dois [implementações de Kubernetes] [ kubernetes-deployment] -um para as aplicações do Azure Vote Python de exemplo e outro para uma instância de Redis. Dois [serviços do Kubernetes] [ kubernetes-service] também são criados - um serviço interno para a instância de Redis e um serviço externo para aceder a aplicação Azure Vote a partir da internet.

> [!TIP]
> Neste início rápido, crie e implemente manualmente os seus manifestos de aplicação para o cluster do AKS. Em mais cenários do mundo real, pode utilizar o [Azure Dev Spaces][azure-dev-spaces] para iterar e depurar o seu código rápida e diretamente no cluster do AKS. Pode utilizar o Dev Spaces em várias plataformas do SO e ambientes de desenvolvimento, e trabalhar em conjunto com outras pessoas na sua equipa.

Crie um ficheiro denominado `azure-vote.yaml` e copie a seguinte definição de YAML. Se utilizar o Azure Cloud Shell, este ficheiro pode ser criado usando `vi` ou `nano` como se trabalhar num sistema físico ou virtual:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-back
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      containers:
      - name: azure-vote-back
        image: redis
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
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
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-front
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      containers:
      - name: azure-vote-front
        image: microsoft/azure-vote-front:v1
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
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

Implemente a aplicação com o [aplicam-se de kubectl] [ kubectl-apply] de comando e especifique o nome do seu manifesto YAML:

```azurecli-interactive
kubectl apply -f azure-vote.yaml
```

O resultado de exemplo seguinte mostra as implementações e os serviços criados com êxito:

```
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>Testar a aplicação

Quando o aplicativo for executado, um serviço do Kubernetes expõe à aplicação de front-end à internet. Este processo pode demorar alguns minutos a concluir.

Para monitorizar o progresso, utilize o comando [kubectl get service][kubectl-get] com o argumento `--watch`.

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

Inicialmente o *EXTERNAL-IP* para o *do azure-vote-front* serviço é mostrado como *pendente*.

```
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Quando o *EXTERNAL-IP* é alterado de endereço *pendente* para um endereço IP público real, utilize `CTRL-C` para parar o `kubectl` veja processo. O resultado de exemplo seguinte mostra um endereço IP público válido atribuído ao serviço:

```
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Para ver a aplicação Azure Vote em ação, abra um navegador da web para o endereço IP externo do seu serviço.

![Imagem de navegação para o Azure Vote](media/container-service-kubernetes-walkthrough/azure-vote.png)

## <a name="monitor-health-and-logs"></a>Monitorizar o estado de funcionamento e os registos

Quando foi criado o cluster do AKS, o Monitor do Azure para contentores foi ativado para capturar métricas de estado de funcionamento para os nós de cluster e os pods. Estas métricas de estado de funcionamento estão disponíveis no portal do Azure.

Para ver o estado atual e a utilização de recursos dos pods de Azure Vote, conclua os seguintes passos:

1. Abra um browser no portal do Azure[https://portal.azure.com][azure-portal].
1. Selecione o grupo de recursos, como *myResourceGroup* e selecione o cluster do AKS, como *myAKSCluster*.
1. Sob **monitorização** no lado esquerdo, selecione **Insights**
1. Na parte superior, opte por **+ Adicionar Filtro**
1. Selecione *Espaço de nomes* como a propriedade e, em seguida, escolha *\<Todas, exceto o sistema kube\>*
1. Selecione para ver os **Contentores**.

Os contentores *azure-vote-back* e *azure-vote-front* são apresentados, conforme mostrado no exemplo seguinte:

![Ver o estado de funcionamento dos contentores em execução no AKS](media/kubernetes-walkthrough/monitor-containers.png)

Para ver os registos relativos ao pod `azure-vote-front`, selecione a ligação **Ver registos de contentor** no lado direito da lista de contentores. Esses registos incluem os fluxos *stdout* e *stderr* do contentor.

![Ver os registos dos contentores no AKS](media/kubernetes-walkthrough/monitor-container-logs.png)

## <a name="delete-cluster"></a>Eliminar o cluster

Quando o cluster já não for necessário, utilize o comando [az group delete][az-group-delete] para remover o grupo de recursos, o serviço de contentores e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Quando elimina o cluster, o principal de serviço do Azure Active Directory utilizado pelo cluster do AKS não é removido. Para obter passos sobre como remover o principal de serviço, consulte [Considerações sobre e eliminação do principal de serviço AKS][sp-delete].

## <a name="get-the-code"></a>Obter o código

Neste início rápido, foram utilizadas imagens de contentores pré-criadas para criar uma implementação de Kubernetes. O código da aplicação relacionado, o Dockerfile, e o ficheiro de manifesto do Kubernetes, estão disponíveis no GitHub.

[https://github.com/Azure-Samples/azure-voting-app-redis][azure-vote-app]

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, implementou um cluster do Kubernetes e implementou uma aplicação de vários contentores no mesmo.  [Aceder ao dashboard de web do Kubernetes] [ kubernetes-dashboard] para o cluster que acabou de criar.

Para saber mais sobre o AKS e ver um exemplo completo de código para implementação, avance para o tutorial dos clusters de Kubernetes.

> [!div class="nextstepaction"]
> [Tutorial do AKS][aks-tutorial]

<!-- LINKS - external -->
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[azure-dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: https://aka.ms/coingfonboarding
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks?view=azure-cli-latest#az-aks-browse
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli-install]: /cli/azure/install-azure-cli
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
