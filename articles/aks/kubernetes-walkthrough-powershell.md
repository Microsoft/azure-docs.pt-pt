---
title: 'Quickstart: Implementar um cluster de serviço Azure Kubernetes'
description: Aprenda a criar rapidamente um cluster Kubernetes, implemente uma aplicação e monitorize o desempenho no Serviço Azure Kubernetes (AKS) usando o PowerShell.
services: container-service
ms.topic: quickstart
ms.date: 05/26/2020
ms.openlocfilehash: a89f76344e48d5af8c71c5a674a94767795b41a9
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83871479"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-cluster-using-powershell"></a>Quickstart: Implementar um cluster de serviço Azure Kubernetes utilizando powerShell

Neste arranque rápido, você implementa um cluster azure Kubernetes Service (AKS) usando powerShell. O AKS é um serviço kubernetes gerido que permite implementar e gerir rapidamente clusters. Uma aplicação multi-contentor que inclui um frontend web e uma instância Redis é executada no cluster. Em seguida, veja como monitorizar a saúde do cluster e das cápsulas que executam a sua aplicação.

Para saber mais sobre a criação de um conjunto de nós do Windows Server, consulte [Criar um cluster AKS que suporta os recipientes do Windows Server][windows-container-powershell].

![App de votação implantada no Serviço Azure Kubernetes](./media/kubernetes-walkthrough-powershell/voting-app-deployed-in-azure-kubernetes-service.png)

Este guia de introdução parte do princípio de que possui conhecimentos básicos dos conceitos do Kubernetes. Para mais informações, consulte os [conceitos centrais da Kubernetes para o Serviço Azure Kubernetes (AKS)][kubernetes-concepts].

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

Se optar por utilizar o PowerShell localmente, este artigo requer que instale o módulo Az PowerShell e se ligue à sua conta Azure utilizando o cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/Connect-AzAccount) Para obter mais informações sobre a instalação do módulo Az PowerShell, consulte [Instalar o Azure PowerShell][install-azure-powershell].

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Se tiver várias subscrições azure, escolha a subscrição adequada na qual os recursos devem ser faturados. Selecione um ID de subscrição específico utilizando o cmdlet [Set-AzContext.](/powershell/module/az.accounts/set-azcontext)

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de [recursos Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) é um grupo lógico no qual os recursos azure são implantados e geridos. Quando cria um grupo de recursos, é-lhe pedido que especifique uma localização. Esta localização é onde os metadados do grupo de recursos são armazenados, é também onde os seus recursos funcionam em Azure se você não especificar outra região durante a criação de recursos. Crie um grupo de recursos utilizando o cmdlet [New-AzResourceGroup.][new-azresourcegroup]

