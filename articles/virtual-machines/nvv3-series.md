---
title: Série NVv3 - Azure Virtual Machines
description: Especificações para os VMs da série NVv3.
services: virtual-machines
ms.subservice: sizes
author: vikancha-MSFT
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 9f5d5bd9775507a663a4171faa8b8892357f65b0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84736548"
---
# <a name="nvv3-series"></a>Série NVv3

As máquinas virtuais da série NVv3 são alimentadas pela [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPUs e pela tecnologia NVIDIA GRID com CPUs Intel E5-2690 v4 (Broadwell) e Tecnologia de Hiper-Threading Intel. Estas máquinas virtuais são direcionadas para aplicações gráficas aceleradas da GPU e desktops virtuais onde os clientes querem visualizar os seus dados, simular resultados para ver, trabalhar no CAD ou renderizar e transmitir conteúdo. Além disso, estas máquinas virtuais podem executar cargas de trabalho de precisão única, tais como codificação e renderização. As máquinas virtuais NVv3 suportam o Armazenamento Premium e vêm com o dobro da memória do sistema (RAM) quando comparadas com as suas séries NV antecessoras.  

Cada GPU em instâncias NVv3 vem com uma licença GRID. Esta licença dá-lhe a flexibilidade para usar uma instância NV como uma estação de trabalho virtual para um único utilizador, ou 25 utilizadores simultâneos podem ligar-se ao VM para um cenário de aplicação virtual.

Caching de armazenamento premium: Suportado

Migração ao vivo: Não suportado

Atualizações de preservação da memória: Não suportadas

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | GPU | Memória gpu: GiB | Discos de dados máximos | Produção de disco não-abacatado por maxilar: IOPS/MBps | NICs máximos | Estações de trabalho virtuais | Aplicações Virtuais |
|---|---|---|---|---|---|---|---|---|---|---|
| Standard_NV12s_v3 |12 | 112 | 320  | 1 | 8  | 12 | 20000/200 | 4 | 1 | 25  |
| Standard_NV24s_v3 |24 | 224 | 640  | 2 | 16 | 24 | 40000/400 | 8 | 2 | 50  |
| Standard_NV48s_v3 |48 | 448 | 1280 | 4 | 32 | 32 | 80000/800 | 8 | 4 | 100 |

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

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre como [as unidades de computação Azure (ACU)](acu.md) podem ajudá-lo a comparar o desempenho do cálculo em Azure SKUs.
