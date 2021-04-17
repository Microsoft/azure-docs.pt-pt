---
title: Conheça os termos da Infraestrutura Azure BareMetal
description: Conheça os termos da Infraestrutura Azure BareMetal.
ms.topic: conceptual
ms.subservice: workloads
ms.date: 04/06/2021
ms.openlocfilehash: aa7d9693b3417ff0bb6c6a61800aee72cd416c48
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536776"
---
# <a name="know-the-terms-for-baremetal-infrastructure"></a>Conheça os termos da Infraestrutura BareMetal

Neste artigo, cobriremos alguns termos importantes relacionados com a Infraestruturas BareMetal.

- **Revisão**: Há uma revisão original do selo conhecida como Revisão 3 (Rev 3), e duas revisões adicionais de selos para selos de instância BareMetal. Cada carimbo difere na arquitetura e na proximidade com os anfitriões de máquinas virtuais Azure:
    - **Revisão 4** (Rev 4): Um design mais recente que proporciona uma maior proximidade com a máquina virtual Azure (VM) acolhe e diminui a latência entre os VMs Azure e os casos SAP HANA. 
    - **Revisão 4.2** (Rev 4.2): A mais recente infraestrutura BareMetal remarcada utilizando a arquitetura rev 4 existente. O Rev 4 proporciona uma maior proximidade com os anfitriões da máquina virtual Azure (VM). Tem melhorias significativas na latência da rede entre os VMs Azure e os casos BareMetal implantados em selos ou linhas Rev 4. Pode aceder e gerir as suas instâncias BareMetal através do portal Azure.    

- **Carimbo**: Define o tamanho de implementação interna da Microsoft de instâncias BareMetal. Antes de poderem ser implementados casos, um carimbo de instância BareMetal constituído por cremalheira de computação, rede e armazenamento deve ser implantado num local de datacenter. Tal implantação é chamada de carimbo de instância BareMetal.

- **Inquilino**: Um cliente que implante um carimbo de instância BareMetal fica isolado como *inquilino.* Um inquilino está isolado na camada de networking, armazenamento e computação de outros inquilinos. As unidades de armazenamento e computação atribuídas aos diferentes inquilinos não podem ver-se ou comunicar entre si no nível de selo de instância BareMetal. Um cliente pode optar por ter implementações em diferentes inquilinos. Mesmo assim, não há comunicação entre inquilinos no nível de selo de instância BareMetal.

## <a name="next-steps"></a>Passos seguintes

Agora que foi apresentado a uma terminologia importante da Infraestrutura BareMetal, talvez queira aprender sobre:
- Mais detalhes sobre a [Infraestrutura BareMetal.](concepts-baremetal-infrastructure-overview.md)
- Como [ligar as instâncias da Infraestrutura BareMetal em Azure](connect-baremetal-infrastructure.md).