O exemplo seguinte cria um grupo de recursos chamado **myResourceGroup** na região **oriental.**

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location eastus
```

A saída de exemplo seguinte mostra o grupo de recursos criado com sucesso:

```Output
ResourceGroupName : myResourceGroup
Location          : eastus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup
```

## <a name="create-aks-cluster"></a>Criar um cluster do AKS

Utilize o utilitário da linha de comando para gerar um par de `ssh-keygen` chaves SSH. Para mais detalhes, consulte [Passos Rápidos: Crie e use um par de chaves público-privado SSH para VMs Linux em Azure](/azure/virtual-machines/linux/mac-create-ssh-keys).

Use o cmdlet [New-AzAks][new-azaks] para criar um cluster AKS. O exemplo seguinte cria um cluster com o nome **myAKSCluster** com um nó. O Monitor Azure para contentores também está ativado por defeito. Isto leva vários minutos para ser concluído.

> [!NOTE]
> Ao criar um cluster AKS, um segundo grupo de recursos é automaticamente criado para armazenar os recursos AKS. Para mais informações, veja por que dois grupos de [recursos são criados com AKS?](https://docs.microsoft.com/azure/aks/faq#why-are-two-resource-groups-created-with-aks)

```azurepowershell-interactive
New-AzAks -ResourceGroupName myResourceGroup -Name myAKSCluster -NodeCount 1
```

Após alguns minutos, o comando completa e devolve informações sobre o cluster.

## <a name="connect-to-the-cluster"></a>Ligar ao cluster

Para gerir um cluster Kubernetes, você usa [kubectl,][kubectl]o cliente da linha de comando Kubernetes. Se utilizar a Azure Cloud Shell, `kubectl` já está instalada. Para instalar `kubectl` localmente, utilize o `Install-AzAksKubectl` cmdlet:

```azurepowershell
Install-AzAksKubectl
```

Para configurar `kubectl` a ligação ao seu cluster Kubernetes, utilize o cmdlet [Import-AzAksCredential.][import-azakscredential] O exemplo seguinte descarrega credenciais e confunde o ClI Kubernetes para usá-las.

```azurepowershell-interactive
Import-AzAksCredential -ResourceGroupName myResourceGroup -Name myAKSCluster
```

Para verificar a ligação ao cluster, utilize o comando [kubectl get][kubectl-get] para devolver uma lista de nós do cluster.

```azurepowershell-interactive
.\kubectl get nodes
```

A saída de exemplo seguinte mostra o nó único criado nos passos anteriores. Certifique-se de que o estado do nó está **pronto:**

```Output
NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.15.10
```

## <a name="run-the-application"></a>Executar a aplicação

Um ficheiro manifesto Kubernetes define um estado desejado para o cluster, como quais as imagens de contentores a executar. Neste início rápido, é utilizado um manifesto para criar todos os objetos necessários para executar a aplicação Azure Vote. Este manifesto inclui duas [implantações kubernetes][kubernetes-deployment] - uma para as aplicações azure vote Python, e outra para uma instância Redis. Dois [Kubernetes Services também são criados - um serviço interno para a instância Redis, e um serviço externo para aceder à aplicação Azure Vote a partir da internet.

> [!TIP]
> Neste início rápido, crie e implemente manualmente os seus manifestos de aplicação para o cluster do AKS.
> Em mais cenários do mundo real, pode utilizar o [Azure Dev Spaces][azure-dev-spaces] para iterar e depurar o seu código rápida e diretamente no cluster do AKS. Pode utilizar o Dev Spaces em várias plataformas do SO e ambientes de desenvolvimento, e trabalhar em conjunto com outras pessoas na sua equipa.

Crie um ficheiro nomeado `azure-vote.yaml` e copie na seguinte definição YAML. Se utilizar a Casca de Nuvem Azure, este ficheiro pode ser criado utilizando `vi` ou `nano` como se trabalhasse num sistema virtual ou físico:

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
      nodeSelector:
        "beta.kubernetes.io/os": linux
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

Implemente a aplicação utilizando o [kubectl aplique][kubectl-apply] o comando e especifique o nome do seu manifesto YAML:

```azurepowershell-interactive
.\kubectl apply -f azure-vote.yaml
```

A saída de exemplo a seguir mostra as Implantações e Serviços criados com sucesso:

```Output
deployment.apps/azure-vote-back created
service/azure-vote-back created
deployment.apps/azure-vote-front created
service/azure-vote-front created
```

## <a name="test-the-application"></a>Testar a aplicação

Quando a aplicação é executado, um serviço Kubernetes expõe o frontend da aplicação à internet.
Este processo pode demorar alguns minutos a concluir.

Para monitorizar o progresso, utilize o comando [kubectl get service][kubectl-get] com o argumento `--watch`.

```azurepowershell-interactive
.\kubectl get service azure-vote-front --watch
```

Inicialmente, o **IP EXTERNO** para o serviço de frente para **voto sinuoso** é apresentado como **pendente**.

```Output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Quando o endereço **EXTERNO-IP** passar de **pendente** para um endereço IP público real, utilize `CTRL-C` para parar o processo de `kubectl` observação. A saída de exemplo seguinte mostra um endereço IP público válido atribuído ao serviço:

```Output
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Para ver a aplicação Azure Vote em ação, abra um navegador web para o endereço IP externo do seu serviço.

![App de votação implantada no Serviço Azure Kubernetes](./media/kubernetes-walkthrough-powershell/voting-app-deployed-in-azure-kubernetes-service.png)

Quando o cluster AKS foi criado, [o Monitor Azure para contentores](../azure-monitor/insights/container-insights-overview.md) foi permitido capturar métricas de saúde tanto para os nós de cluster como para as cápsulas. Estas métricas de estado de funcionamento estão disponíveis no portal do Azure.

## <a name="delete-the-cluster"></a>Eliminar o cluster

Para evitar as acusações do Azure, deve limpar recursos desnecessários. Quando o cluster já não for necessário, utilize o cmdlet [Remove-AzResourceGroup][remove-azresourcegroup] para remover o grupo de recursos, o serviço de contentores e todos os recursos conexos.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

> [!NOTE]
> Quando elimina o cluster, o principal de serviço do Azure Active Directory utilizado pelo cluster do AKS não é removido. Para obter passos sobre como remover o principal de serviço, consulte [Considerações sobre e eliminação do principal de serviço AKS][sp-delete]. Se usou uma identidade gerida, a identidade é gerida pela plataforma e não requer remoção.

## <a name="get-the-code"></a>Obter o código

Neste arranque rápido, imagens de contentores pré-criadas foram usadas para criar uma implantação kubernetes. O código da aplicação relacionado, o Dockerfile, e o ficheiro de manifesto do Kubernetes, estão disponíveis no GitHub.

[https://github.com/Azure-Samples/azure-voting-app-redis][azure-vote-app]

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, implementou um cluster do Kubernetes e implementou uma aplicação de vários contentores no mesmo. Também pode [aceder ao painel web da Kubernetes][kubernetes-dashboard] para o seu cluster AKS.

Para saber mais sobre o AKS e ver um exemplo completo de código para implementação, avance para o tutorial dos clusters de Kubernetes.

> [!div class="nextstepaction"]
> [Tutorial AKS][aks-tutorial]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[azure-dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/
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
