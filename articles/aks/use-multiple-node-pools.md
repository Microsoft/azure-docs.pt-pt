---
title: Utilize várias piscinas de nós no Serviço Azure Kubernetes (AKS)
description: Aprenda a criar e gerir várias piscinas de nós para um cluster no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 02/14/2020
ms.openlocfilehash: 3e0890a0e8600526da2047cabc0b50af8177ea37
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78374514"
---
# <a name="create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>Crie e gerencie várias piscinas de nós para um cluster no Serviço Azure Kubernetes (AKS)

No Serviço Azure Kubernetes (AKS), os nódosos da mesma configuração são agrupados em *piscinas*de nó . Estas piscinas de nó contêm os VMs subjacentes que executam as suas aplicações. O número inicial de nós e o seu tamanho (SKU) são definidos quando se cria um cluster AKS, que cria uma piscina de *nós padrão*. Para suportar aplicações que tenham diferentes exigências de computação ou armazenamento, pode criar piscinas adicionais de nós. Por exemplo, utilize estes conjuntos de nós adicionais para fornecer GPUs para aplicações intensivas em cálculos ou acesso a armazenamento sSD de alto desempenho.

> [!NOTE]
> Esta funcionalidade permite um maior controlo sobre como criar e gerir várias piscinas de nós. Como resultado, são necessários comandos separados para criar/atualizar/eliminar. Anteriormente, as operações de cluster através `az aks create` ou `az aks update` utilizaram a API gerida do Cluster e foram a única opção para alterar o seu plano de controlo e uma única piscina de nó. Esta função expõe um conjunto de operação separado para piscinas de agentes através da API do agente Pool e exige a utilização do conjunto de comando `az aks nodepool` para executar operações numa piscina de nó individual.

Este artigo mostra-lhe como criar e gerir várias piscinas de nós num cluster AKS.

## <a name="before-you-begin"></a>Antes de começar

Precisa da versão Azure CLI 2.0.76 ou posteriormente instalada e configurada. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure][install-azure-cli].

## <a name="limitations"></a>Limitações

As seguintes limitações aplicam-se quando cria e gere clusters AKS que suportam múltiplos conjuntos de nós:

* Ver Quotas, restrições de tamanho de máquina virtual e disponibilidade da [região no Serviço Azure Kubernetes (AKS)][quotas-skus-regions].
* Não é possível eliminar o nó do sistema, por padrão, a primeira piscina de nós.
* O cluster AKS deve utilizar o equilibrador de carga SKU padrão para utilizar várias piscinas de nós, a funcionalidade não é suportada com equilibradores básicos de carga SKU.
* O cluster AKS deve utilizar conjuntos de escala de máquinas virtuais para os nós.
* O nome de uma piscina de nó só pode conter caracteres alfanuméricos minúsculos e deve começar com uma letra minúscula. Para piscinas de nó Linux o comprimento deve ser entre 1 e 12 caracteres, para piscinas de nó windows o comprimento deve ser entre 1 e 6 caracteres.
* Todas as piscinas de nós devem residir na mesma rede e subnet.
* Ao criar várias piscinas de nós no cluster criar tempo, todas as versões Kubernetes usadas por piscinas de nós devem corresponder à versão definida para o plano de controlo. Isto pode ser atualizado após o aglomerado ter sido provisionado utilizando operações de piscina de nó.

## <a name="create-an-aks-cluster"></a>Criar um cluster do AKS (Create an AKS cluster)

Para começar, crie um cluster AKS com uma única piscina de nó. O exemplo seguinte usa o [grupo AZ criar][az-group-create] comando para criar um grupo de recursos chamado *myResourceGroup* na região *oriental.* Um cluster AKS chamado *myAKSCluster* é então criado usando as [aks az criar][az-aks-create] comando. Uma *versão --kubernetes* de *1.15.7* é usada para mostrar como atualizar um conjunto de nó num passo seguinte. Pode especificar qualquer [versão Kubernetes suportada][supported-versions].

> [!NOTE]
> O equilíbrio de carga *Básico* SKU não é **suportado** quando se utilizam várias piscinas de nó. Por padrão, os clusters AKS são criados com o balanceador de carga *Standard* SKU do portal Azure CLI e Azure.

