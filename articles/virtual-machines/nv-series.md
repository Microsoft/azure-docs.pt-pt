---
title: Série NV - Máquinas Virtuais Azure
description: Especificações para os VMs da série NV.
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 7fd9fff87564b790915acf01373c078c2012371e
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273875"
---
# <a name="nv-series"></a>Série NV

As máquinas virtuais da série NV são alimentadas pela tecnologia [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPUs e NVIDIA GRID para aplicações aceleradas de desktop e desktops virtuais onde os clientes são capazes de visualizar os seus dados ou simulações. Os utilizadores são capazes de visualizar os seus fluxos de trabalho intensivos gráficos nas instâncias de NV para obter uma capacidade gráfica superior e, adicionalmente, executar cargas de trabalho de precisão única, como codificação e renderização. Os VMs da série NV também são alimentados por Intel Xeon E5-2690 v3 (Haswell) CPUs.

Cada GPU em instâncias de NV vem com uma licença GRID. Esta licença dá-lhe a flexibilidade para usar uma instância de NV como uma estação de trabalho virtual para um único utilizador, ou 25 utilizadores simultâneos podem ligar-se ao VM para um cenário de aplicação virtual.

Armazenamento Premium: Não Suportado

Caching de armazenamento premium: Não suportado

Migração Ao Vivo: Não Suportado

Atualizações de preservação da memória: não suportadas

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | GPU | Memória DE GPU: GiB | Discos de dados máximos | NICs máximos | Estações de Trabalho Virtuais | Aplicações Virtuais |
|---|---|---|---|---|---|---|---|---|---|
| Standard_NV6  | 6  | 56  | 340  | 1 | 8  | 24 | 1 | 1 | 25  |
| Standard_NV12 | 12 | 112 | 680  | 2 | 16 | 48 | 2 | 2 | 50  |
| Standard_NV24 | 24 | 224 | 1440 | 4 | 32 | 64 | 4 | 4 | 100 |

1 GPU = metade de uma placa M60.

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
