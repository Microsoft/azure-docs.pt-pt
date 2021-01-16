---
title: 'Quickstart: Implementar um cluster AKS utilizando o PowerShell'
description: Aprenda a criar rapidamente um cluster Kubernetes, implementar uma aplicação e monitorizar o desempenho no Serviço Azure Kubernetes (AKS) utilizando o PowerShell.
services: container-service
ms.topic: quickstart
ms.date: 01/13/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 41412a4ec95bb9b89df5fded6962965594e823fc
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2021
ms.locfileid: "98248518"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-cluster-using-powershell"></a>Quickstart: Implementar um cluster de serviço Azure Kubernetes utilizando o PowerShell

Neste arranque rápido, você implanta um cluster Azure Kubernetes Service (AKS) usando PowerShell. AKS é um serviço gerido pela Kubernetes que permite implementar e gerir rapidamente clusters. Uma aplicação multi-contentor que inclui um frontend web e uma instância Redis é executada no cluster. Em seguida, você vê como monitorizar a saúde do cluster e pods que executam a sua aplicação.

Para saber mais sobre a criação de um conjunto de nós do Windows Server, consulte [criar um cluster AKS que suporte recipientes do Windows Server][windows-container-powershell].

![App de votação implantada no Serviço Azure Kubernetes](./media/kubernetes-walkthrough-powershell/voting-app-deployed-in-azure-kubernetes-service.png)

Este guia de introdução parte do princípio de que possui conhecimentos básicos dos conceitos do Kubernetes. Para obter mais informações, consulte [os conceitos fundamentais da Kubernetes para o Serviço Azure Kubernetes (AKS)][kubernetes-concepts].

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

Se optar por utilizar o PowerShell localmente, este artigo requer que instale o módulo Az PowerShell e se conecte à sua conta Azure utilizando o cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/Connect-AzAccount) Para obter mais informações sobre a instalação do módulo Az PowerShell, consulte [instalar a Azure PowerShell][install-azure-powershell].

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Se tiver várias subscrições do Azure, escolha a subscrição adequada na qual os recursos devem ser faturados. Selecione um ID de subscrição específico utilizando o [cmdlet Set-AzContext.](/powershell/module/az.accounts/set-azcontext)

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um [grupo de recursos Azure](../azure-resource-manager/management/overview.md) é um grupo lógico no qual os recursos da Azure são implantados e geridos. Quando cria um grupo de recursos, é-lhe pedido que especifique uma localização. Esta localização é onde os metadados do grupo de recursos são armazenados, é também onde os seus recursos funcionam em Azure se você não especificar outra região durante a criação de recursos. Criar um grupo de recursos utilizando o cmdlet [New-AzResourceGroup.][new-azresourcegroup]

