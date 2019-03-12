---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: rogarana
ms.openlocfilehash: e7e57c6a821731874dcb1d99a3133b6ede1da26e
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553938"
---
**Discos de máquinas de virtuais não geridos Premium: Limites de por conta**

| Recurso | Limite predefinido |
| --- | --- |
| Capacidade total do disco por conta |35 TB |
| Capacidade total do instantâneo por conta |10 TB |
| Largura de banda máxima por conta (entrada + saída)<sup>1</sup> |<=50 Gbps |

<sup>1</sup>*entrada* refere-se a todos os dados de pedidos que são enviados para uma conta de armazenamento. *Saída* refere-se a todos os dados a partir de respostas de que são recebidas a partir de uma conta de armazenamento.

**Discos de máquinas de virtuais não geridos Premium: Limites de por disco**

| Tipo de disco de armazenamento Premium | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- |
| Tamanho do disco |128 GiB |512 GiB |1.024 giB (1 TB) |2048 giB (2 TB)|4095 giB (4 TB)|
| IOPS máximo por disco |500 |2,300 |5.000 |7.500 |7,500 |
| Débito máximo por disco |100 MB/seg | 150 MB/seg |200 MB/sec |250 MB/sec |250 MB/sec |
| Número máximo de discos por conta de armazenamento |280 |70 |35 | 17 | 8 |

**Discos de máquinas de virtuais não geridos Premium: Limites de por VM**

| Recurso | Limite predefinido |
| --- | --- |
| IOPS máximo por VM |80.000 IOPS com GS5 VM |
| Débito máximo por VM |2.000 MB/s com GS5 VM |

