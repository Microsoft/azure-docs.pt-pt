---
title: Visão geral do SQL Server Sempre Em grupos de disponibilidade
description: Este artigo introduz o SQL Server Always On availability groups em Azure Virtual Machines.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.topic: overview
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/07/2020
ms.author: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: d04f689dec3a3c182c0da23007247c20c4f8063d
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94504395"
---
# <a name="always-on-availability-group-on-sql-server-on-azure-vms"></a>Sempre no grupo de disponibilidade no SQL Server em VMs Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo introduz grupos de disponibilidade always on para SQL Server em Azure Virtual Machines (VMs). 

## <a name="overview"></a>Descrição geral

Sempre Em grupos de disponibilidade em Azure Virtual Machines são semelhantes a [Always On availability groups no local](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). No entanto, uma vez que as máquinas virtuais estão hospedadas em Azure, existem também algumas considerações adicionais, como a redundância de VM e o tráfego de encaminhamento na rede Azure. 

O diagrama a seguir ilustra um grupo de disponibilidade para o SQL Server em VMs Azure:

![Grupo de Disponibilidade](./media/availability-group-overview/00-EndstateSampleNoELB.png)


## <a name="vm-redundancy"></a>Redundância VM 

Para aumentar a redundância e a elevada disponibilidade, os VMs do SQL Server devem estar no mesmo [conjunto de disponibilidade,](../../../virtual-machines/windows/tutorial-availability-sets.md#availability-set-overview)ou em [diferentes zonas de disponibilidade.](../../../availability-zones/az-overview.md)

Um conjunto de disponibilidade é um agrupamento de recursos que são configurados de modo a que não haja dois terrenos na mesma zona de disponibilidade. Isto evita o impacto de múltiplos recursos no grupo durante os lançamentos de implantação. 


## <a name="connectivity"></a>Conectividade 

Numa implantação tradicional no local, os clientes conectam-se ao ouvinte do grupo de disponibilidade usando o nome de rede virtual (VNN), e o ouvinte encaminha o tráfego para a réplica adequada do SQL Server no grupo de disponibilidade. No entanto, existe um requisito adicional para a rota do tráfego na rede Azure. 

Com o SQL Server em VMs Azure, configurar um equilibrador de carga para [encaminhar](availability-group-vnn-azure-load-balancer-configure.md) o tráfego para o seu ouvinte do grupo de disponibilidade, ou, se estiver no SQL Server 2019 CU8 e mais tarde, pode configurar um [ouvinte de rede distribuído (DNN)](availability-group-distributed-network-name-dnn-listener-configure.md) para substituir o ouvinte de grupo de disponibilidade tradicional VNN. 


### <a name="vnn-listener"></a>Serviço de escuta VNN 

Utilize um Balançador de [Carga Azure](../../../load-balancer/load-balancer-overview.md) para encaminhar o tráfego do cliente para o tradicional nome de rede virtual de grupo de disponibilidade (VNN) na rede Azure. 

O equilibrador de carga contém os endereços IP para o ouvinte VNN. Se tiver mais de um grupo de disponibilidade, cada grupo requer um ouvinte VNN. Um equilibrador de carga pode suportar vários ouvintes.

Para começar, consulte [configurar um equilibrador de carga.](availability-group-vnn-azure-load-balancer-configure.md) 

### <a name="dnn-listener"></a>Serviço de escuta DNN

SQL Server 2019 CU8 introduz suporte para o ouvinte de rede distribuído (DNN). O ouvinte do DNN substitui o ouvinte tradicional do grupo de disponibilidade, negando a necessidade de um Azure Loud Balancer para encaminhar o tráfego na rede Azure. 

O ouvinte DNN é a solução de conectividade HADR recomendada no Azure, uma vez que simplifica a implementação, reduz a manutenção e o custo e reduz o tempo de incumprimento em caso de falha. 

Utilize o ouvinte DNN para substituir um ouvinte VNN existente, ou, em alternativa, utilize-o em conjunto com um ouvinte VNN existente de modo a que o seu grupo de disponibilidade tenha dois pontos de ligação distintos - um utilizando o nome do ouvinte VNN (e porta se não estiver predefinido), e outro utilizando o nome e porta do ouvinte DNN. Isto pode ser útil para os clientes que pretendam evitar a latência do balanceador de carga, mas ainda assim aproveitar as funcionalidades do SQL Server que dependem do ouvinte VNN, tais como grupos de disponibilidade distribuídos, corretor de serviço ou fluxo de ficheiros. Para saber mais, consulte [o ouvinte DNN e a interoperabilidade do SqL Server](availability-group-dnn-interoperability.md)

Para começar, consulte [a configuração de um ouvinte da DNN](availability-group-distributed-network-name-dnn-listener-configure.md).


## <a name="deployment"></a>Implementação 

Existem múltiplas opções para implementar um grupo de disponibilidade para SQL Server em VMs Azure, alguns com mais automatização do que outros. 

O quadro a seguir fornece uma comparação das opções disponíveis:

| | Portal do Azure | Azure CLI / PowerShell | Modelos de arranque rápido | Manual |
|---------|---------|---------|---------|---------|
|**Versão do SQL Server** |2016 + |2016 +|2016 +|2012 +|
|**Edição do SQL Server** |Enterprise |Enterprise |Enterprise |Enterprise, Standard|
|**Versão do Windows Server**| 2016 + | 2016 + | 2016 + | Todos|
|**Cria o cluster para si**|Sim|Sim | Sim |Não|
|**Cria o grupo de disponibilidade para si** |Sim |Não|Não|Não|
|**Cria ouvinte e equilibrador de carga de forma independente** |Não|Não|Não|Sim|
|**Possível criar ouvinte dNN utilizando este método?**|Não|Não|Não|Sim|
|**Configuração do quórum WSFC**|Testemunha de cloud|Testemunha de cloud|Testemunha de cloud|Todos|
|**DR com várias regiões** |Não|Não|Não|Sim|
|**Suporte multi-subscrição** |Sim|Sim|Sim|Sim|
|**Apoio a um AD existente**|Sim|Sim|Sim|Sim|
|**DR com multizona na mesma região**|Sim|Sim|Sim|Sim|
|**AG distribuído sem AD**|Não|Não|Não|Sim|
|**AG distribuído sem cluster** |Não|Não|Não|Sim|

Para mais informações, consulte [o portal Azure,](availability-group-azure-portal-configure.md) [Azure CLI / PowerShell,](./availability-group-az-commandline-configure.md) [Quickstart Templates](availability-group-quickstart-template-configure.md)e [Manual](availability-group-manually-configure-prerequisites-tutorial.md).

## <a name="considerations"></a>Considerações 

Num cluster de ativação pós-falha convidado da VM IaaS do Azure, recomendamos um único NIC por servidor (nó de cluster) e uma única sub-rede. O azure networking tem redundância física, o que torna NICs adicionais e sub-redes desnecessários em um cluster de hóspedes Azure IaaS VM. Embora o relatório de validação do cluster emita um aviso de que os nós apenas são acessíveis numa única rede, este aviso pode ser ignorado com segurança nos clusters de ativação pós-falha convidados da VM IaaS do Azure. 

## <a name="next-steps"></a>Passos seguintes

Reveja as [melhores práticas hadr](hadr-cluster-best-practices.md) e, em seguida, inicie a implementação do seu grupo de disponibilidade utilizando o [portal Azure](availability-group-azure-portal-configure.md), [Azure CLI / PowerShell,](./availability-group-az-commandline-configure.md) [Quickstart Templates](availability-group-quickstart-template-configure.md) ou [manualmente](availability-group-manually-configure-prerequisites-tutorial.md).

Em alternativa, pode implantar um [grupo de disponibilidade sem clusters](availability-group-clusterless-workgroup-configure.md) ou um grupo de disponibilidade em [várias regiões.](availability-group-manually-configure-multiple-regions.md)