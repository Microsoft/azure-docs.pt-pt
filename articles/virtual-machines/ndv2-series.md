---
title: Série NDv2
description: Especificações para os VMs da série NDv2.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 7a417def30fcb3a2b429151b9d4c9ba4152220a1
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101669164"
---
# <a name="updated-ndv2-series"></a>Série NDv2 atualizada

A máquina virtual da série NDv2 é uma nova adição à família GPU projetada para as necessidades da mais exigente IA acelerada pela GPU, machine learning, simulação e cargas de trabalho HPC.

O NDv2 é alimentado por 8 GPUs ligados ao NVIDIA Tesla V100, cada um com 32 GB de memória GPU. Cada NDv2 VM também tem 40 núcleos Intel Xeon Platinum 8168 (Skylake) não-HyperThreaded e 672 GiB de memória do sistema.

As instâncias NDv2 proporcionam um excelente desempenho para as cargas de trabalho de HPC e IA utilizando núcleos de computação otimizados pela CUDA GPU, e as muitas ferramentas de IA, ML e analítica que suportam a aceleração da GPU 'fora de caixa', tais como TensorFlow, Pytorch, Caffe, RAPIDS e outros quadros.

Criticamente, o NDv2 é construído para uma escala computacionalmente intensa (aproveitando 8 GPUs por VM) e cargas de carga de escala (aproveitando várias VMs trabalhando em conjunto). A série NDv2 suporta agora a rede de backend EDR 100 Gigabit InfiniBand EDR, semelhante à disponível na série HB de HPC VM, para permitir o agrupamento de alto desempenho para cenários paralelos, incluindo formação distribuída para IA e ML. Esta rede de backend suporta todos os principais protocolos InfiniBand, incluindo os utilizados pelas bibliotecas NCCL2 da NVIDIA, permitindo um agrupamento sem emendas de GPUs.

> [!IMPORTANT]
> Quando [ativar a InfiniBand](./workloads/hpc/enable-infiniband.md) no VM ND40rs_v2, utilize o controlador 4.7-1.0.0.1 Mellanox OFED.
>
> Devido ao aumento da memória da GPU, o novo ND40rs_v2 VM requer a utilização de [VMs de Geração 2](./generation-2.md) e imagens de marketplace. 
>
> Nota: O ND40s_v2 com 16 GB de memória por GPU já não está disponível para pré-visualização e foi substituído pela ND40rs_v2 atualizada.

<br>

[Armazenamento Premium](premium-storage-performance.md): Suportado<br>
[Caching premium armazenamento](premium-storage-performance.md): Suportado<br>
[Migração ao Vivo](maintenance-and-updates.md): Não Suportado<br>
[Atualizações de preservação da memória](maintenance-and-updates.md): Não suportado<br>
[Suporte vm geração](generation-2.md): Geração 2<br>
[Rede Acelerada](../virtual-network/create-vm-accelerated-networking-cli.md): Suportado<br>
[Discos EFÉMEROS :](ephemeral-os-disks.md)Não Suportados <br>
InfiniBand: Suportado<br>
Nvidia NVLink Interconnect: Suportado<br>
<br>

| Tamanho | vCPU | Memória: GiB | Armazenamento Temporário (SSD): GiB | GPU | Memória GPU: GiB | Discos de dados máximos | Débito máximo do disco não colocado em cache: IOPS/MBps | Largura de banda da rede Max | NICs máximos |
|---|---|---|---|---|---|---|---|---|---|
| Standard_ND40rs_v2 | 40 | 672 | 2948 | 8 V100 32 GB (NVLink) | 32 | 32 | 80000 / 800 | 24000 Mbps | 8 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Sistemas operativos e controladores suportados

Para tirar partido das capacidades da GPU dos VMs da série Azure N, os controladores da NVIDIA GPU devem ser instalados.

A [extensão do condutor do GPU NVIDIA](./extensions/hpccompute-gpu-linux.md) instala controladores NVIDIA CUDA ou GRID apropriados num VM da série N. Instale ou gerencie a extensão utilizando o portal Azure ou ferramentas como os modelos Azure PowerShell ou Azure Resource Manager. Para obter informações gerais sobre extensões de VM, consulte [extensões e funcionalidades de máquinas virtuais Azure](./extensions/overview.md).

Se optar por instalar manualmente os controladores da NVIDIA GPU, consulte a [configuração do controlador gpu da série N para o Linux](./linux/n-series-driver-setup.md).

## <a name="other-sizes"></a>Outros tamanhos

- [Fins gerais](sizes-general.md)
- [Com otimização de memória](sizes-memory.md)
- [Com otimização de armazenamento](sizes-storage.md)
- [Otimizada para GPU](sizes-gpu.md)
- [Computação de elevado desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como [as unidades de computação Azure (ACU)](acu.md) podem ajudá-lo a comparar o desempenho do cálculo em Azure SKUs.
