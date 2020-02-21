---
title: Série ND - Máquinas Virtuais Azure
description: Especificações para os VMs da série ND.
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 1f61c977dd82e8763fbc815de0a4764bed1fa560
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77493361"
---
# <a name="nd-series"></a>Série ND

As máquinas virtuais da série ND são uma nova adição à família GPU projetada para cargas de trabalho de IA e Deep Learning. Oferecem excelente desempenho para treino e inferência. As instâncias ND são alimentadas por [NVIDIA Tesla P40](https://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf) GPUs e Intel Xeon E5-2690 v4 (Broadwell) CPUs. Estes casos proporcionam um excelente desempenho para operações de ponto flutuante de precisão única, para cargas de trabalho de IA utilizando o Microsoft Cognitive Toolkit, TensorFlow, Caffe e outras estruturas. A série ND também oferece um tamanho de memória GPU muito maior (24 GB), permitindo encaixar modelos de rede neural muito maiores. Tal como a série NC, a série ND oferece uma configuração com uma rede secundária de baixa latência, de alta frequência através de RDMA, e conectividade InfiniBand para que possa executar trabalhos de formação em larga escala abrangendo muitas GPUs.

Armazenamento Premium: Suportado

Caching de armazenamento premium: Suportado

> [!IMPORTANT]
> Para esta série VM, a quota vCPU (core) por região na sua subscrição está inicialmente definida para 0. [Solicite um aumento de quota vCPU](../azure-supportability/resource-manager-core-quotas-request.md) para esta série numa [região disponível.](https://azure.microsoft.com/regions/services/)
>
| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | GPU | Memória DE GPU: GiB | Discos de dados máximos | Potência de disco max uncached: IOPS/MBps | NICs máximos |
|---|---|---|---|---|---|---|---|---|
| Standard_ND6s    | 6  | 112 | 736  | 1 | 24 | 12 | 20000/200 | 4 |
| Standard_ND12s   | 12 | 224 | 1474 | 2 | 48 | 24 | 40000/400 | 8 |
| Standard_ND24s   | 24 | 448 | 2948 | 4 | 96 | 32 | 80000/800 | 8 |
| Standard_ND24rs* | 24 | 448 | 2948 | 4 | 96 | 32 | 80000/800 | 8 |

1 GPU = um cartão P40.

*RDMA capaz [!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Sistemas operativos e controladores suportados

Para tirar partido das capacidades de GPU das VMs da série N Azure, os condutores de GPU da NVIDIA devem ser instalados.

A Extensão do [Condutor GPU da NVIDIA](/extensions/hpccompute-gpu-windows.md) instala os condutores adequados da NVIDIA CUDA ou grid num VM da série N. Instale ou gerea extensão utilizando o portal Azure ou ferramentas como os modelos Azure PowerShell ou Azure Resource Manager. Consulte a documentação de extensão do [condutor da NVIDIA GPU](/extensions/hpccompute-gpu-windows.md) para sistemas operativos suportados e etapas de implementação. Para obter informações gerais sobre extensões VM, consulte [extensões e funcionalidades da máquina virtual Azure.](/extensions/overview.md)

Se optar por instalar manualmente os controladores GPU da NVIDIA, consulte a configuração do controlador GPU da série N para a configuração do controlador [GPU da série N](/windows/n-series-driver-setup.md) para o [Linux](/linux/n-series-driver-setup) para sistemas operativos suportados, controladores, instalação e verificação.

## <a name="other-sizes"></a>Outros tamanhos

- [Fins gerais](sizes-general.md)
- [Com otimização de memória](sizes-memory.md)
- [Com otimização de armazenamento](sizes-storage.md)
- [Com otimização de GPU](sizes-gpu.md)
- [Computação de elevado desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como as unidades de [computação Azure (ACU)](acu.md) podem ajudá-lo a comparar o desempenho da computação em Azure SKUs.