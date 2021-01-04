---
title: Utilize várias piscinas de nó no Serviço Azure Kubernetes (AKS)
description: Saiba como criar e gerir várias piscinas de nó para um cluster no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 04/08/2020
ms.openlocfilehash: db153123622a59bbdde71afca4ea30e03a6fbf98
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/19/2020
ms.locfileid: "97694236"
---
# <a name="create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>Criar e gerir múltiplos conjuntos de nós para um cluster no Azure Kubernetes Service (AKS)

No Serviço Azure Kubernetes (AKS), os nós da mesma configuração são agrupados em *piscinas de nó.* Estas piscinas de nó contêm os VMs subjacentes que executam as suas aplicações. O número inicial de nós e o seu tamanho (SKU) é definido quando se cria um cluster AKS, que cria uma [piscina de nó do sistema.][use-system-pool] Para suportar aplicações que tenham diferentes exigências de computação ou armazenamento, pode criar piscinas de *nó de utilizador adicionais.* As piscinas de nó de sistema servem o principal propósito de hospedar cápsulas de sistema críticas, tais como CoreDNS e frente de túneis. As piscinas de nó de utilizador servem o principal propósito de hospedar as suas cápsulas de aplicação. No entanto, as cápsulas de aplicação podem ser agendadas nas piscinas de nó do sistema se desejar ter apenas uma piscina no seu cluster AKS. As piscinas de nó do utilizador são onde coloca as suas cápsulas específicas para a aplicação. Por exemplo, utilize estes conjuntos de nó de utilizador adicionais para fornecer GPUs para aplicações intensivas de computação ou acesso a armazenamento SSD de alto desempenho.

> [!NOTE]
> Esta funcionalidade permite um maior controlo sobre como criar e gerir várias piscinas de nós. Como resultado, são necessários comandos separados para criar/atualizar/eliminar. Anteriormente, as operações de cluster através `az aks create` ou `az aks update` utilização da API gerida doCluster foram a única opção para alterar o seu plano de controlo e uma única piscina de nós. Esta função expõe um conjunto de operação separado para piscinas de agentes através do agentePool API e requer a utilização do conjunto de `az aks nodepool` comandos para executar operações num conjunto de nós individuais.

Este artigo mostra-lhe como criar e gerir várias piscinas de nó num cluster AKS.

## <a name="before-you-begin"></a>Antes de começar

Precisa da versão Azure CLI 2.2.0 ou posteriormente instalada e configurada. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][install-azure-cli].

## <a name="limitations"></a>Limitações

Aplicam-se as seguintes limitações quando cria e gere clusters AKS que suportam múltiplas piscinas de nó:

* Consulte [quotas, restrições de tamanho de máquina virtual e disponibilidade da região no Serviço Azure Kubernetes (AKS)][quotas-skus-regions].
* Pode eliminar as piscinas de nó do sistema, desde que tenha outra piscina de nó de sistema para ocupar o seu lugar no cluster AKS.
* As piscinas do sistema devem conter pelo menos um nó e as piscinas de nó do utilizador podem conter zero ou mais nós.
* O cluster AKS deve utilizar o balanceador de carga Standard SKU para utilizar vários conjuntos de nós, a função não é suportada com equilibradores de carga Basic SKU.
* O cluster AKS deve utilizar conjuntos de balança de máquinas virtuais para os nós.
* O nome de uma piscina de nó só pode conter caracteres alfanuméricos minúsculos e deve começar com uma letra minúscula. Para as piscinas de nól de Linux, o comprimento deve ter entre 1 e 12 caracteres, para piscinas de nó do Windows o comprimento deve ter entre 1 e 6 caracteres.
* Todas as piscinas de nódis devem residir na mesma rede virtual.
* Ao criar várias piscinas de nó no cluster criar tempo, todas as versões Kubernetes utilizadas por piscinas de nó devem corresponder ao conjunto de versão definida para o plano de controlo. Isto pode ser atualizado depois de o cluster ter sido a provisionado utilizando operações de piscina por nó.

## <a name="create-an-aks-cluster"></a>Criar um cluster do AKS (Create an AKS cluster)

