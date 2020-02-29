---
title: Continuidade do negócio e recuperação de desastres - Regiões Emparelhadas de Azure
description: Saiba mais sobre o emparelhamento regional do Azure, para garantir que as aplicações são resilientes durante falhas no centro de dados.
author: rayne-wiselman
manager: carmon
ms.service: multiple
ms.topic: article
ms.date: 07/01/2019
ms.author: raynew
ms.openlocfilehash: c1e14db9dafc8b03acbeb1c6b97e5ac0e27cb0fd
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "78163053"
---
# <a name="business-continuity-and-disaster-recovery-bcdr-azure-paired-regions"></a>Continuidade do negócio e recuperação de desastres (BCDR): Regiões Emparelhadas de Azure

## <a name="what-are-paired-regions"></a>O que são regiões emparelhadas?

Azure opera em múltiplas geografias em todo o mundo. Uma geografia Azure é uma área definida do mundo que contém pelo menos uma região azure. Uma região de Azure é uma área dentro de uma geografia, contendo um ou mais datacenters.

Cada região de Azure é emparelhada com outra região dentro da mesma geografia, fazendo juntos um par regional. A exceção é o Brasil Sul, que está emparelhado com uma região fora da sua geografia. Em toda a região, o Azure serializa as atualizações da plataforma (manutenção planeada), de modo que apenas uma região emparelhada é atualizada de cada vez. Em caso de paralisação que afete várias regiões, pelo menos uma região de cada par será priorizada para recuperação.

