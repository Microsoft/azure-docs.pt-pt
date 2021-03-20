---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: rogarana
ms.openlocfilehash: ef18feb10dabc6a77e6512c6a32ad44b32c6e832
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "80334977"
---
**Discos de máquinas virtuais premium não geridos: limites por conta**

| Recurso | Limite |
| --- | --- |
| Capacidade total do disco por conta |35 TB |
| Capacidade total do instantâneo por conta |10 TB |
| Largura de banda máxima por conta (entrada + saída)<sup>1</sup> |<=50 Gbps |

<sup>1</sup>*Ingress* refere-se a todos os dados de pedidos enviados para uma conta de armazenamento. *Egress* refere-se a todos os dados de respostas recebidas de uma conta de armazenamento.

**Discos de máquinas virtuais premium não geridos: limites por disco**

| Tipo de disco de armazenamento Premium | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- |
| Tamanho do disco |128 GiB |512 GiB |1.024 GiB (1 TB) |2.048 GiB (2 TB)|4.095 GiB (4 TB)|
| IOPS máximo por disco |500 |2300 |5000 |7.500 |7.500 |
| Produção máxima por disco |100 MB/seg | 150 MB/seg |200 MB/seg |250 MB/seg |250 MB/seg |
| Número máximo de discos por conta de armazenamento |280 |70 |35 | 17 | 8 |

**Discos de máquinas virtuais premium não geridos: limites por VM**

| Recurso | Limite |
| --- | --- |
| IOPS máximo por VM |80.000 IOPS com GS5 VM |
| Produção máxima por VM |2.000 MB/seg com GS5 VM |

