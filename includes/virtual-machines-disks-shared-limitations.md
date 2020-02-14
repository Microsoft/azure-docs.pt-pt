---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/13/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4c150d874d56e3de495b0682bee979d13304a01d
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77202219"
---
Durante a pré-visualização, os discos geridos que tenham discos partilhados estão sujeitos às seguintes limitações:

- Atualmente apenas disponível com SSDs premium.
- Atualmente apenas apoiado na região centro-oeste dos EUA.
- Todas as máquinas virtuais que partilhem um disco devem ser implantadas nos [mesmos grupos](../articles/virtual-machines/windows/proximity-placement-groups.md)de colocação de proximidade.
- Só pode ser ativado em discos de dados, não em discos OS.
- Apenas os discos básicos podem ser usados com algumas versões do Cluster Failover do Windows Server, para detalhes ver requisitos de hardware de [clusterde Failover e opções](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements)de armazenamento .
- LeiaSó o caching do hospedeiro não está disponível para SSDs premium com `maxShares>1`.
- Os conjuntos de disponibilidade e os conjuntos de escala de máquinas virtuais só podem ser utilizados com `FaultDomainCount` definido si para 1.
- O suporte de backup azure e de recuperação do site Azure ainda não está disponível.

Se está interessado em experimentar discos partilhados, [inscreva-se para a nossa pré-visualização](https://aka.ms/shareddisksignup).
