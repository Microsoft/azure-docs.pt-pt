---
title: Personalizar as definições de avaliação do Azure Migrate | Documentos da Microsoft
description: Descreve como configurar e executar uma avaliação de migração de VMs de VMware para Azure utilizando o planeador de migração do Azure
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 01/10/2019
ms.author: raynew
ms.openlocfilehash: 8419d7e7a91e4cbfd0eebfe00d35bf498cf5998c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62129828"
---
# <a name="customize-an-assessment"></a>Personalizar uma avaliação

[O Azure Migrate](migrate-overview.md) cria avaliações com propriedades predefinidas. Depois de criar uma avaliação, pode modificar as propriedades de padrão com as instruções neste artigo.


## <a name="edit-assessment-properties"></a>Editar propriedades de avaliação

1. Na página **Avaliações** página do projeto de migração, selecione a avaliação e clique em **Editar propriedades**.
2. Personalize as propriedades de avaliação com base nos seguintes detalhes:

    **Definição** | **Detalhes** | **Predefinição**
    --- | --- | ---
    **Localização de destino** | A localização do Azure para a qual pretende migrar.<br/><br/> Atualmente, o Azure Migrate suporta 30 regiões, incluindo Alemanha Central, Ásia Oriental, Canadá Central, Coreia do Sul Central, EUA Central, EUA Centro-Norte, EUA Centro-Oeste, EUA Centro-Sul, EUA Leste, EUA Leste 2, EUA Oeste, EUA Oeste 2, Europa do Norte, Europa Ocidental, Índia Central, Oeste da Índia, Leste da Austrália, Leste da China, Leste do Canadá, Leste do Japão, Nordeste da Alemanha, Norte da China, Oeste do Japão, Oeste do Reino Unido, Sudeste Asiático, Sudeste da Austrália, Sul da Coreia do Sul, Sul da Índia, Sul BR, Sul do Reino Unido, US Gov – Arizona, US Gov – Texas e US Gov – Virginia. |  E.U.A. oeste 2 é a localização predefinida.
    **Tipo de armazenamento** | Pode utilizar esta propriedade para especificar o tipo de discos que pretende mover para no Azure. Para como-no local de dimensionamento, pode especificar o tipo de disco de destino como discos geridos Premium ou discos geridos Standard. Para o dimensionamento baseado no desempenho, pode especificar o tipo de disco de destino como automática de discos geridos Premium ou discos geridos Standard. Quando especificar o tipo de armazenamento como automático, a recomendação de disco é feita com base nos dados de desempenho de discos (IOPS e débito). Por exemplo, se quer atingir um [única instância de VM SLA de 99,9%](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/), talvez queira especificar o tipo de armazenamento como discos geridos Premium. Isto garante que todos os discos na avaliação são recomendados como discos geridos Premium. Note que o Azure Migrate só suporta discos geridos para avaliação de migrações. | O valor predefinido é de discos geridos Premium (com o critério de dimensionamento como *no local dimensionamento*).
    **Instâncias Reservadas** |  Também pode especificar se tem [instâncias reservadas](https://azure.microsoft.com/pricing/reserved-vm-instances/) no Azure e o Azure Migrate fará uma estimativa dos custos em conformidade. Instâncias reservadas são atualmente suportadas apenas para a oferta pay as you go no Azure Migrate. | O valor predefinido para esta propriedade é instâncias de 3 anos reservados.
    **Critérios de dimensionamento** | Os critérios a serem utilizados pelo Azure Migrate para encontrar VMs de tamanho adequado para o Azure. Pode dimensionar *com base no desempenho* ou dimensionar as VMs *como no local*, sem ter em conta o histórico do desempenho. | O dimensionamento baseado no desempenho é a opção predefinida.
    **Histórico de desempenho** | A duração a considerar para avaliar o desempenho das VMs. Esta propriedade só é aplicável se o critério de dimensionamento for  *dimensionamento baseado no desempenho*. | A predefinição é um dia.
    **Utilização de percentil** | O valor de percentil da amostra de desempenho definido para ser considerado para o dimensionamento certo. Esta propriedade só é aplicável se o critério de dimensionamento for  *dimensionamento baseado no desempenho*.  | A predefinição é o percentil 95.
    **Série das VMs** | Pode especificar a série das VMs que gostaria de considerar para redimensionamento. Por exemplo, se tiver um ambiente de produção que não planeja a migração para VMs de série no Azure, pode excluir série A partir da lista ou série e o dimensionamento certo é feita apenas na série selecionada. | Por predefinição, são selecionadas todas as séries VM.
    **Fator de conforto** | O Azure Migrate considera uma memória intermédia (fator de conforto) durante a avaliação. Esta memória intermédia é aplicada em cima dos dados de utilização das VMs (CPU, memória, disco e rede). O fator de conforto dá conta de problemas como utilização sazonal, histórico de desempenho breve e prováveis aumentos na utilização futura.<br/><br/> Por exemplo, uma VM com 10 núcleos e 20% de utilização resulta, normalmente, numa VM de 2 núcleos. No entanto, com um fator de conforto de 2,0 x, o resultado é uma VM de 4 núcleos. | A predefinição é 1,3 x.
    **Oferta** | A [Oferta do Azure](https://azure.microsoft.com/support/legal/offer-details/) em que se inscreveu. | [Pay as you go](https://azure.microsoft.com/offers/ms-azr-0003p/) é a predefinição.
    **Moeda** | A moeda de faturação. | Predefinição é não utilizados no compromisso E.U.A.
    **Desconto (%)** | Eventuais descontos para uma subscrição específica que receba sobre a oferta do Azure. | A predefinição é 0%.
    **Tempo de atividade de VM** | Se não se pretender que as VMs em execução 24x7 no Azure, pode especificar a duração (número de dias por mês) e o número de horas por dia para que eles estariam em execução e as estimativas de custos teriam de ser feitas em conformidade. | O valor predefinido é 31 dias por mês e 24 horas por dia.
    **Benefício Híbrido do Azure** | Especifique se tem software assurance e se é elegível para o [Benefício Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Se definido como Sim, são considerados para as VMs do Windows preços não Microsoft Azure. | A predefinição é Sim.

3. Clique em **guardar** para atualizar a avaliação.

## <a name="faqs-on-assessment-properties"></a>Perguntas frequentes sobre propriedades de avaliação

### <a name="what-is-the-difference-between-as-on-premises-sizing-and-performance-based-sizing"></a>O que é a diferença entre o dimensionamento como no local e o dimensionamento com base no desempenho?

Quando especificar o critério de tamanho para ser como no local de dimensionamento, do Azure Migrate não considera os dados de desempenho das VMs e os tamanhos de VMs com base na configuração no local. Se o critério de dimensionamento for baseado no desempenho, o dimensionamento é feito com base nos dados de utilização. Por exemplo, se existir uma VM no local com 4 núcleos e 8 GB de memória com 50% de utilização da CPU e 50% de utilização da memória. Se o critério de dimensionamento for como o dimensionamento no local, é recomendado um SKU de VM do Azure com 4 núcleos e 8 GB de memória. No entanto, se o critério de dimensionamento for baseado no desempenho, seria recomendado um SKU de VM de 2 núcleos e 4 GB, uma vez que é considerada a percentagem de utilização ao recomendar o tamanho.

Da mesma forma, para discos, o dimensionamento do disco depende de duas propriedades de avaliação - tipo de armazenamento e de critério de dimensionamento. Se o critério de dimensionamento for baseado no desempenho e o tipo de armazenamento for automático, os valores de IOPS e de débito do disco são considerados para identificar o tipo de disco de destino (Standard ou Premium). Se o critério de dimensionamento for baseado no desempenho e o tipo de armazenamento for premium, é recomendado um disco premium e o SKU de disco premium no Azure é selecionado com base no tamanho do disco no local. É utilizada a mesma lógica para o dimensionamento do disco quando o critério de dimensionamento for como o dimensionamento no local e o tipo de armazenamento for standard ou premium.

### <a name="what-impact-does-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>O impacto que o utilização de percentil e de histórico de desempenho tem as recomendações de tamanho?

Estas propriedades só são aplicáveis ao dimensionamento baseado no desempenho. O Azure Migrate recolhe o histórico de desempenho das máquinas no local e utiliza-o para recomendar o tipo de disco e o tamanho da VM no Azure.

- A aplicação de recoletor cria continuamente perfis do ambiente no local para recolher dados de utilização em tempo real a cada 20 segundos.
- A aplicação acumula os exemplos de 20 segundos e cria um ponto de dados individual para cada 15 minutos. Para criar o ponto de dados individual, a aplicação seleciona o valor de pico de todos os exemplos de 20 segundos e envia-o para o Azure.
- Quando cria uma avaliação no Azure, com base na duração do desempenho e no valor de percentil do histórico de desempenho, o Azure Migrate calcula o valor de utilização eficiente e utiliza-o no dimensionamento.

Por exemplo, se tiver definido a duração do desempenho para ser de 1 dia e o valor de percentil para 95, o Azure Migrate utiliza os pontos de exemplo de 15 minutos enviados pelo recoletor para o último dia, classifica-os por ordem ascendente e escolhe o valor de percentil 95 como utilização eficiente. O valor de percentil 95 garante que está a ignorar todos os valores atípicos, que podem surgir se escolher o percentil 99. Se quiser escolher a utilização de pico para o período e não quiser perder quaisquer valores atípicos, deve selecionar o percentil 99.

## <a name="next-steps"></a>Passos Seguintes

[Saiba mais](concepts-assessment-calculation.md) sobre como são calculadas as avaliações.
