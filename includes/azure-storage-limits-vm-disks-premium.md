---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: rogarana
ms.openlocfilehash: ef18feb10dabc6a77e6512c6a32ad44b32c6e832
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334977"
---
**Discos de máquinas virtuais não geridos premium: limites por conta**

| Recurso | Limite |
| --- | --- |
| Capacidade total do disco por conta |35 TB |
| Capacidade total do instantâneo por conta |10 TB |
| Largura de banda máxima por conta (ingress + egress)<sup>1</sup> |<=50 Gbps |

<sup>1</sup>*Ingress* refere-se a todos os dados dos pedidos enviados para uma conta de armazenamento. *A Egress* refere-se a todos os dados de respostas recebidas de uma conta de armazenamento.

**Discos de máquinas virtuais não geridos premium: limites por disco**

| Tipo de disco de armazenamento Premium | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- |
| Tamanho do disco |128 GiB |512 GiB |1.024 GiB (1 TB) |2.048 GiB (2 TB)|4.095 GiB (4 TB)|
| IOPS máximopor disco |500 |2300 |5000 |7.500 |7.500 |
| Potência máxima por disco |100 MB/seg | 150 MB/seg |200 MB/seg |250 MB/seg |250 MB/seg |
| Número máximo de discos por conta de armazenamento |280 |70 |35 | 17 | 8 |

**Discos de máquinas virtuais não geridos premium: limites Per-VM**

| Recurso | Limite |
| --- | --- |
| IOPS máximos por VM |80.000 IOPS com GS5 VM |
| Potência máxima por VM |2.000 MB/seg com GS5 VM |