> [!Important]
> Se executar uma única piscina de nó de sistema para o seu cluster AKS em ambiente de produção, recomendamos que use pelo menos três nós para a piscina de nós.

Para começar, crie um cluster AKS com uma única piscina de nós. O exemplo a seguir utiliza o [comando do grupo az][az-group-create] para criar um grupo de recursos chamado *myResourceGroup* na região *leste.* Um cluster AKS chamado *myAKSCluster* é então criado usando o comando [az aks create.][az-aks-create]

> [!NOTE]
> O *Balancer de* carga Básico SKU não é **suportado** quando se utilizam várias piscinas de nós. Por padrão, os clusters AKS são criados com o balanceador de carga *Standard* SKU do portal Azure CLI e Azure.

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
    --load-balancer-sku standard
```

A criação do cluster demora alguns minutos.

> [!NOTE]
> Para garantir que o seu cluster funciona de forma fiável, deverá executar pelo menos 2 (dois) nós no conjunto de nós predefinidos, uma vez que os serviços essenciais do sistema estão a correr através deste conjunto de nós.

Quando o cluster estiver pronto, use o comando [az aks get-credentials][az-aks-get-credentials] para obter as credenciais de cluster para uso com `kubectl` :

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-a-node-pool"></a>Adicionar um conjunto de nós

O cluster criado no degrau anterior tem uma única piscina de nós. Vamos adicionar uma segunda piscina de nó usando o comando [de adicionar nodepool az aks.][az-aks-nodepool-add] O exemplo a seguir cria uma piscina de nó chamado *mynodepool* que corre *3* nosdes:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3
```

> [!NOTE]
> O nome de uma piscina de nó deve começar com uma letra minúscula e só pode conter caracteres alfanuméricos. Para as piscinas de nól de Linux, o comprimento deve ter entre 1 e 12 caracteres, para piscinas de nó do Windows o comprimento deve ter entre 1 e 6 caracteres.

Para ver o estado das suas piscinas de nó, use o comando [da lista de números az aks][az-aks-nodepool-list] e especifique o seu grupo de recursos e o nome do cluster:

```azurecli-interactive
az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster
```

A saída de exemplo a seguir mostra que *a mynodepool* foi criada com sucesso com três nós na piscina de nós. Quando o cluster AKS foi criado no passo anterior, um *nodepool1* padrão foi criado com uma contagem de nó de *2*.

```output
[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
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
> Se não for especificado nenhum *VmSize* quando adicionar uma piscina de nó, o tamanho padrão é *Standard_D2s_v3* para piscinas de nó windows e *Standard_DS2_v2* para piscinas de nól de linux. Se não for especificada *nenhuma Versão do Orquestrador,* esta é a mesma versão que o plano de controlo.

### <a name="add-a-node-pool-with-a-unique-subnet-preview"></a>Adicione uma piscina de nó com uma sub-rede única (pré-visualização)

Uma carga de trabalho pode exigir dividir os nós de um cluster em piscinas separadas para isolamento lógico. Este isolamento pode ser suportado com sub-redes separadas dedicadas a cada piscina de nós no cluster. Isto pode resolver requisitos como ter espaço de endereço de rede virtual não contíguo para dividir em piscinas de nós.

#### <a name="limitations"></a>Limitações

* Todas as sub-redes atribuídas a nodepools devem pertencer à mesma rede virtual.
* As cápsulas do sistema devem ter acesso a todos os nós do cluster para fornecer funcionalidades críticas, como a resolução de DNS através do coreDNS.
* A atribuição de uma sub-rede única por piscina de nó é limitada a Azure CNI durante a pré-visualização.
* A utilização de políticas de rede com uma sub-rede única por piscina de nó não é suportada durante a pré-visualização.

Para criar uma piscina de nó com uma sub-rede dedicada, passe o ID do recurso sub-rede como um parâmetro adicional ao criar um conjunto de nós.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3 \
    --vnet-subnet-id <YOUR_SUBNET_RESOURCE_ID>
```

## <a name="upgrade-a-node-pool"></a>Atualizar uma piscina de nó

