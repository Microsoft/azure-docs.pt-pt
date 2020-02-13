---
title: Usar vários pools de nó no serviço kubernetes do Azure (AKS)
description: Saiba como criar e gerenciar vários pools de nós para um cluster no serviço kubernetes do Azure (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 01/22/2020
ms.author: mlearned
ms.openlocfilehash: bbfb65c31bf6fd46cc18c9eee66086afbbff1d5f
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77157979"
---
# <a name="create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>Criar e gerenciar vários pools de nós para um cluster no serviço de kubernetes do Azure (AKS)

No Serviço Azure Kubernetes (AKS), os nódosos da mesma configuração são agrupados em *piscinas*de nó . Esses pools de nós contêm as VMs subjacentes que executam seus aplicativos. O número inicial de nós e o seu tamanho (SKU) são definidos quando se cria um cluster AKS, que cria uma piscina de *nós padrão*. Para dar suporte a aplicativos que têm demandas de armazenamento ou de computação diferentes, você pode criar pools de nós adicionais. Por exemplo, use esses pools de nós adicionais para fornecer GPUs para aplicativos de computação intensiva ou acesso ao armazenamento SSD de alto desempenho.

> [!NOTE]
> Esse recurso permite maior controle sobre como criar e gerenciar vários pools de nós. Como resultado, comandos separados são necessários para criar/atualizar/excluir. Anteriormente, as operações de cluster através `az aks create` ou `az aks update` utilizaram a API gerida do Cluster e foram a única opção para alterar o seu plano de controlo e uma única piscina de nó. Esta função expõe um conjunto de operação separado para piscinas de agentes através da API do agente Pool e exige a utilização do conjunto de comando `az aks nodepool` para executar operações numa piscina de nó individual.

Este artigo mostra como criar e gerenciar vários pools de nós em um cluster AKS.

## <a name="before-you-begin"></a>Antes de começar

Você precisa do CLI do Azure versão 2.0.76 ou posterior instalado e configurado. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure][install-azure-cli].

## <a name="limitations"></a>Limitações

As seguintes limitações aplicam-se quando cria e gere clusters AKS que suportam múltiplos conjuntos de nós:

* Ver Quotas, restrições de tamanho de máquina virtual e disponibilidade da [região no Serviço Azure Kubernetes (AKS)][quotas-skus-regions].
* Não é possível excluir o pool de nós padrão (primeiro).
* O add-on de encaminhamento de aplicações HTTP não pode ser utilizado.
* O cluster AKS deve usar o balanceador de carga SKU padrão para usar vários pools de nós, o recurso não tem suporte com balanceadores de carga de SKU básicos.
* O cluster AKS deve usar conjuntos de dimensionamento de máquinas virtuais para os nós.
* O nome de uma piscina de nó só pode conter caracteres alfanuméricos minúsculos e deve começar com uma letra minúscula. Para pools de nós do Linux, o comprimento deve ter entre 1 e 12 caracteres, para pools de nó do Windows o comprimento deve ter entre 1 e 6 caracteres.
* Todas as piscinas de nós devem residir na mesma rede e subnet.
* Ao criar várias piscinas de nós no cluster criar tempo, todas as versões Kubernetes usadas por piscinas de nós devem corresponder à versão definida para o plano de controlo. Isto pode ser atualizado após o aglomerado ter sido provisionado utilizando operações de piscina de nó.

## <a name="create-an-aks-cluster"></a>Criar um cluster do AKS (Create an AKS cluster)

Para começar, crie um cluster AKS com um único pool de nós. O exemplo seguinte usa o [grupo AZ criar][az-group-create] comando para criar um grupo de recursos chamado *myResourceGroup* na região *oriental.* Um cluster AKS chamado *myAKSCluster* é então criado usando as [aks az criar][az-aks-create] comando. Uma *versão --kubernetes* de *1.15.7* é usada para mostrar como atualizar um conjunto de nó num passo seguinte. Pode especificar qualquer [versão Kubernetes suportada][supported-versions].

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
> Para garantir que o cluster opere de forma confiável, você deve executar pelo menos 2 (dois) nós no pool de nós padrão, pois os serviços de sistema essenciais estão sendo executados nesse pool de nós.

Quando o cluster estiver pronto, utilize o comando [az aks get-credentials][az-aks-get-credentials] para obter as credenciais de cluster para uso com `kubectl`:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-a-node-pool"></a>Adicionar um pool de nós

O cluster criado na etapa anterior tem um único pool de nós. Vamos adicionar uma segunda piscina de nó usando o [az aks nodepool adicionar][az-aks-nodepool-add] comando. O exemplo seguinte cria uma piscina de nó chamada *mynodepool* que corre *3* nosdes:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3 \
    --kubernetes-version 1.15.5
