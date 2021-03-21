---
title: 'Quickstart: Implementar um cluster AKS utilizando o PowerShell'
description: Aprenda a criar rapidamente um cluster Kubernetes, implementar uma aplicação e monitorizar o desempenho no Serviço Azure Kubernetes (AKS) utilizando o PowerShell.
services: container-service
ms.topic: quickstart
ms.date: 03/15/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2b61c791390200beac4a18422a4de58dd94fa711
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103492902"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-cluster-using-powershell"></a>Quickstart: Implementar um cluster de serviço Azure Kubernetes utilizando o PowerShell

O Azure Kubernetes Service (AKS) é um serviço gerido pela Kubernetes que permite implementar e gerir rapidamente clusters. Neste arranque rápido, você vai:
* Implemente um cluster AKS utilizando o PowerShell. 
* Executar uma aplicação multi-contentor com uma extremidade frontal web e uma instância Redis no cluster. 
* Monitorize a saúde do cluster e das cápsulas que executam a sua aplicação.

Para saber mais sobre a criação de um conjunto de nós do Windows Server, consulte [criar um cluster AKS que suporte recipientes do Windows Server][windows-container-powershell].

![App de votação implantada no Serviço Azure Kubernetes](./media/kubernetes-walkthrough-powershell/voting-app-deployed-in-azure-kubernetes-service.png)

Este guia de introdução parte do princípio de que possui conhecimentos básicos dos conceitos do Kubernetes. Para obter mais informações, consulte [os conceitos fundamentais da Kubernetes para o Serviço Azure Kubernetes (AKS)][kubernetes-concepts].

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

Se estiver a executar o PowerShell localmente, instale o módulo Az PowerShell e ligue-se à sua conta Azure utilizando o cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/Connect-AzAccount) Para obter mais informações sobre a instalação do módulo Az PowerShell, consulte [instalar a Azure PowerShell][install-azure-powershell].

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Se tiver várias subscrições do Azure, selecione o ID de subscrição apropriado no qual os recursos devem ser faturados utilizando o [cmdlet Set-AzContext.](/powershell/module/az.accounts/set-azcontext)

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um [grupo de recursos Azure](../azure-resource-manager/management/overview.md) é um grupo lógico no qual os recursos da Azure são implantados e geridos. Quando criar um grupo de recursos, será solicitado que especifique uma localização. Esta localização é: 
* A localização de armazenamento dos metadados do seu grupo de recursos.
* Onde os seus recursos serão executados em Azure se não especificar outra região durante a criação de recursos. 

O exemplo a seguir cria um grupo de recursos chamado **myResourceGroup** na região **leste.**

