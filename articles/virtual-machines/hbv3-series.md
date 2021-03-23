---
title: Série HBv3 - Azure Virtual Machines
description: Especificações para os VMs da série HBv3.
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 03/12/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: b435547d7cd3b0fab781cc6cf3b617b13ac1120a
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104774699"
---
# <a name="hbv3-series"></a>Série HBv3

Os VMs da série HBv3 são otimizados para aplicações HPC tais como dinâmica de fluidos, análise explícita e implícita de elementos finitos, modelação meteorológica, processamento sísmico, simulação de reservatório e simulação de RTL. Os HBv3 VMs apresentam até 120 AMD EPYC™ núcleos cpU de série 7003 (Milão), 448 GB de RAM e nenhuma hiperfinal. Os VMs da série HBv3 também fornecem 350 GB/seg de largura de banda de memória, até 32 MB de cache L3 por núcleo, até 7 GB/s de desempenho SSD do dispositivo de bloco, e frequências do relógio até 3,675 GHz. 

Todos os VMs da série HBv3 apresentam 200 Gb/sec HDR InfiniBand da NVIDIA Networking para permitir cargas de cargas de MPI em escala de supercomputadores. Estes VMs estão ligados numa árvore de gordura não bloqueada para um desempenho RDMA otimizado e consistente. O tecido HDR InfiniBand também suporta o Encaminhamento Adaptativo e o Transporte Conectado Dinâmico (DCT, adicional aos transportes RC e UD padrão). Estas funcionalidades aumentam o desempenho da aplicação, escalabilidade e consistência, e a sua utilização é fortemente recomendada.

[Armazenamento Premium](premium-storage-performance.md): Suportado<br>
[Caching premium armazenamento](premium-storage-performance.md): Suportado<br>
[Migração ao Vivo](maintenance-and-updates.md): Não Suportado<br>
[Atualizações de preservação da memória](maintenance-and-updates.md): Não suportado<br>
[Suporte de geração VM](generation-2.md): Geração 1 e 2<br>
[Rede Acelerada](../virtual-network/create-vm-accelerated-networking-cli.md): Em breve<br>
[Discos EFÉMEROS :](ephemeral-os-disks.md)Não Suportados <br>
<br>

|Tamanho |vCPU |Processador |Memória (GiB) |Largura de banda de memória GB/s |Frequência base cpu (GHz) |Frequência all-cores (GHz, pico) |Frequência monomússária (GHz, pico) |Desempenho de RDMA (Gb/s) |Suporte mpi |Armazenamento temporário (GiB) |Discos de dados máximos |Max Ethernet vNICs |
|----|----|----|----|----|----|----|----|----|----|----|----|----|
|Standard_HB120rs_v3    |120 |AMD EPYC 7V13 |448 |350 |2.45 |3,1 |3.675 |200 |Todos |2 * 960 |32 |8 |
|Standard_HB120 96rs_v3 |96  |AMD EPYC 7V13 |448 |350 |2.45 |3,1 |3.675 |200 |Todos |2 * 960 |32 |8 |
|Standard_HB120 64rs_v3 |64  |AMD EPYC 7V13 |448 |350 |2.45 |3,1 |3.675 |200 |Todos |2 * 960 |32 |8 |
|Standard_HB120-32rs_v3 |32  |AMD EPYC 7V13 |448 |350 |2.45 |3,1 |3.675 |200 |Todos |2 * 960 |32 |8 |
|Standard_HB120-16rs_v3 |16  |AMD EPYC 7V13 |448 |350 |2.45 |3,1 |3.675 |200 |Todos |2 * 960 |32 |8 |

Saiba mais sobre:
- [arquitetura e topologia VM,](./workloads/hpc/hbv3-series-overview.md)
- pilha de [software](./workloads/hpc/hbv3-series-overview.md#software-specifications) suportado, incluindo OS suportado, e
- [desempenho](./workloads/hpc/hbv3-performance.md) esperado do VM da série HBv3.

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

- Leia sobre os últimos anúncios, exemplos de carga de trabalho do HPC e resultados de desempenho nos [Blogs comunitários Azure Compute Tech.](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)
- Para uma visão arquitetónica de nível mais elevado da execução das cargas de trabalho do HPC, consulte [a High Performance Computing (HPC) em Azure](/azure/architecture/topics/high-performance-computing/).
- Saiba mais sobre como [as unidades de computação Azure (ACU)](acu.md) podem ajudá-lo a comparar o desempenho do cálculo em Azure SKUs.