```azurecli-interactive
# Create a resource group in East US
az group create --name myResourceGroup --location eastus

# Create a basic single-node AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 2 \
    --generate-ssh-keys \
    --kubernetes-version 1.15.7 \
    --load-balancer-sku standard
```

A criação do cluster demora alguns minutos.

> [!NOTE]
> Para garantir que o seu cluster funciona de forma fiável, deve executar pelo menos 2 (dois) nós na piscina de nós padrão, uma vez que os serviços essenciais do sistema estão a correr através desta piscina de nós.

Quando o cluster estiver pronto, utilize o comando [az aks get-credentials][az-aks-get-credentials] para obter as credenciais de cluster para uso com `kubectl`:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-a-node-pool"></a>Adicione uma piscina de nó

O cluster criado no passo anterior tem uma única piscina de nó. Vamos adicionar uma segunda piscina de nó usando o [az aks nodepool adicionar][az-aks-nodepool-add] comando. O exemplo seguinte cria uma piscina de nó chamada *mynodepool* que corre *3* nosdes:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3 \
    --kubernetes-version 1.15.5
```

> [!NOTE]
> O nome de uma piscina de nó deve começar com uma letra minúscula e só pode conter caracteres alfanuméricos. Para piscinas de nó Linux o comprimento deve ser entre 1 e 12 caracteres, para piscinas de nó windows o comprimento deve ser entre 1 e 6 caracteres.

Para ver o estado das piscinas do seu nó, use o comando da lista de [piscinas az aks][az-aks-nodepool-list] e especifique o seu grupo de recursos e o nome do cluster:

```azurecli-interactive
az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster
```

A saída de exemplo seguinte mostra que a *mynodepool* foi criada com sucesso com três nódosos na piscina do nó. Quando o cluster AKS foi criado no passo anterior, um nó de *numpool1* padrão foi criado com uma contagem de nó de *2*.

```console
$ az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.5",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

> [!TIP]
> Se não for especificado nenhum *VmSize* quando adicionar uma piscina de nó, o tamanho padrão é *Standard_DS2_v3* para piscinas de nó do Windows e *Standard_DS2_v2* para piscinas de nó linux. Se não for especificada a *OrchestratorVersion,* a mesma versão do plano de controlo.

## <a name="upgrade-a-node-pool"></a>Atualizar uma piscina de nó

