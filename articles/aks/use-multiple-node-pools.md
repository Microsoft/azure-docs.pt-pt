---
title: Usar vários pools de nó no serviço kubernetes do Azure (AKS)
description: Saiba como criar e gerenciar vários pools de nós para um cluster no serviço kubernetes do Azure (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 01/22/2020
ms.author: mlearned
ms.openlocfilehash: 6ea1bce6c14d7266b5ce49b94e39d661bfc57717
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76713305"
---
# <a name="create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>Criar e gerenciar vários pools de nós para um cluster no serviço de kubernetes do Azure (AKS)

No AKS (serviço kubernetes do Azure), os nós da mesma configuração são agrupados em *pools de nós*. Esses pools de nós contêm as VMs subjacentes que executam seus aplicativos. O número inicial de nós e seu tamanho (SKU) são definidos quando você cria um cluster AKS, que cria um *pool de nós padrão*. Para dar suporte a aplicativos que têm demandas de armazenamento ou de computação diferentes, você pode criar pools de nós adicionais. Por exemplo, use esses pools de nós adicionais para fornecer GPUs para aplicativos de computação intensiva ou acesso ao armazenamento SSD de alto desempenho.

> [!NOTE]
> Esse recurso permite maior controle sobre como criar e gerenciar vários pools de nós. Como resultado, comandos separados são necessários para criar/atualizar/excluir. Anteriormente, as operações de cluster por meio de `az aks create` ou `az aks update` usavam a API managedCluster e eram a única opção para alterar o plano de controle e um único pool de nós. Esse recurso expõe uma operação separada definida para pools de agente por meio da API agentPool e requer o uso do comando `az aks nodepool` definido para executar operações em um pool de nós individual.

Este artigo mostra como criar e gerenciar vários pools de nós em um cluster AKS.

## <a name="before-you-begin"></a>Antes de começar

Você precisa do CLI do Azure versão 2.0.76 ou posterior instalado e configurado. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure][install-azure-cli].

## <a name="limitations"></a>Limitações

As seguintes limitações se aplicam quando você cria e gerencia clusters AKS que dão suporte a vários pools de nós:

* Não é possível excluir o pool de nós padrão (primeiro).
* O complemento de roteamento de aplicativo HTTP não pode ser usado.
* O cluster AKS deve usar o balanceador de carga SKU padrão para usar vários pools de nós, o recurso não tem suporte com balanceadores de carga de SKU básicos.
* O cluster AKS deve usar conjuntos de dimensionamento de máquinas virtuais para os nós.
* O nome de um pool de nós pode conter apenas caracteres alfanuméricos minúsculos e deve começar com uma letra minúscula. Para pools de nós do Linux, o comprimento deve ter entre 1 e 12 caracteres, para pools de nó do Windows o comprimento deve ter entre 1 e 6 caracteres.
* O cluster AKS pode ter um máximo de 10 pools de nós.
* O cluster AKS pode ter um máximo de 1000 nós entre esses 10 pools de nós.
* Todos os pools de nós devem residir na mesma vnet e sub-rede.
* Ao criar vários pools de nós no momento da criação do cluster, todas as versões do kubernetes usadas por pools de nós devem corresponder à versão definida para o plano de controle. Isso pode ser atualizado depois que o cluster tiver sido provisionado usando operações de pool por nó.

## <a name="create-an-aks-cluster"></a>Criar um cluster do AKS (Create an AKS cluster)

Para começar, crie um cluster AKS com um único pool de nós. O exemplo a seguir usa o comando [AZ Group Create][az-group-create] para criar um grupo de recursos chamado *MyResource* Group na região *eastus* . Um cluster AKS chamado *myAKSCluster* é então criado usando o comando [AZ AKs Create][az-aks-create] . A *--kubernetes-Version* de *1.15.7* é usada para mostrar como atualizar um pool de nós em uma etapa seguinte. Você pode especificar qualquer [versão do kubernetes com suporte][supported-versions].

> [!NOTE]
> **Não há suporte para** o SKU do Load Balancer *básico* ao usar vários pools de nós. Por padrão, os clusters AKS são criados com o SKU do Load Balancer *Standard* de CLI do Azure e portal do Azure.

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

Quando o cluster estiver pronto, use o comando [AZ AKs Get-Credentials][az-aks-get-credentials] para obter as credenciais de cluster para uso com `kubectl`:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-a-node-pool"></a>Adicionar um pool de nós

