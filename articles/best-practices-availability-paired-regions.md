---
title: Garantir a continuidade do negócio & recuperação de desastres utilizando regiões emparelhadas Azure
description: Garantir a resiliência da aplicação utilizando o emparelhamento regional Azure
author: barbkess
manager: barbkess
ms.service: multiple
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: barbkess
ms.custom: references_regions
ms.openlocfilehash: b720d9dd824018d885ccc9860ee9fd8a90a46051
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84194322"
---
# <a name="business-continuity-and-disaster-recovery-bcdr-azure-paired-regions"></a>Continuidade de negócio e recuperação após desastre (BCDR): Regiões Emparelhadas do Azure

## <a name="what-are-paired-regions"></a>O que são regiões emparelhadas?

Uma região de Azure é constituída por um conjunto de centros de dados implantados dentro de um perímetro definido pela latência e ligados através de uma rede dedicada de baixa latência.  Isto garante que os serviços Azure dentro de uma região de Azure oferecem o melhor desempenho e segurança possível.  

Uma geografia azul define uma área do mundo contendo pelo menos uma região de Azure. As geografias definem um mercado discreto, tipicamente contendo duas ou mais regiões, que preservam os limites de residência e conformidade de dados.  Saiba mais informações sobre a infraestrutura global da Azure [aqui](https://azure.microsoft.com/global-infrastructure/regions/)

Um par regional é composto por duas regiões dentro da mesma geografia. O Azure serializa as atualizações da plataforma (manutenção planeada) em pares regionais, garantindo que apenas uma região em cada par atualiza de cada vez. Se uma paralisação afetar várias regiões, pelo menos uma região em cada par será priorizada para a recuperação.

![AzureGeography](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

Alguns serviços da Azure aproveitam ainda mais as regiões emparelhadas para garantir a continuidade das empresas e proteger contra a perda de dados.  O Azure oferece várias [soluções](./storage/common/storage-redundancy.md#redundancy-in-a-secondary-region) de armazenamento que aproveitam as regiões emparelhadas para garantir a disponibilidade de dados. Por exemplo, [o Azure Geo-redundante Storage](./storage/common/storage-redundancy.md#geo-redundant-storage) (GRS) replica automaticamente os dados para uma região secundária, garantindo que os dados são duráveis mesmo no caso de a região primária não ser recuperável. 

Note que nem todos os serviços Azure replicam automaticamente os dados, nem todos os serviços Azure automaticamente recuam de uma região falhada para o seu par.  Nestes casos, a recuperação e a replicação devem ser configuradas pelo cliente.

## <a name="can-i-select-my-regional-pairs"></a>Posso escolher os meus pares regionais?

N.º Alguns serviços da Azure dependem de pares regionais, como o [armazenamento redundante](./storage/common/storage-redundancy.md)de Azure. Estes serviços não permitem criar novos pares regionais.  Da mesma forma, como a Azure controla a priorização planeada de manutenção e recuperação para pares regionais, não pode definir os seus próprios pares regionais para tirar partido destes serviços. No entanto, você pode criar a sua própria solução de recuperação de desastres construindo serviços em qualquer número de regiões e aproveitando os serviços Azure para emparelhá-los. 

Por exemplo, pode utilizar serviços Azure como [a AzCopy](./storage/common/storage-use-azcopy-v10.md) para agendar cópias de dados para uma conta de Armazenamento numa região diferente.  Utilizando [o Azure DNS e o Azure Traffic Manager,](./networking/disaster-recovery-dns-traffic-manager.md)os clientes podem projetar uma arquitetura resiliente para as suas aplicações que sobreviverão à perda da região primária.

## <a name="am-i-limited-to-using-services-within-my-regional-pairs"></a>Limito-me a usar serviços dentro dos meus pares regionais?

N.º Enquanto um determinado serviço Azure pode contar com um par regional, você pode hospedar seus outros serviços em qualquer região que satisfaça as suas necessidades de negócio.  Uma solução de armazenamento Azure GRS pode emparelhar dados no Canadá Central com um par no Canadá East enquanto utiliza recursos compute localizados no Leste dos EUA.  

## <a name="must-i-use-azure-regional-pairs"></a>Devo usar pares regionais Azure?

N.º Os clientes podem aproveitar os serviços da Azure para arquiteto um serviço resiliente sem depender dos pares regionais da Azure.  No entanto, recomendamos que configuure a recuperação de desastres de continuidade de negócios (BCDR) em pares regionais para beneficiar do [isolamento](./security/fundamentals/isolation-choices.md) e melhorar [a disponibilidade.](./availability-zones/az-overview.md) Nas aplicações que suportam várias regiões ativas, recomendamos a utilização de ambas as regiões num par de regiões sempre que possível. Isto garante uma ótima disponibilidade para aplicações e tempo de recuperação minimizado em caso de desastre. Sempre que possível, desenhe a sua aplicação para [máxima resiliência](https://docs.microsoft.com/azure/architecture/framework/resiliency/overview) e facilidade de recuperação de [desastres.](https://docs.microsoft.com/azure/architecture/framework/resiliency/backup-and-recovery)

## <a name="azure-regional-pairs"></a>Pares Regionais Azure

| Geografia | Par Regional A | Par Regional B  |
|:--- |:--- |:--- |
| Asia-Pacific |Ásia Oriental (Hong Kong) | Sudeste Asiático (Singapura) |
| Austrália |Leste da Austrália |Austrália Sudeste |
| Austrália |Austrália Central |Austrália Central 2 |
| Brasil |Sul do Brasil |E.U.A. Centro-Sul |
| Canadá |Canadá Central |Leste do Canadá |
| China |Norte da China |Leste da China|
| China |China Norte 2 |China Leste 2|
| Europa |Europa do Norte (Irlanda) |Europa Ocidental (Holanda) |
| França |França Central|Sul de França|
| Alemanha |Alemanha Central |Nordeste da Alemanha |
| Índia |Índia Central |Sul da Índia |
| Índia |Oeste da Índia |Sul da Índia |
| Japão |Leste do Japão |Oeste do Japão |
| Coreia |Coreia do Sul Central |Sul da Coreia do Sul |
| América do Norte |E.U.A. Leste |E.U.A. Oeste |
| América do Norte |E.U.A. Leste 2 |E.U.A. Central |
| América do Norte |E.U.A. Centro-Norte |E.U.A. Centro-Sul |
| América do Norte |E.U.A. Oeste 2 |E.U.A. Centro-Oeste |
| Noruega | Leste da Noruega | Oeste da Noruega |
| África do Sul | África do Sul Norte |África do Sul |
| Suíça | Suíça Norte |Suíça Oeste |
| REINO UNIDO |Oeste do Reino Unido |Sul do Reino Unido |
| Emirados Árabes Unidos | Uae Norte | Centro dos Emirados Árabes Unidos
| Departamento de Defesa dos EUA |US DoD - Leste |US DoD Centro |
| Governo dos Estados Unidos |US Gov - Arizona |US Gov - Texas |
| Governo dos Estados Unidos |US Gov - Iowa |US Gov - Virginia |
| Governo dos Estados Unidos |US Gov - Virginia |US Gov - Texas |

> [!Important]
> - A Índia Ocidental é emparelhada numa só direção. A região secundária da Índia Ocidental é a Índia meridional, mas a região secundária do Sul da Índia é a Índia Central.
> - O Brasil Sul é único porque é emparelhado com uma região fora da sua geografia. A região secundária do Sul do Brasil é a Centro-Sul dos EUA. A região secundária do Centro-Sul dos EUA não é o Sul do Brasil.


## <a name="an-example-of-paired-regions"></a>Um exemplo de regiões emparelhadas
A imagem abaixo ilustra uma aplicação hipotética que usa o par regional para a recuperação de desastres. Os números verdes destacam as atividades transnacional de três serviços Azure (Azure compute, armazenamento e base de dados) e como estão configurados para se replicarem em todas as regiões. Os benefícios únicos da implantação em regiões emparelhadas são destacados pelos números laranja.

![Visão geral dos benefícios da região emparelhada](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

Figura 2 - Par regional hipotético azure

## <a name="cross-region-activities"></a>Atividades inter-regionais
Como referido na figura 2.

1. **Azure Compute (IaaS)** – É necessário disponibilizar recursos adicionais para garantir que os recursos estão disponíveis noutra região durante uma catástrofe. Para mais informações, consulte [a orientação técnica de resiliência do Azure.](https://github.com/uglide/azure-content/blob/master/articles/resiliency/resiliency-technical-guidance.md) 

2. **Azure Storage** - Se estiver a usar discos geridos, aprenda sobre [backups de região cruzada com](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region#virtual-machines) Azure Backup, e [replicando VMs](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication) de uma região para outra com Azure Site Recovery. Se estiver a utilizar contas de armazenamento, então o armazenamento geo-redundante (GRS) é configurado por padrão quando uma conta de Armazenamento Azure é criada. Com GRS, os seus dados são automaticamente replicados três vezes na região primária, e três vezes na região emparelhada. Para obter mais informações, consulte [as Opções de Redundância de Armazenamento Azure](storage/common/storage-redundancy.md).

3. **Base de Dados Azure SQL** – Com Geo-Replicação da Base de Dados Azure SQL, pode configurar a replicação assíncrona de transações para qualquer região do mundo; no entanto, recomendamos que você implemente estes recursos numa região emparelhada para a maioria dos cenários de recuperação de desastres. Para obter mais informações, consulte [a Geo-Replicação na Base de Dados Azure SQL](sql-database/sql-database-geo-replication-overview.md).

4. **Azure Resource Manager** – o Resource Manager proporciona inerentemente o isolamento lógico dos componentes entre regiões. Isto significa que as falhas lógicas numa região são menos suscetíveis de ter impacto noutra.

## <a name="benefits-of-paired-regions"></a>Benefícios das regiões emparelhadas

5. **Isolamento físico** – Quando possível, o Azure prefere pelo menos 300 milhas de separação entre datacenters em pares regionais, embora isso não seja prático ou possível em todas as geografias. A separação física do centro de dados reduz a probabilidade de desastres naturais, agitação civil, falhas de energia ou falhas físicas na rede que afetam ambas as regiões ao mesmo tempo. O isolamento está sujeito aos constrangimentos dentro da geografia (dimensão geográfica, disponibilidade de infraestruturas de energia/rede, regulamentos, etc.).  

6. **Replicação fornecida pela plataforma** - Alguns serviços como Geo-Redundant Armazenamento fornecem replicação automática para a região emparelhada.

7. **Ordem de recuperação** da região – Em caso de paralisação alargada, a recuperação de uma região é prioritária em cada par. As aplicações implementadas entre regiões emparelhadas têm a garantia de ter uma das regiões recuperadas com prioridade. Se um pedido for implementado em regiões que não estão emparelhadas, a recuperação pode ser adiada – no pior dos casos, as regiões escolhidas podem ser as duas últimas a ser recuperadas.

8. **Atualizações sequenciais** – As atualizações planeadas do sistema Azure são lançadas para regiões emparelhadas sequencialmente (não ao mesmo tempo) para minimizar o tempo de inatividade, o efeito de erros e falhas lógicas no caso raro de uma má atualização.

9. **Residência de dados** – Uma região reside dentro da mesma geografia que o seu par (com exceção do Brasil Sul) para satisfazer os requisitos de residência de dados para efeitos de jurisdição fiscal e de aplicação da lei.
