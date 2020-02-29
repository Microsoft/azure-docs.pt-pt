---
title: Resolução de problemas Falhas de atribuição de VM Azure no modelo de implantação clássico Microsoft Docs
description: Falhas de atribuição de problemas quando cria, reinicia ou redimensiona um VM clássico em Azure
services: azure-service-management
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-resource-manager,azure-service-management
ms.assetid: bb939e23-77fc-4948-96f7-5037761c30e8
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 20e64e5225987a8045e406a0e8fcae098c580c61
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77913383"
---
# <a name="troubleshooting-steps-specific-to-allocation-failure-scenarios-in-the-classic-deployment-model"></a>Passos de resolução de problemas específicos para cenários de falha de alocação no modelo clássico de implementação

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Seguem-se os cenários comuns de atribuição que fazem com que um pedido de atribuição seja fixado. Mergulharemos em cada cenário mais tarde neste artigo.

- Redimensionar um VM ou adicionar VMs ou exemplos de papéis a um serviço de nuvem existente
- Reiniciar VMs parcialmente paradas (desalocadas)
- Reiniciar VMs completamente paradas (desalocadas)
- Implantações de encenação e produção (plataforma apenas como serviço)
- Grupo de afinidade (VM ou proximidade de serviço)
- Rede virtual baseada em affinity-grupo

Quando receber um erro de atribuição, verifique se algum dos cenários listados se aplica ao seu erro. Use o erro de atribuição que é devolvido pela plataforma Azure para identificar o cenário correspondente. Se o seu pedido for fixado, remova algumas das restrições de fixação para abrir o seu pedido a mais aglomerados, aumentando assim a possibilidade de sucesso de atribuição.
Em geral, se o erro não indicar que "o tamanho de VM solicitado não é suportado", pode sempre voltar a tentar mais tarde. Isto porque recursos suficientes podem ter sido libertados no aglomerado para acomodar o seu pedido. Se o problema é que o tamanho de VM solicitado não é suportado, tente um tamanho VM diferente. Caso contrário, a única opção é remover a restrição de fixação.

Dois cenários comuns de fracasso estão relacionados com grupos de afinidade. No passado, um grupo de afinidade foi usado para fornecer proximidade com VMs e instâncias de serviço, ou foi usado para permitir a criação de uma rede virtual. Com a introdução de redes virtuais regionais, os grupos de afinidade deixaram de ser obrigados a criar uma rede virtual. Com a redução da latência da rede na infraestrutura Azure, a recomendação de utilização de grupos de afinidade para VMs ou proximidade de serviçomudou.

O diagrama seguinte apresenta a taxonomia dos cenários de atribuição (fixados). 

![Taxonomia de Atribuição Pinned](./media/virtual-machines-common-allocation-failure/Allocation3.png)

## <a name="resize-a-vm-or-add-vms-or-role-instances-to-an-existing-cloud-service"></a>Redimensionar um VM ou adicionar VMs ou exemplos de papéis a um serviço de nuvem existente
**Erro**

Upgrade_VMSizeNotSupported ou GeneralError

**Causa do acolchoado**

Um pedido para redimensionar um VM ou adicionar um VM ou uma instância de papel a um serviço de nuvem existente tem que ser tentado no cluster original que acolhe o serviço de nuvem existente. A criação de um novo serviço na nuvem permite à plataforma Azure encontrar outro cluster que tenha recursos gratuitos ou suporte o tamanho VM que solicitou.

**Solução**

