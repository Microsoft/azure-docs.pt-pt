---
title: Crie um recipiente do Windows Server num cluster AKS utilizando o PowerShell
description: Aprenda a criar rapidamente um cluster Kubernetes, implementar uma aplicação num contentor do Windows Server no Serviço Azure Kubernetes (AKS) utilizando o PowerShell.
services: container-service
ms.topic: article
ms.date: 05/26/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: bf446c858e40014a4085721d646f819e08542064
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87497890"
---
# <a name="create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-powershell"></a>Criar um recipiente do Windows Server num cluster Azure Kubernetes Service (AKS) utilizando o PowerShell

O Azure Kubernetes Service (AKS) é um serviço gerido pela Kubernetes que permite implementar e gerir rapidamente clusters. Neste artigo, você implanta um cluster AKS usando PowerShell. Também implementa uma `ASP.NET` aplicação de amostra num recipiente do Windows Server para o cluster.

![Imagem de navegação para ASP.NET aplicação da amostra](media/windows-container-powershell/asp-net-sample-app.png)

Este artigo pressupõe uma compreensão básica dos conceitos de Kubernetes. Para obter mais informações, consulte [os conceitos fundamentais da Kubernetes para o Serviço Azure Kubernetes (AKS)][kubernetes-concepts].

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

Se optar por utilizar o PowerShell localmente, este artigo requer que instale o módulo Az PowerShell e se conecte à sua conta Azure utilizando o cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/Connect-AzAccount) Para obter mais informações sobre a instalação do módulo Az PowerShell, consulte [instalar a Azure PowerShell][install-azure-powershell].

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Se tiver várias subscrições do Azure, escolha a subscrição adequada na qual os recursos devem ser faturados. Selecione um ID de subscrição específico utilizando o [cmdlet Set-AzContext.](/powershell/module/az.accounts/set-azcontext)

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="limitations"></a>Limitações

Aplicam-se as seguintes limitações quando cria e gere clusters AKS que suportam múltiplas piscinas de nó:

* Não pode apagar a primeira piscina de nós.

As seguintes limitações adicionais aplicam-se aos conjuntos de nó de nó do Windows Server:

* O cluster AKS pode ter um máximo de 10 piscinas de nó.
* O cluster AKS pode ter um máximo de 100 nós em cada piscina de nós.
* O nome do número de conjunto do Windows Server tem um limite de 6 caracteres.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um [grupo de recursos Azure](../azure-resource-manager/management/overview.md) é um grupo lógico no qual os recursos da Azure são implantados e geridos. Quando cria um grupo de recursos, é-lhe pedido que especifique uma localização. Esta localização é onde os metadados do grupo de recursos são armazenados, é também onde os seus recursos funcionam em Azure se você não especificar outra região durante a criação de recursos. Criar um grupo de recursos utilizando o cmdlet [New-AzResourceGroup.][new-azresourcegroup]

O exemplo seguinte cria um grupo de recursos com o nome **myResourceGroup** na localização **eastus**.

> [!NOTE]
> Este artigo usa a sintaxe PowerShell para os comandos neste tutorial. Se estiver a utilizar a Azure Cloud Shell, certifique-se de que o recuo na parte superior esquerda da janela Cloud Shell está definido para **PowerShell**.

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

## <a name="create-an-aks-cluster"></a>Criar um cluster do AKS (Create an AKS cluster)

Utilize o `ssh-keygen` utilitário de linha de comando para gerar um par de chaves SSH. Para obter mais detalhes, consulte [passos rápidos: Crie e utilize um par de chaves SSH público-privado para Os VMs Linux em Azure](../virtual-machines/linux/mac-create-ssh-keys.md).

Para executar um cluster AKS que suporta piscinas de nós para recipientes do Windows Server, o seu cluster precisa de utilizar uma política de rede que utilize plugin de rede [Azure CNI][azure-cni-about] (avançado). Para obter informações mais pormenorizadas para ajudar a planear as gamas de sub-redes necessárias e considerações de rede, consulte [a configuração da rede CNI Azure][use-advanced-networking]. Utilize o cmdlet [New-AzAks][new-azaks] abaixo para criar um cluster AKS chamado **myAKSCluster**. O exemplo a seguir cria os recursos de rede necessários se não existirem.