> [!NOTE]
> As operações de atualização e escala num cluster ou num conjunto de nósoloreos não podem ocorrer simultaneamente, se se tentar um erro ser devolvido. Em vez disso, cada tipo de funcionamento deve ser preenchido no recurso-alvo antes do próximo pedido sobre esse mesmo recurso. Leia mais sobre isso no nosso guia de resolução de [problemas.](https://aka.ms/aks-pending-upgrade)

Quando o seu cluster AKS foi inicialmente criado no primeiro passo, foi especificado um `--kubernetes-version` de *1.15.7.* Isto definiu a versão Kubernetes tanto para o plano de controlo como para a piscina de nós padrão. Os comandos nesta secção explicam como atualizar uma única piscina de nó específica.

A relação entre a atualização da versão Kubernetes do plano de controlo e a piscina do nó é explicada na [secção abaixo](#upgrade-a-cluster-control-plane-with-multiple-node-pools).

> [!NOTE]
> A versão de imagem osso do conjunto de nós está ligada à versão Kubernetes do cluster. Só irá obter upgrades de imagem de OS, após uma atualização de cluster.

Uma vez que há duas piscinas de nós neste exemplo, temos de usar o [upgrade de az aks nodepool][az-aks-nodepool-upgrade] para atualizar uma piscina de nós. Vamos atualizar a *miodepool* para Kubernetes *1.15.7*. Utilize o comando de upgrade de [nodepool az aks][az-aks-nodepool-upgrade] para atualizar a piscina do nó, como mostra o seguinte exemplo:

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version 1.15.7 \
    --no-wait
```

Enumere o estado das piscinas do seu nó novamente usando o comando da lista de [piscinas az aks.][az-aks-nodepool-list] O exemplo que se segue mostra que a *minodepool* está no estado de *modernização* para *1.15.7:*

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Upgrading",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Leva alguns minutos para atualizar os nódosos para a versão especificada.

Como uma boa prática, você deve atualizar todas as piscinas de nós em um cluster AKS para a mesma versão Kubernetes. O comportamento padrão da `az aks upgrade` é atualizar todas as piscinas de nós juntamente com o plano de controlo para alcançar este alinhamento. A capacidade de atualizar piscinas individuais de nós permite-lhe realizar uma atualização rolante e agendar cápsulas entre piscinas de nós para manter o tempo de up-up da aplicação dentro dos constrangimentos acima mencionados.

## <a name="upgrade-a-cluster-control-plane-with-multiple-node-pools"></a>Atualize um plano de controlo de cluster com várias piscinas de nós

> [!NOTE]
> A Kubernetes utiliza o esquema padrão de [versão semântica.](https://semver.org/) O número da versão é expresso como *x.y.z*, onde *x* é a versão principal, *y* é a versão menor, e *z* é a versão patch. Por exemplo, na versão *1.12.6*, 1 é a versão principal, 12 é a versão menor, e 6 é a versão patch. A versão Kubernetes do plano de controlo e a piscina inicial do nó são definidas durante a criação do cluster. Todos os nós adicionais têm a sua versão Kubernetes definida quando são adicionadas ao cluster. As versões Kubernetes podem diferir entre piscinas de nós, bem como entre uma piscina de nó e o plano de controlo.

Um cluster AKS tem dois objetos de recursos de cluster com versões Kubernetes associadas.

1. Uma versão kubernetes de plano de controlo de clusters.
2. Uma piscina de nó com uma versão Kubernetes.

Um plano de controlo mapeia para uma ou muitas piscinas de nó. O comportamento de uma operação de atualização depende do comando Azure CLI.

A atualização de um plano de controlo AKS requer a utilização de `az aks upgrade`. Isto melhora a versão do plano de controlo e todas as piscinas de nós no cluster. 

Emitir o comando `az aks upgrade` com a bandeira `--control-plane-only` atualiza apenas o plano de controlo de clusters. Nenhuma das piscinas de nós associadas no cluster é alterada.

A atualização das piscinas individuais do nó requer a utilização de `az aks nodepool upgrade`. Isto atualiza apenas o conjunto de nó alvo com a versão especificada kubernetes

### <a name="validation-rules-for-upgrades"></a>Regras de validação para upgrades

As atualizações válidas da Kubernetes para um plano de controlo e piscinas de nó de um cluster são validadas pelos seguintes conjuntos de regras.

* Regras para versões válidas para atualizar piscinas de nó:
   * A versão do piscina do nó deve ter a mesma versão *principal* do plano de controlo.
   * A versão *menor* do nó pool deve estar dentro de duas versões *menores* da versão de plano de controlo.
   * A versão do pool de nó não pode ser maior do que a versão `major.minor.patch` de controlo.

* Regras para a apresentação de uma operação de atualização:
   * Não é possível desvalorizar o plano de controlo ou uma versão kubernetes de piscina de nó.
   * Se não for especificada uma versão Kubernetes de piscina de nó, o comportamento depende da utilização do cliente. A declaração nos modelos do Gestor de Recursos recai para a versão existente definida para o conjunto do nó se utilizada, se nenhuma for definida, a versão do plano de controlo é usada para recorrer.
   * Você pode atualizar ou escalar um plano de controlo ou uma piscina de nó num dado momento, você não pode submeter múltiplas operações em um único plano de controlo ou recursos de piscina de nó simultaneamente.

## <a name="scale-a-node-pool-manually"></a>Escala rema da piscina manualmente

Como a sua carga horária de aplicação exige alterações, poderá ter de escalar o número de nós numa piscina de nós. O número de nós pode ser escalado para cima ou para baixo.

<!--If you scale down, nodes are carefully [cordoned and drained][kubernetes-drain] to minimize disruption to running applications.-->

Para escalar o número de nós numa piscina de nós, use o comando à escala de [piscina az aks.][az-aks-nodepool-scale] O exemplo seguinte elevou o número de nós na *miodepool* para *5:*

```azurecli-interactive
az aks nodepool scale \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 5 \
    --no-wait
```

Enumere o estado das piscinas do seu nó novamente usando o comando da lista de [piscinas az aks.][az-aks-nodepool-list] O exemplo que se segue mostra que a *miodepool* está no estado de *Escalacom* uma nova contagem de *5* nódosos:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Scaling",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Leva alguns minutos para a operação de escala ser concluída.

## <a name="scale-a-specific-node-pool-automatically-by-enabling-the-cluster-autoscaler"></a>Escala automaticamente uma piscina de nó específica, permitindo o autoescalador do cluster

O AKS oferece uma funcionalidade separada para escalar automaticamente piscinas de nós com uma funcionalidade chamada [autoscaler cluster](cluster-autoscaler.md). Esta funcionalidade pode ser ativada por piscina de nó com uma escala mínima e máxima única por piscina de nó. Aprenda a [utilizar o autoscaler](cluster-autoscaler.md#use-the-cluster-autoscaler-with-multiple-node-pools-enabled)de cluster por piscina de nó .

## <a name="delete-a-node-pool"></a>Apague uma piscina de nó

Se já não precisar de uma piscina, pode eliminá-la e remover os nós VM subjacentes. Para eliminar uma piscina de nó, utilize a [piscina az aks para apagar][az-aks-nodepool-delete] o comando e especificar o nome da piscina do nó. O exemplo seguinte elimina a *mynoodepool* criada nos passos anteriores:

> [!CAUTION]
> Não existem opções de recuperação para perda de dados que possam ocorrer quando eliminar um conjunto de nó. Se as cápsulas não puderem ser programadas noutras piscinas de nós, essas aplicações não estão disponíveis. Certifique-se de que não elimina um conjunto de nós quando as aplicações em uso não têm cópias de segurança de dados ou a capacidade de correr em outros conjuntos de nós no seu cluster.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name mynodepool --no-wait
```

A saída de exemplo a seguir do comando da lista de [piscinas az aks][az-aks-nodepool-list] mostra que *mynodepool* está no estado de *exclusão:*

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Deleting",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Leva alguns minutos para apagar os nódosos e a piscina do nó.

## <a name="specify-a-vm-size-for-a-node-pool"></a>Especifique um tamanho VM para uma piscina de nó

Nos exemplos anteriores para criar uma piscina de nó, um tamanho VM padrão foi usado para os nós criados no cluster. Um cenário mais comum é para você criar piscinas de nós com diferentes tamanhos e capacidades vm. Por exemplo, você pode criar um conjunto de nós que contém nós com grandes quantidades de CPU ou memória, ou um conjunto de nós que fornece suporte de GPU. No passo seguinte, [você usa manchas e tolerações](#schedule-pods-using-taints-and-tolerations) para dizer ao programador Kubernetes como limitar o acesso a casulos que podem funcionar nestes nós.

No exemplo seguinte, crie uma piscina de nó baseada em GPU que utilize o *tamanho Standard_NC6* VM. Estes VMs são alimentados pelo cartão NVIDIA Tesla K80. Para obter informações sobre os tamanhos vm disponíveis, consulte [Sizes para máquinas virtuais Linux em Azure][vm-sizes].

Crie uma piscina de nó usando a piscina de [nó az aks adicionar][az-aks-nodepool-add] comando novamente. Desta vez, especifique o nome *gpunodepool,* e utilize o parâmetro `--node-vm-size` para especificar o tamanho *Standard_NC6:*

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunodepool \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --no-wait
```

A saída de exemplo a seguir do comando da lista de [piscinas az aks][az-aks-nodepool-list] mostra que *o gpunodepool* está *a criar* nódosos com o *VmSize*especificado:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "gpunodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "vmSize": "Standard_NC6",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Leva alguns minutos para que a *piscina* seja criada com sucesso.

## <a name="schedule-pods-using-taints-and-tolerations"></a>Agendar cápsulas usando manchas e tolerâncias

Você agora tem duas piscinas de nós no seu cluster - a piscina de nós padrão inicialmente criada, e a piscina de nó baseada em GPU. Use o [kubectl obter][kubectl-get] comando de nós para ver os nós no seu aglomerado. A saída de exemplo a seguir mostra os nódosos:

```console
$ kubectl get nodes

NAME                                 STATUS   ROLES   AGE     VERSION
aks-gpunodepool-28993262-vmss000000  Ready    agent   4m22s   v1.15.7
aks-nodepool1-28993262-vmss000000    Ready    agent   115m    v1.15.7
```

O programador Kubernetes pode usar manchas e tolerâncias para restringir o que as cargas de trabalho podem ser executadas em nódosos.

* Uma **mancha** é aplicada a um nó que indica que apenas podem ser programadas cápsulas específicas.
* Uma **tolerância** é então aplicada a uma vagem que lhes permite *tolerar* a mancha de um nó.

Para obter mais informações sobre como usar funcionalidades agendadas avançadas da Kubernetes, consulte [as melhores práticas para funcionalidades avançadas de programadores em AKS][taints-tolerations]

Neste exemplo, aplique uma mancha no nó baseado em GPU usando o comando --nó-manchas. Especifique o nome do seu nó baseado em GPU a partir da saída do comando `kubectl get nodes` anterior. A mancha é aplicada como uma *chave:valor* e, em seguida, uma opção de agendamento. O exemplo seguinte utiliza o par *sku=gpu* e define as cápsulas de outra forma têm a capacidade *NoSchedule:*

```console
az aks nodepool add --node-taints aks-gpunodepool-28993262-vmss000000 sku=gpu:NoSchedule
```

O seguinte exemplo básico YAML manifesto usa uma tolerância para permitir que o programador Kubernetes execute uma cápsula NGINX no nó baseado em GPU. Para um exemplo mais adequado, mas intensivo no tempo, para executar um trabalho de Tensorflow contra o conjunto de dados MNIST, consulte [as GPUs de utilização para cargas de trabalho intensivas em matéria de cálculo no AKS][gpu-cluster].

Crie um ficheiro chamado `gpu-toleration.yaml` e copie no seguinte exemplo YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: nginx:1.15.9
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 1
        memory: 2G
  tolerations:
  - key: "sku"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
```

Agende a cápsula utilizando o comando `kubectl apply -f gpu-toleration.yaml`:

```console
kubectl apply -f gpu-toleration.yaml
```

Leva alguns segundos para agendar a cápsula e puxar a imagem NGINX. Utilize o comando de [cápsula de utilização kubectl][kubectl-describe] para ver o estado da cápsula. A saída de exemplo condensado seguinte mostra a toleração *sku=gpu:NoSchedule* é aplicada. Na secção de eventos, o programador atribuiu a cápsula ao nó *aks-gpunodepool-28993262-vmss0000000000com* GpU- based:

```console
$ kubectl describe pod mypod

[...]
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
                 sku=gpu:NoSchedule
Events:
  Type    Reason     Age    From                                          Message
  ----    ------     ----   ----                                          -------
  Normal  Scheduled  4m48s  default-scheduler                             Successfully assigned default/mypod to aks-gpunodepool-28993262-vmss000000
  Normal  Pulling    4m47s  kubelet, aks-gpunodepool-28993262-vmss000000  pulling image "nginx:1.15.9"
  Normal  Pulled     4m43s  kubelet, aks-gpunodepool-28993262-vmss000000  Successfully pulled image "nginx:1.15.9"
  Normal  Created    4m40s  kubelet, aks-gpunodepool-28993262-vmss000000  Created container
  Normal  Started    4m40s  kubelet, aks-gpunodepool-28993262-vmss000000  Started container
```

Apenas as cápsulas que tenham esta mancha aplicada podem ser programadas em nós em *gpunodepool*. Qualquer outra cápsula seria programada na piscina de nó de *nodepool1.* Se você criar piscinas adicionais de nós, você pode usar manchas adicionais e tolerações para limitar o que as cápsulas podem ser programadas nesses recursos do nó.

## <a name="specify-a-tag-for-a-node-pool"></a>Especifique uma etiqueta para uma piscina de nó

Você pode aplicar uma etiqueta Azure em piscinas de nós no seu cluster AKS. As etiquetas aplicadas a uma piscina de nó são aplicadas a cada nó dentro da piscina do nó e são persistidas através de upgrades. As etiquetas também são aplicadas a novos nódosos adicionados a uma piscina de nó durante as operações de escala. Adicionar uma etiqueta pode ajudar com tarefas como o rastreio de políticas ou a estimativa de custos.

> [!IMPORTANT]
> Para utilizar etiquetas de piscina de nó, necessita da versão de extensão CLI de *pré-visualização* de aks 0.4.29 ou superior. Instale a extensão Azure CLI de *pré-visualização de aks* utilizando o comando de adição de [extensão az][az-extension-add] e, em seguida, verifique se há atualizações disponíveis utilizando o comando de atualização de [extensão az:][az-extension-update]
> 
> ```azurecli-interactive
> # Install the aks-preview extension
> az extension add --name aks-preview
> 
> # Update the extension to make sure you have the latest version installed
> az extension update --name aks-preview
> ```

Crie uma piscina de nó usando a piscina de [nó az aks adicionar][az-aks-nodepool-add]. Especifique o nome *tagnodepool* e utilize o parâmetro `--tag` para especificar *dept=IT* e *costcenter=9999* para etiquetas.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name tagnodepool \
    --node-count 1 \
    --tags dept=IT costcenter=9999 \
    --no-wait
```

> [!NOTE]
> Também pode utilizar o parâmetro `--tags` quando utilizar o comando de [atualização az aks nodepool,][az-aks-nodepool-update] bem como durante a criação do cluster. Durante a criação do cluster, o parâmetro `--tags` aplica a etiqueta à piscina de nó inicial criada com o cluster. Todos os nomes de etiquetas devem aderir às limitações nas [etiquetas de utilização para organizar os seus recursos Azure][tag-limitation]. Atualizar um conjunto de nó com o parâmetro `--tags` atualiza quaisquer valores de etiqueta existentes e anexa quaisquer novas etiquetas. Por exemplo, se o seu conjunto de nós tivesse *dept=IT* e *costcenter=9999* para tags e o atualizou com *team=dev* e *costcenter=111* para tags, você nodepool teria *dept=IT,* *costcenter=111*, e *team=dev* para tags.

A saída de exemplo a seguir do comando da [lista de nodepool az aks][az-aks-nodepool-list] mostra que *tagnodepool* está *criando* nódosos com a *etiqueta*especificada:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "tagnodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "tags": {
      "dept": "IT",
      "costcenter": "9999"
    },
    ...
  },
 ...
]
```

## <a name="manage-node-pools-using-a-resource-manager-template"></a>Gerir piscinas de nó usando um modelo de Gestor de Recursos

Quando utiliza um modelo de Gestor de Recursos Azure para criar e gerir recursos, normalmente pode atualizar as definições do seu modelo e recolocar para atualizar o recurso. Com piscinas de nós em AKS, o perfil inicial do pool do nó não pode ser atualizado uma vez que o cluster AKS foi criado. Este comportamento significa que não pode atualizar um modelo de Gestor de Recursos existente, fazer uma alteração nas piscinas do nó e reimplantar. Em vez disso, você deve criar um modelo separado de Gestor de Recursos que atualiza apenas as piscinas de nós para um cluster AKS existente.

Crie um modelo como `aks-agentpools.json` e cola o seguinte manifesto exemplo. Este modelo de exemplo configura as seguintes definições:

* Atualiza a piscina de *nólino Linux* chamada *myagentpool* para executar três nódosos.
* Define os nódosos na piscina do nó para executar a versão Kubernetes *1.15.7*.
* Define o tamanho do nó como *Standard_DS2_v2*.

Editar estes valores como necessário para atualizar, adicionar ou eliminar piscinas de nós conforme necessário:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "clusterName": {
            "type": "string",
            "metadata": {
                "description": "The name of your existing AKS cluster."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "The location of your existing AKS cluster."
            }
        },
        "agentPoolName": {
            "type": "string",
            "defaultValue": "myagentpool",
            "metadata": {
                "description": "The name of the agent pool to create or update."
            }
        },
        "vnetSubnetId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The Vnet subnet resource ID for your existing AKS cluster."
            }
        }
    },
    "variables": {
        "apiVersion": {
            "aks": "2020-01-01"
        },
        "agentPoolProfiles": {
            "maxPods": 30,
            "osDiskSizeGB": 0,
            "agentCount": 3,
            "agentVmSize": "Standard_DS2_v2",
            "osType": "Linux",
            "vnetSubnetId": "[parameters('vnetSubnetId')]"
        }
    },
    "resources": [
        {
            "apiVersion": "2020-01-01",
            "type": "Microsoft.ContainerService/managedClusters/agentPools",
            "name": "[concat(parameters('clusterName'),'/', parameters('agentPoolName'))]",
            "location": "[parameters('location')]",
            "properties": {
                "maxPods": "[variables('agentPoolProfiles').maxPods]",
                "osDiskSizeGB": "[variables('agentPoolProfiles').osDiskSizeGB]",
                "count": "[variables('agentPoolProfiles').agentCount]",
                "vmSize": "[variables('agentPoolProfiles').agentVmSize]",
                "osType": "[variables('agentPoolProfiles').osType]",
                "storageProfile": "ManagedDisks",
                "type": "VirtualMachineScaleSets",
                "vnetSubnetID": "[variables('agentPoolProfiles').vnetSubnetId]",
                "orchestratorVersion": "1.15.7"
            }
        }
    ]
}
```

Desloque este modelo utilizando a implementação do [grupo Az criar][az-group-deployment-create] comando, como mostra o seguinte exemplo. Você é solicitado para o nome e localização do cluster AKS existente:

```azurecli-interactive
az group deployment create \
    --resource-group myResourceGroup \
    --template-file aks-agentpools.json