> [!NOTE]
> As operações de atualização e escala num cluster ou num agrupamento de nónão não podem ocorrer simultaneamente, se se tentar um erro ser devolvido. Em vez disso, cada tipo de operação deve ser preenchido no recurso-alvo antes do pedido seguinte sobre esse mesmo recurso. Leia mais sobre isso no nosso [guia de resolução de problemas.](./troubleshooting.md#im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-upgraded-or-has-failed-upgrade)

Os comandos nesta secção explicam como atualizar uma única piscina de nós específico. A relação entre a atualização da versão Kubernetes do plano de controlo e a piscina de nós são explicadas na [secção abaixo](#upgrade-a-cluster-control-plane-with-multiple-node-pools).

> [!NOTE]
> A versão de imagem de grupo de nó os está ligada à versão Kubernetes do cluster. Só obterá atualizações de imagem de SO, após uma atualização de cluster.

Uma vez que existem duas piscinas de nó neste exemplo, devemos usar [a atualização az aks nodepool][az-aks-nodepool-upgrade] para atualizar uma piscina de nó. Para ver as atualizações disponíveis use [atualizações az aks][az-aks-get-upgrades]

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

Vamos atualizar a *mynodepool.* Utilize o comando [de atualização do nodepool az aks][az-aks-nodepool-upgrade] para atualizar o conjunto de nó, como mostra o seguinte exemplo:

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version KUBERNETES_VERSION \
    --no-wait
```

Enuque ao estado das suas piscinas de nó novamente utilizando o comando [da lista de piscinas de nó az aks.][az-aks-nodepool-list] O exemplo a seguir mostra que *a mynodepool* está no estado *de modernização* para *KUBERNETES_VERSION:*

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "KUBERNETES_VERSION",
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

Demora alguns minutos a atualizar os nós para a versão especificada.

Como uma boa prática, você deve atualizar todas as piscinas de nós em um cluster AKS para a mesma versão Kubernetes. O comportamento padrão `az aks upgrade` é atualizar todas as piscinas de nós juntamente com o plano de controlo para alcançar este alinhamento. A capacidade de atualizar as piscinas individuais de nós permite-lhe realizar uma atualização rolante e agendar cápsulas entre piscinas de nó para manter o tempo de funcionamento dentro dos constrangimentos acima mencionados.

## <a name="upgrade-a-cluster-control-plane-with-multiple-node-pools"></a>Atualize um plano de controlo de cluster com várias piscinas de nó

> [!NOTE]
> Kubernetes usa o esquema padrão de versão [semântica.](https://semver.org/) O número da versão é expresso em *x.y.z*, onde *x* é a versão principal, *y* é a versão menor, e *z* é a versão patch. Por exemplo, na versão *1.12.6*, 1 é a versão principal, 12 é a versão menor, e 6 é a versão patch. A versão Kubernetes do plano de controlo e a piscina inicial do nó são definidas durante a criação do cluster. Todas as piscinas de nó adicionais têm a sua versão Kubernetes definida quando são adicionadas ao cluster. As versões Kubernetes podem diferir entre piscinas de nós, bem como entre uma piscina de nó e o plano de controlo.

Um cluster AKS tem dois objetos de recursos de cluster com versões Kubernetes associadas.

1. Uma versão do plano de controlo de clusters Kubernetes.
2. Uma piscina de nó com uma versão Kubernetes.

Um avião de controlo mapeia para uma ou muitas piscinas de nós. O comportamento de uma operação de atualização depende do comando Azure CLI.

A atualização de um plano de controlo AKS requer a utilização `az aks upgrade` . Este comando atualiza a versão do plano de controlo e todas as piscinas de nós no cluster.

A emissão do `az aks upgrade` comando com a bandeira atualiza `--control-plane-only` apenas o plano de controlo de clusters. Nenhuma das piscinas de nó associados no cluster é alterada.

A atualização das piscinas individuais de nós requer a utilização `az aks nodepool upgrade` . Este comando atualiza apenas o conjunto de nó-alvo com a versão especificada de Kubernetes

### <a name="validation-rules-for-upgrades"></a>Regras de validação para upgrades

As atualizações válidas de Kubernetes para um plano de controlo de cluster e piscinas de nó são validadas pelos seguintes conjuntos de regras.

* Regras para versões válidas para atualizar piscinas de nó:
   * A versão do node pool deve ter a mesma versão *principal* do plano de controlo.
   * A versão *menor* da piscina de nó deve estar dentro de duas *versões menores* da versão do plano de controlo.
   * A versão do conjunto de nós não pode ser maior do que a versão de `major.minor.patch` controlo.

* Regras para a apresentação de uma operação de atualização:
   * Não é possível desclassificar o plano de controlo ou uma versão kubernetes de piscina de nó.
   * Se não for especificada uma versão kubernetes de piscina de nó, o comportamento depende da utilização do cliente. A declaração nos modelos do Gestor de Recursos recua para a versão existente definida para o conjunto de nós se for utilizada, se nenhuma for definida, a versão do plano de controlo é usada para voltar a cair.
   * Pode atualizar ou escalar um avião de controlo ou uma piscina de nó num dado num dado dado, não pode submeter várias operações num único plano de controlo ou num recurso de piscina de nó simultaneamente.

## <a name="scale-a-node-pool-manually"></a>Escalar uma piscina de nó manualmente

Como a carga de trabalho da sua aplicação exige alterações, poderá ter de escalar o número de nós numa piscina de nós. O número de nós pode ser aumentado para cima ou para baixo.

<!--If you scale down, nodes are carefully [cordoned and drained][kubernetes-drain] to minimize disruption to running applications.-->

Para escalar o número de nós numa piscina de nó, utilize o comando [de balança de balança de node az aks.][az-aks-nodepool-scale] O exemplo a seguir escala o número de nós na *mynodepool* para *5*:

```azurecli-interactive
az aks nodepool scale \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 5 \
    --no-wait
```

Enuque ao estado das suas piscinas de nó novamente utilizando o comando [da lista de piscinas de nó az aks.][az-aks-nodepool-list] O exemplo a seguir mostra que *a mynodepool* está no estado *de Escala com* uma nova contagem de *5* nóns:

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
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

Leva alguns minutos para a operação de escala estar completa.

## <a name="scale-a-specific-node-pool-automatically-by-enabling-the-cluster-autoscaler"></a>Dimensione automaticamente um conjunto de nóis específico, permitindo o autoescalador do cluster

A AKS oferece uma funcionalidade separada para escalar automaticamente as piscinas de nós com uma funcionalidade chamada [de autoescala de cluster](cluster-autoscaler.md). Esta função pode ser ativada por piscina de nó com contagem mínima e máxima única por piscina de nó. Saiba como [utilizar o cluster autoscaler por piscina de nó.](cluster-autoscaler.md#use-the-cluster-autoscaler-with-multiple-node-pools-enabled)

## <a name="delete-a-node-pool"></a>Apagar uma piscina de nó

Se já não precisar de piscina, pode eliminá-la e remover os nós VM subjacentes. Para eliminar uma piscina de nó, utilize o [conjunto de nós az aks eliminar][az-aks-nodepool-delete] o comando e especificar o nome do número de piscina. O exemplo a seguir elimina a *mynoodepool* criada nos passos anteriores:

> [!CAUTION]
> Não existem opções de recuperação para a perda de dados que possam ocorrer quando elimina um conjunto de nós. Se as cápsulas não puderem ser programadas em outras piscinas de nós, essas aplicações não estão disponíveis. Certifique-se de que não apaga um conjunto de nós quando as aplicações em uso não têm cópias de segurança de dados ou a capacidade de funcionar em outros grupos de nós no seu cluster.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name mynodepool --no-wait
```

A saída do exemplo a seguir do comando da lista de [piscinas de nó az aks][az-aks-nodepool-list] mostra que *a mynodepool* está no estado *de Eliminação:*

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
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

Leva alguns minutos para apagar os nós e a piscina de nós.

## <a name="specify-a-vm-size-for-a-node-pool"></a>Especificar um tamanho VM para uma piscina de nó

Nos exemplos anteriores para criar uma piscina de nó, foi utilizado um tamanho VM padrão para os nós criados no cluster. Um cenário mais comum é criar piscinas de nó com diferentes tamanhos e capacidades de VM. Por exemplo, você pode criar uma piscina de nós que contém nós com grandes quantidades de CPU ou memória, ou um conjunto de nós que fornece suporte GPU. No passo seguinte, [você usa manchas e tolerações](#setting-nodepool-taints) para dizer ao programador Kubernetes como limitar o acesso a cápsulas que podem funcionar nestes nós.

No exemplo seguinte, crie uma piscina de nó à base de GPU que utilize o *tamanho Standard_NC6* VM. Estes VMs são alimentados pelo cartão NVIDIA Tesla K80. Para obter informações sobre os tamanhos VM disponíveis, consulte [tamanhos para máquinas virtuais Linux em Azure][vm-sizes].

Crie uma piscina de nó usando o [número az aks node adicionar][az-aks-nodepool-add] comando novamente. Desta vez, especifique o nome *gpunodepool*, e use o `--node-vm-size` parâmetro para especificar o tamanho *Standard_NC6:*

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunodepool \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --no-wait
```

A saída de exemplo a partir do comando da lista de [piscinas de nó aks az aks][az-aks-nodepool-list] mostra que *a gpunodepool* está *a criar* nosdes com o *VmSize* especificado:

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
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

Leva alguns minutos para que o *gpunodepool* seja criado com sucesso.

## <a name="specify-a-taint-label-or-tag-for-a-node-pool"></a>Especifique uma mancha, etiqueta ou etiqueta para uma piscina de nó

### <a name="setting-nodepool-taints"></a>Definição de manchas de nodepool

Ao criar uma piscina de nó, pode adicionar manchas, etiquetas ou etiquetas à piscina de nó. Quando se adiciona uma mancha, etiqueta ou etiqueta, todos os nós dentro dessa piscina de nó também obtêm essa mancha, etiqueta ou etiqueta.

Para criar uma piscina de nó com uma mancha, use [a az aks nodepool add][az-aks-nodepool-add]. Especifique o nome *taintnp* e use o `--node-taints` parâmetro para especificar *sku=gpu:NoSchedule* para a mancha.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name taintnp \
    --node-count 1 \
    --node-taints sku=gpu:NoSchedule \
    --no-wait
```

> [!NOTE]
> Uma mancha só pode ser definida para piscinas de nó durante a criação da piscina de nó.

A saída de exemplo a seguir do comando da [lista de nodepool az aks][az-aks-nodepool-list] mostra que *taintnp* está *criando* nódes com os *nóns especificados*:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "taintnp",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "nodeTaints":  [
      "sku=gpu:NoSchedule"
    ],
    ...
  },
 ...
]
```

A informação de mancha é visível em Kubernetes para lidar com regras de agendamento de nós. O programador Kubernetes pode usar manchas e tolerâncias para restringir que cargas de trabalho podem funcionar em nós.

* Uma **mancha** é aplicada a um nó que indica que apenas podem ser programadas cápsulas específicas neles.
* Uma **tolerância** é então aplicada a uma vagem que lhes permite *tolerar* a mancha de um nó.

Para obter mais informações sobre como utilizar funcionalidades avançadas de Kubernetes [programadas, consulte as melhores práticas para funcionalidades avançadas do programador em AKS][taints-tolerations]

No passo anterior, você aplicou a mancha *sku=gpu:NoSchedule* quando criou a sua piscina de nó. O exemplo básico seguinte YAML manifesto usa uma tolerância para permitir que o programador Kubernetes execute uma cápsula NGINX em um nó nesse conjunto de nós.

Crie um ficheiro nomeado `nginx-toleration.yaml` e copie no seguinte exemplo YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: mcr.microsoft.com/oss/nginx/nginx:1.15.9-alpine
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

Agende a cápsula utilizando o `kubectl apply -f nginx-toleration.yaml` comando:

```console
kubectl apply -f nginx-toleration.yaml
```

Leva alguns segundos para agendar a cápsula e puxar a imagem NGINX. Utilize o comando do [pod de descrever kubectl][kubectl-describe] para visualizar o estado do pod. A seguinte saída de exemplo condensada mostra que a tolerância *sku=gpu:NoSchedule* é aplicada. Na secção de eventos, o programador atribuiu a cápsula ao nó *aks-taintnp-28993262-vmss0000000:*

```console
kubectl describe pod mypod
```

```output
[...]
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
                 sku=gpu:NoSchedule
Events:
  Type    Reason     Age    From                Message
  ----    ------     ----   ----                -------
  Normal  Scheduled  4m48s  default-scheduler   Successfully assigned default/mypod to aks-taintnp-28993262-vmss000000
  Normal  Pulling    4m47s  kubelet             pulling image "mcr.microsoft.com/oss/nginx/nginx:1.15.9-alpine"
  Normal  Pulled     4m43s  kubelet             Successfully pulled image "mcr.microsoft.com/oss/nginx/nginx:1.15.9-alpine"
  Normal  Created    4m40s  kubelet             Created container
  Normal  Started    4m40s  kubelet             Started container
```

Apenas as cápsulas que têm esta tolerância aplicada podem ser programadas em nós em *taintnp*. Qualquer outra cápsula seria agendada na piscina de *nóndes1.* Se criar piscinas de nó adicionais, pode usar manchas adicionais e tolerâncias para limitar que cápsulas podem ser programadas nesses recursos de nó.

### <a name="setting-nodepool-labels"></a>Definição de etiquetas de nodepool

Também pode adicionar etiquetas a uma piscina de nó durante a criação da piscina de nó. As etiquetas colocadas na piscina do nó são adicionadas a cada nó na piscina do nó. Estas [etiquetas são visíveis em Kubernetes][kubernetes-labels] para manusear regras de agendamento para nós.

Para criar uma piscina de nó com uma etiqueta, use [a az aks nodepool add][az-aks-nodepool-add]. Especifique a *etiqueta* de nome e use o `--labels` parâmetro para especificar *dept=IT* e *costcenter=9999* para etiquetas.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name labelnp \
    --node-count 1 \
    --labels dept=IT costcenter=9999 \
    --no-wait
```

> [!NOTE]
> A etiqueta só pode ser definida para piscinas de nó durante a criação da piscina de nó. As etiquetas também devem ser um par chave/valor e ter uma [sintaxe válida][kubernetes-label-syntax].

A saída de exemplo a seguir do comando da [lista de nodepool az aks][az-aks-nodepool-list] mostra que *a etiqueta* está *a criar* nosdes com os *nódulos especificados:*

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "labelnp",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "nodeLabels":  {
      "dept": "IT",
      "costcenter": "9999"
    },
    ...
  },
 ...
]
```

### <a name="setting-nodepool-azure-tags"></a>Definição de placas Azure de nodepool

Pode aplicar uma etiqueta Azure em piscinas de nó no seu cluster AKS. As etiquetas aplicadas a uma piscina de nó são aplicadas a cada nó dentro da piscina do nó e são persistiu através de upgrades. As etiquetas também são aplicadas a novos nós adicionados a um conjunto de nós durante as operações de escala. Adicionar uma etiqueta pode ajudar em tarefas como rastreio de políticas ou estimativa de custos.

As etiquetas Azure têm chaves que são insensíveis a operações, tais como quando recuperam uma etiqueta procurando na chave. Neste caso, uma etiqueta com a chave dada será atualizada ou recuperada independentemente do invólucro. Os valores da etiqueta são sensíveis a maiôs.

Em AKS, se várias tags forem definidas com teclas idênticas, mas invólucros diferentes, a etiqueta utilizada é a primeira na ordem alfabética. Por exemplo, `{"Key1": "val1", "kEy1": "val2", "key1": "val3"}` resulta e está a ser `Key1` `val1` definido.

Crie uma piscina de nó usando o [add de nodepool az aks][az-aks-nodepool-add]. Especifique o *nome tagnodepool* e use o `--tag` parâmetro para especificar *dept=IT* e *costcenter=9999* para etiquetas.

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
> Também pode utilizar o parâmetro quando utilizar o `--tags` comando [de atualização az aks nodepool,][az-aks-nodepool-update] bem como durante a criação do cluster. Durante a criação do cluster, o `--tags` parâmetro aplica a etiqueta à piscina inicial do nó criado com o cluster. Todos os nomes de identificação devem aderir às limitações nas [etiquetas de utilização para organizar os seus recursos Azure.][tag-limitation] A atualização de um conjunto de nós com o `--tags` parâmetro atualiza quaisquer valores de etiqueta existentes e anexa quaisquer novas etiquetas. Por exemplo, se o seu node pool tivesse *dept=IT* e *costcenter=9999* para tags e o atualizasse com *team=dev* e *costcenter=111* para tags, você nodepool teria *dept=IT,* *costcenter=111*, e *team=dev* para tags.

A saída de exemplo a partir do comando da [lista de nodepool az aks][az-aks-nodepool-list] mostra que *o tagnodepool* está *a criar* nosdes com a *etiqueta* especificada:

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
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

Quando utiliza um modelo de Gestor de Recursos Azure para criar e gerir recursos, normalmente pode atualizar as definições do seu modelo e recolocar para atualizar o recurso. Com piscinas de nó em AKS, o perfil inicial do conjunto de nós não pode ser atualizado uma vez que o cluster AKS tenha sido criado. Este comportamento significa que não é possível atualizar um modelo de Gestor de Recursos existente, fazer uma alteração nas piscinas de nós e reimplantar. Em vez disso, deve criar um modelo separado de Gestor de Recursos que atualize apenas os conjuntos de nós para um cluster AKS existente.

Crie um modelo como `aks-agentpools.json` e cole o manifesto de exemplo a seguir. Este modelo de exemplo configura as seguintes definições:

* Atualiza a piscina de nóleiros *Linux* chamada *myagentpool* para executar três nós.
* Define os nós na piscina de nós para executar a versão *1.15.7* de Kubernetes .
* Define o tamanho do nó como *Standard_DS2_v2*.

Edite estes valores conforme necessário para atualizar, adicionar ou eliminar piscinas de nó, se necessário:

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

Implemente este modelo utilizando o [grupo de implementação az criar][az-deployment-group-create] comando, como mostrado no exemplo seguinte. Você é solicitado para o nome e localização do cluster AKS existentes:

```azurecli-interactive
az deployment group create \
    --resource-group myResourceGroup \
    --template-file aks-agentpools.json
