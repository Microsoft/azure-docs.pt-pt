---
title: 'Quickstart: Implementar um cluster AKS utilizando o Azure CLI'
description: Aprenda a criar rapidamente um cluster Kubernetes, implementar uma aplicação e monitorizar o desempenho no Serviço Azure Kubernetes (AKS) utilizando o CLI Azure.
services: container-service
ms.topic: quickstart
ms.date: 02/26/2021
ms.custom:
- H1Hack27Feb2017
- mvc
- devcenter
- seo-javascript-september2019
- seo-javascript-october2019
- seo-python-october2019
- devx-track-azurecli
- contperf-fy21q1
ms.openlocfilehash: b3d6c7695f74c048cb03e3f4e7ae822005c81c06
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103492885"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-cluster-using-the-azure-cli"></a>Quickstart: Implementar um cluster de serviço Azure Kubernetes utilizando o Azure CLI

O Azure Kubernetes Service (AKS) é um serviço gerido pela Kubernetes que permite implementar e gerir rapidamente clusters. Neste arranque rápido, você vai:
* Implementar um cluster AKS utilizando o Azure CLI. 
* Executar uma aplicação multi-contentor com uma extremidade frontal web e uma instância Redis no cluster. 
* Monitorize a saúde do cluster e das cápsulas que executam a sua aplicação.

  ![App de votação implantada no Serviço Azure Kubernetes](./media/container-service-kubernetes-walkthrough/voting-app-deployed-in-azure-kubernetes-service.png)

Este guia de introdução parte do princípio de que possui conhecimentos básicos dos conceitos do Kubernetes. Para obter mais informações, consulte [os conceitos fundamentais da Kubernetes para o Serviço Azure Kubernetes (AKS)][kubernetes-concepts].

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Para saber mais sobre a criação de um conjunto de nós do Windows Server, consulte [criar um cluster AKS que suporte recipientes do Windows Server][windows-container-cli].

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo requer a versão 2.0.64 ou maior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

> [!NOTE]
> Executar os comandos como administrador se planeia executar os comandos neste quickstart localmente em vez de em Azure Cloud Shell.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um [grupo de recursos Azure](../azure-resource-manager/management/overview.md) é um grupo lógico no qual os recursos da Azure são implantados e geridos. Quando criar um grupo de recursos, será solicitado que especifique uma localização. Esta localização é: 
* A localização de armazenamento dos metadados do seu grupo de recursos.
* Onde os seus recursos serão executados em Azure se não especificar outra região durante a criação de recursos. 

O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

Criar um grupo de recursos utilizando o [grupo az criar][az-group-create] comando.


```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Saída para grupo de recursos criado com sucesso:

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

## <a name="enable-cluster-monitoring"></a>Permitir a monitorização do cluster

1. Verifique *se microsoft.OperationsManagement* e *Microsoft.OperationalInsights* estão registados na sua subscrição. Para verificar o estado do registo:

    ```azurecli
    az provider show -n Microsoft.OperationsManagement -o table
    az provider show -n Microsoft.OperationalInsights -o table
    ```
 
    Se não estiverem registados, registe *o Microsoft.OperationsManagement* e *o Microsoft.OperationalInsights* utilizando:
 
    ```azurecli
    az provider register --namespace Microsoft.OperationsManagement
    az provider register --namespace Microsoft.OperationalInsights
    ```

2. Ativar [o Monitor Azure para os recipientes][azure-monitor-containers] que utilizem o parâmetro *de monitorização dos addons ativantes.* 

## <a name="create-aks-cluster"></a>Criar um cluster do AKS

Crie um cluster AKS utilizando os [az aks criar][az-aks-create] comando. O exemplo a seguir cria um cluster chamado *myAKSCluster* com um nó: 

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --node-count 1 --enable-addons monitoring --generate-ssh-keys
```

Após alguns minutos, o comando completa e devolve informações formatadas com JSON sobre o cluster.

