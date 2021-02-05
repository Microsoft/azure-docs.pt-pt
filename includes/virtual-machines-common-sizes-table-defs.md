---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: d610193783d44b86d48cd5ceaa91377b32b7061b
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99579981"
---
<!-- Not used for Ls-series -->

## <a name="size-table-definitions"></a>Definições da tabela de dimensionamento

- A capacidade de armazenamento é apresentada em unidades de GiB ou 1024^3 bytes. Quando comparar os discos medidos em GB (1000^3 bytes) com discos medidos em GiB (1024^3) lembre-se que os números de capacidade dados em GiB podem parecer menores. Por exemplo, 1023 GiB = 1098,4 GB.
- O débito do disco é medido em operações de entrada/saída por segundo (IOPS) e MBps, em que MBps = 10^6 bytes/seg.
- Os discos de dados podem operar nos modos em cache ou não colocado em cache. Para uma operação do disco de dados em cache, o modo de cache do anfitrião está definido como **ReadOnly** ou **ReadWrite**.  Para uma operação do disco de dados não colocada em cache, o modo de cache do anfitrião está definido como **None**.
- Para aprender a obter o melhor desempenho de armazenamento para os seus VMs, consulte a [máquina virtual e o desempenho do disco.](../articles/virtual-machines/disks-performance.md)
- **A largura de banda de rede esperada** é a largura de banda agregada máxima atribuída por tipo VM em todos os NICs, para todos os destinos. Para obter mais informações, consulte [a largura de banda da rede de máquinas virtuais.](../articles/virtual-network/virtual-machine-network-throughput.md)

  Limites superiores não são garantidos. Os limites oferecem orientação para selecionar o tipo VM certo para a aplicação pretendida. O desempenho real da rede dependerá de vários fatores, incluindo o congestionamento da rede, as cargas de aplicação e as definições de rede. Para obter informações sobre a otimização do rendimento da rede, consulte [o rendimento da rede Otimize para máquinas virtuais Azure](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md). Para obter o desempenho esperado da rede no Linux ou Windows, poderá ter de selecionar uma versão específica ou otimizar o seu VM. Para obter mais informações, consulte [os testes de largura de banda/produção (NTTTCP)](../articles/virtual-network/virtual-network-bandwidth-testing.md).



