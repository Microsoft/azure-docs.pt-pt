---
title: Personalizar avaliações da avaliação do Azure Migrate Server | Documentos da Microsoft
description: Descreve como personalizar as avaliações criadas com a avaliação do servidor de migrar do Azure
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: 8b200ce3d6e73a575b1b89d82a9323d58f435a48
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807913"
---
# <a name="customize-an-assessment"></a>Personalizar uma avaliação

Este artigo descreve como personalizar as avaliações criadas pela avaliação de servidor de migrar do Azure.

[O Azure Migrate](migrate-services-overview.md) fornece um concentrador central para controlar a deteção, avaliação e migração de aplicações no local e cargas de trabalho e pública/privada na cloud VMs, para o Azure. O hub fornece ferramentas do Azure Migrate para avaliação e migração, bem como ofertas de (ISV fornecedor) de terceiros independentes de software.

Pode utilizar a ferramenta de avaliação do servidor de migrar do Azure para criar avaliações para as VMs de VMware no local e VMs de Hyper-V, em preparação para a migração para o Azure. 

## <a name="about-assessments"></a>Sobre avaliações

Existem dois tipos de avaliações, pode executar a avaliação do servidor de migrar do Azure ao utilizar.

**Assessment** | **Detalhes** | **Dados**
--- | --- | ---
**Com base em desempenho** | Avaliações com base nos dados de desempenho recolhidos | **Tamanho VM recomendado**: Com base nos dados de utilização de CPU e memória.<br/><br/> **Recomendado o tipo de disco (standard ou premium disco gerido)** : Com base no IOPS e débito dos discos no local.
**Como no local** | Avaliações com base no dimensionamento no local. | **Tamanho VM recomendado**: Com base no tamanho VM no local<br/><br> **Recomendado o tipo de disco**: Com base na definição de tipo de armazenamento que selecionar para a avaliação.


## <a name="how-is-an-assessment-done"></a>Como é feita uma avaliação?

Uma avaliação do Azure Migrate tem três fases. Avaliação começa com uma análise de adequação, seguida de dimensionamento, e por último, estimativa de custos mensais. Uma máquina apenas move ao longo para numa fase posterior se for aprovado relativamente anterior. Por exemplo, se um computador falhar a verificação de adequação do Azure, está marcada como não adequado para dimensionamento e custos e do Azure, a não ser feito.

## <a name="whats-in-an-assessment"></a>Novidades nas avaliações

**Propriedade** | **Detalhes**
--- | ---
**Localização de destino** | A localização do Azure para a qual pretende migrar.<br/> Atualmente, o Azure Migrate suporta estas regiões de destino: Leste da Austrália, Sudeste da Austrália, sul do Brasil, Canadá Central, leste do Canadá, Índia Central, E.U.A. Central, leste da China, Norte da China, Ásia Oriental, E.U.A. leste, e.u.a. Leste 2, Alemanha Central, Alemanha Nordeste, Japão leste, oeste do Japão, Coreia Central, Coreia, Sul, Norte Centro dos E.U.A., Europa do Norte, dos E.U.A. centro-Sul, Sudeste asiático, Sul da Índia, sul do Reino Unido, oeste do Reino Unido, E.U.A. Gov Arizona, US Gov Texas, US Gov Virgínia, EUA Centro-Oeste, Europa Ocidental, Índia Ocidental, E.U.A. oeste e E.u.a. oeste2.<br/> Por predefinição, a região de destino está definida como E.U.A. Oeste 2.
**Tipo de armazenamento** | Discos/Standard do padrão HHD discos SSD/Premium.<br/> Quando especificar o tipo de armazenamento como automática nas avaliações, a recomendação de disco baseia-se nos dados de desempenho de discos (IOPS e débito).<br/> Se especificar o tipo de armazenamento como Premium ou Standard, a recomenda de avaliação um disco SKU dentro do tipo de armazenamento selecionada.<br/> Se quer atingir uma única instância de VM SLA de 99,9%, pode definir o tipo de armazenamento como discos geridos Premium. Em seguida, todos os discos na avaliação irão ser recomendados como discos geridos Premium. <br/> O Azure Migrate só suporta discos geridos para avaliação de migrações.<br/> 
**Instâncias reservadas (RIS)** | Especifica esta propriedade se têm reservados instâncias no Azure. Estimativas de custos na avaliação levará descontos de RI em conta. Instâncias reservadas são atualmente apenas suportado para pay as you go oferece no Azure Migrate.
**Critérios de dimensionamento** | Utilizado para VMs de tamanho adequado. Dimensionamento pode ser baseado no desempenho, ou **como no local**, sem considerar o histórico de desempenho.
**Histórico de desempenho** | A duração a considerar para avaliar o desempenho da VM. Esta propriedade só é aplicável quando o dimensionamento é baseado no desempenho.
**Utilização de percentil** | O valor de percentil da amostra de desempenho que é utilizado para VMs de redimensionamento. Esta propriedade só é aplicável quando o dimensionamento é baseado no desempenho.
**Série das VMs** | A série das VMs utilizada para os cálculos de tamanho. Por exemplo, se tiver um ambiente de produção que não pretende migrar para VMs da série A no Azure, poderá excluir a série A da lista ou da série. O dimensionamento tem por base apenas as séries selecionadas.
**Fator de conforto** | Avaliação Server do Azure Migrate considera uma memória intermédia (fator de conforto) durante a avaliação. Esta memória intermédia é aplicada em cima dos dados de utilização das VMs (CPU, memória, disco e rede). O fator de conforto dá conta de problemas como utilização sazonal, histórico de desempenho breve e prováveis aumentos na utilização futura.<br/><br/> Por exemplo, uma VM com 10 núcleos e 20% de utilização resulta, normalmente, numa VM de 2 núcleos. No entanto, com um fator de conforto de 2,0 x, o resultado é uma VM de 4 núcleos.
**Oferta** | A [oferta do Azure](https://azure.microsoft.com/support/legal/offer-details/) em que está escrito. O Azure Migrate calcula o custo em conformidade.
**Moeda** | A moeda de faturação. 
**Desconto (%)** | Eventuais descontos para uma subscrição específica que receba sobre a oferta do Azure.<br/> A predefinição é 0%.
**Tempo de atividade de VM** | Se não se pretender que as VMs em execução 24x7 no Azure, pode especificar a duração (número de dias por mês) e o número de horas por dia para que eles estariam em execução e as estimativas de custos teriam de ser feitas em conformidade.<br/> O valor predefinido é 31 dias por mês e 24 horas por dia.
**Benefício Híbrido do Azure** | Especifica se tem o software assurance e são elegíveis para [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Se definido como Sim, são considerados para as VMs do Windows preços não Microsoft Azure. | A predefinição é Sim.


## <a name="edit-assessment-properties"></a>Editar propriedades de avaliação

Para editar as propriedades de avaliação depois de criar uma avaliação, faça o seguinte:

1. No projeto do Azure Migrate, clique em **servidores**.
2. No **do Azure Migrate: Avaliação de servidor**, clique na contagem de avaliações.
3. Na **Assessment**, clique na avaliação relevante > **editar as propriedades**.
5. Personalize as propriedades de avaliação em conformidade com a tabela acima.
6. Clique em **guardar** para atualizar a avaliação.


Também pode editar propriedades de avaliação quando estiver a criar uma avaliação.


## <a name="next-steps"></a>Passos Seguintes

[Saiba mais](concepts-assessment-calculation.md) sobre como são calculadas as avaliações.
