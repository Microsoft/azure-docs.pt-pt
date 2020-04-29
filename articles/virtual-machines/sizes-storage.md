---
title: Tamanhos Azure VM - Armazenamento / Microsoft Docs
description: Lista os diferentes tamanhos otimizados de armazenamento disponíveis para máquinas virtuais em Azure. Lista informações sobre o número de vCPUs, discos de dados e NICs, bem como a entrada de armazenamento e largura de banda da rede para tamanhos nesta série.
services: virtual-machines
documentationcenter: ''
author: sasha-melamed
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jonbeck
ms.openlocfilehash: 5a611dc288fc18a14f6000689f9f9b49d4b3feb8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77913341"
---
# <a name="storage-optimized-virtual-machine-sizes"></a>Armazenamento otimizado tamanhos de máquina virtual

Os tamanhos de VM otimizados de armazenamento oferecem alta entrada de disco e IO, e são ideais para big data, bases de dados SQL, NoSQL, armazenamento de dados e grandes bases de dados transacionais.  Exemplos incluem Cassandra, MongoDB, Cloudera e Redis. Este artigo fornece informações sobre o número de vCPUs, discos de dados e NICs, bem como a entrada local de armazenamento e largura de banda da rede para cada tamanho otimizado.

A [série Lsv2](lsv2-series.md) apresenta alta potência, baixa latência, armazenamento nVMe local mapeado diretamente no [processador AMD EPYC<sup>TM</sup> 7551](https://www.amd.com/en/products/epyc-7000-series) com um impulso de núcleo de 2,55GHz e um aumento máximo de 3,0GHz. Os VMs da série Lsv2 vêm em tamanhos de 8 a 80 vCPU numa configuração multi-rosca simultânea.  Existe 8 GiB de memória por vCPU, e um dispositivo 1.92TB NVMe SSD M.2 por 8 vCPUs, com até 19.2TB (10x1.92TB) disponíveis no L80s v2.

## <a name="other-sizes"></a>Outros tamanhos

- [Fins gerais](sizes-general.md)
- [Com otimização de computação](sizes-compute.md)
- [Com otimização de memória](sizes-memory.md)
- [Com otimização de GPU](sizes-gpu.md)
- [Computação de elevado desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como as unidades de [computação Azure (ACU)](acu.md) podem ajudá-lo a comparar o desempenho da computação em Azure SKUs.

Saiba otimizar o desempenho nas máquinas virtuais da série Lsv2 para [Windows](windows/storage-performance.md) ou [Linux](linux/storage-performance.md).
