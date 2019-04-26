---
title: Resolução de problemas de falhas de alocação de VM do Azure no modelo de implementação clássica | Documentos da Microsoft
description: Resolver problemas de falhas de alocação ao criar, reiniciar ou redimensionar uma VM clássica no Azure
services: azure-service-management
documentationcenter: ''
author: genlin
manager: willchen
editor: ''
tags: top-support-issue,azure-resource-manager,azure-service-management
ms.assetid: bb939e23-77fc-4948-96f7-5037761c30e8
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 7cd7897e3a0b940bbc636b2fbc3dbbc13b7cf540
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60505570"
---
# <a name="troubleshooting-steps-specific-to-allocation-failure-scenarios-in-the-classic-deployment-model"></a>Passos de resolução de problemas específicos para cenários de falha de alocação no modelo de implementação clássica

Seguem-se cenários comuns de alocação que fazem com que uma solicitação de alocação ser afixado. Vamos mergulhar em cada cenário neste artigo.

- Redimensionar uma VM ou adicionar VMs ou instâncias de função a um serviço cloud existente
- Reiniciar VMs parcialmente paradas (desalocadas)
- Reiniciar VMs completamente paradas (desalocadas)
- Implementações de teste e produção (plataforma como serviço apenas)
- Grupo de afinidade (VM ou de proximidade de serviço)
- Rede virtual baseada em afinidade – grupo

Quando receber um erro de alocação, verifique se qualquer um dos cenários listados se aplicar a seu erro. Utilize o erro de alocação que é devolvido pela plataforma do Azure para identificar o cenário correspondente. Se o seu pedido está afixado, remova algumas das restrições de afixação para abrir o seu pedido para obter mais clusters, aumentando assim a probabilidade de êxito na alocação.
Em geral, se o erro de estado de que "o tamanho da VM pedida não é suportado", pode sempre tentar novamente mais tarde. Isso é porque recursos suficientes podem ter sido liberados no cluster para acomodar o seu pedido. Se o problema é que o tamanho da VM pedida não é suportado, experimente um tamanho VM diferente. Caso contrário, a única opção é remover a restrição de afixação.

Dois cenários comuns de falha estão relacionados com grupos de afinidade. No passado, um grupo de afinidades foi utilizado para fornecer os próximos a VMs e instâncias de serviço ou foi utilizado para permitir a criação de uma rede virtual. Com a introdução das redes virtuais regionais, grupos de afinidades já não são necessários para criar uma rede virtual. Com a redução de latência de rede na infraestrutura do Azure, a recomendação para utilizar grupos de afinidade para VMs ou de proximidade de serviço foi alterado.

O diagrama seguinte apresenta a taxonomia dos cenários de alocação (afixados). 

![Taxonomia de alocação afixado](./media/virtual-machines-common-allocation-failure/Allocation3.png)

## <a name="resize-a-vm-or-add-vms-or-role-instances-to-an-existing-cloud-service"></a>Redimensionar uma VM ou adicionar VMs ou instâncias de função a um serviço cloud existente
**Error**

Upgrade_VMSizeNotSupported ou GeneralError

**Causa de fixação de cluster**

Um pedido para redimensionar uma VM ou adicionar uma VM ou uma instância de função a um serviço cloud existente tem de ser tentada no cluster original que aloja o serviço em nuvem existente. Criar um novo serviço em nuvem permite que a plataforma do Azure encontrar o outro cluster que tem recursos gratuitos ou suporta o tamanho da VM que pediu.

**Solução**

