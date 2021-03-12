---
title: Tamanhos Azure VM - Computação otimizada | Microsoft Docs
description: Lista os diferentes tamanhos otimizados do cálculo disponíveis para máquinas virtuais em Azure. Lista informações sobre o número de vCPUs, discos de dados e NICs, bem como a produção de armazenamento e largura de banda de rede para tamanhos nesta série.
author: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-compute
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: mimckitt
ms.openlocfilehash: 01eba7bff571a8db25591b8bfa5c5e712511588f
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102557697"
---
# <a name="compute-optimized-virtual-machine-sizes"></a>Calcular tamanhos de máquinas virtuais otimizados

Os tamanhos de VM otimizados computacional têm uma elevada relação CPU-memória. Estes tamanhos são bons para servidores web de tráfego médio, aparelhos de rede, processos de lote e servidores de aplicações. Este artigo fornece informações sobre o número de vCPUs, discos de dados e NICs. Também inclui informações sobre o armazenamento e largura de banda de rede para cada tamanho neste agrupamento.

A [série Fsv2](fsv2-series.md) funciona nos processadores Intel 8168 da 2ª Geração Intel® Xeon® Platinum 8272CL (Cascade Lake) e processadores Intel® Xeon® Platinum 8168 (Skylake). Apresenta uma velocidade do relógio Turbo de 3,4 GHz e uma frequência máxima de turbo mono-núcleo de 3,7 GHz. As instruções ® AVX-512 da Intel são novas nos processadores Intel Scalable. Estas instruções fornecem até um aumento de desempenho de 2X para cargas de trabalho de processamento de vetores em operações de ponto flutuante de precisão única e dupla. Por outras palavras, são muito rápidos para qualquer carga de trabalho computacional.

A um preço mais baixo por hora, a série Fsv2 é o melhor valor no desempenho de preço na carteira Azure com base na Unidade Azure Compute (ACU) por vCPU.

## <a name="other-sizes"></a>Outros tamanhos

- [Fins gerais](sizes-general.md)
- [Com otimização de memória](sizes-memory.md)
- [Com otimização de armazenamento](sizes-storage.md)
- [Otimizada para GPU](sizes-gpu.md)
- [Computação de elevado desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como [as unidades de computação Azure (ACU)](acu.md) podem ajudá-lo a comparar o desempenho do cálculo em Azure SKUs.

Para obter mais informações sobre como a Azure dá nome aos seus VMs, consulte [o tamanho da máquina virtual Azure que nomeia convenções.](./vm-naming-conventions.md)