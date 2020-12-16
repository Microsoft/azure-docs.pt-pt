---
title: Série NVv3 - Azure Virtual Machines
description: Especificações para os VMs da série NVv3.
services: virtual-machines
ms.subservice: sizes
author: vikancha-MSFT
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 65107b0924351ca6debf37201e8849ffe8dd1eab
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97589432"
---
# <a name="nvv3-series"></a>Série NVv3

As máquinas virtuais da série NVv3 são alimentadas pela [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPUs e pela tecnologia NVIDIA GRID com CPUs Intel E5-2690 v4 (Broadwell) e Tecnologia intel Hyper-Threading. Estas máquinas virtuais são direcionadas para aplicações gráficas aceleradas da GPU e desktops virtuais onde os clientes querem visualizar os seus dados, simular resultados para ver, trabalhar no CAD ou renderizar e transmitir conteúdo. Além disso, estas máquinas virtuais podem executar cargas de trabalho de precisão única, tais como codificação e renderização. As máquinas virtuais NVv3 suportam o Armazenamento Premium e vêm com o dobro da memória do sistema (RAM) quando comparadas com as suas séries NV antecessoras.  

Cada GPU em instâncias NVv3 vem com uma licença GRID. Esta licença dá-lhe a flexibilidade para usar uma instância NV como uma estação de trabalho virtual para um único utilizador, ou 25 utilizadores simultâneos podem ligar-se ao VM para um cenário de aplicação virtual.

[Armazenamento Premium](premium-storage-performance.md): Suportado<br>
[Caching premium armazenamento](premium-storage-performance.md): Suportado<br>
[Migração ao Vivo](maintenance-and-updates.md): Não Suportado<br>
[Atualizações de preservação da memória](maintenance-and-updates.md): Não suportado<br>
[Suporte de geração VM](generation-2.md): Geração 1 e 2<br>
Rede Acelerada: Suportada<br>
<br>

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | GPU | Memória gpu: GiB | Discos de dados máximos | Produção de disco não-abacatado por maxilar: IOPS/MBps | Max NICs / Largura de banda esperada (Mbps) | Estações de trabalho virtuais | Aplicações Virtuais |
|---|---|---|---|---|---|---|---|---|---|---|
| Standard_NV12s_v3 |12 | 112 | 320  | 1 | 8  | 12 | 20000/200 | 4 / 6000 | 1 | 25  |
| Standard_NV24s_v3 |24 | 224 | 640  | 2 | 16 | 24 | 40000/400 | 8 / 12000 | 2 | 50  |
| Standard_NV48s_v3 |48 | 448 | 1280 | 4 | 32 | 32 | 80000/800 | 8 / 24000 | 4 | 100 |

<sup>1</sup> 1 GPU = meia cartão M60.

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
