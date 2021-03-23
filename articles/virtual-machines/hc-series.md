---
title: Série HC - Azure Virtual Machines
description: Especificações para os VMs da série HC.
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 03/05/2021
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: 43f8b6efd7c4cc510dac8fcbe84ab38f59008ed9
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104773538"
---
# <a name="hc-series"></a>Série HC

Os VMs da série HC são otimizados para aplicações impulsionadas por computação densa, tais como análise implícita de elementos finitos, dinâmica molecular e química computacional. Os VMS HC dispõem de 44 núcleos de processador Intel Xeon Platinum 8168, 8 GB de RAM por núcleo CPU e sem hiperligação. A plataforma Intel Xeon Platinum suporta o rico ecossistema de ferramentas de software da Intel, como a Biblioteca Intel Math Kernel e capacidades avançadas de processamento de vetores, como o AVX-512.

Os VMs da série HC apresentam 100 Gb/sec Mellanox EDR InfiniBand. Estes VMs estão ligados numa árvore de gordura não bloqueada para um desempenho RDMA otimizado e consistente. Estes VMs suportam o Encaminhamento Adaptativo e o Transporte Conectado Dinâmico (DCT, adicional aos transportes RC e UD padrão). Estas funcionalidades aumentam o desempenho da aplicação, escalabilidade e consistência, e a sua utilização é recomendada. 

[ACU](acu.md): 297-315<br>
[Armazenamento Premium](premium-storage-performance.md): Suportado<br>
[Caching premium armazenamento](premium-storage-performance.md): Suportado<br>
[Migração ao Vivo](maintenance-and-updates.md): Não Suportado<br>
[Atualizações de preservação da memória](maintenance-and-updates.md): Não suportado<br>
[Suporte de geração VM](generation-2.md): Geração 1 e 2<br>
[Rede Acelerada](../virtual-network/create-vm-accelerated-networking-cli.md): Suportado[(Saiba mais](https://techcommunity.microsoft.com/t5/azure-compute/accelerated-networking-on-hb-hc-hbv2-and-ndv2/ba-p/2067965) sobre desempenho e questões potenciais)<br>
[Discos EFÉMEROS](ephemeral-os-disks.md): Suportados <br>
<br>

| Tamanho | vCPU | Processador | Memória (GiB) | Largura de banda de memória GB/s | Frequência base cpu (GHz) | Frequência all-cores (GHz, pico) | Frequência monomússária (GHz, pico) | Desempenho de RDMA (Gb/s) | Suporte mpi | Armazenamento temporário (GiB) | Discos de dados máximos | Max Ethernet vNICs |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HC44rs | 44 | Intel Xeon Platinum 8168 | 352 | 191 | 2.7 | 3.4 | 3.7 | 100 | Todos | 700 | 4 | 8 |

Saiba mais sobre:
- [arquitetura e topologia VM,](./workloads/hpc/hc-series-overview.md)
- pilha de [software](./workloads/hpc/hc-series-overview.md#software-specifications) suportado, incluindo OS suportado, e
- [desempenho](./workloads/hpc/hc-series-performance.md) esperado da série HC VM.

[!INCLUDE [hpc-include](./workloads/hpc/includes/hpc-include.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Outros tamanhos

- [Fins gerais](sizes-general.md)
- [Com otimização de memória](sizes-memory.md)
- [Com otimização de armazenamento](sizes-storage.md)
- [Otimizada para GPU](sizes-gpu.md)
- [Computação de elevado desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Passos seguintes

- Leia sobre os últimos anúncios, exemplos de carga de trabalho da HPC e resultados de desempenho nos [Blogs comunitários Azure Compute Tech](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Para uma visão arquitetónica de alto nível da execução das cargas de trabalho do HPC, consulte [a High Performance Computing (HPC) em Azure](/azure/architecture/topics/high-performance-computing/).
- Saiba mais sobre como [as unidades de computação Azure (ACU)](acu.md) podem ajudá-lo a comparar o desempenho do cálculo em Azure SKUs.
