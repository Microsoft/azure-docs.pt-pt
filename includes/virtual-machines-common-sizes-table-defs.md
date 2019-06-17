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
ms.openlocfilehash: ad21dbb356a6630cec0a6f5658b20cb8568890f9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66145728"
---
<!-- Not used for Ls-series -->

## <a name="size-table-definitions"></a>Definições da tabela de dimensionamento

- A capacidade de armazenamento é apresentada em unidades de GiB ou 1024^3 bytes. Quando comparar discos medidos em GB (1000^3 bytes) com discos medidos em GiB (1024^3), não se esqueça de que os números de capacidade especificados em GiB podem aparecer mais pequenos. Por exemplo, 1023 GiB = 1098,4 GB
- O débito do disco é medido em operações de entrada/saída por segundo (IOPS) e MBps, em que MBps = 10^6 bytes/seg.
- Os discos de dados podem operar nos modos em cache ou não colocado em cache. Para uma operação do disco de dados em cache, o modo de cache do anfitrião está definido como **ReadOnly** ou **ReadWrite**.  Para uma operação do disco de dados não colocada em cache, o modo de cache do anfitrião está definido como **None**.
-   Se quiser obter o melhor desempenho para as suas VMs, deve limitar o número de discos de dados de 2 discos por vCPU.
- **Esperado de largura de banda de rede** é o máximo agregado [largura de banda alocada por tipo de VM](../articles/virtual-network/virtual-machine-network-throughput.md) em todas as NICs, para todos os destinos. Os limites superiores não são garantidos, mas foram concebidos para fornecer orientações para selecionar o tipo de VM correto para a aplicação pretendida. O desempenho de rede real irá depender de vários fatores, incluindo congestionamento, cargas e definições da rede. Para obter mais informações sobre a otimização do débito de rede, veja [Otimizar o débito de rede para Windows e Linux](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md). Para alcançar o desempenho de rede esperado no Linux ou no Windows, poderá ser necessário selecionar uma versão específica ou otimizar a VM. Para obter mais informações, veja [Como fazer um teste fiável de um débito de máquina virtual](../articles/virtual-network/virtual-network-bandwidth-testing.md).



