---
title: Pré-visualização - Adicione uma piscina de nó spot a um cluster azure Kubernetes Service (AKS)
description: Aprenda a adicionar uma piscina de nó spot a um cluster azure Kubernetes Service (AKS).
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 02/25/2020
ms.author: zarhoads
ms.openlocfilehash: 466ad7c88547b6676ba0ae263b74d14059322f1c
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77622048"
---
# <a name="preview---add-a-spot-node-pool-to-an-azure-kubernetes-service-aks-cluster"></a>Pré-visualização - Adicione uma piscina de nó spot a um cluster azure Kubernetes Service (AKS)

Uma piscina de nó spot é uma piscina de nó apoiada por um conjunto de [escala de máquina virtual spot][vmss-spot]. A utilização de VMs spot para nós com o seu cluster AKS permite-lhe tirar partido da capacidade não utilizada em Azure a uma significativa poupança de custos. A quantidade de capacidade não utilizada disponível variará em função de muitos fatores, incluindo o tamanho do nó, a região e a hora do dia.

Ao implantar uma piscina de nó spot, o Azure irá alocar os nós de spot se houver capacidade disponível. Mas não há SLA para os nódoas. Um conjunto de escala de spot que apoia o conjunto de nódeos de mancha é implantado em um único domínio de falha e não oferece garantias de alta disponibilidade. A qualquer momento em que o Azure precise de volta da capacidade, a infraestrutura Azure despeja os nós do local.

Os nós spot são ótimos para cargas de trabalho que podem lidar com interrupções, rescisões antecipadas ou despejos. Por exemplo, cargas de trabalho como trabalhos de processamento de lotes, ambientes de desenvolvimento e teste, e grandes cargas de trabalho de cálculo podem ser bons candidatos a serem programados numa piscina de nó sem carga.

Neste artigo, você adiciona uma piscina de nó de spot secundário a um cluster de Serviço Azure Kubernetes (AKS) existente.

Este artigo assume uma compreensão básica dos conceitos Kubernetes e Azure Load Balancer. Para mais informações, consulte os [conceitos centrais da Kubernetes para o Serviço Azure Kubernetes (AKS)][kubernetes-concepts].

Esta funcionalidade encontra-se em pré-visualização.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="before-you-begin"></a>Antes de começar

Quando cria um cluster para utilizar uma piscina de nó de spot, esse cluster também deve utilizar conjuntos de escala de máquina virtual para piscinas de nós e o equilibrador de carga *SKU Padrão.* Você também deve adicionar uma piscina de nó adicional depois de criar o seu cluster para usar uma piscina de nó spot. A adição de uma piscina adicional de nó é coberta num passo posterior, mas primeiro precisa de ativar uma função de pré-visualização.

> [!IMPORTANT]
> As funcionalidades de pré-visualização AKS são self-service, opt-in. São fornecidos para recolher feedback e insetos da nossa comunidade. Na pré-visualização, estas funcionalidades não se destinam ao uso da produção. As características na pré-visualização pública são do apoio ao "melhor esforço". A assistência das equipas de apoio técnico da AKS está disponível apenas durante o horário de trabalho do Pacífico (PST). Para mais informações, consulte os seguintes artigos de apoio:
>
> * [Políticas de apoio aks][aks-support-policies]
> * [FaQ de suporte azure][aks-faq]

### <a name="register-spotpoolpreview-preview-feature"></a>Registar funcionalidade de pré-visualização spotpoolpreview

Para criar um cluster AKS que utilize uma piscina de nó spot, deve ativar a bandeira de visualização de *spotpoolna* na sua subscrição. Esta funcionalidade fornece o mais recente conjunto de melhorias de serviço ao configurar um cluster.

> [!CAUTION]
> Ao registar uma funcionalidade numa subscrição, não pode atualmente desregistar essa funcionalidade. Depois de ativar algumas funcionalidades de pré-visualização, podem ser utilizadas predefinições para todos os clusters AKS e depois criados na subscrição. Não ative funcionalidades de pré-visualização nas subscrições de produção. Utilize uma subscrição separada para testar funcionalidades de pré-visualização e recolher feedback.

