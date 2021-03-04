---
title: NCv3-série - Azure Virtual Machines
description: Especificações para os VMs da série NCv3.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 313ca9ddb893ad2d84fdb3f808bdee38dfc9e846
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102119761"
---
# <a name="ncv3-series"></a>Série NCv3

Os VMs da série NCv3 são alimentados por GPUs NVIDIA Tesla V100. Estas GPUs podem fornecer 1,5x o desempenho computacional da série NCv2. Os clientes podem tirar partido destas GPUs atualizadas para cargas de trabalho tradicionais de HPC, tais como modelação de reservatórios, sequenciação de ADN, análise de proteínas, simulações de Monte Carlo, entre outros. A configuração NC24rs v3 proporciona uma interface de rede de baixa latência e alta produção otimizada para cargas de trabalho de computação paralela bem acopladas. Além das GPUs, os VMs da série NCv3 também são alimentados por CPUs Intel Xeon E5-2690 v4 (Broadwell).

[Armazenamento Premium](premium-storage-performance.md): Suportado<br>
[Caching premium armazenamento](premium-storage-performance.md): Suportado<br>
[Migração ao Vivo](maintenance-and-updates.md): Não Suportado<br>
[Atualizações de preservação da memória](maintenance-and-updates.md): Não suportado<br>
[Suporte de geração VM](generation-2.md): Geração 1 e 2<br>
[Rede Acelerada](../virtual-network/create-vm-accelerated-networking-cli.md): Não Suportado<br>
[Discos EFÉMEROS :](ephemeral-os-disks.md)Não Suportados <br>
Nvidia NVLink Interconnect: Não suportado<br>

> [!IMPORTANT]
> Para esta série VM, a quota vCPU (core) na sua subscrição está inicialmente definida para 0 em cada região. [Solicitar um aumento da quota vCPU](../azure-portal/supportability/resource-manager-core-quotas-request.md) para esta série numa [região disponível](https://azure.microsoft.com/regions/services/). Estes SKUs não estão disponíveis para subscrições de Azure assinantes do Estúdio Visual ou do Visual Studio. O seu nível de subscrição pode não suportar a seleção ou implementação destes SKUs. 
>

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | GPU | Memória gpu: GiB | Discos de dados máximos | Produção de disco não-abacatado por maxilar: IOPS/MBps | NICs máximos |
|---|---|---|---|---|---|---|---|---|
| Standard_NC6s_v3    | 6  | 112 | 736  | 1 | 16 | 12 | 20000/200 | 4 |
| Standard_NC12s_v3   | 12 | 224 | 1474 | 2 | 32 | 24 | 40000/400 | 8 |
| Standard_NC24s_v3   | 24 | 448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |
| Standard_NC24rs_v3* | 24 | 448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |

1 GPU = uma carta V100.

*Com capacidade RDMA

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Sistemas operativos e controladores suportados

Para tirar partido das capacidades da GPU dos VMs da série Azure N, os controladores da NVIDIA GPU devem ser instalados.

A [extensão do condutor do GPU NVIDIA](./extensions/hpccompute-gpu-windows.md) instala controladores NVIDIA CUDA ou GRID apropriados num VM da série N. Instale ou gerencie a extensão utilizando o portal Azure ou ferramentas como os modelos Azure PowerShell ou Azure Resource Manager. Consulte a documentação da [extensão do condutor do GPU da NVIDIA](./extensions/hpccompute-gpu-windows.md) para sistemas operativos suportados e etapas de implantação. Para obter informações gerais sobre extensões de VM, consulte [extensões e funcionalidades de máquinas virtuais Azure](./extensions/overview.md).

Se optar por instalar manualmente os controladores da NVIDIA GPU, consulte a [configuração do controlador GPU da série N para](./windows/n-series-driver-setup.md) [configuração do controlador GPU do](./linux/n-series-driver-setup.md) Windows ou da série N para o Linux para sistemas operativos suportados, controladores, instalações e etapas de verificação.

## <a name="other-sizes"></a>Outros tamanhos

- [Fins gerais](sizes-general.md)
- [Com otimização de memória](sizes-memory.md)
- [Com otimização de armazenamento](sizes-storage.md)
- [Otimizada para GPU](sizes-gpu.md)
- [Computação de elevado desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como [as unidades de computação Azure (ACU)](acu.md) podem ajudá-lo a comparar o desempenho do cálculo em Azure SKUs.
