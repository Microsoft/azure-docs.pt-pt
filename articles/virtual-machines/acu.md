---
title: Visão geral da Unidade Azure Compute
description: Visão geral do conceito das unidades computacional Azure. A ACU fornece uma forma de comparar o desempenho do CPU em todos os SKUs do Azure.
author: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: mimckitt
ms.reviewer: davberg
ms.openlocfilehash: ba8c3f730897c01c52aaf4499269425426d0b7b5
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2020
ms.locfileid: "91667185"
---
# <a name="azure-compute-unit-acu"></a>Unidade de computação Azure (ACU)

O conceito da Unidade Azure Compute (ACU) fornece uma forma de comparar o desempenho do computação (CPU) em Azure SKUs. Isto ajudará a identificar facilmente que SKU é mais provável de satisfazer as suas necessidades de desempenho. ACU é atualmente padronizada em um VM Small (Standard_A1) sendo 100 e todos os outros SKUs então representam aproximadamente o quanto mais rápido que o SKU pode executar um benchmark padrão

*ACUs usam a tecnologia Intel® Turbo para aumentar a frequência do CPU e proporcionar um aumento de desempenho.  A quantidade do aumento de desempenho pode variar em função do tamanho de VM, carga de trabalho e outras cargas de trabalho que estão a funcionar no mesmo hospedeiro.

**ACUs usam a tecnologia AMD® Boost para aumentar a frequência do CPU e proporcionar um aumento de desempenho.  A quantidade do aumento de desempenho pode variar em função do tamanho de VM, carga de trabalho e outras cargas de trabalho que estão a funcionar no mesmo hospedeiro.

Hiper-roscado e capaz de executar virtualização aninhada

> [!IMPORTANT]
> A ACU é apenas uma orientação. Os resultados da sua carga de trabalho podem variar.
<br>

| Família de SKU | ACU \ vCPU | vCPU: Core |
| --- | --- |---|
| [A0](sizes-previous-gen.md) |50 | 1:1 |
| [A1 - A4](sizes-previous-gen.md) |100 | 1:1 |
| [A5 - A7](sizes-previous-gen.md) |100 | 1:1 |
| [A1_v2 - A8_v2](sizes-general.md) |100 | 1:1 |
| [A2m_v2 - A8m_v2](sizes-general.md) |100 | 1:1 |
| [A8 - A11](sizes-previous-gen.md) |225* | 1:1 |
| [B](sizes-b-series-burstable.md) |Varia | Varia |
| [D1 - D14](sizes-previous-gen.md) |160 - 250 | 1:1 |
| [D1_v2 - D15_v2](dv2-dsv2-series.md) |210 - 250* | 1:1 |
| [DS1 - DS14](sizes-previous-gen.md) |160 - 250 | 1:1 |
| [DS1_v2 - DS15_v2](dv2-dsv2-series.md) |210 - 250* | 1:1 |
| [D_v3](dv3-dsv3-series.md) |160 - 190* | 2:1\*\*\* |
| [Ds_v3](dv3-dsv3-series.md) |160 - 190* | 2:1\*\*\* |
| [Dav4](dav4-dasv4-series.md) |230 - 260** | 2:1 |
| [Dasv4](dav4-dasv4-series.md) |230 - 260** | 2:1 |
| [Dv4](dv4-dsv4-series.md) | 195 - 210 | 2:1\*\*\* |
| [Dsv4](dv4-dsv4-series.md) | 195 - 210 | 2:1\*\*\* |
| [Ddv4](ddv4-ddsv4-series.md) | 195 -210* | 2:1\*\*\* |
| [Ddsv4](ddv4-ddsv4-series.md) | 195 - 210* | 2:1\*\*\* |
| [E_v3](ev3-esv3-series.md) |160 - 190* | 2:1\*\*\*|
| [Es_v3](ev3-esv3-series.md) |160 - 190* | 2:1\*\*\* |
| [Eav4](eav4-easv4-series.md) |230 - 260** | 2:1 |
| [Easv4](eav4-easv4-series.md) | 230 - 260** | 2:1 |
| [Ev4](ev4-esv4-series.md) | 195 - 210 | 2:1\*\*\* |
| [Esv4](ev4-esv4-series.md) | 195 - 210 | 2:1\*\*\* |
| [Edv4](edv4-edsv4-series.md) | 195 - 210* | 2:1\*\*\* |
| [Edsv4](edv4-edsv4-series.md) | 195 - 210* | 2:1\*\*\* |
| [F2s_v2 - F72s_v2](fsv2-series.md) |195 - 210* | 2:1\*\*\* |
| [F1 - F16](sizes-previous-gen.md) |210 - 250* | 1:1 |
| [F1s - F16s](sizes-previous-gen.md) |210 - 250* | 1:1 |
| [G1 - G5](sizes-previous-gen.md) |180 - 240* | 1:1 |
| [GS1 - GS5](sizes-previous-gen.md) |180 - 240* | 1:1 |
| [H](h-series.md) |290 - 300* | 1:1 |
| [HB](hb-series.md) |199 - 216** | 1:1 |
| [HC](hc-series.md) |297 - 315* | 1:1 |
| [L4s - L32s](sizes-previous-gen.md) |180 - 240* | 1:1 |
| [L8s_v2 - L80s_v2](lsv2-series.md) |150 - 175** | 2:1 |
| [M](m-series.md) | 160 - 180 | 2:1\*\*\* |
| [NVv4](nvv4-series.md) |230 - 260** | 2:1 |

Aqui estão links para mais informações sobre os diferentes tamanhos:

- [Fins gerais](sizes-general.md)
- [Com otimização de memória](sizes-memory.md)
- [Com otimização de computação](sizes-compute.md)
- [Com otimização de GPU](sizes-gpu.md)
- [Computação de elevado desempenho](sizes-hpc.md)
- [Com otimização de armazenamento](sizes-storage.md)