```

> [!TIP]
> Pode adicionar uma etiqueta à sua piscina de nó, adicionando a propriedade da *etiqueta* no modelo, como mostra o exemplo seguinte.
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

Pode levar alguns minutos para atualizar o seu cluster AKS dependendo das definições e operações do conjunto de nós que define no seu modelo de Gestor de Recursos.

## <a name="assign-a-public-ip-per-node-for-your-node-pools-preview"></a>Atribua um IP público por nó para as suas piscinas de nó (pré-visualização)

> [!WARNING]
> Tem de instalar a extensão de pré-visualização do CLI 0.4.43 ou superior para utilizar a função IP por nó público.

Os nós AKS não requerem os seus próprios endereços IP públicos para comunicação. No entanto, os cenários podem exigir que os nós num conjunto de nós recebam os seus próprios endereços IP públicos dedicados. Um cenário comum é para as cargas de trabalho dos jogos, onde uma consola precisa de fazer uma ligação direta a uma máquina virtual em nuvem para minimizar o lúpulo. Este cenário pode ser alcançado em AKS registando-se para uma funcionalidade de pré-visualização, Node Public IP (pré-visualização).

Para instalar e atualizar a mais recente extensão de pré-visualização de aks, utilize os seguintes comandos Azure CLI:

```azurecli
az extension add --name aks-preview
az extension update --name aks-preview
az extension list
```

Registe-se na função IP público do nó com o seguinte comando Azure CLI:

```azurecli-interactive
az feature register --name NodePublicIPPreview --namespace Microsoft.ContainerService
```
Pode levar vários minutos para a funcionalidade se registar.  Pode verificar o estado com o seguinte comando:

```azurecli-interactive
 az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/NodePublicIPPreview')].{Name:name,State:properties.state}"
