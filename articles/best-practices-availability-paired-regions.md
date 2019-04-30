---
title: 'Negócios continuidade e recuperação após desastre (BCDR): Regiões emparelhadas do Azure | Documentos da Microsoft'
description: Saiba mais sobre emparelhamento regional do Azure, para garantir que os aplicativos sejam resilientes durante falhas de datacenters.
author: rayne-wiselman
manager: carmon
ms.service: multiple
ms.topic: article
ms.date: 04/17/2019
ms.author: raynew
ms.openlocfilehash: ecbe73e02631e3c3601bd929282d467cb05b41e4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60616843"
---
# <a name="business-continuity-and-disaster-recovery-bcdr-azure-paired-regions"></a>Negócios continuidade e recuperação após desastre (BCDR): Regiões emparelhadas do Azure

## <a name="what-are-paired-regions"></a>Quais são as regiões emparelhadas?

O Azure opera em várias localizações geográficas em todo o mundo. Uma geografia do Azure é uma área definida de todo o mundo que contém pelo menos uma região do Azure. Uma região do Azure é uma área dentro de uma localização geográfica, que contém um ou mais datacenters.

Cada região do Azure é emparelhada com outra região na mesma geografia, em conjunto, tornando um par regional. A exceção é o sul do Brasil, que se encontra emparelhado com uma região fora da sua área geográfica. Entre os pares de região Azure serializa atualizações de plataforma (manutenção planeada), para que apenas um emparelhado a região é atualizada ao mesmo tempo. Em caso de interrupção que afetam várias regiões, pelo menos uma região em cada par irá ser priorizada para recuperação.

