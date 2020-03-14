---
title: Garantir a continuidade do negócio e a recuperação de desastres utilizando as Regiões Emparelhadas de Azure
description: Garantir a resiliência da aplicação usando o emparelhamento regional azure
author: jpconnock
manager: angrobe
ms.service: multiple
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: jeconnoc
ms.openlocfilehash: 778943dad9a04632797d5d9165b6f1f9a3eb9850
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79248258"
---
# <a name="business-continuity-and-disaster-recovery-bcdr-azure-paired-regions"></a>Continuidade do negócio e recuperação de desastres (BCDR): Regiões Emparelhadas de Azure

## <a name="what-are-paired-regions"></a>O que são regiões emparelhadas?

Uma região do Azure é constituída por um conjunto de centros de dados implantados dentro de um perímetro definido pela latência e ligados através de uma rede dedicada de baixa latência.  Isto garante que os serviços Azure dentro de uma região do Azure oferecem o melhor desempenho e segurança possível.  

Uma geografia Azure define uma área do mundo contendo pelo menos uma região de Azure. As geografias definem um mercado discreto, tipicamente contendo duas ou mais regiões, que preservam os limites da residência de dados e da conformidade.  Encontre mais informações sobre a infraestrutura global do Azure [aqui](https://azure.microsoft.com/global-infrastructure/regions/)

Um par regional é composto por duas regiões dentro da mesma geografia. O Azure serializa atualizações de plataformas (manutenção planeada) em pares regionais, garantindo que apenas uma região em cada par atualiza de cada vez. Se uma paragem afetar várias regiões, pelo menos uma região em cada par será priorizada para recuperação.

![AzureGeography](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

Alguns serviços azure aproveitam ainda mais as regiões emparelhadas para garantir a continuidade do negócio e proteger contra perdas de dados.  O Azure fornece [várias soluções](./storage/common/storage-redundancy.md#redundancy-in-a-secondary-region) de armazenamento que aproveitam as regiões emparelhadas para garantir a disponibilidade de dados. Por exemplo, o [Armazenamento Geo-Redundante Azure](./storage/common/storage-redundancy.md#geo-redundant-storage) (GRS) replica automaticamente os dados para uma região secundária, garantindo que os dados são duráveis mesmo no caso de a região primária não ser recuperável. 

Note que nem todos os serviços Azure replicam automaticamente dados, nem todos os serviços Azure recuam automaticamente de uma região falhada para o seu par.  Nesses casos, a recuperação e a replicação devem ser configuradas pelo cliente.

## <a name="can-i-select-my-regional-pairs"></a>Posso escolher os meus pares regionais?

Não. Alguns serviços azure dependem de pares regionais, como o [armazenamento redundante](./storage/common/storage-redundancy.md)de Azure. Estes serviços não permitem criar novos pares regionais.  Da mesma forma, como o Azure controla a priorização planeada da manutenção e recuperação para pares regionais, não é possível definir os seus próprios pares regionais para tirar partido destes serviços. No entanto, você pode criar sua própria solução de recuperação de desastres construindo serviços em várias regiões e alavancando os serviços Azure para emparelhá-los. 

Por exemplo, pode utilizar serviços Azure, como [o AzCopy,](./storage/common/storage-use-azcopy-v10.md) para agendar cópias de dados para uma conta de Armazenamento numa região diferente.  Utilizando o [Azure DNS e o Azure Traffic Manager,](./networking/disaster-recovery-dns-traffic-manager.md)os clientes podem projetar uma arquitetura resiliente para as suas aplicações que sobreviverão à perda da região primária.

## <a name="am-i-limited-to-using-services-within-my-regional-pairs"></a>Estou limitado a usar serviços dentro dos meus pares regionais?

Não. Enquanto um determinado serviço Azure pode contar com um par regional, você pode hospedar seus outros serviços em qualquer região que satisfaça as suas necessidades de negócio.  Uma solução de armazenamento Azure GRS pode emparelhar dados no Canadá Central com um par no Canadá Leste enquanto utiliza recursos computacionais localizados no Leste dos EUA.  

## <a name="must-i-use-azure-regional-pairs"></a>Devo usar pares regionais Azure?

Não. Os clientes podem aproveitar os serviços azure para arquiteto um serviço resiliente sem depender dos pares regionais de Azure.  No entanto, recomendamos que configure a recuperação de desastres de continuidade do negócio (BCDR) em pares regionais para beneficiar do [isolamento](./security/fundamentals/isolation-choices.md) e melhorar a [disponibilidade.](./availability-zones/az-overview.md) Para aplicações que suportem várias regiões ativas, recomendamos a utilização de ambas as regiões num par de regiões, sempre que possível. Isto garante uma disponibilidade ótima para aplicações e tempo de recuperação minimizado em caso de desastre. Sempre que possível, desenhe a sua aplicação para a [máxima resiliência](https://docs.microsoft.com/azure/architecture/framework/resiliency/overview) e facilidade de recuperação de [desastres.](https://docs.microsoft.com/azure/architecture/framework/resiliency/backup-and-recovery)

## <a name="azure-regional-pairs"></a>Pares Regionais de Azure

| Geografia | Par Regional A | Par Regional B  |
|:--- |:--- |:--- |
| Ásia-Pacífico |Ásia Oriental (Hong Kong) | Sudeste Asiático (Singapura) |
| Austrália |Leste da Austrália |Sudeste da Austrália |
| Austrália |Austrália Central |Austrália Central 2 |
| Brasil |Sul do Brasil |EUA Centro-Sul |
| Canadá |Canadá Central |Leste do Canadá |
| China |China Norte |China Leste|
| China |China Norte 2 |China Leste 2|
| Europa |Norte da Europa (Irlanda) |Europa Ocidental (Holanda) |
| França |França Central|Sul de França|
| Alemanha |Alemanha Central |Nordeste da Alemanha |
| Índia |Índia Central |Sul da Índia |
| Índia |Oeste da Índia |Sul da Índia |
| Japão |Leste do Japão |Oeste do Japão |
| Coreia |Coreia do Sul Central |Coreia do Sul |
| América do Norte |EUA Leste |EUA Oeste |
| América do Norte |EUA Leste 2 |EUA Central |
| América do Norte |EUA Centro-Norte |EUA Centro-Sul |
| América do Norte |EUA Oeste 2 |EUA Centro-Oeste |
| Noruega | Noruega Leste | Noroeste da Noruega |
| África do Sul | África do Sul Norte |África do Sul Ocidental |
| Suíça | Suíça Norte |Suíça Oeste |
| RU |Reino Unido Oeste |Reino Unido Sul |
| Emirados Árabes Unidos | Emirados Unidos norte | Central dos Emirados Emirados Unidos
| Departamento de Defesa dos EUA |US DoD Leste |US DoD Centro |
| Governo dos EUA |Gov (US) - Arizona |Gov (US) - Texas |
| Governo dos EUA |US Gov - Iowa |Gov (US) - Virginia |
| Governo dos EUA |Gov (US) - Virginia |Gov (US) - Texas |

> [!Important]
> - A Índia Ocidental é emparelhada numa só direção. A região secundária da Índia Ocidental é o sul da Índia, mas a região secundária do sul da Índia é a Índia Central.
> - O Brasil Sul é único porque é emparelhado com uma região fora da sua geografia. A região secundária do Brasil Sul é a Central Sul dos EUA. A região secundária do Centro-Sul dos EUA não é o Brasil Sul.


## <a name="an-example-of-paired-regions"></a>Um exemplo de regiões emparelhadas
A imagem abaixo ilustra uma aplicação hipotética que usa o par regional para a recuperação de desastres. Os números verdes destacam as atividades transversais de três serviços Azure (computação Azure, armazenamento e base de dados) e como estão configurados para se replicarem em todas as regiões. Os benefícios únicos de implantar em regiões emparelhadas são destacados pelos números laranja.

![Visão geral dos benefícios da região emparelhada](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

Figura 2 – Hipotético par regional azure

## <a name="cross-region-activities"></a>Atividades transversais
Como referido na figura 2.

1. **Azure Compute (IaaS)** – Deve disponibilizar recursos adicionais de computação com antecedência para garantir que os recursos estão disponíveis noutra região durante uma catástrofe. Para mais informações, consulte a orientação técnica de [resiliência do Azure.](https://github.com/uglide/azure-content/blob/master/articles/resiliency/resiliency-technical-guidance.md) 

2. **Armazenamento Azure** - Se estiver a usar discos geridos, aprenda sobre [backups de regiões transversais](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region#virtual-machines) com Backup Azure e [replicando VMs](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication) de uma região para outra com a Recuperação do Site Azure. Se estiver a usar contas de armazenamento, o armazenamento geo-redundante (GRS) é configurado por padrão quando é criada uma conta de Armazenamento Azure. Com GRS, os seus dados são automaticamente replicados três vezes dentro da região primária, e três vezes na região emparelhada. Para mais informações, consulte opções de [redundância](storage/common/storage-redundancy.md)de armazenamento azure .

3. Base de **Dados Azure SQL** – Com geo-replicação de base de dados Azure SQL, pode configurar a replicação assíncrona de transações para qualquer região do mundo; no entanto, recomendamos que implemente estes recursos numa região emparelhada para a maioria dos cenários de recuperação de desastres. Para mais informações, consulte [Geo-Replication na Base de Dados Azure SQL](sql-database/sql-database-geo-replication-overview.md).

4. **Azure Resource Manager** - O Gestor de Recursos fornece inerentemente isolamento lógico de componentes em todas as regiões. Isto significa que as falhas lógicas numa região são menos propensas a ter impacto noutra.

## <a name="benefits-of-paired-regions"></a>Benefícios das regiões emparelhadas

5. **Isolamento físico** – Quando possível, Azure prefere pelo menos 300 milhas de separação entre datacenters em pares regionais, embora isso não seja prático ou possível em todas as geografias. A separação física de datacenters reduz a probabilidade de desastres naturais, agitação civil, falhas de energia ou interrupções físicas da rede que afetam ambas as regiões ao mesmo tempo. O isolamento está sujeito aos constrangimentos dentro da geografia (tamanho da geografia, disponibilidade de infraestrutura de energia/rede, regulamentos, etc.).  

6. **Replicação fornecida pela plataforma** - Alguns serviços como o Armazenamento Geo-Redundante fornecem replicação automática para a região emparelhada.

7. Ordem de recuperação da **região** – Em caso de paralisação alargada, a recuperação de uma região é priorizada em cada par. As aplicações que são implantadas em regiões emparelhadas são garantidas para ter uma das regiões recuperadas com prioridade. Se uma aplicação for implementada em regiões que não sejam emparelhadas, a recuperação poderá ser adiada – no pior dos casos, as regiões escolhidas podem ser as duas últimas a serem recuperadas.

8. **Atualizações sequenciais** – As atualizações planeadas do sistema Azure são lançadas para regiões emparelhadas sequencialmente (não ao mesmo tempo) para minimizar o tempo de inatividade, o efeito dos bugs e falhas lógicas no caso raro de uma má atualização.

9. **Residência** de dados – Uma região reside na mesma geografia que o seu par (com exceção do Brasil Sul) para satisfazer os requisitos de residência de dados para efeitos de jurisdição fiscal e de aplicação da lei.
