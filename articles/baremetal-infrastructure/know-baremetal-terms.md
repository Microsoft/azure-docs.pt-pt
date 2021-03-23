---
title: Conheça os termos da Infraestrutura Azure BareMetal
description: Conheça os termos da Infraestrutura Azure BareMetal.
ms.topic: conceptual
ms.date: 1/4/2021
ms.openlocfilehash: b22a6cecb2647df3878cb8fd4ade93d9a7d963fd
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104770891"
---
# <a name="know-the-terms-for-baremetal-infrastructure"></a>Conheça os termos da Infraestrutura BareMetal

Neste artigo, cobriremos alguns termos importantes da BareMetal.

- **Revisão**: Há uma revisão original do selo conhecida como Revisão 3 (Rev 3), e duas revisões de selos diferentes para selos BareMetal Instance. Cada carimbo difere na arquitetura e na proximidade com os anfitriões de máquinas virtuais Azure:
    - **Revisão 4** (Rev 4): um design mais recente que proporciona uma maior proximidade com a máquina virtual Azure (VM) acolhe e diminui a latência entre as unidades Azure VMs e BareMetal Instance. 
    - **Revisão 4.2** (Rev 4.2): a mais recente infraestrutura BareMetal remarcada utilizando a arquitetura rev 4 existente. O Rev 4 proporciona uma maior proximidade com os anfitriões da máquina virtual Azure (VM). Tem melhorias significativas na latência da rede entre as unidades de instância Azure VMs e BareMetal implantadas em selos ou linhas Rev 4. Pode aceder e gerir as suas instâncias BareMetal através do portal Azure.    

- **Carimbo**: Define o tamanho de implementação interna da Microsoft de Instâncias BareMetal. Antes de as unidades de exemplo poderem ser implantadas, um carimbo bareMetal Instance, composto por computação, rede e prateleiras de armazenamento, deve ser implantado num local de datacenter. Tal implantação é chamada de carimbo Desnmômetros ou da Revisão 4.2.

- **Inquilino**: Um cliente implantado no carimbo BareMetal Instance fica isolado num *inquilino.* Um inquilino está isolado na camada de networking, armazenamento e computação de outros inquilinos. As unidades de armazenamento e computação atribuídas aos diferentes inquilinos não podem ver-se ou comunicar entre si no nível de selo BareMetal Instance. Um cliente pode optar por ter implementações em diferentes inquilinos. Mesmo assim, não há comunicação entre inquilinos no nível de selo BareMetal Instance.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre a [Infraestrutura BareMetal](concepts-baremetal-infrastructure-overview.md) ou como [identificar e interagir com unidades de Exemplo BareMetal](connect-baremetal-infrastructure.md). 