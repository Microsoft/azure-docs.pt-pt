---
title: Série NCv3 - Máquinas Virtuais Azure
description: Especificações para os VMs da série NCv3.
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: cdd2a1b25eec4f649b062721200349d932fb7599
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273908"
---
# <a name="ncv3-series"></a>Série NCv3

Os VMs da série NCv3 são alimentados por [GPUs NVIDIA Tesla V100.](https://www.nvidia.com/data-center/tesla-v100/) Estes GPUs podem fornecer 1,5x o desempenho computacional da série NCv2. Os clientes podem tirar partido destas GPUs atualizadas para cargas de trabalho tradicionais de HPC, tais como modelação de reservatórios, sequenciação de ADN, análise de proteínas, simulações de Monte Carlo, entre outros. A configuração NC24rs v3 proporciona uma interface de rede de baixa latência e alta potência otimizada para trabalhos de computação paralela salutares. Além das GPUs, os VMs da série NCv3 também são alimentados por CpUs Intel Xeon E5-2690 v4 (Broadwell).

Armazenamento Premium: Suportado

Caching de armazenamento premium: Suportado

Migração Ao Vivo: Não Suportado

Atualizações de preservação da memória: não suportadas

> [!IMPORTANT]
> Para esta série VM, a quota vCPU (core) na sua subscrição está inicialmente definida para 0 em cada região. [Solicite um aumento de quota vCPU](../azure-supportability/resource-manager-core-quotas-request.md) para esta série numa [região disponível.](https://azure.microsoft.com/regions/services/)
>
| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | GPU | Memória DE GPU: GiB | Discos de dados máximos | Potência de disco max uncached: IOPS/MBps | NICs máximos |
|---|---|---|---|---|---|---|---|---|
| Standard_NC6s_v3    | 6  | 112 | 736  | 1 | 16 | 12 | 20000/200 | 4 |
| Standard_NC12s_v3   | 12 | 224 | 1474 | 2 | 32 | 24 | 40000/400 | 8 |
| Standard_NC24s_v3   | 24 | 448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |
| Standard_NC24rs_v3* | 24 | 448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |

1 GPU = um cartão V100.

*RDMA capaz [!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

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
