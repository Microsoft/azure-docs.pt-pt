---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 77fc6070010791bf96c944114929eba95842c9d4
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77471712"
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

Se está interessado em experimentar discos partilhados, [inscreva-se para a nossa pré-visualização](https://aka.ms/AzureSharedDiskPreviewSignUp).
