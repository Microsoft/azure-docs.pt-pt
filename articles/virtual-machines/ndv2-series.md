---
title: Série NDv2 - Máquinas Virtuais Azure
description: Especificações para os VMs da série NDv2.
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 1aa2a6402a58ba69a7b5999803bb10d48169a035
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2020
ms.locfileid: "78267445"
---
# <a name="updated-ndv2-series-preview"></a>Série NDv2 atualizada (Pré-visualização)

A máquina virtual da série NDv2 é uma nova adição à família GPU projetada para as necessidades da IA acelerada por GPU, machine learning, simulação e hPC.

O NDv2 é alimentado por 8 GPUs ligados a NVIDIA Tesla V100 NVLINK, cada um com 32 GB de memória GPU. Cada NDv2 VM também tem 40 núcleos Intel Xeon Platinum 8168 (Skylake) e 672 GiB de memória do sistema.

Os casos NDv2 proporcionam um excelente desempenho para as cargas de trabalho de HPC e IA utilizando os núcleos de computação otimizados pela CUDA GPU, e as muitas ferramentas de IA, ML e analytics que suportam a aceleração do GPU 'out-of-box', tais como TensorFlow, Pytorch, Caffe, RAPIDS, entre outros quadros.

Criticamente, o NDv2 é construído para cargas de trabalho computacionalmente intensas (aproveitando 8 GPUs por VM) e scale-out (aproveitando vários VMs que trabalham em conjunto). A série NDv2 suporta agora a rede de backend 100-Gigabit InfiniBand EDR, semelhante à disponível na série HB de HPC VM, para permitir o agrupamento de alto desempenho para cenários paralelos, incluindo formação distribuída para IA e ML. Esta rede de backend suporta todos os principais protocolos InfiniBand, incluindo os empregados pelas bibliotecas NCCL2 da NVIDIA, permitindo um agrupamento sem emenda de GPUs.


> [!NOTE]
> Ao [ativar a InfiniBand](https://docs.microsoft.com/azure/virtual-machines/workloads/hpc/enable-infiniband) no ND40rs_v2 VM, utilize o condutor 4.7-1.1.0.0.1 Mellanox OFED.
>
> Devido ao aumento da memória gpu, o novo ND40rs_v2 VM requer a utilização de [VMs](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2) de Geração 2 e imagens de mercado. 
>
> [Inscreva-se para solicitar acesso antecipado à pré-visualização da máquina virtual NDv2.](https://aka.ms/AzureNDrv2Preview)
>
> Nota: O ND40s_v2 com 16 GB de memória por GPU já não está disponível para pré-visualização e foi substituído pela ND40rs_v2 atualizada.

<br>

Armazenamento Premium: Suportado

Caching de armazenamento premium: Suportado

Migração Ao Vivo: Não Suportado

Atualizações de preservação da memória: não suportadas

InfiniBand: Suportado

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD): GiB | GPU | Memória GPU: GiB | Discos de dados máximos | Débito máximo do disco não colocado em cache: IOPS/MBps | Largura de banda de rede max | NICs máximos |
|---|---|---|---|---|---|---|---|---|---|
| Standard_ND40rs_v2 | 40 | 672 | 2948 | 8 V100 32 GB (NVLink) | 16 | 32 | 80000 / 800 | 24000 Mbps | 8 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Sistemas operativos e controladores suportados

Para tirar partido das capacidades de GPU das VMs da série N Azure, os condutores de GPU da NVIDIA devem ser instalados.

A Extensão do [Condutor GPU da NVIDIA](./extensions/hpccompute-gpu-windows.md) instala os condutores adequados da NVIDIA CUDA ou grid num VM da série N. Instale ou gerea extensão utilizando o portal Azure ou ferramentas como os modelos Azure PowerShell ou Azure Resource Manager. Consulte a documentação de extensão do [condutor da NVIDIA GPU](./extensions/hpccompute-gpu-windows.md) para sistemas operativos suportados e etapas de implementação. Para obter informações gerais sobre extensões VM, consulte [extensões e funcionalidades da máquina virtual Azure.](./extensions/overview.md)

Se optar por instalar manualmente os controladores GPU da NVIDIA, consulte a configuração do controlador GPU da série N para a configuração do controlador [GPU da série N](./windows/n-series-driver-setup.md) para o [Linux](./linux/n-series-driver-setup.md) para sistemas operativos suportados, controladores, instalação e verificação.

## <a name="other-sizes"></a>Outros tamanhos

- [Fins gerais](sizes-general.md)
- [Com otimização de memória](sizes-memory.md)
- [Com otimização de armazenamento](sizes-storage.md)
- [Com otimização de GPU](sizes-gpu.md)
- [Computação de elevado desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como as unidades de [computação Azure (ACU)](acu.md) podem ajudá-lo a comparar o desempenho da computação em Azure SKUs.
