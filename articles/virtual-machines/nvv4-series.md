---
title: Série NVv4
description: Especificações para os VMs da série NVv4.
services: virtual-machines
ms.subservice: sizes
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 856ad9cede58b7a8dd0856cac9568832702d4b23
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/10/2020
ms.locfileid: "84678063"
---
# <a name="nvv4-series"></a>Série NVv4 

As máquinas virtuais da série NVv4 são alimentadas por GPUs [AMD Radeon Instinct MI25](https://www.amd.com/en/products/professional-graphics/instinct-mi25) e CPUs AMD EPYC 7V12(Roma). Com a série NVv4, o Azure está a introduzir máquinas virtuais com GPUs parciais. Escolha a máquina virtual de tamanho certo para aplicações gráficas aceleradas da GPU e desktops virtuais a partir de 1/8 de um GPU com 2 tampão de moldura GiB para uma GPU completa com 16 tampão de moldura GiB. Atualmente, as máquinas virtuais NVv4 suportam apenas o sistema operativo para hóspedes do Windows.

<br>

Armazenamento Premium: Suportado

Caching de armazenamento premium: Suportado

Migração ao vivo: Não suportado

Atualizações de preservação da memória: Não suportadas

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | GPU | Memória gpu: GiB | Discos de dados máximos | NICs máximos |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV4as_v4 |4 |14 |88 | 1/8 | 2 | 4 | 2 |
| Standard_NV8as_v4 |8 |28 |176 | 1/4 | 4 | 8 | 4 |
| Standard_NV16as_v4 |16 |56 |352 | 1/2 | 8 | 16 | 8 |
| Standard_NV32as_v4 |32 |112 |704 | 1 | 16 | 32 | 8 |

<sup>1</sup> VMs série NVv4 apresentam tecnologia multilênual AMD simultaneamente

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Sistemas operativos e controladores suportados

Para tirar partido das capacidades da GPU dos VMs da série Azure NVv4 que executam o Windows, os controladores da GPU da AMD devem ser instalados.

Para instalar manualmente os controladores da GPU da AMD, consulte a [configuração do controlador GPU da série N para windows](./windows/n-series-amd-driver-setup.md) para sistemas operativos suportados, controladores, instalações e etapas de verificação.

## <a name="other-sizes"></a>Outros tamanhos

- [Fins gerais](sizes-general.md)
- [Com otimização de memória](sizes-memory.md)
- [Com otimização de armazenamento](sizes-storage.md)
- [Com otimização de GPU](sizes-gpu.md)
- [Computação de elevado desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre como [as unidades de computação Azure (ACU)](acu.md) podem ajudá-lo a comparar o desempenho do cálculo em Azure SKUs.