![AzureGeography](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

Figura 1 – pares regionais do Azure

| Geografia | Regiões emparelhadas |  |
|:--- |:--- |:--- |
| Ásia |Ásia Oriental |Sudeste Asiático |
| Austrália |Leste da Austrália |Sudeste da Austrália |
| Austrália |Austrália Central |Austrália Central 2 |
| Brasil |Sul do Brasil |EUA Centro-Sul |
| Canadá |Canadá Central |Leste do Canadá |
| China |China Norte |Leste da China|
| China |Norte da China 2 |Leste da China 2|
| Europa |Europa do Norte |Europa Ocidental |
| França |França Central|Sul de França|
| Alemanha |Alemanha Central |Alemanha Nordeste |
| Alemanha |Norte da Alemanha | Alemanha Oeste-Central
| Índia |Índia Central |Sul da Índia |
| Índia |Oeste da Índia |Sul da Índia |
| Japão |Leste do Japão |Oeste do Japão |
| Coreia |Coreia do Sul Central |Coreia do Sul |
| América do Norte |EUA Leste |EUA Oeste |
| América do Norte |EUA Leste 2 |EUA Central |
| América do Norte |EUA Centro-Norte |EUA Centro-Sul |
| América do Norte |EUA Oeste 2 |EUA Centro-Oeste 
| América do Norte |E.U.A. oeste 3 |EUA Leste
| Noruega |Leste da Noruega |Oeste da Noruega
| África do Sul | África do Sul, Norte | África do Sul, Oeste
| Suécia |Central de Suécia |Suécia Sul
| Suíça | Norte da Suíça | Oeste da Suíça
| RU |Reino Unido Oeste |Reino Unido Sul |
| RU |Norte do Reino Unido |Sul do Reino Unido 2
| Emirados Árabes Unidos | Norte dos EAU | Emirados Árabes Unidos Centro
| Departamento de Defesa dos E.U.A. |US DoD Leste |US DoD Centro |
| Governo dos Estados Unidos da América |Gov (US) - Arizona |Gov (US) - Texas |
| Governo dos Estados Unidos da América |US Gov - Iowa |Gov (US) - Virginia |
| Governo dos Estados Unidos da América |Gov (US) - Virginia |Gov (US) - Texas |

Tabela 1 - mapeamento de pares regionais do Azure

- Índia Ocidental está emparelhada em apenas uma direção. É de região secundária do oeste da Índia sul da Índia, mas a região secundária do Sul da Índia é Índia Central.
- Sul do Brasil é exclusivo, porque ele está emparelhado com uma região fora do seu próprio geografia. A região secundária do Sul do Brasil é Centro-Sul. Da Sul E.u.a. região secundária não é sul do Brasil.
- A região secundária do US Gov Iowa é Virgínia gov (US).
- A região secundária de US Gov Virgínia é US Gov Texas.
- Região secundária do US Gov Texas é US Gov Arizona.


Recomendamos que configure a recuperação de desastres de continuidade de negócio (BCDR) através de pares regionais para beneficiar de políticas de isolamento e a disponibilidade do Azure. Para aplicações que suportam várias regiões do Active Directory, recomendamos que utilize ambas as regiões num par de região sempre que possível. Isto irá garantir a disponibilidade ideal para aplicações e de tempo de recuperação minimizado em caso de desastre. 

## <a name="an-example-of-paired-regions"></a>Um exemplo de regiões emparelhadas
Figura 2 abaixo mostra um aplicativo hipotético que usa o par regional para recuperação após desastre. Os números verde realçam as atividades entre regiões de três serviços do Azure (Azure computação, armazenamento e base de dados) e como estão configurados para replicar em várias regiões. As vantagens exclusivas da implementação em várias regiões emparelhadas estão realçadas através dos números de cor de laranja.

![Descrição geral dos benefícios de região emparelhada](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

Figura 2 – hipotético par regional do Azure

## <a name="cross-region-activities"></a>Atividades em várias regiões
Como referido na figura 2.

![IaaS](./media/best-practices-availability-paired-regions/1Green.png) **computação do Azure (IaaS)** – terá de aprovisionar recursos de computação adicionais com antecedência para garantir que recursos estão disponíveis noutra região durante um desastre. Para obter mais informações, consulte [orientações técnicas sobre resiliência do Azure](resiliency/resiliency-technical-guidance.md).

![Armazenamento](./media/best-practices-availability-paired-regions/2Green.png) **armazenamento do Azure** -armazenamento Georredundante (GRS) está configurado por predefinição, quando é criada uma conta de armazenamento do Azure. Com a GRS, os dados automaticamente são replicados três vezes numa região principal e três vezes na região associada. Para obter mais informações, consulte [opções de redundância de armazenamento do Azure](storage/common/storage-redundancy.md).

![SQL do Azure](./media/best-practices-availability-paired-regions/3Green.png) **base de dados do Azure SQL** – com o Azure SQL da base de dados-Georreplicação, pode configurar a replicação assíncrona de transações em qualquer região do mundo; no entanto, é recomendável implementar estes recursos num região emparelhada na maioria dos cenários de recuperação após desastre. Para obter mais informações, consulte [Georreplicação no Azure SQL Database](sql-database/sql-database-geo-replication-overview.md).

![Gestor de recursos](./media/best-practices-availability-paired-regions/4Green.png) **do Azure Resource Manager** -Resource Manager inerentemente fornece isolamento lógico dos componentes em várias regiões. Isso significa que as falhas de lógicas numa região são menor probabilidade de afetar o outro.

## <a name="benefits-of-paired-regions"></a>Benefícios das regiões emparelhadas
Como referido na figura 2.  

![Isolamento](./media/best-practices-availability-paired-regions/5Orange.png)
**isolamento físico** -se possível, Azure prefere pelo menos 300 quilómetros de separação entre centros de dados num par regional, embora não seja possível ou prático em todas as localizações geográficas. Separação de Data Center físico reduz a probabilidade de desastres naturais, conflitos civis, falhas de energia ou falhas de rede física que afetem as duas regiões ao mesmo tempo. Isolamento está sujeita as restrições na geografia (tamanho de geografia, disponibilidade da infraestrutura de rede/power, regulamentos, etc.).  

![A replicação](./media/best-practices-availability-paired-regions/6Orange.png)
**replicação fornecidos pelo plataforma** -alguns serviços, como o armazenamento Georredundante fornecem replicação automática para a região emparelhada.

![Recuperação](./media/best-practices-availability-paired-regions/7Orange.png)
**ordem de recuperação de região** – no caso de uma falha abrangente, a recuperação de uma região tem prioridade em cada par. Aplicações que são implementadas em regiões emparelhadas garantia para uma das regiões recuperados com a prioridade. Se uma aplicação é implementada em regiões que não estão emparelhados, pode estar atrasada recuperação – no pior caso que as regiões escolhidas podem ser os dois últimos a ser recuperado.

![As atualizações](./media/best-practices-availability-paired-regions/8Orange.png)
**atualizações sequenciais** – atualizações do sistema Azure planeadas são implementadas em regiões emparelhadas sequencialmente (e não ao mesmo tempo) para minimizar o período de indisponibilidade, o efeito de erros e falhas de lógicas na rara eventualidade de uma má Atualize.

![Dados](./media/best-practices-availability-paired-regions/9Orange.png)
**residência dos dados** – uma região reside dentro da mesma geografia do respetivo par (à exceção do Sul do Brasil), para atender aos requisitos de residência de dados para efeitos de jurisdição de imposição de imposto e lei.
