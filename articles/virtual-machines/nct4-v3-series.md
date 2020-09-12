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
ms.openlocfilehash: af9f7eb21d533bc5fb365e7cbf1fb8fc18184fa7
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89375232"
---
# <a name="ncast4_v3-series-in-preview"></a>NCasT4_v3 série (Na pré-visualização) 

As máquinas virtuais da série NCasT4_v3 são alimentadas por [GPUs Nvidia Tesla T4](https://www.nvidia.com/en-us/data-center/tesla-t4/) e CPUs AMD EPYC 7V12(Roma). Os VMs apresentam até 4 GPUs NVIDIA T4 com 16 GB de memória cada, até 64 núcleos de processador AMD EPYC 7V12(Roma) não multi-leitores e 440 GiB de memória do sistema. Estas máquinas virtuais são ideais para executar cargas de trabalho ML e IA utilizando CUDA, TensorFlow, Pytorch, Caffe, e outros Quadros ou as cargas de trabalho gráficas utilizando a tecnologia NVIDIA GRID. NCasT4_v3 série é ideal para executar cargas de trabalho de inferência.

Pode [submeter um pedido](https://aka.ms/NCT4v3Preview) para fazer parte do programa de pré-visualização.

<br>

ACU: 230-260

Armazenamento Premium: Suportado

Caching de armazenamento premium: Suportado

Migração ao vivo: Não suportado

Atualizações de preservação da memória: Não suportadas

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | GPU | Memória gpu: GiB | Discos de dados máximos | NICs máximos |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NC4as_T4_v3 |4 |28 |180 | 1 | 16 | 8 | 2 |
| Standard_NC8as_T4_v3 |8 |56 |360 | 1 | 16 | 16 | 4  |
| Standard_NC16as_T4_v3 |16 |110 |360 | 1 | 16 | 32 | 8  |
| Standard_NC64as_T4_v3 |64 |440 |2880 | 4 | 64 | 32 | 8  |


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

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre como [as unidades de computação Azure (ACU)](acu.md) podem ajudá-lo a comparar o desempenho do cálculo em Azure SKUs.
