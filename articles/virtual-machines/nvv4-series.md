---
title: Série NVv4 - Máquinas Virtuais Azure
description: Especificações para os VMs da série NVv4.
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 82720a99128facee2d1617d69f1ed852b232de43
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77493283"
---
# <a name="nvv4-series-preview"></a>Série NVv4 (Pré-visualização)

As máquinas virtuais da série NVv4 são alimentadas por [AMD Radeon Instinct MI25](https://www.amd.com/en/products/professional-graphics/instinct-mi25) GPUs e AMD EPYC 7V12 (Roma) CPUs. Com a série NVv4, o Azure está a introduzir máquinas virtuais com GPUs parciais. Escolha a máquina virtual de tamanho certo para aplicações gráficas aceleradas de GPU e ambientes de trabalho virtuais a partir de 1/8 de um GPU com 2 tampão de moldura GiB para um GPU completo com 16 tampão de fotogramas GiB. Atualmente, as máquinas virtuais NVv4 suportam apenas o sistema operativo windows.

[Inscreva-se e obtenha acesso a estas máquinas durante a pré-visualização](https://aka.ms/nvv4signup).
<br>

Armazenamento Premium: Suportado

Caching de armazenamento premium: Suportado

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | GPU | Memória DE GPU: GiB | Discos de dados máximos | NICs máximos |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV4as_v4 |4 |14 |88 | 1/8 | 2 | 4 | 2 |
| Standard_NV8as_v4 |8 |28 |176 | 1/4 | 4 | 8 | 4 |
| Standard_NV16as_v4 |16 |56 |352 | 1/2 | 8 | 16 | 8 |
| Standard_NV32as_v4 |32 |112 |704 | 1 | 16 | 32 | 8 |

<sup>1</sup> VMs série NVv4 apresentam tecnologia multithreading amd simultânea

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Sistemas operativos e controladores suportados

Para tirar partido das capacidades de GPU dos VMs da série N Azure que executam os controladores De GPU Windows, NVIDIA ou AMD GPU devem ser instalados.

A Extensão do [Condutor GPU da NVIDIA](/extensions/hpccompute-gpu-windows.md) instala os condutores adequados da NVIDIA CUDA ou grid num VM da série N. Instale ou gerea extensão utilizando o portal Azure ou ferramentas como os modelos Azure PowerShell ou Azure Resource Manager. Consulte a documentação de extensão do [condutor da NVIDIA GPU](/extensions/hpccompute-gpu-windows.md) para sistemas operativos suportados e etapas de implementação. Para obter informações gerais sobre extensões VM, consulte [extensões e funcionalidades da máquina virtual Azure.](/extensions/overview.md)

Se optar por instalar manualmente os controladores GPU da NVIDIA, consulte a configuração do controlador GPU da série N para a configuração do controlador [GPU da série N](/windows/n-series-driver-setup.md) para o [Linux](/linux/n-series-driver-setup) para sistemas operativos suportados, controladores, instalação e verificação.

Para instalar manualmente os controladores GPU da AMD, consulte a configuração do [controlador GPU da](/windows/n-series-driver-setup.md) série N para windows para sistemas operativos, controladores, instalação e verificação suportados.

## <a name="other-sizes"></a>Outros tamanhos

- [Fins gerais](sizes-general.md)
- [Com otimização de memória](sizes-memory.md)
- [Com otimização de armazenamento](sizes-storage.md)
- [Com otimização de GPU](sizes-gpu.md)
- [Computação de elevado desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como as unidades de [computação Azure (ACU)](acu.md) podem ajudá-lo a comparar o desempenho da computação em Azure SKUs.