Se o erro for Upgrade_VMSizeNotSupported*, tente um tamanho VM diferente. Se utilizar um tamanho VM diferente não é uma opção, mas se for aceitável usar um diferente endereço IP virtual (VIP), crie um novo serviço na nuvem para acolher o novo VM e adicione o novo serviço de nuvem à rede virtual regional onde estão a funcionar os VMs existentes. Se o seu serviço de nuvem existente não utilizar uma rede virtual regional, ainda pode criar uma nova rede virtual para o novo serviço de nuvem e, em seguida, ligar a sua [rede virtual existente à nova rede virtual](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Veja mais sobre [as redes virtuais regionais.](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/)

Se o erro for GeneralError*, é provável que o tipo de recurso (como um tamanho VM particular) seja suportado pelo cluster, mas o cluster não tem recursos gratuitos no momento. À semelhança do cenário acima referido, adicione o recurso computacional desejado através da criação de um novo serviço na nuvem (note que o novo serviço na nuvem tem de usar um VIP diferente) e utilizar uma rede virtual regional para ligar os seus serviços na nuvem.

## <a name="restart-partially-stopped-deallocated-vms"></a>Reiniciar VMs parcialmente paradas (desalocadas)
**Erro**

Erro Geral*

**Causa do acolchoado**

Desalocação parcial significa que parou (deallocated) um ou mais, mas não todos, VMs em um serviço na nuvem. Quando para (desalocar) um VM, os recursos associados são libertados. Reiniciar essa VM interrompida (negociada) é, portanto, um novo pedido de atribuição. Reiniciar vMs num serviço de nuvem parcialmente deallocated é equivalente a adicionar VMs a um serviço de nuvem existente. O pedido de atribuição deve ser tentado no cluster original que acolhe o serviço de nuvem existente. A criação de um serviço de nuvem diferente permite à plataforma Azure encontrar outro cluster que tenha recursos gratuitos ou suporte o tamanho VM que solicitou.

**Solução**

Se for aceitável utilizar um VIP diferente, elimine os VMs parados (mas mantenha os discos associados) e adicione os VMs de volta através de um serviço de nuvem diferente. Utilize uma rede virtual regional para ligar os seus serviços na nuvem:

* Se o seu serviço de nuvem existente utilizar uma rede virtual regional, basta adicionar o novo serviço de nuvem à mesma rede virtual.
* Se o seu serviço de nuvem existente não utilizar uma rede virtual regional, crie uma nova rede virtual para o novo serviço de nuvem e, em seguida, [ligue a sua rede virtual existente à nova rede virtual](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Veja mais sobre [as redes virtuais regionais.](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/)

## <a name="restart-fully-stopped-deallocated-vms"></a>Reiniciar VMs completamente paradas (desalocadas)
**Erro**

Erro Geral*

**Causa do acolchoado**

Desalocação total significa que parou (deallocated) todos os VMs de um serviço na nuvem. Os pedidos de atribuição para reiniciar estes VMs têm de ser tentados no cluster original que acolhe o serviço de nuvem. A criação de um novo serviço na nuvem permite à plataforma Azure encontrar outro cluster que tenha recursos gratuitos ou suporte o tamanho VM que solicitou.

**Solução**

Se for aceitável utilizar um VIP diferente, elimine os VMs originais parados (mas mantenham os discos associados) e elimine o serviço de nuvem correspondente (os recursos de cálculo associados já foram lançados quando parou (deallocated) os VMs). Crie um novo serviço de nuvem para adicionar os VMs de volta.

## <a name="stagingproduction-deployments-platform-as-a-service-only"></a>Implantações de encenação/produção (plataforma apenas como serviço)
**Erro**

New_General* ou New_VMSizeNotSupported*

**Causa do acolchoado**

A implantação da encenação e a implantação da produção de um serviço em nuvem estão alojadas no mesmo cluster. Quando adicionar a segunda implantação, o pedido de atribuição correspondente será tentado no mesmo cluster que acolhe a primeira implantação.

**Solução**

Elimine a primeira implementação e o serviço original de nuvem e reimplante o serviço de nuvem. Esta ação pode aterrar a primeira implantação num cluster que tem recursos livres suficientes para se adaptar tanto a implantações como a um cluster que suporta os tamanhos vm que solicitou.

## <a name="affinity-group-vmservice-proximity"></a>Grupo affinity (VM/proximidade de serviço)
**Erro**

New_General* ou New_VMSizeNotSupported*

**Causa do acolchoado**

Qualquer recurso computacional atribuído a um grupo de afinidade está ligado a um cluster. Novos pedidos de recursos computacionais nesse grupo de afinidade são tentados no mesmo cluster onde os recursos existentes são hospedados. Isto é verdade se os novos recursos são criados através de um novo serviço na nuvem ou através de um serviço de nuvem existente.

**Solução**

Se um grupo de afinidade não for necessário, não utilize um grupo de afinidade, nem agrupa os seus recursos de computação em múltiplos grupos de afinidade.

## <a name="affinity-group-based-virtual-network"></a>Rede virtual baseada em grupo de affinity
**Erro**

New_General* ou New_VMSizeNotSupported*

**Causa do acolchoado**

Antes da introdução das redes virtuais regionais, era necessário associar uma rede virtual a um grupo de afinidades. Como resultado, os recursos computacionais colocados num grupo de afinidade estão vinculados pelos mesmos constrangimentos descritos na secção "Alocação: Grupo affinity (VM/proximidade de serviço)" acima. Os recursos computacionais estão ligados a um cluster.

**Solução**

Se não precisa de um grupo de afinidades, crie uma nova rede virtual regional para os novos recursos que adiciona e, em seguida, [ligue a sua rede virtual existente à nova rede virtual](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Veja mais sobre [as redes virtuais regionais.](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/)

Em alternativa, pode migrar a [sua rede virtual baseada em grupode afinidade para uma rede virtual regional](https://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/)e, em seguida, adicionar os recursos desejados novamente.


