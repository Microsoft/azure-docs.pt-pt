---
title: NCas T4 v3 série
description: Especificações para os VMs da série NCas T4 v3.
services: virtual-machines
ms.subservice: sizes
author: vikancha-MSFT
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 08/10/2020
ms.author: vikancha
ms.openlocfilehash: edbd8b2fefb90bb95bfcd08c126a42c810ad081b
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96511604"
---
# <a name="ncast4_v3-series-in-preview"></a>NCasT4_v3 série (Na pré-visualização) 

As máquinas virtuais da série NCasT4_v3 são alimentadas por [GPUs Nvidia Tesla T4](https://www.nvidia.com/en-us/data-center/tesla-t4/) e CPUs AMD EPYC 7V12(Roma). Os VMs apresentam até 4 GPUs NVIDIA T4 com 16 GB de memória cada, até 64 núcleos de processador AMD EPYC 7V12 (Roma) não multi-leitores e 440 GiB de memória do sistema. Estas máquinas virtuais são ideais para a implementação de serviços de IA, como a inferenização em tempo real de pedidos gerados pelo utilizador, ou para cargas de trabalho de gráficos e visualização interativas utilizando o controlador GRID da NVIDIA e a tecnologia virtual de GPU. As cargas de trabalho de cálculo standard da GPU baseadas em CUDA, TensorRT, Caffe, ONNX e outros quadros, ou aplicações gráficas aceleradas pela GPU baseadas em OpenGL e DirectX podem ser implementadas economicamente, com proximidade com os utilizadores, na série NCasT4_v3.

> [!NOTe]
> [Submeta um pedido](https://aka.ms/NCT4v3Preview) para fazer parte do programa de pré-visualização.

<br>

[ACU](acu.md): 230-260<br>
[Armazenamento Premium](premium-storage-performance.md): Suportado<br>
[Caching premium armazenamento](premium-storage-performance.md): Suportado<br>
[Migração ao Vivo](maintenance-and-updates.md): Não Suportado<br>
[Atualizações de preservação da memória](maintenance-and-updates.md): Não suportado<br>
[Suporte de geração VM](generation-2.md): Geração 1<br>
<br>

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | GPU | Memória gpu: GiB | Discos de dados máximos | Max NICs / Largura de banda esperada (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NC4as_T4_v3 |4 |28 |180 | 1 | 16 | 8 | 2 / 8000 |
| Standard_NC8as_T4_v3 |8 |56 |360 | 1 | 16 | 16 | 4 / 8000  |
| Standard_NC16as_T4_v3 |16 |110 |360 | 1 | 16 | 32 | 8 / 8000  |
| Standard_NC64as_T4_v3 |64 |440 |2880 | 4 | 64 | 32 | 8 / 32000  |


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Sistemas operativos e controladores suportados

Para tirar partido das capacidades da GPU dos VMs da série Azure NCasT4_v3 que executam o Windows ou o Linux, os controladores da Nvidia GPU devem ser instalados.

Para instalar manualmente os controladores da Nvidia GPU, consulte a [configuração do controlador gpu da série N para windows](./windows/n-series-driver-setup.md) para sistemas operativos suportados, controladores, instalações e etapas de verificação.

## <a name="other-sizes"></a>Outros tamanhos

- [Fins gerais](sizes-general.md)
- [Com otimização de memória](sizes-memory.md)
- [Com otimização de armazenamento](sizes-storage.md)
- [Com otimização de GPU](sizes-gpu.md)
- [Computação de elevado desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como [as unidades de computação Azure (ACU)](acu.md) podem ajudá-lo a comparar o desempenho do cálculo em Azure SKUs.
