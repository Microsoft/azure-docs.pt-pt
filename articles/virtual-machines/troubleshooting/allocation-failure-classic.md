---
title: Falhas na atribuição de VM do Azure em modelo de implantação clássico Microsoft Docs
description: Falhas de atribuição de resolução de problemas quando cria, reinicia ou redimensiona um VM clássico em Azure
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "77913383"
---
# <a name="troubleshooting-steps-specific-to-allocation-failure-scenarios-in-the-classic-deployment-model"></a>Passos de resolução de problemas específicos para cenários de falha de alocação no modelo de implementação clássica

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Seguem-se cenários comuns de atribuição que fazem com que um pedido de atribuição seja fixado. Mergulharemos em cada cenário mais tarde neste artigo.

- Redimensionar um VM ou adicionar VMs ou instâncias de função a um serviço de nuvem existente
- Reiniciar VMs parcialmente paradas (desalocadas)
- Reiniciar VMs completamente paradas (desalocadas)
- Implantações de encenação e produção (plataforma apenas como serviço)
- Grupo affinity (VM ou proximidade de serviço)
- Rede virtual baseada em grupo de affinity

Quando receber um erro de atribuição, verifique se algum dos cenários listados se aplica ao seu erro. Utilize o erro de atribuição que é devolvido pela plataforma Azure para identificar o cenário correspondente. Se o seu pedido for fixado, remova alguns dos constrangimentos de fixação para abrir o seu pedido a mais clusters, aumentando assim a possibilidade de alocação de sucesso.
Em geral, se o erro não indicar que "o tamanho VM solicitado não é suportado", pode sempre voltar a tentar mais tarde. Isto porque recursos suficientes podem ter sido libertados no cluster para acomodar o seu pedido. Se o problema é que o tamanho VM solicitado não é suportado, experimente um tamanho VM diferente. Caso contrário, a única opção é remover a restrição de fixação.

Dois cenários de falha comum estão relacionados com grupos de afinidade. No passado, um grupo de afinidade era usado para fornecer proximidade com VMs e instâncias de serviço, ou foi usado para permitir a criação de uma rede virtual. Com a introdução de redes virtuais regionais, os grupos de afinidade deixaram de ser obrigados a criar uma rede virtual. Com a redução da latência da rede na infraestrutura Azure, a recomendação de utilização de grupos de afinidade para VMs ou proximidade de serviços mudou.

O diagrama seguinte apresenta a taxonomia dos cenários de atribuição (fixados). 

![Taxonomia de alocação fixa](./media/virtual-machines-common-allocation-failure/Allocation3.png)

## <a name="resize-a-vm-or-add-vms-or-role-instances-to-an-existing-cloud-service"></a>Redimensionar um VM ou adicionar VMs ou instâncias de função a um serviço de nuvem existente
**Erro**

Upgrade_VMSizeNotSupported ou GeneralError

**Causa da fixação do aglomerado**

Um pedido para redimensionar um VM ou adicionar um VM ou uma instância de papel a um serviço de nuvem existente tem de ser tentado no cluster original que acolhe o serviço de nuvem existente. A criação de um novo serviço em nuvem permite à plataforma Azure encontrar outro cluster que tenha recursos gratuitos ou suporte ao tamanho de VM que solicitou.

**Solução**

Se o erro for Upgrade_VMSizeNotSupported*, experimente um tamanho VM diferente. Se usar um tamanho VM diferente não é uma opção, mas se for aceitável usar um endereço IP virtual diferente (VIP), crie um novo serviço de nuvem para hospedar o novo VM e adicione o novo serviço de nuvem à rede virtual regional onde os VM existentes estão em execução. Se o seu serviço de nuvem existente não utilizar uma rede virtual regional, ainda pode criar uma nova rede virtual para o novo serviço na nuvem e, em seguida, ligar a sua [rede virtual existente à nova rede virtual.](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/) Veja mais sobre [as redes virtuais regionais.](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/)

Se o erro for o GeneralError*, é provável que o tipo de recurso (como um determinado tamanho VM) seja suportado pelo cluster, mas o cluster não tem recursos livres no momento. Semelhante ao cenário acima, adicione o recurso computado desejado através da criação de um novo serviço de nuvem (note que o novo serviço de nuvem tem de usar um VIP diferente) e use uma rede virtual regional para ligar os seus serviços na nuvem.

## <a name="restart-partially-stopped-deallocated-vms"></a>Reiniciar VMs parcialmente paradas (desalocadas)
**Erro**

GeneralError*

**Causa da fixação do aglomerado**

