---
title: Personalizar Avaliações para a avaliação do servidor de migrações para Azure | Microsoft Docs
description: Descreve como personalizar avaliações criadas com a avaliação de servidor de migrações para Azure
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: 2cfac978b0a5af20e9e2fa1e32a7361488f20fbe
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234277"
---
# <a name="customize-an-assessment"></a>Personalizar uma avaliação

Este artigo descreve como personalizar as avaliações criadas pela avaliação do servidor de migrações para Azure.

As migrações para [Azure](migrate-services-overview.md) fornecem um hub central para acompanhar a descoberta, avaliação e migração de seus aplicativos e cargas de trabalho locais e VMs de nuvem privada/pública, para o Azure. O Hub fornece ferramentas de migração do Azure para avaliação e migração, bem como ofertas de ISVs (fornecedores independentes de software) de terceiros.

Você pode usar a ferramenta de avaliação de servidor de migrações para Azure para criar Avaliações para VMs VMware locais e VMs do Hyper-V, em preparação para a migração para o Azure.

## <a name="about-assessments"></a>Sobre avaliações

Há dois tipos de avaliações que você pode executar usando a avaliação de servidor de migrações para Azure.

**Locação** | **Detalhes** | **Dados**
--- | --- | ---
**Baseado em desempenho** | Avaliações com base nos dados de desempenho coletados | **Tamanho de VM recomendado**: Com base nos dados de utilização de CPU e memória.<br/><br/> **Tipo de disco recomendado (disco gerenciado Standard ou Premium)** : Com base na IOPS e na taxa de transferência dos discos locais.
**Como local** | Avaliações com base no dimensionamento local. | **Tamanho de VM recomendado**: Com base no tamanho da VM local<br/><br> **Tipo de disco recomendado**: Com base na configuração de tipo de armazenamento que você selecionar para a avaliação.


## <a name="how-is-an-assessment-done"></a>Como é feita uma avaliação?

Uma avaliação feita na avaliação do servidor de migrações para Azure tem três estágios. A avaliação começa com uma análise de adequação, seguida do dimensionamento e, por fim, de uma estimativa de custo mensal. Um computador só se moverá para um estágio posterior se ele passar o anterior. Por exemplo, se um computador falhar na verificação de adequação do Azure, ele será marcado como não adequado para o Azure, e o dimensionamento e o custo não serão feitos. [Saiba mais.](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation)

## <a name="whats-in-an-assessment"></a>Novidades nas avaliações

