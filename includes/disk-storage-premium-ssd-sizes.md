---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 10/24/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 8bb607d0b3091de34c5c75b09c3b99257f6b2c2d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467750"
---
| Tamanhos de SSD Premium | P1 | P2 | P3 | P4 | P6 | P10 | P15 | P20 | P30 | P40 | P50 | P60 | P70 | P80 |
|-------------------|----|----|----|----|----|-----|-----|-----|-----|-----|-----|------|------|------|
| Tamanho do disco em GiB | 4 | 8 | 16 | 32 | 64 | 128 | 256 | 512 | 1\.024 | 2\.048 | 4\.096 | 8,192 | 16.384 | 32.767 |
| IOPs por disco | 120 | 120 | 120 | 120 | 240 | 500 | 1\.100 | 2\.300 | 5000 | 7\.500 | 7\.500 | 16.000 | 18.000 | 20.000 |
| Débito por disco | 25 MiB/s | 25 MiB/s | 25 MiB/s |Até 25 MiB/s | 50 MiB/s | 100 MiB/s | 125 MiB/s | 150 MiB/s | 200 MiB/s | 250 MiB/s | 250 MiB/s| 500 MiB/s | 750 MiB/s | 900 MiB/s |
| IOPS de intermitência máxima por disco * * | 3\.500 | 3\.500 | 3\.500 | 3\.500 | 3\.500 | 3\.500 | 3\.500 | 3\.500 |
| Taxa de transferência máxima de intermitência por disco * * | 170 MiB/s | 170 MiB/s | 170 MiB/s | 170 MiB/s | 170 MiB/s | 170 MiB/s | 170 MiB/s | 170 MiB/s |
| Duração máxima de intermitência * * | 30 min  | 30 min  | 30 min  | 30 min  | 30 min  | 30 min  | 30 min  | 30 min  |

\*denota um tamanho de disco que está em visualização no momento, para obter informações de disponibilidade regional, consulte [discos Premium: gerenciado e não gerenciado](https://docs.microsoft.com/azure/virtual-machines/linux/faq-for-disks#premium-disks-managed-and-unmanaged).

\** denota um recurso que está atualmente em versão prévia, consulte [intermitência de disco](https://docs.microsoft.com/azure/virtual-machines/linux/disk-bursting#regional-availability) para obter mais informações.
