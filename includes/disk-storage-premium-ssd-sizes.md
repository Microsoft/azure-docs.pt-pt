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
ms.openlocfilehash: c88cba57ed987074c0dde3c8b251f54393667740
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75002699"
---
| Tamanhos de SSD Premium | P1 | P2 | P3 | P4 | P6 | P10 | P15 | P20 | P30 | P40 | P50 | P60 | P70 | P80 |
|-------------------|----|----|----|----|----|-----|-----|-----|-----|-----|-----|------|------|------|
| Tamanho do disco em GiB | 4 | 8 | 16 | 32 | 64 | 128 | 256 | 512 | 1,024 | 2048 | 4\.096 | 8,192 | 16.384 | 32 767 |
| IOPS por disco | 120 | 120 | 120 | 120 | 240 | 500 | 1100 | 2300 | 5000 | 7\.500 | 7\.500 | 16,000 | 18 000 | 20,000 |
| Débito por disco | 25 MiB/s | 25 MiB/s | 25 MiB/s | 25 MiB/s | 50 MiB/s | 100 MiB/s | 125 MiB/s | 150 MiB/s | 200 MiB/s | 250 MiB/s | 250 MiB/s| 500 MiB/s | 750 MiB/s | 900 MiB/s |
| IOPS de intermitência máxima por disco * * | 3500 | 3500 | 3500 | 3500 | 3500 | 3500 | 3500 | 3500 |
| Taxa de transferência máxima de intermitência por disco * * | 170 MiB/s | 170 MiB/s | 170 MiB/s | 170 MiB/s | 170 MiB/s | 170 MiB/s | 170 MiB/s | 170 MiB/s |
| Duração máxima de intermitência * * | 30 min  | 30 min  | 30 min  | 30 min  | 30 min  | 30 min  | 30 min  | 30 min  |

\*denota um tamanho de disco que está em visualização no momento, para obter informações de disponibilidade regional, consulte [novos tamanhos de disco: gerenciados e não gerenciados](https://docs.microsoft.com/azure/virtual-machines/linux/faq-for-disks#new-disk-sizes-managed-and-unmanaged).

\*\*denota um recurso que está atualmente em versão prévia, consulte [intermitência de disco](https://docs.microsoft.com/azure/virtual-machines/linux/disk-bursting#regional-availability) para obter mais informações.