**Propriedade** | **Detalhes**
--- | ---
**Localização de destino** | A localização do Azure para a qual pretende migrar.<br/> Atualmente, a avaliação do servidor dá suporte a estas regiões de destino: Leste da Austrália, sudeste da Austrália, sul do Brasil, centro do Canadá, leste do Canadá, Índia central, EUA Central, Leste da China, Norte da China, Ásia Oriental, leste dos EUA, leste dos dos EUA 2, Alemanha central, Alemanha nordeste, leste do Japão, oeste do Japão, Coreia central, sul da Coreia, norte EUA Central, Europa Setentrional, Sul EUA Central, Sudeste Asiático, sul da Índia, Sul do Reino Unido, Oeste do Reino Unido, US Gov Arizona, US Gov Texas, US Gov-Virgínia, West EUA Central, Europa Ocidental, oeste da Índia, oeste dos EUA e West dos EUA 2.
**Tipo de armazenamento** | Você pode usar essa propriedade para especificar o tipo de discos para o qual deseja mover, no Azure.<br/><br/> Para o dimensionamento local, você pode especificar o tipo de armazenamento de destino como discos gerenciados Premium, discos gerenciados SSD Standard ou discos gerenciados por HDD Standard. Para o dimensionamento baseado em desempenho, você pode especificar o tipo de disco de destino como discos automáticos, gerenciados Premium, discos gerenciados HDD Standard ou discos gerenciados por SSD Standard.<br/><br/> Quando você especifica o tipo de armazenamento como automático, a recomendação de disco é feita com base nos dados de desempenho dos discos (IOPS e taxa de transferência). Se você especificar o tipo de armazenamento como Premium/Standard, a avaliação recomendará um SKU de disco dentro do tipo de armazenamento selecionado. Se você quiser obter um SLA de VM de instância única de 99,9%, talvez queira especificar o tipo de armazenamento como discos gerenciados Premium. Isso garante que todos os discos na avaliação sejam recomendados como discos gerenciados Premium. Azure
**Instâncias reservadas (RI)** | Essa propriedade ajuda a especificar se você tem [instâncias reservadas](https://azure.microsoft.com/pricing/reserved-vm-instances/) no Azure, as estimativas de custo na avaliação são então feitas em descontos de ri. Atualmente, as instâncias reservadas só têm suporte para a oferta pré-paga em migrações para Azure.
**Critérios de dimensionamento** | O critério a ser usado para o tamanho correto das VMs para o Azure. Você pode fazer o dimensionamento *com base no desempenho* ou dimensionar as VMs *como locais*, sem considerar o histórico de desempenho.
**Histórico de desempenho** | A duração a ser considerada para avaliar os dados de desempenho dos computadores. Essa propriedade só é aplicável quando o critério de dimensionamento é *baseado em desempenho*.
**Utilização de percentil** | O valor de percentil da amostra de desempenho definido para ser considerado para o dimensionamento certo. Essa propriedade só é aplicável quando o dimensionamento é *baseado em desempenho*.
**Série das VMs** |     Pode especificar a série das VMs que gostaria de considerar para redimensionamento. Por exemplo, se você tiver um ambiente de produção que não planeja migrar para VMs da série A no Azure, poderá excluir uma série da lista ou da série e o dimensionamento à direita será feito somente na série selecionada.
**Fator de conforto** | A avaliação de servidor de migrações para Azure considera um buffer (fator de conforto) durante a avaliação. Esta memória intermédia é aplicada em cima dos dados de utilização das VMs (CPU, memória, disco e rede). O fator de conforto dá conta de problemas como utilização sazonal, histórico de desempenho breve e prováveis aumentos na utilização futura.<br/><br/> Por exemplo, uma VM com 10 núcleos e 20% de utilização resulta, normalmente, numa VM de 2 núcleos. No entanto, com um fator de conforto de 2,0 x, o resultado é uma VM de 4 núcleos.
**Oferta** | A [oferta do Azure](https://azure.microsoft.com/support/legal/offer-details/) em que está escrito. O Azure Migrate calcula o custo em conformidade.
**Moeda** | A moeda de faturação.
**Desconto (%)** | Eventuais descontos para uma subscrição específica que receba sobre a oferta do Azure.<br/> A predefinição é 0%.
**Tempo de atividade de VM** | Se suas VMs não estiverem em execução 24x7 no Azure, você poderá especificar a duração (número de dias por mês e o número de horas por dia) para as quais elas serão executadas e as estimativas de custo serão feitas de acordo.<br/> O valor padrão é 31 dias por mês e 24 horas por dia.
**Benefício Híbrido do Azure** | Especifique se você tem o Software Assurance e se está qualificado para [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Se definido como Sim, são considerados para as VMs do Windows preços não Microsoft Azure. A predefinição é Sim.


## <a name="edit-assessment-properties"></a>Editar propriedades de avaliação

Para editar as propriedades de avaliação depois de criar uma avaliação, faça o seguinte:

1. No projeto migrações para Azure, clique em **servidores**.
2. Nas **migrações para Azure: Avaliação**do servidor, clique na contagem de avaliações.
3. Em **avaliação**, clique na avaliação relevante > **Editar propriedades**.
5. Personalize as propriedades de avaliação de acordo com a tabela acima.
6. Clique em **salvar** para atualizar a avaliação.


Você também pode editar as propriedades de avaliação quando estiver criando uma avaliação.


## <a name="next-steps"></a>Passos Seguintes

[Saiba mais](concepts-assessment-calculation.md) sobre como são calculadas as avaliações.
