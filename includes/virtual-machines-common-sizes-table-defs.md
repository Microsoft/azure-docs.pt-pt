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
ms.openlocfilehash: 63c53a9b95e27486d7d6944c28f8fb085b1bc6ca
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74279181"
---
<!-- Not used for Ls-series -->

## <a name="size-table-definitions"></a>Definições da tabela de dimensionamento

- A capacidade de armazenamento é apresentada em unidades de GiB ou 1024^3 bytes. Quando comparar discos medidos em GB (1000^3 bytes) com discos medidos em GiB (1024^3) lembre-se que os números de capacidade dados no GiB podem parecer menores. Por exemplo, 1023 GiB = 1098,4 GB.
- O débito do disco é medido em operações de entrada/saída por segundo (IOPS) e MBps, em que MBps = 10^6 bytes/seg.
- Os discos de dados podem operar nos modos em cache ou não colocado em cache. Para uma operação do disco de dados em cache, o modo de cache do anfitrião está definido como **ReadOnly** ou **ReadWrite**.  Para uma operação do disco de dados não colocada em cache, o modo de cache do anfitrião está definido como **None**.
- Se quiser obter o melhor desempenho para os seus VMs, deve limitar o número de discos de dados a dois discos por vCPU.
- **A largura de banda esperada** da rede é a largura de banda agregada máxima atribuída por tipo VM em todos os NICs, para todos os destinos. Para mais informações, consulte a [largura de banda](../articles/virtual-network/virtual-machine-network-throughput.md)da rede virtual da rede de máquinas.

  Limites superiores não estão garantidos. Os limites oferecem orientação para selecionar o tipo de VM certo para a aplicação pretendida. O desempenho real da rede dependerá de vários fatores, incluindo congestionamento da rede, cargas de aplicação e definições de rede. Para obter informações sobre a otimização da entrada da rede, consulte a entrada da [rede Otimize para máquinas virtuais Azure](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md). Para alcançar o desempenho esperado da rede no Linux ou Windows, poderá ser necessário selecionar uma versão específica ou otimizar o seu VM. Para mais informações, consulte os [testes de largura de banda/de entrada (NTTTCP)](../articles/virtual-network/virtual-network-bandwidth-testing.md).



