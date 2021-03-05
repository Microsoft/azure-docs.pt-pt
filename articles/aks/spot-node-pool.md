---
title: Adicionar um conjunto de nós spot a um cluster do Azure Kubernetes Service (AKS)
description: Saiba como adicionar uma piscina de nó de ponto a um cluster Azure Kubernetes Service (AKS).
services: container-service
ms.service: container-service
ms.topic: article
ms.date: 10/19/2020
ms.openlocfilehash: 7f838b2a78f1c6993aa247f2944d4f2a9b1e9556
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102181130"
---
# <a name="add-a-spot-node-pool-to-an-azure-kubernetes-service-aks-cluster"></a>Adicionar um conjunto de nós spot a um cluster do Azure Kubernetes Service (AKS)

Uma piscina de nó de pontos é uma piscina de nó apoiada por um [conjunto de escala de máquina virtual spot][vmss-spot]. A utilização de VMs spot para nós com o seu cluster AKS permite-lhe tirar partido da capacidade não utilizado em Azure com uma poupança significativa de custos. A quantidade de capacidade disponível não utetilizada variará com base em muitos fatores, incluindo o tamanho do nó, região e hora do dia.

Ao implantar uma piscina de nó no local, o Azure irá alocar os nós spot se houver capacidade disponível. Mas não há SLA para os nós spot. Um conjunto de escala de spot que apoia o conjunto de nós spot é implantado num único domínio de falha e não oferece garantias de alta disponibilidade. A qualquer momento em que a Azure precise da capacidade de volta, a infraestrutura do Azure despejará nós spot.

Os nós spot são ótimos para cargas de trabalho que podem lidar com interrupções, rescisões antecipadas ou despejos. Por exemplo, cargas de trabalho como trabalhos de processamento de lotes, ambientes de desenvolvimento e testes, e grandes cargas de trabalho de computação podem ser bons candidatos a serem programados num conjunto de nós no local.

Neste artigo, você adiciona uma piscina de nó de ponto secundário a um cluster de Serviço Azure Kubernetes existente (AKS).

Este artigo pressupõe uma compreensão básica dos conceitos kubernetes e Azure Load Balancer. Para obter mais informações, consulte [os conceitos fundamentais da Kubernetes para o Serviço Azure Kubernetes (AKS)][kubernetes-concepts].

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="before-you-begin"></a>Antes de começar

Quando criar um cluster para utilizar uma piscina de nó de ponto, esse cluster também deve usar conjuntos de balança de máquina virtual para piscinas de nó e o balançador de carga *SKU padrão.* Também deve adicionar uma piscina adicional de nó depois de criar o seu cluster para usar uma piscina de nó de ponto. A adição de uma piscina adicional de nó é coberta num passo posterior.

Este artigo requer que esteja a executar a versão 2.14 ou posterior do Azure CLI. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli-install].

### <a name="limitations"></a>Limitações

Aplicam-se as seguintes limitações quando cria e gere clusters AKS com uma piscina de nó de pontos:

* Uma piscina de nó de pontos não pode ser a piscina de nó padrão do cluster. Uma piscina de nó de pontos só pode ser usada para uma piscina secundária.
* Não é possível atualizar uma piscina de nó de pontos, uma vez que as piscinas de nó de ponto não podem garantir o cordão e a drenagem. Tem de substituir o seu conjunto de nó de pontos existente por um novo para fazer operações como o upgrade da versão Kubernetes. Para substituir uma piscina de nó de pontos, crie uma nova piscina de nó de pontos com uma versão diferente de Kubernetes, aguarde até que o seu estado esteja *pronto,* em seguida, remova a piscina de nó antigo.
* O plano de controlo e as piscinas de nó não podem ser atualizados ao mesmo tempo. Você deve atualizá-los separadamente ou remover a piscina de nó de ponto para atualizar o plano de controlo e as piscinas restantes ao mesmo tempo.
* Uma piscina de nó de pontos deve utilizar conjuntos de balança de máquina virtual.
* Não é possível alterar scaleSetPriority ou SpotMaxPrice após a criação.
* Ao configurar o SpotMaxPrice, o valor deve ser de -1 ou um valor positivo com até cinco casas decimais.
* Uma piscina de nó de pontos terá a etiqueta *kubernetes.azure.com/scalesetpriority:spot,* a mancha *kubernetes.azure.com/scalesetpriority=spot:NoSchedule*, e as cápsulas do sistema terão anti-afinidade.
* Deve adicionar uma [tolerância correspondente][spot-toleration] para agendar cargas de trabalho numa piscina de nó no local.

## <a name="add-a-spot-node-pool-to-an-aks-cluster"></a>Adicionar um conjunto de nós spot a um cluster do AKS

Você deve adicionar uma piscina de nó de ponto a um cluster existente que tem várias piscinas de nó ativadas. Mais detalhes sobre a criação de um cluster AKS com várias piscinas de nós estão disponíveis [aqui.][use-multiple-node-pools]

Crie uma piscina de nó usando o [add de nodepool az aks][az-aks-nodepool-add].
```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name spotnodepool \
    --priority Spot \
    --eviction-policy Delete \
    --spot-max-price -1 \
    --enable-cluster-autoscaler \
    --min-count 1 \
    --max-count 3 \
    --no-wait
```