```

Após o registo bem sucedido, crie um novo grupo de recursos.

```azurecli-interactive
az group create --name myResourceGroup2 --location eastus
```

Crie um novo cluster AKS e anexe um IP público para os seus nós. Cada um dos nós na piscina de nós recebe um IP público único. Pode verificar isto olhando para as instâncias de conjunto de escala de máquina virtual.

```azurecli-interactive
az aks create -g MyResourceGroup2 -n MyManagedCluster -l eastus  --enable-node-public-ip
```

Para os clusters AKS existentes, você também pode adicionar um novo conjunto de nós, e anexar um IP público para os seus nós.

```azurecli-interactive
az aks nodepool add -g MyResourceGroup2 --cluster-name MyManagedCluster -n nodepool2 --enable-node-public-ip
```

> [!Important]
> Durante a pré-visualização, o Serviço de Metadados de Instância Azure não suporta atualmente a recuperação de endereços IP públicos para o nível padrão VM SKU. Devido a esta limitação, não é possível utilizar comandos kubectl para exibir os IPs públicos atribuídos aos nós. No entanto, os IPs são atribuídos e funcionam conforme pretendido. Os IPs públicos para os seus nós estão ligados às instâncias do seu Conjunto de Escala de Máquina Virtual.

Pode localizar os IPs públicos para os seus nóns de várias formas:

* Utilize o comando Azure CLI [az vmss list-instance-public-ips][az-list-ips]
* Utilize [comandos PowerShell ou Bash][vmss-commands]. 
* Também pode ver os IPs públicos no portal Azure visualizando as instâncias no Conjunto de Escala de Máquina Virtual.

> [!Important]
> O [grupo de recursos de nó][node-resource-group] contém os nós e os seus IPs públicos. Utilize o grupo de recursos de nó ao executar comandos para encontrar os IPs públicos para os seus nós.

```azurecli
az vmss list-instance-public-ips -g MC_MyResourceGroup2_MyManagedCluster_eastus -n YourVirtualMachineScaleSetName
```

## <a name="clean-up-resources"></a>Limpar os recursos

Neste artigo, criou um cluster AKS que inclui nós baseados em GPU. Para reduzir custos desnecessários, pode querer eliminar o *gpunodepool*, ou todo o cluster AKS.

Para eliminar o conjunto de nó baseado em GPU, utilize o comando [de exclusão de nodepool az aks,][az-aks-nodepool-delete] como mostrado no exemplo seguinte:

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name gpunodepool
```