![AzureGeography](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

> [!NOTE]
> Os pares regionais do Azure atribuídos não podem ser modificados.

Figura 1 - Pares regionais de Azure

| Geografia | Regiões emparelhadas |  |
|:--- |:--- |:--- |
| Ásia |Ásia Leste |Ásia Sudeste |
| Austrália |Leste da Austrália |Austrália Sudeste |
| Austrália |Austrália Central |Austrália Central 2 |
| Brasil |Sul do Brasil |E.U.A. Centro-Sul |
| Canadá |Canadá Central |Leste do Canadá |
| China |China Norte |China Leste|
| China |China Norte 2 |China Leste 2|
| Europa |Norte da Europa (Irlanda) |Europa Ocidental (Holanda) |
| França |França Central|Sul de França|
| Alemanha |Alemanha Central |Nordeste da Alemanha |
| Índia |Índia Central |Sul da Índia |
| Índia |Oeste da Índia |Sul da Índia |
| Japão |Leste do Japão |Oeste do Japão |
| Coreia |Coreia do Sul Central |Sul da Coreia do Sul |
| América do Norte |E.U.A. Leste |E.U.A. Oeste |
| América do Norte |E.U.A. Leste 2 |E.U.A. Central |
| América do Norte |E.U.A. Centro-Norte |E.U.A. Centro-Sul |
| América do Norte |E.U.A.Oeste 2 |E.U.A. Centro-Oeste 
| África do Sul | África do Sul Norte | África do Sul Ocidental
| RU |Oeste do Reino Unido |Sul do Reino Unido |
| Emirados Árabes Unidos | Emirados Unidos norte | Central dos Emirados Emirados Unidos
| Departamento de Defesa dos EUA |US DoD - Leste |US DoD Centro |
| Governo dos EUA |US Gov - Arizona |US Gov - Texas |
| Governo dos EUA |US Gov - Iowa |US Gov - Virginia |
| Governo dos EUA |US Gov - Virginia |US Gov - Texas |

Quadro 1 - Mapeamento de pares regionais de Azure

- A Índia Ocidental é emparelhada numa só direção. A região secundária da Índia Ocidental é o sul da Índia, mas a região secundária do sul da Índia é a Índia Central.
- O Brasil Sul é único porque é emparelhado com uma região fora da sua própria geografia. A região secundária do Brasil Sul é a Central Sul dos EUA. A região secundária do Centro-Sul dos EUA não é o Brasil Sul.
- A região secundária do Gov Iowa é a Norte-Americana Gov Virginia.
- A região secundária do Gov Virginia é o Norte-Americano Gov Texas.
- A região secundária do U.Gov Texas é o Norte-Americano Gov Arizona.


Recomendamos que configure a recuperação de desastres de continuidade do negócio (BCDR) em pares regionais para beneficiar das políticas de isolamento e disponibilidade de Azure. Para aplicações que suportem várias regiões ativas, recomendamos a utilização de ambas as regiões num par de regiões, sempre que possível. Isto garantirá uma disponibilidade ótima para aplicações e tempo de recuperação minimizado em caso de desastre. 

## <a name="an-example-of-paired-regions"></a>Um exemplo de regiões emparelhadas
A figura 2 abaixo mostra uma aplicação hipotética que usa o par regional para a recuperação de desastres. Os números verdes destacam as atividades transversais de três serviços Azure (computação Azure, armazenamento e base de dados) e como são configurados para se replicarem em todas as regiões. Os benefícios únicos de implantar em regiões emparelhadas são destacados pelos números laranja.

![Visão geral dos benefícios da região emparelhada](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

Figura 2 – Hipotético par regional azure

## <a name="cross-region-activities"></a>Atividades transversais
Como referido na figura 2.

![IaaS](./media/best-practices-availability-paired-regions/1Green.png) **Azure Compute (IaaS)** – É necessário disponibilizar recursos adicionais de computação com antecedência para garantir que os recursos estão disponíveis noutra região durante uma catástrofe. Para mais informações, consulte a orientação técnica de [resiliência do Azure.](https://github.com/uglide/azure-content/blob/master/articles/resiliency/resiliency-technical-guidance.md)

![Armazenamento](./media/best-practices-availability-paired-regions/2Green.png) **Armazenamento Azure** - Se estiver a usar discos geridos, aprenda sobre [backups de regiões transversais](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region#virtual-machines) com Backup Azure e [replicando VMs](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication) de uma região para outra com recuperação do site Azure. Se estiver a usar contas de armazenamento, o armazenamento geo-redundante (GRS) é configurado por padrão quando é criada uma conta de Armazenamento Azure. Com GRS, os seus dados são automaticamente replicados três vezes dentro da região primária, e três vezes na região emparelhada. Para mais informações, consulte opções de [redundância](storage/common/storage-redundancy.md)de armazenamento azure .

![Azure SQL](./media/best-practices-availability-paired-regions/3Green.png) Base de **Dados Azure SQL** – Com georéplica de base de dados Azure SQL, pode configurar a replicação assíncrona de transações para qualquer região do mundo; no entanto, recomendamos que implemente estes recursos numa região emparelhada para a maioria dos cenários de recuperação de desastres. Para mais informações, consulte [Geo-Replication na Base de Dados Azure SQL](sql-database/sql-database-geo-replication-overview.md).

![Gestor de Recursos](./media/best-practices-availability-paired-regions/4Green.png) **Gestor de Recursos Azure** - Gestor de Recursos inerentemente fornece isolamento lógico de componentes em todas as regiões. Isto significa que as falhas lógicas numa região são menos propensas a ter impacto noutra.

## <a name="benefits-of-paired-regions"></a>Benefícios das regiões emparelhadas
Como referido na figura 2.  

![Isolamento](./media/best-practices-availability-paired-regions/5Orange.png)
**Isolamento Físico** – Quando possível, Azure prefere pelo menos 300 milhas de separação entre datacenters em pares regionais, embora isso não seja prático ou possível em todas as geografias. A separação física de datacenters reduz a probabilidade de desastres naturais, agitação civil, falhas de energia ou interrupções físicas da rede que afetam ambas as regiões ao mesmo tempo. O isolamento está sujeito aos constrangimentos dentro da geografia (tamanho da geografia, disponibilidade de infraestrutura de energia/rede, regulamentos, etc.).  

![replicação](./media/best-practices-availability-paired-regions/6Orange.png)
**replicação fornecida pela Plataforma** - Alguns serviços como o Armazenamento Geo-Redundante fornecem replicação automática à região emparelhada.

![Recuperação](./media/best-practices-availability-paired-regions/7Orange.png)
Ordem de **recuperação da Região** – Em caso de paragem alargada, a recuperação de uma região é priorizada em cada par. As aplicações que são implantadas em regiões emparelhadas são garantidas para ter uma das regiões recuperadas com prioridade. Se uma aplicação for implementada em regiões que não sejam emparelhadas, a recuperação poderá ser adiada – no pior dos casos, as regiões escolhidas podem ser as duas últimas a serem recuperadas.

![Atualizações](./media/best-practices-availability-paired-regions/8Orange.png)
**atualizações sequenciais** – As atualizações planeadas do sistema Azure são lançadas para regiões emparelhadas sequencialmente (não ao mesmo tempo) para minimizar o tempo de inatividade, o efeito dos bugs e falhas lógicas no raro caso de uma má atualização.

![Data](./media/best-practices-availability-paired-regions/9Orange.png)
**Data residência** – Uma região reside na mesma geografia que o seu par (com exceção do Brasil Sul) de forma a satisfazer os requisitos de residência de dados para efeitos de jurisdição fiscal e de aplicação da lei.
