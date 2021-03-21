---
title: Tamanhos Azure VM - | de armazenamento Microsoft Docs
description: Lista os diferentes tamanhos otimizados de armazenamento disponíveis para máquinas virtuais em Azure. Lista informações sobre o número de vCPUs, discos de dados e NICs, bem como a produção de armazenamento e largura de banda de rede para tamanhos nesta série.
ms.subservice: vm-sizes-storage
documentationcenter: ''
author: sasha-melamed
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: d97fe6cda1134d45468e257965fd5e28fe170e6f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102561029"
---
# <a name="storage-optimized-virtual-machine-sizes"></a>Tamanhos de máquinas virtuais otimizados de armazenamento

Os tamanhos de VM otimizados de armazenamento oferecem alta produção de disco e IO, e são ideais para big data, SQL, bases de dados NoSQL, armazenamento de dados e grandes bases de dados transacionais.  Exemplos incluem Cassandra, MongoDB, Cloudera e Redis. Este artigo fornece informações sobre o número de vCPUs, discos de dados e NICs, bem como o armazenamento local e largura de banda de rede para cada tamanho otimizado.

A [série Lsv2](lsv2-series.md) apresenta alta produção, baixa latência, armazenamento NVMe local mapeado diretamente no [processador AMD EPYC<sup>TM</sup> 7551](https://www.amd.com/en/products/epyc-7000-series) com um boost de núcleo de 2,55GHz e um boost máximo de 3.0GHz. Os VMs da série Lsv2 vêm em tamanhos de 8 a 80 vCPU numa configuração simultânea de múltiplos roscas.  Há 8 GiB de memória por vCPU, e um dispositivo SSD M.2 de 1.92TB NVMe por 8 vCPUs, com até 19,2TB (10x1.92TB) disponível no L80s v2.

## <a name="other-sizes"></a>Outros tamanhos

- [Fins gerais](sizes-general.md)
- [Com otimização de computação](sizes-compute.md)
- [Com otimização de memória](sizes-memory.md)
- [Otimizada para GPU](sizes-gpu.md)
- [Computação de elevado desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como [as unidades de computação Azure (ACU)](acu.md) podem ajudá-lo a comparar o desempenho do cálculo em Azure SKUs.

Saiba como otimizar o desempenho nas máquinas virtuais da série Lsv2 para [Windows](windows/storage-performance.md) ou [Linux.](linux/storage-performance.md)

Para obter mais informações sobre como a Azure dá nome aos seus VMs, consulte [o tamanho da máquina virtual Azure que nomeia convenções.](./vm-naming-conventions.md)