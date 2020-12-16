---
title: Desempenho da máquina virtual e do disco - Linux
description: Saiba mais sobre como as máquinas virtuais e os seus discos anexos funcionam em combinação para o desempenho no Linux.
author: albecker1
ms.author: albecker
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 2c48672bcfd8c552b36e3ae0807135924669c1d9
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97591859"
---
# <a name="virtual-machine-and-disk-performance-linux"></a>Desempenho da máquina virtual e do disco (Linux)
[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>Máquina virtual sem acasa vs limites em cache
As máquinas virtuais que estão habilitados tanto para armazenamento premium como para o armazenamento premium têm dois limites de largura de banda de armazenamento diferentes. Vejamos a Standard_D8s_v3 máquina virtual como um exemplo. Aqui está a documentação sobre a [série Dsv3](../dv3-dsv3-series.md) e a Standard_D8s_v3:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-2.md)]

Vamos fazer um teste de benchmarking nesta combinação de máquina virtual e disco que cria atividade de IO. Para aprender a comparar o IO de armazenamento de referência no Azure, consulte [Benchmark a sua aplicação no Azure Disk Storage](disks-benchmarks.md). A partir da ferramenta de benchmarking, pode ver que a combinação VM e disco pode alcançar 22.800 IOPS:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-3.md)]