Criar um grupo de recursos utilizando o cmdlet [New-AzResourceGroup.][new-azresourcegroup]

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location eastus
```

Saída para grupo de recursos criado com sucesso:

```plaintext
ResourceGroupName : myResourceGroup
Location          : eastus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup
```

## <a name="create-aks-cluster"></a>Criar um cluster do AKS

1. Gere um par de chaves SSH utilizando o `ssh-keygen` utilitário da linha de comando. 
    * Para obter mais detalhes, consulte [passos rápidos: Crie e utilize um par de chaves SSH público-privado para Os VMs Linux em Azure](../virtual-machines/linux/mac-create-ssh-keys.md).

1. Crie um cluster AKS utilizando o cmdlet [New-AzAks.][new-azaks] O Monitor Azure para contentores está ativado por defeito.

    O exemplo seguinte cria um cluster com o nome **myAKSCluster** com um nó. 

    ```azurepowershell-interactive
    New-AzAksCluster -ResourceGroupName myResourceGroup -Name myAKSCluster -NodeCount 1
    ```

Após alguns minutos, o comando completa e devolve informações sobre o cluster.

> [!NOTE]
> Quando cria um cluster AKS, um segundo grupo de recursos é automaticamente criado para armazenar os recursos AKS. Para mais informações, veja [por que dois grupos de recursos são criados com AKS?](./faq.md#why-are-two-resource-groups-created-with-aks)

## <a name="connect-to-the-cluster"></a>Ligar ao cluster

Para gerir um cluster Kubernetes, utilize o cliente da linha de comando Kubernetes, [kubectl][kubectl]. `kubectl` já está instalado se utilizar a Azure Cloud Shell. 

1. Instale `kubectl` localmente utilizando o `Install-AzAksKubectl` cmdlet:

    ```azurepowershell
    Install-AzAksKubectl
    ```

2. Configure-se `kubectl` para ligar ao seu cluster Kubernetes utilizando o cmdlet [Import-AzAksCredential.][import-azakscredential] O cmdlet seguinte descarrega credenciais e configura o CLI de Kubernetes para usá-las.

    ```azurepowershell-interactive
    Import-AzAksCredential -ResourceGroupName myResourceGroup -Name myAKSCluster
    ```

3. Verifique a ligação ao seu cluster utilizando o [comando kubectl.][kubectl-get] Este comando devolve uma lista dos nós do cluster.

    ```azurepowershell-interactive
    .\kubectl get nodes
    ```

    A saída mostra o único nó criado nos passos anteriores. Certifique-se de que o estado do nó está *pronto:*

    ```plaintext
    NAME                       STATUS   ROLES   AGE     VERSION
    aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.15.10
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
    * Se utilizar o Azure Cloud Shell, este ficheiro pode ser criado utilizando `vi` ou `nano` como se funcionasse num sistema virtual ou físico
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

    ```azurepowershell-interactive
    .\kubectl apply -f azure-vote.yaml
    ```

    A produção mostra as implementações e serviços criados com sucesso:

    ```plaintext
    deployment.apps/azure-vote-back created
    service/azure-vote-back created
    deployment.apps/azure-vote-front created
    service/azure-vote-front created
    ```

## <a name="test-the-application"></a>Testar a aplicação

Quando a aplicação é executado, um serviço Kubernetes expõe a linha frontal da aplicação para a internet. Este processo pode demorar alguns minutos a concluir.

Monitorize o progresso utilizando o [comando de serviço kubectl][kubectl-get] com o `--watch` argumento.

```azurepowershell-interactive
.\kubectl get service azure-vote-front --watch
```

A saída **EXTERNAL-IP** do `azure-vote-front` serviço irá inicialmente mostrar como *pendente*.

```plaintext
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Uma vez que o endereço **EXTERNAL-IP** muda de *pendente* para um endereço IP público real, use `CTRL-C` para parar o processo do `kubectl` relógio. A saída de exemplo a seguir mostra um endereço IP público válido atribuído ao serviço:

```plaintext
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Para ver a aplicação Azure Vote em ação, abra um navegador web para o endereço IP externo do seu serviço.

![App de votação implantada no Serviço Azure Kubernetes](./media/kubernetes-walkthrough-powershell/voting-app-deployed-in-azure-kubernetes-service.png)

Veja as métricas de saúde dos nós de cluster e das cápsulas capturadas pelo Azure Monitor para recipientes no portal Azure. 

## <a name="delete-the-cluster"></a>Eliminar o cluster

Para evitar as acusações do Azure, limpe os seus recursos desnecessários. Utilize o [cmdlet Remove-AzResourceGroup][remove-azresourcegroup] para remover o grupo de recursos, o serviço de contentores e todos os recursos relacionados.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
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
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[azure-dev-spaces]: ../dev-spaces/index.yml
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git

<!-- LINKS - internal -->
[windows-container-powershell]: windows-container-powershell.md
[kubernetes-concepts]: concepts-clusters-workloads.md
[install-azure-powershell]: /powershell/azure/install-az-ps
[new-azresourcegroup]: /powershell/module/az.resources/new-azresourcegroup
[new-azaks]: /powershell/module/az.aks/new-azaks
[import-azakscredential]: /powershell/module/az.aks/import-azakscredential
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[remove-azresourcegroup]: /powershell/module/az.resources/remove-azresourcegroup
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[kubernetes-dashboard]: kubernetes-dashboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
