---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/09/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 1e53dda2c6cb293a9204f344d152daa1937aa38b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81008362"
---
Durante a pré-visualização, a ativação de discos partilhados só está disponível para um subconjunto de tipos de discos. Atualmente, apenas discos ultra e SSDs premium podem ativar discos partilhados. Cada disco gerido que tenha discos partilhados está sujeito às seguintes limitações, organizadas por tipo de disco:

### <a name="ultra-disks"></a>Discos Ultra

Os discos ultra têm a sua própria lista separada de limitações, não relacionadas com discos partilhados. Para limitações ultra discos, consulte a Utilização de [discos ultra Azure](../articles/virtual-machines/linux/disks-enable-ultra-ssd.md).

Ao partilhar discos ultra, têm as seguintes limitações adicionais:

- Atualmente apenas apoiado nos EUA Ocidentais.
- Atualmente limitado ao Gestor de Recursos Azure ou suporte SDK.
- Apenas os discos básicos podem ser usados com algumas versões do Cluster Failover do Windows Server, para detalhes ver requisitos de hardware de [clusterde Failover e opções](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements)de armazenamento .

### <a name="premium-ssds"></a>SSDs Premium

- Atualmente apenas apoiado na região centro-oeste dos EUA.
- Todas as máquinas virtuais que partilhem um disco devem ser implantadas nos [mesmos grupos](../articles/virtual-machines/windows/proximity-placement-groups.md)de colocação de proximidade.
- Só pode ser ativado em discos de dados, não em discos OS.
- Apenas os discos básicos podem ser usados com algumas versões do Cluster Failover do Windows Server, para detalhes ver requisitos de hardware de [clusterde Failover e opções](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements)de armazenamento .
- LeiaSó o cache do hospedeiro não está `maxShares>1`disponível para SSDs premium com .
- Os conjuntos de disponibilidade e os conjuntos `FaultDomainCount` de escala de máquinas virtuais só podem ser utilizados com o conjunto de 1.
- O suporte de backup azure e de recuperação do site Azure ainda não está disponível.

Se está interessado em experimentar discos partilhados, [inscreva-se para a nossa pré-visualização](https://aka.ms/AzureSharedDiskPreviewSignUp).