Por padrão, cria uma piscina de nó com *prioridade* de *Regular* no seu cluster AKS quando cria um cluster com várias piscinas de nós. O comando acima adiciona uma piscina de nó auxiliar a um cluster AKS existente com *uma prioridade* do *Spot*. A *prioridade* do *Spot* faz da piscina de nós uma piscina de nó de ponto. O parâmetro *da política de despejo* é definido para *Eliminar* no exemplo acima, que é o valor padrão. Quando define a [política de despejo][eviction-policy] para *Eliminar,* os nós no conjunto de escala subjacente do conjunto de nós são apagados quando são despejados. Também pode definir a política de despejo para *Deallocate.* Quando se define a política de despejo para *Deallocate,* os nós na escala subjacente são definidos para o estado de paragem após o despejo. Os nós na contagem de estado de negociação parada contra a sua quota de computação e podem causar problemas com a escala de cluster ou a atualização. Os valores *prioritários* e *de política de despejo* só podem ser definidos durante a criação de agrupamentos de nó. Estes valores não podem ser atualizados mais tarde.

O comando também permite o [autoescalador][cluster-autoscaler]do cluster, que é recomendado para ser utilizado com piscinas de nó no local. Com base nas cargas de trabalho que estão a funcionar no seu cluster, o cluster autoscaler escala e escala o número de nós na piscina de nós. Para piscinas de nó pontuais, o autoscaler do cluster aumentará o número de nós após um despejo se ainda forem necessários nós adicionais. Se alterar o número máximo de nós que um conjunto de nós pode ter, também precisa de ajustar o `maxCount` valor associado ao autoescalador do cluster. Se não utilizar um autoescalador de cluster, após o despejo, o pool de spot irá eventualmente diminuir para zero e exigirá uma operação manual para receber quaisquer nós de spot adicionais.

> [!Important]
> Apenas agende cargas de trabalho em piscinas de nó no local que podem lidar com interrupções, tais como trabalhos de processamento de lotes e ambientes de teste. Recomenda-se que você instale [manchas e tolerações][taints-tolerations] na sua piscina de nó de ponto para garantir que apenas cargas de trabalho que podem lidar com despejos de nó são agendadas em uma piscina de nó no local. Por exemplo, o padrão de ny de comando acima adiciona uma mancha de *kubernetes.azure.com/scalesetpriority=spot:NoSchedule* para que apenas cápsulas com uma tolerância correspondente são agendadas neste nó.

## <a name="verify-the-spot-node-pool"></a>Verifique a piscina do nó no ponto

Para verificar se a piscina do nó foi adicionada como uma piscina de nó de pontos:

```azurecli
az aks nodepool show --resource-group myResourceGroup --cluster-name myAKSCluster --name spotnodepool
```

Confirmar *escalaSetPrioridade* é *Spot*.

Para agendar uma vagem para correr num nó de ponto, adicione uma tolerância que corresponda à mancha aplicada ao nó de ponto. O exemplo a seguir mostra uma parte de um ficheiro yaml que define uma tolerância que corresponde a uma *mancha kubernetes.azure.com/scalesetpriority=spot:NoSchedule* usada no passo anterior.

```yaml
spec:
  containers:
  - name: spot-example
  tolerations:
  - key: "kubernetes.azure.com/scalesetpriority"
    operator: "Equal"
    value: "spot"
    effect: "NoSchedule"
   ...
```

Quando uma cápsula com esta tolerância é implantada, kubernetes pode agendar com sucesso a vagem nos nós com a mancha aplicada.

## <a name="max-price-for-a-spot-pool"></a>Preço máximo para uma piscina de spot
[Os preços para os casos pontuais são variáveis,][pricing-spot]com base na região e no SKU. Para mais informações, consulte os preços para [Linux][pricing-linux] e [Windows.][pricing-windows]

Com preços variáveis, você tem a opção de definir um preço máximo, em dólares americanos (USD), usando até 5 casas decimais. Por exemplo, o valor *0.98765* seria um preço máximo de $0.98765 USD por hora. Se fixar o preço máximo para *-1,* o caso não será despejado com base no preço. O preço, por exemplo, será o preço atual para o Spot ou o preço para uma instância padrão, o que for menor, desde que exista capacidade e quota disponível.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu a adicionar uma piscina de nó de ponto a um cluster AKS. Para obter mais informações sobre como controlar os casulos em piscinas de nós, consulte [as melhores práticas para funcionalidades avançadas de programador em AKS][operator-best-practices-advanced-scheduler].

<!-- LINKS - External -->
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - Internal -->
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-nodepool-add]: /cli/azure/aks/nodepool#az-aks-nodepool-add
[cluster-autoscaler]: cluster-autoscaler.md
[eviction-policy]: ../virtual-machine-scale-sets/use-spot.md#eviction-policy
[kubernetes-concepts]: concepts-clusters-workloads.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[pricing-linux]: https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/
[pricing-spot]: ../virtual-machine-scale-sets/use-spot.md#pricing
[pricing-windows]: https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/
[spot-toleration]: #verify-the-spot-node-pool
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[use-multiple-node-pools]: use-multiple-node-pools.md
[vmss-spot]: ../virtual-machine-scale-sets/use-spot.md