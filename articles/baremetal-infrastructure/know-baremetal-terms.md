---
title: Conheça os termos da Infraestrutura Azure BareMetal
description: Conheça os termos da Infraestrutura Azure BareMetal.
ms.topic: conceptual
ms.date: 12/31/2020
ms.openlocfilehash: f11bc4cfdd463623010ed7b6677235344ec7cd62
ms.sourcegitcommit: 42922af070f7edf3639a79b1a60565d90bb801c0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2020
ms.locfileid: "97829216"
---
# <a name="know-the-terms-for-baremetal-infrastructure"></a>Conheça os termos da Infraestrutura BareMetal

Neste artigo, cobriremos alguns termos importantes da BareMetal.

- **Revisão**: Existem duas revisões de selos diferentes para os selos BareMetal Instance. Cada versão difere em arquitetura e proximidade com os anfitriões de máquinas virtuais Azure:
    - **Revisão 3** (Rev 3): é o desenho original.
    - **Revisão 4** (Rev 4): é um novo design que proporciona uma maior proximidade com os anfitriões da máquina virtual Azure (VM) e diminui a latência entre as unidades Azure VMs e BareMetal Instance. 
    - **Revisão 4.2** (Rev 4.2): é a mais recente infraestrutura baremetal remarcada que utiliza a arquitetura rev 4 existente. Pode aceder e gerir as suas instâncias BareMetal através do portal Azure.  

- **Carimbo**: Define o tamanho de implementação interna da Microsoft de Instâncias BareMetal. Antes de as unidades de exemplo poderem ser implantadas, um carimbo BareMetal Instance, composto por computação, rede e prateleiras de armazenamento, deve ser implantado num local de datacenter. Tal implantação é chamada de carimbo Desnmômetros ou da Revisão 4.2.

- **Inquilino**: Um cliente implantado no carimbo BareMetal Instance fica isolado num *inquilino.* Um inquilino está isolado na camada de networking, armazenamento e computação de outros inquilinos. As unidades de armazenamento e computação atribuídas aos diferentes inquilinos não podem ver-se ou comunicar entre si no nível de selo BareMetal Instance. Um cliente pode optar por ter implementações em diferentes inquilinos. Mesmo assim, não há comunicação entre inquilinos no nível de selo BareMetal Instance.

## <a name="next-steps"></a>Passos seguintes
Saiba como identificar e interagir com unidades de Exemplo BareMetal através do [portal Azure](workloads/sap/baremetal-infrastructure-portal.md).


 