> [!NOTE]
> Para garantir que o seu cluster funciona de forma fiável, deve executar pelo menos 2 (dois) nós na piscina de nós predefinidos.

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzAKS -ResourceGroupName myResourceGroup -Name myAKSCluster -NodeCount 2 -KubernetesVersion 1.16.7 -NetworkPlugin azure -NodeVmSetType VirtualMachineScaleSets -WindowsProfileAdminUserName akswinuser -WindowsProfileAdminUserPassword $Password
```

> [!Note]
> Se não conseguir criar o cluster AKS porque a versão não é suportada nesta região, então pode usar o `Get-AzAksVersion -Location eastus` comando para encontrar a lista de versão suportada para esta região.

Após alguns minutos, o comando completa e devolve informações sobre o cluster. Ocasionalmente, o cluster pode demorar mais do que alguns minutos a providenciar. Deixe até 10 minutos nestes casos.

## <a name="add-a-windows-server-node-pool"></a>Adicione uma piscina de nó do servidor do Windows

Por padrão, um cluster AKS é criado com uma piscina de nó que pode executar recipientes Linux. Utilize `New-AzAksNodePool` o cmdlet para adicionar uma piscina de nó que pode executar os recipientes do Windows Server ao lado da piscina de nóleiros Linux.

```azurepowershell-interactive
New-AzAksNodePool -ResourceGroupName myResourceGroup -ClusterName myAKSCluster -OsType Windows -Name npwin -KubernetesVersion 1.16.7
```

O comando acima cria uma nova piscina de nó chamado **npwin** e adiciona-o ao **myAKSCluster**. Ao criar uma piscina de nó para executar recipientes do Windows Server, o valor padrão para **VmSize** é **Standard_D2s_v3**. Se optar por definir o parâmetro **VmSize,** verifique a lista de [tamanhos VM restritos][restricted-vm-sizes]. O tamanho mínimo recomendado é **Standard_D2s_v3**. O comando anterior também utiliza a sub-rede predefinida na vnet predefinida criada ao executar `New-AzAks` .

## <a name="connect-to-the-cluster"></a>Ligar ao cluster

Para gerir um cluster Kubernetes, você usa [kubectl,][kubectl]o cliente da linha de comando Kubernetes. Se utilizar a Azure Cloud Shell, `kubectl` já está instalada. Para instalar `kubectl` localmente, utilize o `Install-AzAksKubectl` cmdlet:

```azurepowershell-interactive
Install-AzAksKubectl
```

Para configurar `kubectl` para ligar ao seu cluster Kubernetes, utilize o cmdlet [Import-AzAksCredential.][import-azakscredential] Este comando descarrega credenciais e configura o CLI de Kubernetes para usá-las.

```azurepowershell-interactive
Import-AzAksCredential -ResourceGroupName myResourceGroup -Name myAKSCluster
```

Para verificar a ligação ao cluster, utilize o comando [kubectl get][kubectl-get] para devolver uma lista de nós do cluster.

```azurepowershell-interactive
kubectl get nodes
```

A saída de exemplo a seguir mostra todos os nós no cluster. Certifique-se de que o estado de todos os nós está **pronto:**

```plaintext
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.16.7
aksnpwin987654                      Ready    agent   108s   v1.16.7
```

## <a name="run-the-application"></a>Executar a aplicação

Um ficheiro manifesto kubernetes define um estado desejado para o cluster, como as imagens do recipiente a executar. Neste artigo, é utilizado um manifesto para criar todos os objetos necessários para executar a aplicação de amostra ASP.NET num recipiente do Windows Server. Este manifesto inclui uma [implementação de Kubernetes][kubernetes-deployment] para a aplicação de amostra ASP.NET e um [serviço externo de Kubernetes][kubernetes-service] para aceder à aplicação a partir da internet.

A aplicação ASP.NET amostra é fornecida como parte das [amostras-quadro .NET][dotnet-samples] e funciona num recipiente do Windows Server. O AKS exige que os contentores do Windows Server sejam baseados em imagens do **Windows Server 2019** ou superiores. O ficheiro manifesto Kubernetes também deve definir um [seletor de nó][node-selector] para dizer ao seu cluster AKS para executar o seu ASP.NET pod da aplicação da amostra num nó que pode executar recipientes do Windows Server.

Crie um ficheiro nomeado `sample.yaml` e copie na seguinte definição YAML. Se utilizar o Azure Cloud Shell, este ficheiro pode ser criado utilizando `vi` ou `nano` funcionando num sistema virtual ou físico:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample
  labels:
    app: sample
spec:
  replicas: 1
  template:
    metadata:
      name: sample
      labels:
        app: sample
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": windows
      containers:
      - name: sample
        image: mcr.microsoft.com/dotnet/framework/samples:aspnetapp
        resources:
          limits:
            cpu: 1
            memory: 800M
          requests:
            cpu: .1
            memory: 300M
        ports:
          - containerPort: 80
  selector:
    matchLabels:
      app: sample
---
apiVersion: v1
kind: Service
metadata:
  name: sample
spec:
  type: LoadBalancer
  ports:
  - protocol: TCP
    port: 80
  selector:
    app: sample
```