O cluster criado na etapa anterior tem um único pool de nós. Vamos adicionar um segundo pool de nós usando o comando [AZ AKs nodepool Add][az-aks-nodepool-add] . O exemplo a seguir cria um pool de nós chamado *mynodepool* que executa *3* nós:

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

Para ver o status dos pools de nós, use o comando [AZ AKs node pool List][az-aks-nodepool-list] e especifique o grupo de recursos e o nome do cluster:

```azurecli-interactive
az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster
```

A saída de exemplo a seguir mostra que *mynodepool* foi criado com êxito com três nós no pool de nós. Quando o cluster AKS foi criado na etapa anterior, um *nodepool1* padrão foi criado com uma contagem de nós de *2*.

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
> Se não for especificado nenhum *VmSize* quando adicionar uma piscina de nó, o tamanho padrão é *Standard_DS2_v3* para piscinas de nó do Windows e *Standard_DS2_v2* para piscinas de nó linux. Se nenhum *OrchestratorVersion* for especificado, o padrão será a mesma versão do plano de controle.

## <a name="upgrade-a-node-pool"></a>Atualizar um pool de nós

> [!NOTE]
> As operações de atualização e dimensionamento em um cluster ou pool de nós não podem ocorrer simultaneamente, se uma tentativa de erro for retornada. Em vez disso, cada tipo de operação deve ser concluído no recurso de destino antes da próxima solicitação no mesmo recurso. Leia mais sobre isso em nosso [Guia de solução de problemas](https://aka.ms/aks-pending-upgrade).

Quando o cluster AKS foi inicialmente criado na primeira etapa, um `--kubernetes-version` de *1.15.7* foi especificado. Isso define a versão kubernetes para o plano de controle e o pool de nós padrão. Os comandos nesta seção explicam como atualizar um único pool de nós específico.

A relação entre a atualização da versão kubernetes do plano de controle e o pool de nós é explicada na [seção abaixo](#upgrade-a-cluster-control-plane-with-multiple-node-pools).

> [!NOTE]
> A versão da imagem do sistema operacional do pool de nós está vinculada à versão kubernetes do cluster. Você só obterá atualizações de imagem do sistema operacional, seguindo uma atualização de cluster.

Como há dois pools de nós neste exemplo, devemos usar [AZ AKs nodepool upgrade][az-aks-nodepool-upgrade] para atualizar um pool de nós. Vamos atualizar o *mynodepool* para kubernetes *1.15.7*. Use o comando [AZ AKs nodepool upgrade][az-aks-nodepool-upgrade] para atualizar o pool de nós, conforme mostrado no exemplo a seguir:

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version 1.15.7 \
    --no-wait
```

Liste o status dos pools de nós novamente usando o comando [AZ AKs node pool List][az-aks-nodepool-list] . O exemplo a seguir mostra que *mynodepool* está no estado *atualizando* para *1.15.7*:

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

Como prática recomendada, você deve atualizar todos os pools de nós em um cluster AKS para a mesma versão kubernetes. O comportamento padrão de `az aks upgrade` é atualizar todos os pools de nós junto com o plano de controle para alcançar esse alinhamento. A capacidade de atualizar pools de nós individuais permite executar uma atualização sem interrupção e agendar pods entre pools de nós para manter o tempo de atividade do aplicativo dentro das restrições acima mencionadas.

## <a name="upgrade-a-cluster-control-plane-with-multiple-node-pools"></a>Atualizar um plano de controle de cluster com vários pools de nós

> [!NOTE]
> Kubernetes usa o esquema de controle de versão de [controle semântico](https://semver.org/) de versão padrão. O número de versão é expresso como *x. y. z*, em que *x* é a versão principal, *y* é a versão secundária e *z* é a versão do patch. Por exemplo, na versão *1.12.6*, 1 é a versão principal, 12 é a versão secundária e 6 é a versão do patch. A versão kubernetes do plano de controle e o pool de nós inicial são definidos durante a criação do cluster. Todos os pools de nós adicionais têm sua versão kubernetes definida quando são adicionados ao cluster. As versões do kubernetes podem ser diferentes entre pools de nós, bem como entre um pool de nós e o plano de controle.

Um cluster AKS tem dois objetos de recurso de cluster com versões do kubernetes associadas.

1. Uma versão kubernetes do plano de controle de cluster.
2. Um pool de nós com uma versão kubernetes.

Um plano de controle é mapeado para um ou vários pools de nós. O comportamento de uma operação de atualização depende de qual CLI do Azure comando é usado.

A atualização de um plano de controle AKS requer o uso de `az aks upgrade`. Isso atualiza a versão do plano de controle e todos os pools de nós no cluster. 

Emitir o comando `az aks upgrade` com o sinalizador `--control-plane-only` atualiza apenas o plano de controle de cluster. Nenhum dos pools de nós associados no cluster foi alterado.

A atualização de pools de nós individuais requer o uso de `az aks nodepool upgrade`. Isso atualiza somente o pool de nós de destino com a versão especificada do kubernetes

### <a name="validation-rules-for-upgrades"></a>Regras de validação para atualizações

As atualizações de kubernetes válidas para o plano de controle e os pools de nós do cluster são validadas pelos seguintes conjuntos de regras.

* Regras para versões válidas para atualizar pools de nós:
   * A versão do pool de nós deve ter a mesma versão *principal* que o plano de controle.
   * A versão *secundária* do pool de nós deve estar dentro de duas versões *secundárias* da versão do plano de controle.
   * A versão do pool de nós não pode ser maior que a versão de `major.minor.patch` de controle.

* Regras para enviar uma operação de atualização:
   * Não é possível fazer downgrade do plano de controle ou de uma versão kubernetes do pool de nós.
   * Se uma versão de kubernetes do pool de nós não for especificada, o comportamento dependerá do cliente que está sendo usado. A declaração nos modelos do Resource Manager volta à versão existente definida para o pool de nós, se for usada, se nenhuma for definida, a versão do plano de controle será usada para fazer fallback.
   * Você pode atualizar ou dimensionar um plano de controle ou um pool de nós em um determinado momento, não pode enviar várias operações em um único plano de controle ou recurso de pool de nós simultaneamente.

## <a name="scale-a-node-pool-manually"></a>Dimensionar um pool de nós manualmente

À medida que as demandas de carga de trabalho do aplicativo mudam, talvez seja necessário dimensionar o número de nós em um pool de nós. O número de nós pode ser aumentado ou reduzido verticalmente.

<!--If you scale down, nodes are carefully [cordoned and drained][kubernetes-drain] to minimize disruption to running applications.-->

Para dimensionar o número de nós em um pool de nós, use o comando [AZ AKs node pool Scale][az-aks-nodepool-scale] . O exemplo a seguir dimensiona o número de nós em *mynodepool* para *5*:

```azurecli-interactive
az aks nodepool scale \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 5 \
    --no-wait
```

Liste o status dos pools de nós novamente usando o comando [AZ AKs node pool List][az-aks-nodepool-list] . O exemplo a seguir mostra que *mynodepool* está no estado de *dimensionamento* com uma nova contagem de *5* nós:

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

O AKS oferece um recurso separado para dimensionar automaticamente os pools de nós com um recurso chamado de [dimensionador](cluster-autoscaler.md)automático do cluster. Esse recurso pode ser habilitado por pool de nós com contagens de escala mínima e máxima exclusivas por pool de nós. Saiba como [usar o conjunto de dimensionamento de clusters por pool de nós](cluster-autoscaler.md#use-the-cluster-autoscaler-with-multiple-node-pools-enabled).

## <a name="delete-a-node-pool"></a>Excluir um pool de nós

Se você não precisar mais de um pool, poderá excluí-lo e remover os nós de VM subjacentes. Para excluir um pool de nós, use o comando [AZ AKs node pool Delete][az-aks-nodepool-delete] e especifique o nome do pool de nós. O exemplo a seguir exclui o *mynoodepool* criado nas etapas anteriores:

> [!CAUTION]
> Não há opções de recuperação para perda de dados que podem ocorrer quando você exclui um pool de nós. Se os pods não puderem ser agendados em outros pools de nós, esses aplicativos não estarão disponíveis. Certifique-se de não excluir um pool de nós quando aplicativos em uso não tiverem backups de dados ou a capacidade de executar em outros pools de nós no cluster.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name mynodepool --no-wait
```

A saída de exemplo a seguir do comando [AZ AKs node pool List][az-aks-nodepool-list] mostra que *mynodepool* está no estado *excluindo* :

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

Nos exemplos anteriores para criar um pool de nós, um tamanho de VM padrão foi usado para os nós criados no cluster. Um cenário mais comum é criar pools de nós com diferentes tamanhos e recursos de VM. Por exemplo, você pode criar um pool de nós que contém nós com grandes quantidades de CPU ou memória, ou um pool de nós que fornece suporte à GPU. Na próxima etapa, você [usa os tolerationss e](#schedule-pods-using-taints-and-tolerations) os informativos para informar ao agendador de kubernetes como limitar o acesso a pods que pode ser executado nesses nós.

No exemplo seguinte, crie uma piscina de nó baseada em GPU que utilize o *tamanho Standard_NC6* VM. Essas VMs são alimentadas pelo cartão NVIDIA Tesla K80. Para obter informações sobre tamanhos de VM disponíveis, consulte [tamanhos de máquinas virtuais do Linux no Azure][vm-sizes].

Crie um pool de nós usando o comando [AZ AKs node pool Add][az-aks-nodepool-add] novamente. Desta vez, especifique o nome *gpunodepool,* e utilize o parâmetro `--node-vm-size` para especificar o tamanho *Standard_NC6:*

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunodepool \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --no-wait
```

A saída de exemplo a seguir do comando [AZ AKs node pool List][az-aks-nodepool-list] mostra que *gpunodepool* está *criando* nós com o *VmSize*especificado:

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

Leva alguns minutos para que o *gpunodepool* seja criado com êxito.

## <a name="schedule-pods-using-taints-and-tolerations"></a>Agendar pods usando os e Tolerations

Agora você tem dois pools de nós no cluster – o pool de nós padrão inicialmente criado e o pool de nós baseado em GPU. Use o comando [kubectl Get Nodes][kubectl-get] para exibir os nós no cluster. A saída de exemplo a seguir mostra os nós:

```console
$ kubectl get nodes

NAME                                 STATUS   ROLES   AGE     VERSION
aks-gpunodepool-28993262-vmss000000  Ready    agent   4m22s   v1.15.7
aks-nodepool1-28993262-vmss000000    Ready    agent   115m    v1.15.7
```

O Agendador kubernetes pode usar os conteúdo e os Tolerations para restringir quais cargas de trabalho podem ser executadas em nós.

* Um seu **pods é aplicado** a um nó que indica que apenas um inconsistente específico pode ser agendado neles.
* Em seguida, um **toleration** é aplicado a um pod que permite *tolerar* o de um nó.

Para obter mais informações sobre como usar os recursos agendados do kubernetes avançados, consulte [práticas recomendadas para recursos avançados do Agendador no AKs][taints-tolerations]

Neste exemplo, aplique uma mancha no nó baseado em GPU usando o comando --nó-manchas. Especifique o nome do nó baseado em GPU na saída do comando de `kubectl get nodes` anterior. O o o comparado é aplicado como um *valor chave:* e, em seguida, uma opção de agendamento. O exemplo a seguir usa o par *SKU = GPU* e define pods, caso contrário, tem a capacidade *NoSchedule* :

```console
az aks nodepool --node-taints aks-gpunodepool-28993262-vmss000000 sku=gpu:NoSchedule
```

O manifesto YAML de exemplo básico a seguir usa um toleration para permitir que o Agendador de kubernetes execute um pod NGINX no nó baseado em GPU. Para obter um exemplo mais apropriado, mas com uso intensivo de tempo para executar um trabalho do Tensorflow no conjunto de informações do MNIST, consulte [usar GPUs para cargas de trabalhos com uso intensivo de computação no AKs][gpu-cluster].

Crie um arquivo chamado `gpu-toleration.yaml` e copie no exemplo a seguir YAML:

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

Agende o Pod usando o comando `kubectl apply -f gpu-toleration.yaml`:

```console
kubectl apply -f gpu-toleration.yaml
```

Leva alguns segundos para agendar o pod e efetuar pull da imagem NGINX. Use o comando [kubectl para descrever o Pod][kubectl-describe] para exibir o status do pod. A saída de exemplo condensada a seguir mostra o *SKU = GPU: NoSchedule* toleration é aplicado. Na seção de eventos, o Agendador atribuiu o Pod ao nó baseado em *AKs-gpunodepool-28993262-vmss000000* :

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

Somente os pods que têm esse seu gpunodepool aplicado podem ser agendados em nós no *am*. Qualquer outro pod seria agendado no pool de nós *nodepool1* . Se você criar pools de nós adicionais, poderá usar os conteúdo e os Tolerations adicionais para limitar o que os pods podem ser agendados nesses recursos de nó.

## <a name="manage-node-pools-using-a-resource-manager-template"></a>Gerenciar pools de nós usando um modelo do Resource Manager

Ao usar um modelo de Azure Resource Manager para criar e gerenciar recursos, você normalmente pode atualizar as configurações em seu modelo e reimplantar para atualizar o recurso. Com pools de nós no AKS, o perfil do pool de nós inicial não pode ser atualizado depois que o cluster AKS tiver sido criado. Esse comportamento significa que você não pode atualizar um modelo existente do Resource Manager, fazer uma alteração nos pools de nós e reimplantar. Em vez disso, você deve criar um modelo do Resource Manager separado que atualize apenas os pools de nós para um cluster AKS existente.

Crie um modelo como `aks-agentpools.json` e cole o manifesto de exemplo a seguir. Este modelo de exemplo define as seguintes configurações:

* Atualiza o pool de nós do *Linux* chamado *myagentpool* para executar três nós.
* Define os nós no pool de nós para executar o kubernetes versão *1.15.7*.
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

Implante esse modelo usando o comando [AZ Group Deployment Create][az-group-deployment-create] , conforme mostrado no exemplo a seguir. O nome e o local do cluster AKS existentes serão solicitados:

```azurecli-interactive
az group deployment create \
    --resource-group myResourceGroup \
    --template-file aks-agentpools.json
```

> [!TIP]
> Você pode adicionar uma marca ao pool de nós adicionando a propriedade *tag* no modelo, conforme mostrado no exemplo a seguir.
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
> Durante a versão prévia de atribuição de um IP público por nó, ele não pode ser usado com o *Standard Load BALANCER SKU em AKs* devido a possíveis regras do balanceador de carga em conflito com o provisionamento de VM. Enquanto estiver na versão prévia, você deverá usar o *SKU do Load Balancer básico* se precisar atribuir um IP público por nó.

Os nós AKS não exigem seus próprios endereços IP públicos para comunicação. No entanto, alguns cenários podem exigir que os nós em um pool de nós tenham seus próprios endereços IP públicos. Um exemplo é o jogo, onde um console do precisa fazer uma conexão direta com uma máquina virtual de nuvem para minimizar os saltos. Isso pode ser feito registrando-se para um recurso de visualização separado, o IP público do nó (versão prévia).

```azurecli-interactive
az feature register --name NodePublicIPPreview --namespace Microsoft.ContainerService
```

Após o registro bem-sucedido, implante um modelo de Azure Resource Manager seguindo as mesmas instruções [acima](#manage-node-pools-using-a-resource-manager-template) e adicione a propriedade valor booliano `enableNodePublicIP` a agentPoolProfiles. Defina o valor como `true`, como por padrão, ele será definido como `false` se não for especificado. Esta é uma propriedade somente de tempo de criação e requer uma versão de API mínima de 2019-06-01. Isso pode ser aplicado a pools de nós do Linux e do Windows.

## <a name="clean-up-resources"></a>Limpar recursos

Neste artigo, você criou um cluster AKS que inclui nós baseados em GPU. Para reduzir o custo desnecessário, talvez você queira excluir o *gpunodepool*ou todo o cluster AKs.

Para excluir o pool de nós baseado em GPU, use o comando [AZ AKs nodepool Delete][az-aks-nodepool-delete] , conforme mostrado no exemplo a seguir:

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name gpunodepool
```

Para excluir o próprio cluster, use o comando [AZ Group Delete][az-group-delete] para excluir o grupo de recursos AKs:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu a criar e gerenciar vários pools de nós em um cluster AKS. Para obter mais informações sobre como controlar os pods nos pools de nós, consulte [práticas recomendadas para recursos avançados do Agendador no AKs][operator-best-practices-advanced-scheduler].

Para criar e usar pools de nós de contêiner do Windows Server, consulte [criar um contêiner do Windows Server em AKs][aks-windows].

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- INTERNAL LINKS -->
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
