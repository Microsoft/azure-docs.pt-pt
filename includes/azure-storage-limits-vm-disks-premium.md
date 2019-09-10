---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: rogarana
ms.openlocfilehash: e7e57c6a821731874dcb1d99a3133b6ede1da26e
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "67184248"
---
**Discos de máquina virtual não gerenciados Premium: Limites por conta**

| Resource | Limite predefinido |
| --- | --- |
| Capacidade total do disco por conta |35 TB |
| Capacidade total do instantâneo por conta |10 TB |
| Largura de banda máxima por conta (entrada + saída)<sup>1</sup> |<=50 Gbps |

<sup>1</sup> A *entrada* refere-se a todos os dados de solicitações enviadas para uma conta de armazenamento. *Saída* refere-se a todos os dados de respostas recebidas de uma conta de armazenamento.

**Discos de máquina virtual não gerenciados Premium: Limites por disco**

| Tipo de disco de armazenamento Premium | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- |
| Tamanho do disco |128 GiB |512 GiB |1\.024 GiB (1 TB) |2\.048 GiB (2 TB)|4\.095 GiB (4 TB)|
| IOPS máximo por disco |500 |2,300 |5\.000 |7\.500 |7,500 |
| Taxa de transferência máxima por disco |100 MB/s | 150 MB/s |200 MB/s |250 MB/s |250 MB/s |
| Número máximo de discos por conta de armazenamento |280 |70 |35 | 17 | 8 |

**Discos de máquina virtual não gerenciados Premium: Limites por VM**

| Resource | Limite predefinido |
| --- | --- |
| IOPS máximo por VM |IOPS de 80.000 com VM GS5 |
| Taxa de transferência máxima por VM |2\.000 MB/s com VM GS5 |