A negociação parcial significa que paraste (deallocated) um ou mais, mas não todos, VMs num serviço de nuvem. Quando paras (deallocate) um VM, os recursos associados são libertados. Reiniciar o que parou (deallocated) VM é, portanto, um novo pedido de atribuição. Reiniciar VMs num serviço de nuvem parcialmente translocado equivale a adicionar VMs a um serviço de nuvem existente. O pedido de atribuição tem de ser tentado no cluster original que acolhe o serviço de nuvem existente. A criação de um serviço de nuvem diferente permite à plataforma Azure encontrar outro cluster que tenha recursos gratuitos ou suporte o tamanho VM que solicitou.

**Solução**

Se for aceitável utilizar um VIP diferente, elimine os VMs parados (deallocados) (mas mantenha os discos associados) e adicione os VMs de volta através de um serviço de nuvem diferente. Utilize uma rede virtual regional para ligar os seus serviços na nuvem:

* Se o seu serviço de nuvem existente utilizar uma rede virtual regional, basta adicionar o novo serviço de nuvem à mesma rede virtual.
* Se o seu serviço de nuvem existente não utilizar uma rede virtual regional, crie uma nova rede virtual para o novo serviço na nuvem e, em seguida, [ligue a sua rede virtual existente à nova rede virtual.](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/) Veja mais sobre [as redes virtuais regionais.](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/)

## <a name="restart-fully-stopped-deallocated-vms"></a>Reiniciar VMs completamente paradas (desalocadas)
**Erro**

GeneralError*

**Causa da fixação do aglomerado**

A negociação total significa que paraste (deallocated) todos os VMs de um serviço na nuvem. Os pedidos de atribuição para reiniciar estes VMs têm de ser tentados no cluster original que acolhe o serviço de nuvem. A criação de um novo serviço em nuvem permite à plataforma Azure encontrar outro cluster que tenha recursos gratuitos ou suporte ao tamanho de VM que solicitou.

**Solução**

Se for aceitável utilizar um VIP diferente, elimine os VMs originais (deallocados) (mas mantenha os discos associados) e elimine o serviço de nuvem correspondente (os recursos computacional associados já foram lançados quando parou (deallocated) os VMs). Crie um novo serviço de nuvem para adicionar os VMs de volta.

## <a name="stagingproduction-deployments-platform-as-a-service-only"></a>Implantações de encenação/produção (plataforma apenas como serviço)
**Erro**

New_General* ou New_VMSizeNotSupported*

**Causa da fixação do aglomerado**

A implantação de encenação e a implantação de um serviço de nuvem estão hospedadas no mesmo cluster. Quando adicionar a segunda implantação, o pedido de atribuição correspondente será tentado no mesmo cluster que acolhe a primeira implantação.

**Solução**

Elimine a primeira implantação e o serviço de nuvem original e reimplante o serviço de nuvem. Esta ação pode aterrar a primeira implantação num cluster que tenha recursos livres suficientes para se adaptar tanto a implantações como a um cluster que suporte os tamanhos VM que solicitou.

## <a name="affinity-group-vmservice-proximity"></a>Grupo affinity (VM/proximidade de serviço)
**Erro**

New_General* ou New_VMSizeNotSupported*

**Causa da fixação do aglomerado**

Qualquer recurso de cálculo atribuído a um grupo de afinidade está ligado a um cluster. Novos pedidos de recursos computatórios nesse grupo de afinidade são tentados no mesmo cluster onde os recursos existentes são hospedados. Isto é verdade se os novos recursos são criados através de um novo serviço na nuvem ou através de um serviço de nuvem existente.

**Solução**

Se um grupo de afinidade não for necessário, não utilize um grupo de afinidades ou agrupe os seus recursos de computação em múltiplos grupos de afinidade.

## <a name="affinity-group-based-virtual-network"></a>Rede virtual baseada em grupo de afinidade
**Erro**

New_General* ou New_VMSizeNotSupported*

**Causa da fixação do aglomerado**

Antes de as redes virtuais regionais serem introduzidas, foi-lhe exigido que associasse uma rede virtual a um grupo de afinidades. Como resultado, os recursos de cálculo colocados num grupo de afinidade estão ligados pelos mesmos constrangimentos descritos na secção "Alocação: Grupo de Afinidade (VM/proximidade de serviço)" acima. Os recursos computacional estão ligados a um cluster.

**Solução**

Se não precisar de um grupo de afinidades, crie uma nova rede virtual regional para os novos recursos que está a adicionar e, em seguida, [ligue a sua rede virtual existente à nova rede virtual.](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/) Veja mais sobre [as redes virtuais regionais.](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/)

Em alternativa, pode [migrar a sua rede virtual baseada em grupo de afinidade para uma rede virtual regional](https://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/)e, em seguida, adicionar novamente os recursos desejados.