Implemente a aplicação utilizando o comando [de aplicação de kubectl][kubectl-apply] e especifique o nome do seu manifesto YAML:

```azurepowershell-interactive
kubectl apply -f sample.yaml
```

A saída de exemplo a seguir mostra a Implementação e o Serviço criados com sucesso:

```plaintext
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>Testar a aplicação

Quando a aplicação é executado, um serviço Kubernetes expõe o frontend da aplicação para a internet.
Este processo pode demorar alguns minutos a concluir. Ocasionalmente, o serviço pode demorar mais do que alguns minutos a providenciar. Deixe até 10 minutos nestes casos.

Para monitorizar o progresso, utilize o comando [kubectl get service][kubectl-get] com o argumento `--watch`.

```azurepowershell-interactive
kubectl get service sample --watch
```

Inicialmente, o **EXTERNAL-IP** para o serviço de **amostragem** é apresentado como **pendente**.

```plaintext
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Quando o endereço **EXTERNAL-IP** mudar de **pendente** para um endereço IP público real, use `CTRL-C` para parar o processo do `kubectl` relógio. A saída de exemplo a seguir mostra um endereço IP público válido atribuído ao serviço:

```plaintext
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Para ver a aplicação da amostra em ação, abra um navegador web para o endereço IP externo do seu serviço.

![Imagem de navegação para ASP.NET aplicação da amostra](media/windows-container-powershell/asp-net-sample-app.png)

> [!Note]
> Se receber um tempo limite de ligação ao tentar carregar a página, deverá verificar se a aplicação da amostra está pronta com o seguinte comando `kubectl get pods --watch` . Por vezes, o recipiente das janelas não será iniciado no momento em que o seu endereço IP externo estiver disponível.

## <a name="delete-cluster"></a>Eliminar o cluster

Quando o cluster já não for necessário, utilize o cmdlet [Remove-AzResourceGroup][remove-azresourcegroup] para remover o grupo de recursos, o serviço de contentores e todos os recursos relacionados.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

> [!NOTE]
> Quando elimina o cluster, o principal de serviço do Azure Active Directory utilizado pelo cluster do AKS não é removido. Para obter passos sobre como remover o principal de serviço, consulte [Considerações sobre e eliminação do principal de serviço AKS][sp-delete]. Se usou uma identidade gerida, a identidade é gerida pela plataforma e não necessita de remoção.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, implementou um cluster Kubernetes e implementou uma `ASP.NET` aplicação de amostra num contentor do Windows Server. [Aceda ao painel web Kubernetes][kubernetes-dashboard] para o cluster que criou.

Para saber mais sobre o AKS e ver um exemplo completo de código para implementação, avance para o tutorial dos clusters de Kubernetes.

> [!div class="nextstepaction"]
> [Tutorial AKS][aks-tutorial]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[dotnet-samples]: https://hub.docker.com/_/microsoft-dotnet-framework-samples/
[node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[install-azure-powershell]: /powershell/azure/install-az-ps
[new-azresourcegroup]: /powershell/module/az.resources/new-azresourcegroup
[azure-cni-about]: concepts-network.md#azure-cni-advanced-networking
[use-advanced-networking]: configure-azure-cni.md
[new-azaks]: /powershell/module/az.aks/new-azaks
[restricted-vm-sizes]: quotas-skus-regions.md#restricted-vm-sizes
[import-azakscredential]: /powershell/module/az.aks/import-azakscredential
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[remove-azresourcegroup]: /powershell/module/az.resources/remove-azresourcegroup
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[kubernetes-dashboard]: kubernetes-dashboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
