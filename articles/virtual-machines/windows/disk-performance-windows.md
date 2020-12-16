---
title: Desempenho da máquina virtual e do disco - Windows
description: Saiba mais sobre como as máquinas virtuais e os seus discos anexos funcionam em combinação para o desempenho no Windows.
author: albecker1
ms.author: albecker
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: dec8b0cc33a9fffa7cac1ac9261b3c38ef5a6449
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97584124"
---
# <a name="virtual-machine-and-disk-performance-windows"></a>Desempenho da máquina virtual e do disco (Windows)
[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>Máquina virtual sem acasa vs limites em cache
 As máquinas virtuais que são ambas de armazenamento premium habilitados e o caching de armazenamento premium habilitado têm dois limites diferentes de largura de banda de armazenamento. Vamos continuar a olhar para a Standard_D8s_v3 máquina virtual como um exemplo. Aqui está a documentação sobre a [série Dsv3](../dv3-dsv3-series.md) e sobre ela o Standard_D8s_v3:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-2.md)]

Vamos fazer um teste de benchmarking nesta combinação de VM e disco que irá criar atividade de IO e você pode aprender tudo sobre como comparar o armazenamento IO em Azure [aqui.](disks-benchmarks.md) A partir da ferramenta de benchmarking, pode ver que a combinação VM e Disco é capaz de alcançar 22.800 IOPS:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-3.md)]