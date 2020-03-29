---
title: Personalizar avaliações para a Avaliação do Servidor Migratório Azure [ Microsoft Docs
description: Descreve como personalizar avaliações criadas com a Avaliação do Servidor Migratório Azure
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: 2cfac978b0a5af20e9e2fa1e32a7361488f20fbe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68234277"
---
# <a name="customize-an-assessment"></a>Personalizar uma avaliação

Este artigo descreve como personalizar avaliações criadas pela Azure Migrate Server Assessment.

[A Azure Migrate](migrate-services-overview.md) fornece um centro central para acompanhar a descoberta, avaliação e migração das suas aplicações e cargas de trabalho no local, e VMs de nuvem privada/pública, para O Azure. O hub fornece ferramentas azure migrate para avaliação e migração, bem como ofertas de fornecedores de software independentes de terceiros (ISV).

Pode utilizar a ferramenta de avaliação do servidor de migração Azure para criar avaliações para VMware VMs no local e VMs Hiper-V, em preparação para a migração para O Azure.

## <a name="about-assessments"></a>Sobre avaliações

Existem dois tipos de avaliações que pode executar usando a Avaliação do Servidor Migratório Azure.

**Avaliação** | **Detalhes** | **Dados**
--- | --- | ---
**Baseado no desempenho** | Avaliações baseadas em dados de desempenho recolhidos | **Tamanho VM recomendado**: Baseado em CPU e dados de utilização da memória.<br/><br/> **Tipo de disco recomendado (disco gerido padrão ou premium)**: Baseado no IOPS e na entrada dos discos no local.
**Como no local** | Avaliações baseadas no dimensionamento no local. | **Tamanho VM recomendado**: Baseado no tamanho vm no local<br/><br> **Tipo**de disco recomendado : Com base na definição do tipo de armazenamento selecionada para a avaliação.


## <a name="how-is-an-assessment-done"></a>Como é feita uma avaliação?

Uma avaliação feita na Avaliação do Servidor Migratório Azure tem três fases. A avaliação começa com uma análise de adequação, seguida de dimensionamento, e, por último, uma estimativa mensal de custos. Uma máquina só se move para uma fase posterior se passar a anterior. Por exemplo, se uma máquina falhar a verificação de adequação do Azure, está marcada como inadequada para o Azure, e o tamanho e o custo não serão feitos. [Saiba mais.](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation)

## <a name="whats-in-an-assessment"></a>Novidades nas avaliações

**Propriedade** | **Detalhes**
--- | ---
**Localização de destino** | A localização do Azure para a qual pretende migrar.<br/> A tualmente, a Server Assessment apoia estas regiões-alvo: Austrália Leste, Austrália Sudeste, Brasil Sul, Canadá Central, Canadá Leste, Índia Central, Eua Central, China Leste, China Norte, Ásia Oriental, Leste dos EUA, Leste dos EUA, Leste dos EUA, Alemanha Central, Alemanha Nordeste, Japão Leste, Japão Oeste, Coreia Central, Coreia sul, Norte Central EUA, Norte Europa, Sul Dos EUA, Sudeste Asiático, Índia do Sul, Reino Unido, Reino Unido Oeste, EUA Gov Arizona, EUA Gov Texas, EUA Gov Virginia, West Central US, West Europe, West India, West US, e West US2.
**Tipo de armazenamento** | Pode utilizar esta propriedade para especificar o tipo de discos para os quais pretende mover-se, em Azure.<br/><br/> Para o dimensionamento de instalações as-on, pode especificar o tipo de armazenamento alvo, quer como discos geridos por Premium, discos geridos por Standard SSD ou discos geridos por Standard HDD. Para dimensionamento baseado no desempenho, pode especificar o tipo de disco-alvo quer como discos automáticos, geridos por Prémios, discos geridos por HDD standard ou discos geridos por Standard SSD.<br/><br/> Quando especifica o tipo de armazenamento como automático, a recomendação do disco é feita com base nos dados de desempenho dos discos (IOPS e entrada). Se especificar o tipo de armazenamento como premium/standard, a avaliação recomendará um SKU de disco dentro do tipo de armazenamento selecionado. Se pretender atingir uma única instância VM SLA de 99,9%, poderá querer especificar o tipo de armazenamento como discos geridos por Prémios. Isto garante que todos os discos da avaliação são recomendados como discos geridos por Prémio. Azure
**Instâncias Reservadas (RI)** | Esta propriedade ajuda-o a especificar se tem [Instâncias Reservadas](https://azure.microsoft.com/pricing/reserved-vm-instances/) em Azure, as estimativas de custos na avaliação são feitas tendo em descontos RI. Atualmente, as instâncias reservadas são apenas suportadas para a oferta pay-As-You-Go em Azure Migrate.
**Critério de dimensionamento** | O critério a utilizar para VMs de tamanho direito para O Azure. Você pode fazer *tamanhos baseados no desempenho* ou dimensionar os VMs *como no local*, sem considerar o histórico de desempenho.
**Histórico de desempenho** | A duração a considerar para avaliar os dados de desempenho das máquinas. Esta propriedade só é aplicável quando o critério de dimensionamento é *baseado no desempenho.*
**Utilização de percentil** | O valor de percentil da amostra de desempenho definido para ser considerado para o dimensionamento certo. Esta propriedade só é aplicável quando o dimensionamento é *baseado no desempenho.*
**Série das VMs** |     Pode especificar a série das VMs que gostaria de considerar para redimensionamento. Por exemplo, se tiver um ambiente de produção que não planeie migrar para VMs da série A em Azure, pode excluir séries A da lista ou série e o tamanho certo é feito apenas na série selecionada.
**Fator de conforto** | A Avaliação do Servidor Migratório Azure considera um tampão (fator de conforto) durante a avaliação. Esta memória intermédia é aplicada em cima dos dados de utilização das VMs (CPU, memória, disco e rede). O fator de conforto dá conta de problemas como utilização sazonal, histórico de desempenho breve e prováveis aumentos na utilização futura.<br/><br/> Por exemplo, uma VM com 10 núcleos e 20% de utilização resulta, normalmente, numa VM de 2 núcleos. No entanto, com um fator de conforto de 2,0 x, o resultado é uma VM de 4 núcleos.
**Oferta** | A [oferta do Azure](https://azure.microsoft.com/support/legal/offer-details/) em que está escrito. O Azure Migrate calcula o custo em conformidade.
**Moeda** | A moeda de faturação.
**Desconto (%)** | Eventuais descontos para uma subscrição específica que receba sobre a oferta do Azure.<br/> A predefinição é 0%.
**Tempo de atividade de VM** | Se os seus VMs não estiverem a funcionar 24x7 em Azure, pode especificar a duração (número de dias por mês e número de horas por dia) para os quais estariam a funcionar e as estimativas de custos seriam feitas em conformidade.<br/> O valor predefinido é de 31 dias por mês e 24 horas por dia.
**Benefício Híbrido do Azure** | Especifique se tem garantia de software e é elegível para [O Benefício Híbrido Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Se definido como Sim, são considerados para as VMs do Windows preços não Microsoft Azure. A predefinição é Sim.


## <a name="edit-assessment-properties"></a>Editar propriedades de avaliação

Para editar propriedades de avaliação após a criação de uma avaliação, faça o seguinte:

1. No projeto Azure Migrate, clique em **Servidores**.
2. Em **Azure Migrate: Avaliação**do servidor, clique na contagem de avaliações.
3. Em **Avaliação,** clique na avaliação relevante > **editar propriedades.**
5. Personalize as propriedades de avaliação de acordo com a tabela acima.
6. Clique em **Guardar** para atualizar a avaliação.


Também pode editar propriedades de avaliação quando estiver a criar uma avaliação.


## <a name="next-steps"></a>Passos seguintes

[Saiba mais](concepts-assessment-calculation.md) sobre como são calculadas as avaliações.