> [!NOTE]
> Quando cria um cluster AKS, um segundo grupo de recursos é automaticamente criado para armazenar os recursos AKS. Para mais informações, veja [por que dois grupos de recursos são criados com AKS?](./faq.md#why-are-two-resource-groups-created-with-aks)

## <a name="connect-to-the-cluster"></a>Ligar ao cluster

Para gerir um cluster Kubernetes, utilize o cliente da linha de comando Kubernetes, [kubectl][kubectl]. `kubectl` já está instalado se utilizar a Azure Cloud Shell. 

1. Instale `kubectl` localmente utilizando o comando [az aks instalar-cli:][az-aks-install-cli]

    ```azurecli
    az aks install-cli
    ```

2. Configurar `kubectl` para ligar ao seu cluster Kubernetes usando o comando [az aks get-credentials.][az-aks-get-credentials] O seguinte comando:  
      * Descarrega credenciais e configura o CLI de Kubernetes para usá-las.
      * Utiliza `~/.kube/config` , a localização padrão para o [ficheiro de configuração Kubernetes][kubeconfig-file]. Especifique uma localização diferente para o seu ficheiro de configuração Kubernetes utilizando *o ficheiro --file*.


    ```azurecli-interactive
    az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
    ```

3. Verifique a ligação ao seu cluster utilizando o [comando kubectl.][kubectl-get] Este comando devolve uma lista dos nós do cluster.

    ```azurecli-interactive
    kubectl get nodes
    ```

    A saída mostra o único nó criado nos passos anteriores. Certifique-se de que o estado do nó está *pronto:*

    ```output
    NAME                       STATUS   ROLES   AGE     VERSION
    aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.12.8
    ```

## <a name="run-the-application"></a>Executar a aplicação

Um [ficheiro manifesto de Kubernetes][kubernetes-deployment] define o estado desejado de um cluster, como quais as imagens do contentor a executar. 

Neste arranque rápido, utilizará um manifesto para criar todos os objetos necessários para executar a [aplicação Azure Vote][azure-vote-app]. Este manifesto inclui duas [missões de Kubernetes:][kubernetes-deployment]
* A amostra Azure Vote Python aplicações.
* Um exemplo do Redis. 

São também criados dois [Serviços Kubernetes:][kubernetes-service]
* Um serviço interno para o caso Redis.
* Um serviço externo para aceder à aplicação Azure Vote a partir da internet.

1. Crie um ficheiro com o nome `azure-vote.yaml`.
    * Se utilizar o Azure Cloud Shell, este ficheiro pode ser criado utilizando `code` , ou como se `vi` `nano` trabalhasse num sistema virtual ou físico
1. Copiar na seguinte definição YAML:

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
          nodeSelector:
            "beta.kubernetes.io/os": linux
          containers:
          - name: azure-vote-back
            image: mcr.microsoft.com/oss/bitnami/redis:6.0.8
            env:
            - name: ALLOW_EMPTY_PASSWORD
              value: "yes"
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
          nodeSelector:
            "beta.kubernetes.io/os": linux
          containers:
          - name: azure-vote-front
            image: mcr.microsoft.com/azuredocs/azure-vote-front:v1
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

1. Implemente a aplicação utilizando o comando [de aplicação de kubectl][kubectl-apply] e especifique o nome do seu manifesto YAML:

    ```console
    kubectl apply -f azure-vote.yaml
    ```

    A produção mostra as implementações e serviços criados com sucesso:

    ```output
    deployment "azure-vote-back" created
    service "azure-vote-back" created
    deployment "azure-vote-front" created
    service "azure-vote-front" created
    ```

## <a name="test-the-application"></a>Testar a aplicação

Quando a aplicação é executado, um serviço Kubernetes expõe a linha frontal da aplicação para a internet. Este processo pode demorar alguns minutos a concluir.

Monitorize o progresso utilizando o [comando de serviço kubectl][kubectl-get] com o `--watch` argumento.

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

A saída **EXTERNAL-IP** do `azure-vote-front` serviço irá inicialmente mostrar como *pendente*.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Uma vez que o endereço **EXTERNAL-IP** muda de *pendente* para um endereço IP público real, use `CTRL-C` para parar o processo do `kubectl` relógio. A saída de exemplo a seguir mostra um endereço IP público válido atribuído ao serviço:

```output
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Para ver a aplicação Azure Vote em ação, abra um navegador web para o endereço IP externo do seu serviço.

![App de votação implantada no Serviço Azure Kubernetes](./media/container-service-kubernetes-walkthrough/voting-app-deployed-in-azure-kubernetes-service.png)

Veja as métricas de saúde dos nós de cluster e das cápsulas capturadas pelo [Azure Monitor para recipientes][azure-monitor-containers] no portal Azure. 

## <a name="delete-the-cluster"></a>Eliminar o cluster

Para evitar as acusações do Azure, limpe os seus recursos desnecessários. Utilize o comando [az group delete][az-group-delete] para remover o grupo de recursos, o serviço de contentores e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> Quando elimina o cluster, o principal de serviço do Azure Active Directory utilizado pelo cluster do AKS não é removido. Para obter passos sobre como remover o principal de serviço, consulte [Considerações sobre e eliminação do principal de serviço AKS][sp-delete].
> 
> Se usou uma identidade gerida, a identidade é gerida pela plataforma e não necessita de remoção.

## <a name="get-the-code"></a>Obter o código

Imagens de contentores pré-existentes foram usadas neste quickstart para criar uma implantação de Kubernetes. O código de aplicação relacionado, Dockerfile e Kubernetes manifesto file estão [disponíveis no GitHub.][azure-vote-app]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, lançou um cluster Kubernetes e, em seguida, implantou uma aplicação de vários contentores para o mesmo. [Aceda ao painel web Kubernetes][kubernetes-dashboard] para o seu cluster AKS.

Para saber mais sobre o AKS e ver um exemplo completo de código para implementação, avance para o tutorial dos clusters de Kubernetes.

> [!div class="nextstepaction"]
> [Tutorial AKS][aks-tutorial]

<!-- LINKS - external -->
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubeconfig-file]: https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: ../azure-monitor/containers/container-insights-onboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks#az-aks-browse
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks#az-aks-install-cli
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-monitor-containers]: ../azure-monitor/containers/container-insights-overview.md
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[windows-container-cli]: windows-container-cli.md