Se o erro é Upgrade_VMSizeNotSupported *, experimente um tamanho VM diferente. Se utilizar um tamanho VM diferente não é uma opção, mas se for aceitável para utilizar um endereço IP virtual (VIP) diferente, crie um novo serviço cloud para alojar a nova VM e adicione o novo serviço cloud para a rede virtual regional em que as VMs existentes estão em execução. Se o seu serviço cloud existente não utiliza uma rede virtual regional, pode ainda criar uma nova rede virtual para o novo serviço de nuvem e, em seguida, ligar seus [rede virtual existente para a nova rede virtual](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Veja mais informações sobre [redes virtuais regionais](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Se o erro é GeneralError *, é provável que o tipo de recurso (por exemplo, um determinado tamanho VM) é suportado pelo cluster, mas o cluster não tem recursos gratuitos no momento. À semelhança cenário acima, adicione o recurso de computação pretendido através da criação de um novo serviço cloud (Observe que o novo serviço cloud tem de utilizar um VIP diferente) e utilizar uma rede virtual regional para ligar os serviços cloud.

## <a name="restart-partially-stopped-deallocated-vms"></a>Reiniciar VMs parcialmente paradas (desalocadas)
**Error**

GeneralError *

**Causa de fixação de cluster**

Desalocação parcial significa que parada (desalocadas) uma ou mais, mas não todos, VMs num serviço cloud. Quando pare (desaloque) uma VM, os recursos associados são lançados. Portanto, a reiniciar a VM parada (desalocada) é um novo pedido de alocação. Reiniciar VMs num serviço cloud parcialmente desalocada equivale a adicionar as VMs para um serviço cloud existente. O pedido de atribuição tem de ser tentada no cluster original que aloja o serviço em nuvem existente. Criar um serviço cloud diferente, permite que a plataforma do Azure encontrar o outro cluster que tem o recurso gratuito ou suporta o tamanho da VM que pediu.

**Solução**

Se ele é aceitável para utilizar um VIP diferente, elimine as VMs paradas (desalocadas) (mas não os discos associados) e adicionar as VMs através de um serviço cloud diferente. Utilize uma rede virtual regional para ligar os serviços cloud:

* Se o seu serviço cloud existente utiliza uma rede virtual regional, basta adicione o novo serviço de nuvem à mesma rede virtual.
* Se o seu serviço cloud existente não utiliza uma rede virtual regional, crie uma nova rede virtual para o novo serviço de nuvem e, em seguida [ligar a sua rede virtual existente para a nova rede virtual](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Veja mais informações sobre [redes virtuais regionais](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

## <a name="restart-fully-stopped-deallocated-vms"></a>Reiniciar VMs completamente paradas (desalocadas)
**Error**

GeneralError *

**Causa de fixação de cluster**

Significa de Desalocação completo que parada (desalocada) todas as VMs de um serviço em nuvem. As solicitações de alocação para reiniciar estas VMs tem de ser tentada no cluster original que aloja o serviço em nuvem. Criar um novo serviço em nuvem permite que a plataforma do Azure encontrar o outro cluster que tem recursos gratuitos ou suporta o tamanho da VM que pediu.

**Solução**

Se for aceitável para utilizar um VIP diferente, elimine as parada (desalocadas) das VMs originais (mas não os discos associados) e eliminar o serviço de nuvem correspondente (os recursos de computação associado já foram liberados quando parada (desalocada) as VMs). Crie um novo serviço cloud para adicionar as VMs de volta.

## <a name="stagingproduction-deployments-platform-as-a-service-only"></a>Implementações de teste/produção (plataforma como serviço apenas)
**Error**

New_General * ou New_VMSizeNotSupported *

**Causa de fixação de cluster**

A implementação de teste e a implementação de produção de um serviço em nuvem estão alojados no mesmo cluster. Ao adicionar a segunda implementação, o pedido de atribuição correspondente será tentado no mesmo cluster que aloja a primeira implementação.

**Solução**

Elimine a primeira implementação e o serviço em nuvem original e volte a implementar o serviço em nuvem. Esta ação pode direcionado para a primeira implementação num cluster que tem recursos suficientes gratuitos, de acordo com ambas as implementações ou num cluster que suporte os tamanhos VM que pediu.

## <a name="affinity-group-vmservice-proximity"></a>Grupo de afinidade (proximidade VM/serviço)
**Error**

New_General * ou New_VMSizeNotSupported *

**Causa de fixação de cluster**

Qualquer computação recursos atribuídos a um grupo de afinidade está associado a um cluster. Novo recurso de computação pedidos em que o grupo de afinidade são tentadas no mesmo cluster onde estão alojados os recursos existentes. Isso é verdadeiro se os novos recursos são criados por meio de um novo serviço cloud ou um serviço cloud existente.

**Solução**

Se um grupo de afinidades não seja necessário, não utilize um grupo de afinidade ou agrupar seus recursos de computação em vários grupos de afinidade.

## <a name="affinity-group-based-virtual-network"></a>Baseado em grupo de afinidade de rede virtual
**Error**

New_General * ou New_VMSizeNotSupported *

**Causa de fixação de cluster**

Antes de serem introduzidos redes virtuais regionais, era necessário para associar uma rede virtual um grupo de afinidades. Como resultado, a computação colocados num grupo de afinidade de recursos estão vinculados pelas mesmas restrições quanto, conforme descrito no "o cenário de alocação: Grupo de afinidade (proximidade VM/serviços) "secção acima. Os recursos de computação estão associados a um cluster.

**Solução**

Se não precisar de um grupo de afinidades, criar uma nova rede virtual regional para os novos recursos que estiver a adicionar, e, em seguida [ligar a sua rede virtual existente para a nova rede virtual](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Veja mais informações sobre [redes virtuais regionais](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Em alternativa, pode [migrar a sua rede virtual baseado em grupo de afinidade para uma rede virtual regional](https://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/)e, em seguida, adicione novamente os recursos desejados.