Para eliminar o próprio cluster, utilize o comando de eliminação do [grupo AZ][az-group-delete] para eliminar o grupo de recursos AKS:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

Também pode eliminar o cluster adicional que criou para o cenário de piscinas de nó público.

```azurecli-interactive
az group delete --name myResourceGroup2 --yes --no-wait
```

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [piscinas de nó de sistema.][use-system-pool]

Neste artigo, você aprendeu a criar e gerir várias piscinas de nós num cluster AKS. Para obter mais informações sobre como controlar os casulos em piscinas de nós, consulte [as melhores práticas para funcionalidades avançadas de programador em AKS][operator-best-practices-advanced-scheduler].

Para criar e utilizar as piscinas de nó de nó de contentores do Windows Server, consulte [criar um recipiente do Servidor do Windows em AKS][aks-windows].

Utilize [grupos de colocação de proximidade][reduce-latency-ppg] para reduzir a latência para as suas aplicações AKS.

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubernetes-labels]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
[kubernetes-label-syntax]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set

<!-- INTERNAL LINKS -->
[aks-windows]: windows-container-cli.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-upgrades]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-upgrades
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
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create
[gpu-cluster]: gpu-cluster.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[quotas-skus-regions]: quotas-skus-regions.md
[supported-versions]: supported-kubernetes-versions.md
[tag-limitation]: ../azure-resource-manager/management/tag-resources.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/sizes.md
[use-system-pool]: use-system-pools.md
[ip-limitations]: ../virtual-network/virtual-network-ip-addresses-overview-arm#standard
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[vmss-commands]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine
[az-list-ips]: /cli/azure/vmss?view=azure-cli-latest.md#az-vmss-list-instance-public-ips
[reduce-latency-ppg]: reduce-latency-ppg.md