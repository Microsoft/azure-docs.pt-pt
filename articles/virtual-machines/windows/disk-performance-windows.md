---
title: Desempenho da máquina virtual e do disco
description: Saiba mais sobre como os VMs e os seus discos anexos funcionam em combinação para o desempenho
author: albecker1
ms.author: albecker
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 88cf9c28a9d325d617f4b049015f0cd238a2fb31
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92016464"
---
# <a name="virtual-machine-and-disk-performance"></a>Desempenho da máquina virtual e do disco
[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>Máquina virtual sem acasa vs limites em cache
 As máquinas virtuais que são ambas de armazenamento premium habilitados e o caching de armazenamento premium habilitado têm dois limites diferentes de largura de banda de armazenamento. Vamos continuar a olhar para a Standard_D8s_v3 máquina virtual como um exemplo. Aqui está a documentação sobre a [série Dsv3](../dv3-dsv3-series.md) e sobre ela o Standard_D8s_v3:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-2.md)]

Vamos fazer um teste de benchmarking nesta combinação de VM e disco que irá criar atividade de IO e você pode aprender tudo sobre como comparar o armazenamento IO em Azure [aqui.](disks-benchmarks.md) A partir da ferramenta de benchmarking, pode ver que a combinação VM e Disco é capaz de alcançar 22.800 IOPS:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-3.md)]