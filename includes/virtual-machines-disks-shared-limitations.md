---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/03/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: ce964ac197fbff64bbb7cc36e8c2bf762f93663f
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84337360"
---
Durante a pré-visualização, permitir discos partilhados só está disponível para um subconjunto de tipos de discos. Atualmente apenas discos ultra e SSDs premium podem ativar discos partilhados. Cada disco gerido que tenha discos partilhados ativados estão sujeitos às seguintes limitações, organizadas por tipo de disco:

### <a name="ultra-disks"></a>Discos Ultra

Os discos ultra têm a sua própria lista de limitações separadas, não relacionadas com discos partilhados. Para obter limitações ultra disco, consulte a [Utilização de discos ultra Azure](../articles/virtual-machines/linux/disks-enable-ultra-ssd.md).

Ao partilhar discos ultra, têm as seguintes limitações adicionais:

- Atualmente limitado ao Azure Resource Manager ou suporte SDK.
- Apenas discos básicos podem ser utilizados com algumas versões do Windows Server Failover Cluster, para mais detalhes ver [os requisitos de hardware e opções de armazenamento de clustering failover.](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements)

### <a name="premium-ssds"></a>SSDs Premium

- Atualmente, apenas apoiado na região centro-americana.
- Todas as máquinas virtuais que partilham um disco devem ser implantadas nos [mesmos grupos de colocação de proximidade.](../articles/virtual-machines/windows/proximity-placement-groups.md)
- Só pode ser ativado em discos de dados, não em discos de SO.
- Apenas discos básicos podem ser utilizados com algumas versões do Windows Server Failover Cluster, para mais detalhes ver [os requisitos de hardware e opções de armazenamento de clustering failover.](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements)
- O caching do anfitrião ReadOnly não está disponível para SSDs premium com `maxShares>1` .
- Os conjuntos de disponibilidade e a balança de máquinas virtuais só podem ser utilizados com `FaultDomainCount` o conjunto de 1.
- O suporte de backup e recuperação do local de Azure ainda não está disponível.

Se está interessado em experimentar discos partilhados, então [inscreva-se para a nossa pré-visualização](https://aka.ms/AzureSharedDiskPreviewSignUp).
