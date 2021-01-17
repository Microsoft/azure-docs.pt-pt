---
title: Desempenho da máquina virtual e do disco
description: Saiba mais sobre como as máquinas virtuais e os seus discos anexos funcionam em combinação para o desempenho.
author: albecker1
ms.author: albecker
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 5e9f6ecc733eccf317e3013752ee2f5b0586ea78
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540765"
---
# <a name="virtual-machine-and-disk-performance"></a>Desempenho da máquina virtual e do disco
[!INCLUDE [VM and Disk Performance](../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>Máquina virtual sem acasa vs limites em cache
As máquinas virtuais que estão habilitados tanto para armazenamento premium como para o armazenamento premium têm dois limites de largura de banda de armazenamento diferentes. Vejamos a Standard_D8s_v3 máquina virtual como um exemplo. Aqui está a documentação sobre a [série Dsv3](dv3-dsv3-series.md) e a Standard_D8s_v3:

[!INCLUDE [VM and Disk Performance](../../includes/virtual-machine-disk-performance-2.md)]

Vamos fazer um teste de benchmarking nesta combinação de máquina virtual e disco que cria atividade de IO. Para aprender a comparar o IO de armazenamento de referência no Azure, consulte [Benchmark a sua aplicação no Azure Disk Storage](disks-benchmarks.md). A partir da ferramenta de benchmarking, pode ver que a combinação VM e disco pode alcançar 22.800 IOPS:

[!INCLUDE [VM and Disk Performance](../../includes/virtual-machine-disk-performance-3.md)]
