---
title: Série NVv3 - Máquinas Virtuais Azure
description: Especificações para os VMs da série NVv3.
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: d74b00a4bade956d3a511a47b0a6b0011b9fb212
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78267417"
---
# <a name="nvv3-series"></a>Série NVv3

As máquinas virtuais da série NVv3 são alimentadas pela tecnologia [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPUs e NVIDIA GRID com cpUs Intel E5-2690 v4 (Broadwell) e Intel Hyper-Threading Technology. Estas máquinas virtuais são direcionadas para aplicações gráficas aceleradas de GPU e desktops virtuais onde os clientes querem visualizar os seus dados, simular resultados para visualizar, trabalhar em CAD ou renderizar e transmitir conteúdo. Além disso, estas máquinas virtuais podem executar cargas de trabalho de precisão única, tais como codificação e renderização. As máquinas virtuais NVv3 suportam o Armazenamento Premium e vêm com o dobro da memória do sistema (RAM) quando comparadas com a sua série NV antecessora.  

Cada GPU em instâncias NVv3 vem com uma licença GRID. Esta licença dá-lhe a flexibilidade para usar uma instância de NV como uma estação de trabalho virtual para um único utilizador, ou 25 utilizadores simultâneos podem ligar-se ao VM para um cenário de aplicação virtual.

Caching de armazenamento premium: Suportado

Migração Ao Vivo: Não Suportado

Atualizações de preservação da memória: não suportadas

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | GPU | Memória DE GPU: GiB | Discos de dados máximos | Potência de disco max uncached: IOPS/MBps | NICs máximos | Estações de Trabalho Virtuais | Aplicações Virtuais |
|---|---|---|---|---|---|---|---|---|---|---|
| Standard_NV12s_v3 |12 | 112 | 320  | 1 | 8  | 12 | 20000/200 | 4 | 1 | 25  |
| Standard_NV24s_v3 |24 | 224 | 640  | 2 | 16 | 24 | 40000/400 | 8 | 2 | 50  |
| Standard_NV48s_v3 |48 | 448 | 1280 | 4 | 32 | 32 | 80000/800 | 8 | 4 | 100 |

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
