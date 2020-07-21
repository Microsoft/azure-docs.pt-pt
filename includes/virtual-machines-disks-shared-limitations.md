---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/14/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: d2cf7dbcd97c8f740447607eaf443bc3ea4a6733
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86500611"
---
Permitir discos partilhados só está disponível para um subconjunto de tipos de discos. Atualmente apenas discos ultra e SSDs premium podem ativar discos partilhados. Cada disco gerido que tenha discos partilhados ativados estão sujeitos às seguintes limitações, organizadas por tipo de disco:

### <a name="ultra-disks"></a>Discos Ultra

Os discos ultra têm a sua própria lista de limitações separadas, não relacionadas com discos partilhados. Para obter limitações ultra disco, consulte a [Utilização de discos ultra Azure](../articles/virtual-machines/linux/disks-enable-ultra-ssd.md).

Ao partilhar discos ultra, têm as seguintes limitações adicionais:

- Atualmente limitado ao Azure Resource Manager ou suporte SDK. 
- Apenas discos básicos podem ser utilizados com algumas versões do Windows Server Failover Cluster, para mais detalhes ver [os requisitos de hardware e opções de armazenamento de clustering failover.](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements)

### <a name="premium-ssds"></a>Discos SSD Premium

- Atualmente, apenas apoiado na região centro-americana.
- Atualmente limitado ao Azure Resource Manager ou suporte SDK. 
- Só pode ser ativado em discos de dados, não em discos de SO.
- O caching do anfitrião **ReadOnly** não está disponível para SSDs premium com `maxShares>1` .
- A explosão do disco não está disponível para SSDs premium com `maxShares>1` .
- Ao utilizar conjuntos de disponibilidade e conjuntos de escala de máquina virtual com discos partilhados Azure, o [alinhamento do domínio de falha de armazenamento](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#use-managed-disks-for-vms-in-an-availability-set) com o domínio de falha da máquina virtual não é aplicado para o disco de dados partilhado.
- Ao utilizar [grupos de colocação de proximidade (PPG),](../articles/virtual-machines/windows/proximity-placement-groups.md)todas as máquinas virtuais que partilham um disco devem fazer parte do mesmo PPG.
- Apenas discos básicos podem ser utilizados com algumas versões do Windows Server Failover Cluster, para mais detalhes ver [os requisitos de hardware e opções de armazenamento de clustering failover.](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements)
- O suporte de backup e recuperação do local de Azure ainda não está disponível.

Se estiver interessado em experimentar discos partilhados, [inscreva-se para acesso](https://aka.ms/AzureSharedDiskGASignUp).