---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/30/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 9aa8857ab10423f460874870a3663929e8e5a5c5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91566958"
---
Permitir discos partilhados só está disponível para um subconjunto de tipos de discos. Atualmente apenas discos ultra e SSDs premium podem ativar discos partilhados. Cada disco gerido que tenha discos partilhados ativados estão sujeitos às seguintes limitações, organizadas por tipo de disco:

### <a name="ultra-disks"></a>Discos Ultra

Os discos ultra têm a sua própria lista de limitações separadas, não relacionadas com discos partilhados. Para obter limitações ultra disco, consulte a [Utilização de discos ultra Azure](../articles/virtual-machines/disks-enable-ultra-ssd.md).

Ao partilhar discos ultra, têm as seguintes limitações adicionais:

- Atualmente limitado ao Azure Resource Manager ou suporte SDK. 
- Apenas discos básicos podem ser utilizados com algumas versões do Windows Server Failover Cluster, para mais detalhes ver [os requisitos de hardware e opções de armazenamento de clustering failover.](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements)

Os discos ultra partilhados estão disponíveis em todas as regiões que suportam discos ultra por padrão, e não exigem que se inscreva para ter acesso a usá-los.

### <a name="premium-ssds"></a>Discos SSD Premium

- Atualmente limitado ao Azure Resource Manager ou suporte SDK. 
- Só pode ser ativado em discos de dados, não em discos de SO.
- O caching do anfitrião **ReadOnly** não está disponível para SSDs premium com `maxShares>1` .
- A explosão do disco não está disponível para SSDs premium com `maxShares>1` .
- Ao utilizar conjuntos de disponibilidade e conjuntos de escala de máquina virtual com discos partilhados Azure, o [alinhamento do domínio de falha de armazenamento](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#use-managed-disks-for-vms-in-an-availability-set) com o domínio de falha da máquina virtual não é aplicado para o disco de dados partilhado.
- Ao utilizar [grupos de colocação de proximidade (PPG),](../articles/virtual-machines/windows/proximity-placement-groups.md)todas as máquinas virtuais que partilham um disco devem fazer parte do mesmo PPG.
- Apenas discos básicos podem ser utilizados com algumas versões do Windows Server Failover Cluster, para mais detalhes ver [os requisitos de hardware e opções de armazenamento de clustering failover.](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements)
- O suporte de backup e recuperação do local de Azure ainda não está disponível.

#### <a name="regional-availability"></a>Disponibilidade regional

Os SSDs premium partilhados estão disponíveis em todas as regiões que os discos geridos estão disponíveis.