Registe a bandeira de *pré-visualização spotpool* utilizando o comando de registo de [características az,][az-feature-register] como mostra o seguinte exemplo:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "spotpoolpreview"
```

Leva alguns minutos para o estado mostrar *registrado.* Pode verificar o estado de registo utilizando o comando da [lista de características az:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/spotpoolpreview')].{Name:name,State:properties.state}"
```

Quando estiver pronto, atualização do registo do fornecedor de recursos *Microsoft.ContainerService* utilizando o comando de registo do [fornecedor az:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="install-aks-preview-cli-extension"></a>Instale a extensão CLI de pré-visualização de aks

Para criar um cluster AKS que utilize um pool de nó spot, você precisa da versão de extensão CLI *de pré-visualização de aks* 0.4.32 ou superior. Instale a extensão Azure CLI de *pré-visualização de aks* utilizando o comando de adição de [extensão az][az-extension-add] e, em seguida, verifique se há atualizações disponíveis utilizando o comando de atualização de [extensão az:][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview
 
# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>Limitações

As seguintes limitações aplicam-se quando cria e gere clusters AKS com um conjunto de nósoques:

* Uma piscina de nó de spot não pode ser a piscina padrão do cluster. Uma piscina de nó de spot só pode ser usada para uma piscina secundária.
* Você não pode atualizar uma piscina de nó spot, uma vez que piscinas de nó spot não podem garantir o cordão e drenar. Você deve substituir o seu conjunto de nódeo spot existente por um novo para fazer operações como atualizar a versão Kubernetes. Para substituir uma piscina de nó spot, crie uma nova piscina de nó spot com uma versão diferente de Kubernetes, espere até que o seu estado esteja *pronto,* em seguida, remova a antiga piscina do nó.
* As piscinas de aviões de controlo e nó não podem ser atualizadas ao mesmo tempo. Deve atualizá-los separadamente ou remover a piscina de nó de mancha para atualizar o plano de controlo e as restantes piscinas de nó ao mesmo tempo.
* Uma piscina de nó de mancha deve usar conjuntos de escala de máquina virtual.
* Não é possível alterar scaleSetPriority ou SpotMaxPrice após a criação.
* Ao definir o SpotMaxPrice, o valor deve ser -1 ou um valor positivo com até cinco casas decimais.
* Uma piscina de nó spot terá o rótulo *kubernetes.azure.com/scalesetpriority:spot*, a mancha *kubernetes.azure.com/scalesetpriority=spot:NoSchedule*, e as cápsulas do sistema terão anti-afinidade.
* Você deve adicionar uma [tolerância correspondente][spot-toleration] para agendar cargas de trabalho em uma piscina de nó spot.

## <a name="add-a-spot-node-pool-to-an-aks-cluster"></a>Adicione uma piscina de nó spot a um cluster AKS

Você deve adicionar uma piscina de nó spot a um cluster existente que tem múltiplas piscinas de nós ativadas. Mais detalhes sobre a criação de um cluster AKS com várias piscinas de nós estão disponíveis [aqui.][use-multiple-node-pools]

Crie uma piscina de nó usando o [add az aks nodepool][az-aks-nodepool-add].
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

Por padrão, você cria um conjunto de nós com uma *prioridade* do *Regular* no seu cluster AKS quando você cria um cluster com várias piscinas de nós. O comando acima adiciona uma piscina auxiliar de nó a um aglomerado AKS existente com uma *prioridade* do *Spot*. A *prioridade* do *Spot* faz da piscina do nó uma piscina de nó spot. O parâmetro *de política de despejo* está definido para *apagar* no exemplo acima, que é o valor padrão. Quando você define a política de [despejo][eviction-policy] para *Apagar*, nós no conjunto de escala subjacente da piscina do nó são eliminados quando são despejados. Também pode definir a política de despejo para *Deallocate.* Quando se define a política de despejo para *Deallocate,* os nós na escala subjacente estão definidos para o estado de parado após o despejo. Os nós na contagem de estado sem acordo contra a sua quota de cálculo podem causar problemas com a escala de cluster ou upgrade. Os valores *prioritários* e *de política de despejo* só podem ser definidos durante a criação de piscinas de nó. Estes valores não podem ser atualizados mais tarde.

O comando também permite o [autoescalador][cluster-autoscaler]do cluster, que é recomendado para usar com piscinas de nó de mancha. Com base nas cargas de trabalho em funcionamento no seu cluster, o autoscaler cluster escala e escala para baixo o número de nós na piscina do nó. Para piscinas de nó spot, o autoscaler cluster irá aumentar o número de nós após um despejo se ainda forem necessários nós adicionais. Se alterar o número máximo de nós que um nó pode ter, também precisa de ajustar o valor `maxCount` associado ao autoscaler do cluster. Se não utilizar um autoescalador de cluster, após o despejo, o pool spot acabará por diminuir para zero e exigirá uma operação manual para receber quaisquer nós de spot adicionais.

> [!Important]
> Apenas programar cargas de trabalho em piscinas de nósojos pontuais que possam lidar com interrupções, tais como trabalhos de processamento de lotes e ambientes de teste. Recomenda-se que instale [manchas e tolerações][taints-tolerations] na sua piscina de nó spot para garantir que apenas as cargas de trabalho que podem lidar com os despejos do nó estão programadas numa piscina de nó spot. Por exemplo, o padrão de nº de comando acima adiciona uma mancha de *kubernetes.azure.com/scalesetpriority=spot:NoSchedule* por isso apenas as cápsulas com uma tolerância correspondente estão programadas neste nó.

## <a name="verify-the-spot-node-pool"></a>Verifique a piscina do nó spot

Para verificar se a sua piscina de nó foi adicionada como uma piscina de nó de spot:

```azurecli
az aks nodepool show --resource-group myResourceGroup --cluster-name myAKSCluster --name spotnodepool
```

Confirmar *escalaSetPriority* é *Spot*.

Para agendar uma cápsula para correr num nó de ponto, adicione uma tolerância que corresponda à mancha aplicada ao seu nó de spot. O exemplo seguinte mostra uma parte de um ficheiro de yaml que define uma tolerância que corresponde a uma *mancha kubernetes.azure.com/scalesetpriority=spot:NoSchedule* usada no passo anterior.

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

Quando uma cápsula com esta tolerância é implantada, os Kubernetes podem agendar com sucesso a cápsula nos nós com a mancha aplicada.

## <a name="max-price-for-a-spot-pool"></a>Preço máximo para uma piscina spot
[Os preços para os casos pontuais são variáveis,][pricing-spot]baseados na região e no SKU. Para mais informações, consulte os preços para [Linux][pricing-linux] e [Windows][pricing-windows].

Com preços variáveis, você tem opção de definir um preço máximo, em dólares americanos (USD), usando até 5 lugares decimais. Por exemplo, o valor *0.98765* seria um preço máximo de $0.98765 USD por hora. Se fixar o preço máximo para *-1,* a instância não será despejada com base no preço. O preço por exemplo será o preço atual para o Spot ou o preço para uma instância padrão, o que for menor, desde que haja capacidade e quota disponíveis.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu a adicionar uma piscina de nó spot a um cluster AKS. Para obter mais informações sobre como controlar as cápsulas através de piscinas de nós, consulte [as melhores práticas para funcionalidades avançadas de programadores em AKS][operator-best-practices-advanced-scheduler].

<!-- LINKS - External -->
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - Internal -->
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-deploy-create]: /cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-template-deploy]: ../azure-resource-manager/templates/deploy-cli.md#deployment-scope
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