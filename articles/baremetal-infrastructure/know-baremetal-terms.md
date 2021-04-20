---
title: Conheça os termos da Infraestrutura Azure BareMetal
description: Conheça os termos da Infraestrutura Azure BareMetal.
ms.topic: conceptual
ms.subservice: workloads
ms.date: 04/06/2021
ms.openlocfilehash: 61ff958e75952f73efb222df3f0c4d5437668cd3
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725463"
---
# <a name="know-the-terms-for-baremetal-infrastructure"></a>Conheça os termos da Infraestrutura BareMetal

Neste artigo, cobriremos alguns termos importantes relacionados com a Infraestruturas BareMetal.

- **Revisão**: Existem duas revisões de selos diferentes para os selos da Infraestrutura BareMetal (HANA Large Instance). Estes diferem na arquitetura e na proximidade com os anfitriões de máquinas virtuais Azure:
    - "Revisão 3" (Rev 3): O desenho original foi implantado em meados de 2016.
    - "Revisão 4.2" (Rev 4.2): Um novo design que proporciona uma maior proximidade aos anfitriões de máquinas virtuais Azure, com latência de rede ultra-baixa entre VMs Azure e HANA Large Instances. Os recursos no portal Azure são referidos como "Infraestruturas BareMetal", e os clientes podem aceder aos seus recursos como instâncias BareMetal do portal Azure.

- **Carimbo**: Define o tamanho de implementação interna da Microsoft de instâncias BareMetal. Antes de poderem ser implementados casos, um carimbo de instância BareMetal constituído por cremalheira de computação, rede e armazenamento deve ser implantado num local de datacenter. Tal implantação é chamada de carimbo de instância BareMetal.

- **Inquilino**: Um cliente que implante um carimbo de instância BareMetal fica isolado como *inquilino.* Um inquilino está isolado na camada de networking, armazenamento e computação de outros inquilinos. As unidades de armazenamento e computação atribuídas aos diferentes inquilinos não podem ver-se ou comunicar entre si no nível de selo de instância BareMetal. Um cliente pode optar por ter implementações em diferentes inquilinos. Mesmo assim, não há comunicação entre inquilinos no nível de selo de instância BareMetal.

## <a name="next-steps"></a>Passos seguintes

Agora que foi apresentado a uma terminologia importante da Infraestrutura BareMetal, talvez queira aprender sobre:
- Mais detalhes sobre a [Infraestrutura BareMetal.](concepts-baremetal-infrastructure-overview.md)
- Como [ligar as instâncias da Infraestrutura BareMetal em Azure](connect-baremetal-infrastructure.md).