```

> [!TIP]
> Você pode adicionar uma etiqueta à sua piscina de nó adicionando a propriedade de *etiqueta* no modelo, como mostra o seguinte exemplo.
> 
> ```json
> ...
> "resources": [
> {
>   ...
>   "properties": {
>     ...
>     "tags": {
>       "name1": "val1"
>     },
>     ...
>   }
> }
> ...
> ```

Pode levar alguns minutos para atualizar o seu cluster AKS dependendo das configurações e operações do conjunto de nós que define no seu modelo de Gestor de Recursos.

## <a name="assign-a-public-ip-per-node-in-a-node-pool"></a>Atribuir um IP público por nó em uma piscina de nó

> [!WARNING]
> Durante a pré-visualização da atribuição de um IP público por nó, não pode ser utilizado com o *SKU standard Load Balancer em AKS* devido a possíveis regras de equilíbrio de carga em conflito com o provisionamento de VM. Como resultado desta limitação, as piscinas de agentes do Windows não são suportadas com esta funcionalidade de pré-visualização. Durante a pré-visualização, deve utilizar o *SKU* de Equilíbrio de Carga Básico se precisar de atribuir um IP público por nó.

Os nódosos AKS não requerem os seus próprios endereços IP públicos para comunicação. No entanto, alguns cenários podem exigir que nós numa piscina de nós tenham os seus próprios endereços IP públicos. Um exemplo é o jogo, onde uma consola precisa fazer uma ligação direta a uma máquina virtual em nuvem para minimizar o lúpulo. Isto pode ser conseguido registando-se para uma função de pré-visualização separada, Node Public IP (pré-visualização).

```azurecli-interactive
az feature register --name NodePublicIPPreview --namespace Microsoft.ContainerService
```

Após o registo bem sucedido, implemente um modelo de Gestor de Recursos Azure seguindo as mesmas instruções [que acima](#manage-node-pools-using-a-resource-manager-template) e adicione a propriedade de valor booleano `enableNodePublicIP` ao agentePoolProfiles. Detete o valor para `true` como por defeito, é definido como `false` se não especificado. Trata-se de uma propriedade apenas para criar tempo e requer uma versão API mínima de 2019-06-01. Isto pode ser aplicado tanto nas piscinas de nó linux como no Windows.

## <a name="clean-up-resources"></a>Limpar recursos

Neste artigo, criou um cluster AKS que inclui nós baseados em GPU. Para reduzir custos desnecessários, pode querer eliminar o *gpunodepool*, ou todo o cluster AKS.

Para eliminar o conjunto de nósolos baseado em GPU, utilize o [az aks nodepool eliminar][az-aks-nodepool-delete] o comando como mostrado no exemplo seguinte:

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name gpunodepool
```

Para eliminar o aglomerado em si, utilize o [grupo Az eliminar][az-group-delete] o comando para eliminar o grupo de recursos AKS:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a criar e gerir várias piscinas de nós num cluster AKS. Para obter mais informações sobre como controlar as cápsulas através de piscinas de nós, consulte [as melhores práticas para funcionalidades avançadas de programadores em AKS][operator-best-practices-advanced-scheduler].

Para criar e utilizar piscinas de nó de contentores do Windows Server, consulte [Criar um recipiente de servidor windows em AKS][aks-windows].

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- INTERNAL LINKS -->
[aks-windows]: windows-container-cli.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-list
[az-aks-nodepool-update]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-update
[az-aks-nodepool-upgrade]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-delete
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[gpu-cluster]: gpu-cluster.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[quotas-skus-regions]: quotas-skus-regions.md
[supported-versions]: supported-kubernetes-versions.md
[tag-limitation]: ../azure-resource-manager/resource-group-using-tags.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/linux/sizes.md