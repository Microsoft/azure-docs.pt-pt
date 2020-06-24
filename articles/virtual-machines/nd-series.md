---
title: Série ND - Azure Virtual Machines
description: Especificações para os VMs da série ND.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: article
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 6b29125087bd60041f85b1b5023646b039bd6a4a
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/12/2020
ms.locfileid: "84736956"
---
# <a name="nd-series"></a>Série ND

As máquinas virtuais da série ND são uma nova adição à família GPU projetada para IA e cargas de trabalho deep learning. Oferecem excelente desempenho para treino e inferência. As instâncias ND são alimentadas por [GPUs NVIDIA Tesla P40](https://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf) e Intel Xeon E5-2690 v4 (Broadwell) CPUs. Estes casos proporcionam um excelente desempenho para operações de ponto flutuante de precisão única, para cargas de carga de IA utilizando o Microsoft Cognitive Toolkit, TensorFlow, Caffe e outras estruturas. A série ND também oferece um tamanho de memória GPU muito maior (24 GB), permitindo encaixar em modelos de rede neural muito maior. Tal como a série NC, a série ND oferece uma configuração com uma rede secundária de baixa latência, de alta produção através da RDMA, e conectividade InfiniBand para que possa executar trabalhos de formação em larga escala abrangendo muitas GPUs.

Armazenamento Premium: Suportado

Caching de armazenamento premium: Suportado

Migração ao vivo: Não suportado

Atualizações de preservação da memória: Não suportadas

> [!IMPORTANT]
> Para esta série VM, a quota vCPU (core) por região na sua subscrição está inicialmente definida para 0. [Solicitar um aumento da quota vCPU](../azure-supportability/resource-manager-core-quotas-request.md) para esta série numa [região disponível](https://azure.microsoft.com/regions/services/).
>
| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | GPU | Memória gpu: GiB | Discos de dados máximos | Produção de disco não-abacatado por maxilar: IOPS/MBps | NICs máximos |
|---|---|---|---|---|---|---|---|---|
| Standard_ND6s    | 6  | 112 | 736  | 1 | 24 | 12 | 20000/200 | 4 |
| Standard_ND12s   | 12 | 224 | 1474 | 2 | 48 | 24 | 40000/400 | 8 |
| Standard_ND24s   | 24 | 448 | 2948 | 4 | 96 | 32 | 80000/800 | 8 |
| Standard_ND24rs* | 24 | 448 | 2948 | 4 | 96 | 32 | 80000/800 | 8 |

1 GPU = uma carta P40.

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
- [Com otimização de GPU](sizes-gpu.md)
- [Computação de elevado desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como [as unidades de computação Azure (ACU)](acu.md) podem ajudá-lo a comparar o desempenho do cálculo em Azure SKUs.
