---
title: Série NV - Azure Virtual Machines
description: Especificações para os VMs da série NV.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 6dd912313176eecce1973849875234149700c5e6
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87284783"
---
# <a name="nv-series"></a>Série NV

As máquinas virtuais da série NV são alimentadas pela [tecnologia NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPUs e NVIDIA GRID para aplicações aceleradas no ambiente de trabalho e desktops virtuais onde os clientes são capazes de visualizar os seus dados ou simulações. Os utilizadores são capazes de visualizar os seus fluxos de trabalho intensivos gráficos nas instâncias NV para obter uma capacidade gráfica superior e, adicionalmente, executar cargas de trabalho de precisão única, tais como codificação e renderização. Os VMs da série NV também são alimentados por CPUs Intel Xeon E5-2690 v3 (Haswell).

Cada GPU em instâncias NV vem com uma licença GRID. Esta licença dá-lhe a flexibilidade para usar uma instância NV como uma estação de trabalho virtual para um único utilizador, ou 25 utilizadores simultâneos podem ligar-se ao VM para um cenário de aplicação virtual.

Armazenamento Premium: Não Suportado

Caching de armazenamento premium: Não suportado

Migração ao vivo: Não suportado

Atualizações de preservação da memória: Não suportadas

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | GPU | Memória gpu: GiB | Discos de dados máximos | NICs máximos | Estações de trabalho virtuais | Aplicações Virtuais |
|---|---|---|---|---|---|---|---|---|---|
| Standard_NV6  | 6  | 56  | 340  | 1 | 8  | 24 | 1 | 1 | 25  |
| Standard_NV12 | 12 | 112 | 680  | 2 | 16 | 48 | 2 | 2 | 50  |
| Standard_NV24 | 24 | 224 | 1440 | 4 | 32 | 64 | 4 | 4 | 100 |

1 GPU = metade de uma placa M60.

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