O exemplo a seguir cria um grupo de recursos chamado **myResourceGroup** na região **leste.**

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location eastus
```

A saída de exemplo a seguir mostra o grupo de recursos criado com sucesso:

```plaintext
ResourceGroupName : myResourceGroup
Location          : eastus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup
```

## <a name="create-aks-cluster"></a>Criar um cluster do AKS

Utilize o `ssh-keygen` utilitário de linha de comando para gerar um par de chaves SSH. Para obter mais detalhes, consulte [passos rápidos: Crie e utilize um par de chaves SSH público-privado para Os VMs Linux em Azure](../virtual-machines/linux/mac-create-ssh-keys.md).

Utilize o cmdlet [New-AzAks][new-azaks] para criar um cluster AKS. O exemplo seguinte cria um cluster com o nome **myAKSCluster** com um nó. O Monitor Azure para contentores também está ativado por defeito. Isto leva vários minutos para ser concluído.

> [!NOTE]
> Ao criar um cluster AKS, um segundo grupo de recursos é automaticamente criado para armazenar os recursos AKS. Para mais informações, veja [por que dois grupos de recursos são criados com AKS?](./faq.md#why-are-two-resource-groups-created-with-aks)

```azurepowershell-interactive
New-AzAksCluster -ResourceGroupName myResourceGroup -Name myAKSCluster -NodeCount 1
```

Após alguns minutos, o comando completa e devolve informações sobre o cluster.

## <a name="connect-to-the-cluster"></a>Ligar ao cluster

Para gerir um cluster Kubernetes, você usa [kubectl,][kubectl]o cliente da linha de comando Kubernetes. Se utilizar a Azure Cloud Shell, `kubectl` já está instalada. Para instalar `kubectl` localmente, utilize o `Install-AzAksKubectl` cmdlet:

```azurepowershell
Install-AzAksKubectl
```

Para configurar `kubectl` para ligar ao seu cluster Kubernetes, utilize o cmdlet [Import-AzAksCredential.][import-azakscredential] O exemplo a seguir descarrega credenciais e configura o CLI de Kubernetes para usá-las.

```azurepowershell-interactive
Import-AzAksCredential -ResourceGroupName myResourceGroup -Name myAKSCluster
```

Para verificar a ligação ao cluster, utilize o comando [kubectl get][kubectl-get] para devolver uma lista de nós do cluster.

```azurepowershell-interactive
.\kubectl get nodes
```

A saída de exemplo seguinte mostra o nó único criado nos passos anteriores. Certifique-se de que o estado do nó está **pronto:**

```plaintext
NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.15.10
```

## <a name="run-the-application"></a>Executar a aplicação

Um ficheiro manifesto kubernetes define um estado desejado para o cluster, como as imagens do recipiente a executar. Neste início rápido, é utilizado um manifesto para criar todos os objetos necessários para executar a aplicação Azure Vote. Este manifesto inclui [duas implementações de Kubernetes][kubernetes-deployment] - uma para a amostra aplicações Azure Vote Python, e outra para um caso Redis. Dois [Serviços Kubernetes também são criados - um serviço interno para a instância Redis, e um serviço externo para aceder à aplicação Azure Vote a partir da internet.

Crie um ficheiro nomeado `azure-vote.yaml` e copie na seguinte definição YAML. Se utilizar o Azure Cloud Shell, este ficheiro pode ser criado utilizando `vi` ou `nano` funcionando num sistema virtual ou físico:

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

Implemente a aplicação utilizando o comando [de aplicação de kubectl][kubectl-apply] e especifique o nome do seu manifesto YAML:

```azurepowershell-interactive
.\kubectl apply -f azure-vote.yaml
```

A saída de exemplo a seguir mostra as Implementações e Serviços criados com sucesso:

```plaintext
deployment.apps/azure-vote-back created
service/azure-vote-back created
deployment.apps/azure-vote-front created
service/azure-vote-front created
```

## <a name="test-the-application"></a>Testar a aplicação

Quando a aplicação é executado, um serviço Kubernetes expõe o frontend da aplicação para a internet.
Este processo pode demorar alguns minutos a concluir.

Para monitorizar o progresso, utilize o comando [kubectl get service][kubectl-get] com o argumento `--watch`.

```azurepowershell-interactive
.\kubectl get service azure-vote-front --watch
```

Inicialmente, o **IP EXTERNO** para o serviço de frente de **voto azul** é apresentado como **pendente**.

```plaintext
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Quando o endereço **EXTERNAL-IP** mudar de **pendente** para um endereço IP público real, use `CTRL-C` para parar o processo do `kubectl` relógio. A saída de exemplo a seguir mostra um endereço IP público válido atribuído ao serviço:

```plaintext
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Para ver a aplicação Azure Vote em ação, abra um navegador web para o endereço IP externo do seu serviço.

![App de votação implantada no Serviço Azure Kubernetes](./media/kubernetes-walkthrough-powershell/voting-app-deployed-in-azure-kubernetes-service.png)

Quando o cluster AKS foi criado, [o Azure Monitor para contentores](../azure-monitor/insights/container-insights-overview.md) foi habilitado a capturar métricas de saúde tanto para os nós de cluster como para as cápsulas. Estas métricas de estado de funcionamento estão disponíveis no portal do Azure.

## <a name="delete-the-cluster"></a>Eliminar o cluster

Para evitar as acusações do Azure, devias limpar recursos não necessários. Quando o cluster já não for necessário, utilize o cmdlet [Remove-AzResourceGroup][remove-azresourcegroup] para remover o grupo de recursos, o serviço de contentores e todos os recursos relacionados.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

> [!NOTE]
> Quando elimina o cluster, o principal de serviço do Azure Active Directory utilizado pelo cluster do AKS não é removido. Para obter passos sobre como remover o principal de serviço, consulte [Considerações sobre e eliminação do principal de serviço AKS][sp-delete]. Se usou uma identidade gerida, a identidade é gerida pela plataforma e não necessita de remoção.

## <a name="get-the-code"></a>Obter o código

Neste início rápido, imagens de contentores pré-criadas foram usadas para criar uma implantação de Kubernetes. O código da aplicação relacionado, o Dockerfile, e o ficheiro de manifesto do Kubernetes, estão disponíveis no GitHub.

[https://github.com/Azure-Samples/azure-voting-app-redis][azure-vote-app]

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, implementou um cluster do Kubernetes e implementou uma aplicação de vários contentores no mesmo. Também pode [aceder ao painel web kubernetes][kubernetes-dashboard] para o seu cluster AKS.

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
