---
title: Visão geral da Unidade de Computação Azure [ Microsoft Docs
description: Visão geral do conceito das unidades de computação Azure. A ACU fornece uma forma de comparar o desempenho da CPU em todas as UsS DoT.
services: virtual-machines
documentationcenter: ''
author: mimckitt
manager: vashan
editor: ''
tags: azure-resource-manager,azure-service-management
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: mimckitt
ms.openlocfilehash: 738ce8dad7237f9703d3ccb998c221ffec29dc17
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82629104"
---
# <a name="azure-compute-unit-acu"></a>Unidade de computação Azure (ACU)

O conceito da Unidade de Computação Azure (ACU) fornece uma forma de comparar o desempenho da computação (CPU) em todas as SKUs azure. Isto ajudará a identificar facilmente que SKU é mais provável de satisfazer as suas necessidades de desempenho. A ACU é atualmente padronizada num Pequeno (Standard_A1) VM sendo 100 e todos os outros SKUs então representam aproximadamente quão mais rápido que sKU pode executar um benchmark padrão

*A ACUs usa a tecnologia Intel® Turbo para aumentar a frequência do CPU e proporcionar um aumento de desempenho.  A quantidade do aumento de desempenho pode variar em função do tamanho do VM, carga de trabalho e outras cargas de trabalho em funcionamento no mesmo hospedeiro.

**ACUs usam a tecnologia AMD® Boost para aumentar a frequência do CPU e proporcionar um aumento de desempenho.  A quantidade do aumento de desempenho pode variar em função do tamanho do VM, carga de trabalho e outras cargas de trabalho em funcionamento no mesmo hospedeiro.

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
| [D1 - D14](sizes-previous-gen.md) |160 - 250 | 1:1 |
| [D1_v2 - D15_v2](dv2-dsv2-series.md) |210 - 250* | 1:1 |
| [DS1 - DS14](sizes-previous-gen.md) |160 - 250 | 1:1 |
| [DS1_v2 - DS15_v2](dv2-dsv2-series.md) |210 - 250* | 1:1 |
| [D_v3](dv3-dsv3-series.md) |160 - 190* | 2:1\*\*\* |
| [Ds_v3](dv3-dsv3-series.md) |160 - 190* | 2:1\*\*\* |
| [E_v3](ev3-esv3-series.md) |160 - 190* | 2:1\*\*\*|
| [Es_v3](ev3-esv3-series.md) |160 - 190* | 2:1\*\*\* |
| [F2s_v2 - F72s_v2](fsv2-series.md) |195 - 210* | 2:1\*\*\* |
| [F1 - F16](sizes-previous-gen.md) |210 - 250* | 1:1 |
| [F1 - F16s](sizes-previous-gen.md) |210 - 250* | 1:1 |
| [G1 - G5](sizes-previous-gen.md) |180 - 240* | 1:1 |
| [GS1 - GS5](sizes-previous-gen.md) |180 - 240* | 1:1 |
| [H](h-series.md) |290 - 300* | 1:1 |
| [HB](hb-series.md) |199 - 216** | 1:1 |
| [HC](hc-series.md) |297 - 315* | 1:1 |
| [L4s - L32s](sizes-previous-gen.md) |180 - 240* | 1:1 |
| [L8s_v2 - L80s_v2](lsv2-series.md) |150 - 175** | 2:1 |
| [M](m-series.md) | 160 - 180 | 2:1\*\*\* |

Aqui estão os links para mais informações sobre os diferentes tamanhos:

- [Fins gerais](sizes-general.md)
- [Com otimização de memória](sizes-memory.md)
- [Com otimização de computação](sizes-compute.md)
- [Com otimização de GPU](sizes-gpu.md)
- [Computação de elevado desempenho](sizes-hpc.md)
- [Com otimização de armazenamento](sizes-storage.md)