```

> [!NOTE]
> O nome de um pool de nós deve começar com uma letra minúscula e só pode conter caracteres alfanuméricos. Para pools de nós do Linux, o comprimento deve ter entre 1 e 12 caracteres, para pools de nó do Windows o comprimento deve ter entre 1 e 6 caracteres.

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

## <a name="upgrade-a-node-pool"></a>Atualizar um pool de nós

> [!NOTE]
> As operações de atualização e dimensionamento em um cluster ou pool de nós não podem ocorrer simultaneamente, se uma tentativa de erro for retornada. Em vez disso, cada tipo de funcionamento deve ser preenchido no recurso-alvo antes do próximo pedido sobre esse mesmo recurso. Leia mais sobre isso no nosso guia de resolução de [problemas.](https://aka.ms/aks-pending-upgrade)

Quando o seu cluster AKS foi inicialmente criado no primeiro passo, foi especificado um `--kubernetes-version` de *1.15.7.* Isso define a versão kubernetes para o plano de controle e o pool de nós padrão. Os comandos nesta seção explicam como atualizar um único pool de nós específico.

A relação entre a atualização da versão Kubernetes do plano de controlo e a piscina do nó é explicada na [secção abaixo](#upgrade-a-cluster-control-plane-with-multiple-node-pools).

> [!NOTE]
> A versão da imagem do sistema operacional do pool de nós está vinculada à versão kubernetes do cluster. Você só obterá atualizações de imagem do sistema operacional, seguindo uma atualização de cluster.

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

Leva alguns minutos para atualizar os nós para a versão especificada.

Como prática recomendada, você deve atualizar todos os pools de nós em um cluster AKS para a mesma versão kubernetes. O comportamento padrão da `az aks upgrade` é atualizar todas as piscinas de nós juntamente com o plano de controlo para alcançar este alinhamento. A capacidade de atualizar pools de nós individuais permite executar uma atualização sem interrupção e agendar pods entre pools de nós para manter o tempo de atividade do aplicativo dentro das restrições acima mencionadas.

## <a name="upgrade-a-cluster-control-plane-with-multiple-node-pools"></a>Atualizar um plano de controle de cluster com vários pools de nós

> [!NOTE]
> A Kubernetes utiliza o esquema padrão de [versão semântica.](https://semver.org/) O número da versão é expresso como *x.y.z*, onde *x* é a versão principal, *y* é a versão menor, e *z* é a versão patch. Por exemplo, na versão *1.12.6*, 1 é a versão principal, 12 é a versão menor, e 6 é a versão patch. A versão Kubernetes do plano de controlo e a piscina inicial do nó são definidas durante a criação do cluster. Todos os pools de nós adicionais têm sua versão kubernetes definida quando são adicionados ao cluster. As versões Kubernetes podem diferir entre piscinas de nós, bem como entre uma piscina de nó e o plano de controlo.

Um cluster AKS tem dois objetos de recurso de cluster com versões do kubernetes associadas.

1. Uma versão kubernetes de plano de controlo de clusters.
2. Uma piscina de nó com uma versão Kubernetes.

Um plano de controle é mapeado para um ou vários pools de nós. O comportamento de uma operação de atualização depende de qual CLI do Azure comando é usado.

A atualização de um plano de controlo AKS requer a utilização de `az aks upgrade`. Isto melhora a versão do plano de controlo e todas as piscinas de nós no cluster. 

Emitir o comando `az aks upgrade` com a bandeira `--control-plane-only` atualiza apenas o plano de controlo de clusters. Nenhuma das piscinas de nós associadas no cluster é alterada.

A atualização das piscinas individuais do nó requer a utilização de `az aks nodepool upgrade`. Isso atualiza somente o pool de nós de destino com a versão especificada do kubernetes

### <a name="validation-rules-for-upgrades"></a>Regras de validação para upgrades

As atualizações válidas da Kubernetes para um plano de controlo e piscinas de nó de um cluster são validadas pelos seguintes conjuntos de regras.

* Regras para versões válidas para atualizar piscinas de nó:
   * A versão do piscina do nó deve ter a mesma versão *principal* do plano de controlo.
   * A versão *menor* do nó pool deve estar dentro de duas versões *menores* da versão de plano de controlo.
   * A versão do pool de nó não pode ser maior do que a versão `major.minor.patch` de controlo.

* Regras para a apresentação de uma operação de atualização:
   * Não é possível desvalorizar o plano de controlo ou uma versão kubernetes de piscina de nó.
   * Se uma versão de kubernetes do pool de nós não for especificada, o comportamento dependerá do cliente que está sendo usado. A declaração nos modelos do Gestor de Recursos recai para a versão existente definida para o conjunto do nó se utilizada, se nenhuma for definida, a versão do plano de controlo é usada para recorrer.
   * Você pode atualizar ou escalar um plano de controlo ou uma piscina de nó num dado momento, você não pode submeter múltiplas operações em um único plano de controlo ou recursos de piscina de nó simultaneamente.

## <a name="scale-a-node-pool-manually"></a>Dimensionar um pool de nós manualmente

À medida que as demandas de carga de trabalho do aplicativo mudam, talvez seja necessário dimensionar o número de nós em um pool de nós. O número de nós pode ser aumentado ou reduzido verticalmente.

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

Leva alguns minutos para que a operação de dimensionamento seja concluída.

## <a name="scale-a-specific-node-pool-automatically-by-enabling-the-cluster-autoscaler"></a>Dimensionar um pool de nós específico automaticamente habilitando o dimensionador automático do cluster

O AKS oferece uma funcionalidade separada para escalar automaticamente piscinas de nós com uma funcionalidade chamada [autoscaler cluster](cluster-autoscaler.md). Esse recurso pode ser habilitado por pool de nós com contagens de escala mínima e máxima exclusivas por pool de nós. Aprenda a [utilizar o autoscaler](cluster-autoscaler.md#use-the-cluster-autoscaler-with-multiple-node-pools-enabled)de cluster por piscina de nó .

## <a name="delete-a-node-pool"></a>Excluir um pool de nós

Se você não precisar mais de um pool, poderá excluí-lo e remover os nós de VM subjacentes. Para eliminar uma piscina de nó, utilize a [piscina az aks para apagar][az-aks-nodepool-delete] o comando e especificar o nome da piscina do nó. O exemplo seguinte elimina a *mynoodepool* criada nos passos anteriores:

> [!CAUTION]
> Não há opções de recuperação para perda de dados que podem ocorrer quando você exclui um pool de nós. Se os pods não puderem ser agendados em outros pools de nós, esses aplicativos não estarão disponíveis. Certifique-se de não excluir um pool de nós quando aplicativos em uso não tiverem backups de dados ou a capacidade de executar em outros pools de nós no cluster.

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

Leva alguns minutos para excluir os nós e o pool de nós.

## <a name="specify-a-vm-size-for-a-node-pool"></a>Especificar um tamanho de VM para um pool de nós

Nos exemplos anteriores para criar um pool de nós, um tamanho de VM padrão foi usado para os nós criados no cluster. Um cenário mais comum é criar pools de nós com diferentes tamanhos e recursos de VM. Por exemplo, você pode criar um pool de nós que contém nós com grandes quantidades de CPU ou memória, ou um pool de nós que fornece suporte à GPU. No passo seguinte, [você usa manchas e tolerações](#schedule-pods-using-taints-and-tolerations) para dizer ao programador Kubernetes como limitar o acesso a casulos que podem funcionar nestes nós.

No exemplo seguinte, crie uma piscina de nó baseada em GPU que utilize o *tamanho Standard_NC6* VM. Essas VMs são alimentadas pelo cartão NVIDIA Tesla K80. Para obter informações sobre os tamanhos vm disponíveis, consulte [Sizes para máquinas virtuais Linux em Azure][vm-sizes].

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

## <a name="schedule-pods-using-taints-and-tolerations"></a>Agendar pods usando os e Tolerations

Agora você tem dois pools de nós no cluster – o pool de nós padrão inicialmente criado e o pool de nós baseado em GPU. Use o [kubectl obter][kubectl-get] comando de nós para ver os nós no seu aglomerado. A saída de exemplo a seguir mostra os nós:

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

O manifesto YAML de exemplo básico a seguir usa um toleration para permitir que o Agendador de kubernetes execute um pod NGINX no nó baseado em GPU. Para um exemplo mais adequado, mas intensivo no tempo, para executar um trabalho de Tensorflow contra o conjunto de dados MNIST, consulte [as GPUs de utilização para cargas de trabalho intensivas em matéria de cálculo no AKS][gpu-cluster].

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

Leva alguns segundos para agendar o pod e efetuar pull da imagem NGINX. Utilize o comando de [cápsula de utilização kubectl][kubectl-describe] para ver o estado da cápsula. A saída de exemplo condensado seguinte mostra a toleração *sku=gpu:NoSchedule* é aplicada. Na secção de eventos, o programador atribuiu a cápsula ao nó *aks-gpunodepool-28993262-vmss0000000000com* GpU- based:

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

Apenas as cápsulas que tenham esta mancha aplicada podem ser programadas em nós em *gpunodepool*. Qualquer outra cápsula seria programada na piscina de nó de *nodepool1.* Se você criar pools de nós adicionais, poderá usar os conteúdo e os Tolerations adicionais para limitar o que os pods podem ser agendados nesses recursos de nó.

## <a name="manage-node-pools-using-a-resource-manager-template"></a>Gerenciar pools de nós usando um modelo do Resource Manager

Ao usar um modelo de Azure Resource Manager para criar e gerenciar recursos, você normalmente pode atualizar as configurações em seu modelo e reimplantar para atualizar o recurso. Com pools de nós no AKS, o perfil do pool de nós inicial não pode ser atualizado depois que o cluster AKS tiver sido criado. Esse comportamento significa que você não pode atualizar um modelo existente do Resource Manager, fazer uma alteração nos pools de nós e reimplantar. Em vez disso, você deve criar um modelo separado de Gestor de Recursos que atualiza apenas as piscinas de nós para um cluster AKS existente.

Crie um modelo como `aks-agentpools.json` e cola o seguinte manifesto exemplo. Este modelo de exemplo define as seguintes configurações:

* Atualiza a piscina de *nólino Linux* chamada *myagentpool* para executar três nódosos.
* Define os nódosos na piscina do nó para executar a versão Kubernetes *1.15.7*.
* Define o tamanho do nó como *Standard_DS2_v2*.

Edite esses valores conforme necessário para atualizar, adicionar ou excluir pools de nós conforme necessário:

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

Desloque este modelo utilizando a implementação do [grupo Az criar][az-group-deployment-create] comando, como mostra o seguinte exemplo. O nome e o local do cluster AKS existentes serão solicitados:

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

Pode levar alguns minutos para atualizar o cluster AKS dependendo das configurações do pool de nós e das operações definidas no modelo do Resource Manager.

## <a name="assign-a-public-ip-per-node-in-a-node-pool"></a>Atribuir um IP público por nó em um pool de nós

> [!WARNING]
> Durante a pré-visualização da atribuição de um IP público por nó, não pode ser utilizado com o *SKU standard Load Balancer em AKS* devido a possíveis regras de equilíbrio de carga em conflito com o provisionamento de VM. Como resultado desta limitação, as piscinas de agentes do Windows não são suportadas com esta funcionalidade de pré-visualização. Durante a pré-visualização, deve utilizar o *SKU* de Equilíbrio de Carga Básico se precisar de atribuir um IP público por nó.

Os nós AKS não exigem seus próprios endereços IP públicos para comunicação. No entanto, alguns cenários podem exigir que os nós em um pool de nós tenham seus próprios endereços IP públicos. Um exemplo é o jogo, onde um console do precisa fazer uma conexão direta com uma máquina virtual de nuvem para minimizar os saltos. Isso pode ser feito registrando-se para um recurso de visualização separado, o IP público do nó (versão prévia).

```azurecli-interactive
az feature register --name NodePublicIPPreview --namespace Microsoft.ContainerService
```

Após o registo bem sucedido, implemente um modelo de Gestor de Recursos Azure seguindo as mesmas instruções [que acima](#manage-node-pools-using-a-resource-manager-template) e adicione a propriedade de valor booleano `enableNodePublicIP` ao agentePoolProfiles. Detete o valor para `true` como por defeito, é definido como `false` se não especificado. Esta é uma propriedade somente de tempo de criação e requer uma versão de API mínima de 2019-06-01. Isso pode ser aplicado a pools de nós do Linux e do Windows.

## <a name="clean-up-resources"></a>Limpar recursos

Neste artigo, você criou um cluster AKS que inclui nós baseados em GPU. Para reduzir custos desnecessários, pode querer eliminar o *gpunodepool*, ou todo o cluster AKS.

Para eliminar o conjunto de nósolos baseado em GPU, utilize o [az aks nodepool eliminar][az-aks-nodepool-delete] o comando como mostrado no exemplo seguinte:

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name gpunodepool
```

Para eliminar o aglomerado em si, utilize o [grupo Az eliminar][az-group-delete] o comando para eliminar o grupo de recursos AKS:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu a criar e gerenciar vários pools de nós em um cluster AKS. Para obter mais informações sobre como controlar as cápsulas através de piscinas de nós, consulte [as melhores práticas para funcionalidades avançadas de programadores em AKS][operator-best-practices-advanced-scheduler].

Para criar e utilizar piscinas de nó de contentores do Windows Server, consulte [Criar um recipiente de servidor windows em AKS][aks-windows].

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- INTERNAL LINKS -->
[quotas-skus-regions]: quotas-skus-regions.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-nodepool-add]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-list
[az-aks-nodepool-upgrade]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-delete
[vm-sizes]: ../virtual-machines/linux/sizes.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[gpu-cluster]: gpu-cluster.md
[az-group-delete]: /cli/azure/group#az-group-delete
[install-azure-cli]: /cli/azure/install-azure-cli
[supported-versions]: supported-kubernetes-versions.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-windows]: windows-container-cli.md
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
