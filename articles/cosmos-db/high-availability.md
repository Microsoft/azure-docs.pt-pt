---
title: Alta disponibilidade em Azure Cosmos DB
description: Este artigo descreve como a Azure Cosmos DB fornece alta disponibilidade
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2020
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 58507703ca3440e73dbc41757e0bc70f56e886c3
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93360161"
---
# <a name="how-does-azure-cosmos-db-provide-high-availability"></a>Como é que a Azure Cosmos DB fornece alta disponibilidade
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

A Azure Cosmos DB proporciona alta disponibilidade de duas formas primárias. Primeiro, a Azure Cosmos DB replica dados em regiões configuradas numa conta cosmos. Em segundo lugar, a Azure Cosmos DB mantém 4 réplicas de dados dentro de uma região.

AZure Cosmos DB é um serviço de base de dados distribuído globalmente e é um serviço fundamental em Azure. Por padrão, está disponível em [todas as regiões onde o Azure está disponível.](https://azure.microsoft.com/global-infrastructure/services/?products=cosmos-db&regions=all) Pode associar qualquer número de regiões do Azure à sua conta Azure Cosmos e os seus dados são replicados de forma automática e transparente. Pode adicionar ou remover uma região na sua conta Azure Cosmos a qualquer momento. Cosmos DB está disponível em todos os cinco ambientes distintos da nuvem Azure disponíveis para os clientes:

* **Nuvem pública azul,** que está disponível globalmente.

* **O Azure China 21Vianet** está disponível através de uma parceria única entre a Microsoft e a 21Vianet, um dos maiores fornecedores de internet do país na China.

* **A Azure Germany** presta serviços ao abrigo de um modelo de fiduciário de dados, que garante que os dados dos clientes permanecem na Alemanha sob o controlo da T-Systems International GmbH, uma subsidiária da Deutsche Telekom, atuando como administradora de dados alemã.

* **O Governo de Azure** está disponível em quatro regiões dos Estados Unidos para agências governamentais dos EUA e seus parceiros.

* **O Governo de Azure para o Departamento de Defesa (DoD)** está disponível em duas regiões dos Estados Unidos para o Departamento de Defesa dos EUA.

Dentro de uma região, a Azure Cosmos DB mantém quatro cópias dos seus dados como réplicas dentro de divisórias físicas, como mostra a seguinte imagem:

:::image type="content" source="./media/high-availability/cosmosdb-data-redundancy.png" alt-text="Partição física" border="false":::

* Os dados dentro dos contentores da Azure Cosmos são [divididos horizontalmente.](partitioning-overview.md)

* Um conjunto de divisórias é uma coleção de múltiplos conjuntos de réplicas. Em cada região, cada divisória é protegida por uma réplica-set com todas as escritas replicadas e duramente comprometidas pela maioria das réplicas. As réplicas são distribuídas por domínios de falhas de 10 a 20.

* Cada divisória em todas as regiões é replicada. Cada região contém todas as divisórias de dados de um recipiente Azure Cosmos e pode aceitar escritas e servir leituras.  

Se a sua conta Azure Cosmos estiver distribuída pelas regiões *N* Azure, haverá pelo menos *N* x 4 cópias de todos os seus dados. Ter uma conta Azure Cosmos em mais de 2 regiões melhora a disponibilidade da sua aplicação e proporciona baixa latência em todas as regiões associadas.

## <a name="slas-for-availability"></a>SLAs para disponibilidade

Como uma base de dados distribuída globalmente, a Azure Cosmos DB fornece SLAs abrangentes que englobam a produção, latência no percentil 99, consistência e alta disponibilidade. O quadro abaixo mostra as garantias de elevada disponibilidade fornecidas pela Azure Cosmos DB para contas individuais e multi-regiões. Para uma elevada disponibilidade, configuure sempre as suas contas Azure Cosmos para ter várias regiões de escrita.

|Tipo de operação  | Região única |Multi-região (escreve uma única região)|Multi-região (escreve várias regiões) |
|---------|---------|---------|-------|
|Escritas    | 99,99    |99,99   |99,999|
|Leituras     | 99,99    |99,999  |99,999|

> [!NOTE]
> Na prática, a disponibilidade de escrita real para os modelos de estagnação, sessão, prefixo consistente e eventual consistência é significativamente maior do que as SLAs publicadas. A disponibilidade de leitura real para todos os níveis de consistência é significativamente maior do que as AS publicadas.

## <a name="high-availability-with-azure-cosmos-db-in-the-event-of-regional-outages"></a>Alta disponibilidade com Azure Cosmos DB em caso de paralisações regionais

Para os raros casos de paragem regional, a Azure Cosmos DB garante que a sua base de dados está sempre altamente disponível. Os seguintes detalhes capturam o comportamento do Azure Cosmos DB durante uma paragem, dependendo da configuração da sua conta Azure Cosmos:

* Com a Azure Cosmos DB, antes de uma operação de escrita ser reconhecida ao cliente, os dados são duramente cometidos por um quórum de réplicas dentro da região que aceita as operações de escrita. Para mais detalhes, consulte [os níveis de consistência e produção](./consistency-levels.md#consistency-levels-and-throughput)

* As contas multi-regiões configuradas com regiões de escrita múltipla estarão altamente disponíveis tanto para as gravações como para as leituras. As falhas regionais são detetadas e tratadas no cliente DB da Azure Cosmos. Também são instantâneos e não requerem alterações na aplicação.

* As contas de uma região única podem perder disponibilidade na sequência de uma paragem regional. É sempre recomendado criar **pelo menos duas regiões** (de preferência, pelo menos duas regiões de escrita) com a sua conta Azure Cosmos para garantir uma elevada disponibilidade em todos os momentos.

### <a name="multi-region-accounts-with-a-single-write-region-write-region-outage"></a>Contas multi-regiões com uma região de escrita única (interrupção da região de escrita)

* Durante uma interrupção da região de escrita, a conta Azure Cosmos promoverá automaticamente uma região secundária para ser a nova região de escrita primária quando **permitir a falha automática** é configurada na conta Azure Cosmos. Quando ativado, o failover ocorrerá para outra região na ordem da prioridade da região que especificou.

* Quando a região anteriormente impactada está novamente on-line, quaisquer dados de escrita que não foram replicados quando a região falhou, é disponibilizado através dos [conflitos alimentados.](how-to-manage-conflicts.md#read-from-conflict-feed) As aplicações podem ler os conflitos feed, resolver os conflitos com base na lógica específica da aplicação, e escrever os dados atualizados de volta para o recipiente Azure Cosmos conforme apropriado.

* Uma vez que a região de escrita previamente impactada recupere, torna-se automaticamente disponível como uma região de leitura. Pode voltar para a região recuperada como região de escrita. Pode mudar as regiões utilizando o [portal PowerShell, Azure CLI ou Azure](how-to-manage-database-account.md#manual-failover). Não há perda de **dados ou disponibilidade** antes, durante ou depois de mudar a região de escrita e a sua aplicação continua altamente disponível.

> [!IMPORTANT]
> Recomenda-se vivamente que configuure as contas Azure Cosmos utilizadas para a produção de cargas de trabalho para permitir a **falha automática.** A falha manual requer conectividade entre a região de escrita secundária e primária para completar uma verificação de consistência para garantir que não há perda de dados durante a falha. Se a região primária não estiver disponível, esta verificação de consistência não pode ser completada e o failover manual não será bem sucedido, resultando na perda de disponibilidade por escrito durante a paragem regional.

### <a name="multi-region-accounts-with-a-single-write-region-read-region-outage"></a>Contas multi-regiões com uma região de escrita única (ler interrupção da região)

* Durante uma paragem na região de leitura, as contas da Azure Cosmos utilizando qualquer nível de consistência ou forte consistência com três ou mais regiões de leitura continuarão altamente disponíveis para leituras e escritas.

* As contas de Azure Cosmos usando uma forte consistência com duas ou menos regiões de leitura (que inclui a leitura & região de escrita) perderão a disponibilidade de leitura escrita durante uma interrupção da região de leitura.

* A região afetada é automaticamente desligada e será marcada offline. Os [Azure Cosmos DB SDKs](sql-api-sdk-dotnet.md) redirecionarão as chamadas de leitura para a próxima região disponível na lista de regiões preferidas.

* Se nenhuma das regiões na lista de regiões preferenciais estiver disponível, as chamadas revertem automaticamente para a região de escrita atual.

* Não são necessárias alterações no seu código de aplicação para lidar com a interrupção da região de leitura. Quando a região de leitura impactada estiver novamente on-line, sincronizar-se-á automaticamente com a atual região de escrita e estará novamente disponível para atender pedidos de leitura.

* As leituras subsequentes são redirecionadas para a região recuperada sem necessidade de alterar o código da aplicação. Durante o fracasso e o regresso de uma região anteriormente fracassada, leia-se que as garantias de consistência continuam a ser honradas pela Azure Cosmos DB.

* Mesmo num evento raro e infeliz quando a região do Azure é permanentemente irrecuperável, não há perda de dados se a sua conta Azure Cosmos multi-região estiver configurada com *forte* consistência. No caso de uma região de escrita permanentemente irrecuperável, uma conta Azure Cosmos multi-região configurada com consistência limitada, a janela de perda de dados potencial é restrita à janela de estagnação *(K* ou *T)* onde K=100.000 atualizações e T=5 minutos. Para a sessão, prefixo consistente e eventuais níveis de consistência, a janela de perda de dados potencial é limitada a um máximo de 15 minutos. Para obter mais informações sobre os alvos de RTO e RPO para Azure Cosmos DB, consulte [os níveis de consistência e durabilidade dos dados](./consistency-levels.md#rto)

## <a name="availability-zone-support"></a>Suporte à Zona de Disponibilidade

Além da resiliência transversal da região, pode agora permitir a **redundância de zona** ao selecionar uma região para associar-se à sua base de dados Azure Cosmos.

Com o suporte da Zona de Disponibilidade, a Azure Cosmos DB garantirá que as réplicas são colocadas em várias zonas dentro de uma determinada região para fornecer alta disponibilidade e resiliência durante falhas zonais. Não há alterações na latência e outros SLAs nesta configuração. Em caso de falha de uma única zona, a redundância de zona proporciona uma durabilidade completa dos dados com RPO=0 e disponibilidade com RTO=0.

A redundância da zona é uma *capacidade suplementar* para a [replicação em várias regiões.](how-to-multi-master.md) Por si só, não se pode confiar na redundância da zona para alcançar a resiliência regional. Por exemplo, em caso de interrupções regionais ou de baixo acesso à latência em todas as regiões, é aconselhável ter múltiplas regiões de escrita, além de redundância de zona.

Ao configurar as gravações multi-regiões para a sua conta Azure Cosmos, pode optar pela redundância da zona sem custos adicionais. Caso contrário, consulte a nota abaixo sobre os preços para o apoio ao despedimento de zona. Você pode ativar a redundância de zona em uma região existente da sua conta Azure Cosmos removendo a região e adicionando-a de volta com a zona de redundância ativada. Para obter uma lista de regiões onde as zonas de disponibilidade são suportadas, consulte a documentação [das zonas disponibilidade.](../availability-zones/az-region.md)

A tabela a seguir resume a elevada capacidade de disponibilidade de várias configurações de conta:

|KPI  |Região única sem Zonas de Disponibilidade (Não-AZ)  |Região única com Zonas de Disponibilidade (AZ)  |Várias regiões escrevem com Zonas de Disponibilidade (AZ, 2 regiões) – Definição mais recomendada |
|---------|---------|---------|---------|
|Escrever disponibilidade SLA | 99,99% | 99,99% | 99,999% |
|Ler disponibilidade SLA  | 99,99% | 99,99% | 99,999% |
|Preço | Taxa única de faturação da região | Tarifa de faturação da zona de disponibilidade de região única | Taxa de faturação multi-região |
|Falhas de zona - perda de dados | Perda de dados | Nenhuma perda de dados | Nenhuma perda de dados |
|Falhas de zona – disponibilidade | Perda de disponibilidade | Sem perda de disponibilidade | Sem perda de disponibilidade |
|Ler latência | Região transversal | Região transversal | Baixo |
|Escrever latência | Região transversal | Região transversal | Baixo |
|Interrupção regional – perda de dados | Perda de dados |  Perda de dados | Perda de dados <br/><br/> Ao utilizar a consistência limitada com múltiplas regiões de escrita e mais de uma região, a perda de dados limita-se à estagnação limitada na sua conta <br /><br />Pode evitar a perda de dados durante uma paragem regional configurando uma forte consistência com várias regiões. Esta opção vem com compensações que afetam a disponibilidade e o desempenho. Só pode ser configurado em contas configuradas para as gravações de uma região única. |
|Paralisação regional – disponibilidade | Perda de disponibilidade | Perda de disponibilidade | Sem perda de disponibilidade |
|Débito | X R/s provisto | X R/s provisões * 1.25 | 2X RU/s produção a provisionada <br/><br/> Este modo de configuração requer o dobro da quantidade de produção quando comparada com uma única região com Zonas de Disponibilidade porque existem duas regiões. |

> [!NOTE]
> Para permitir o suporte da Zona de Disponibilidade para uma conta Azure Cosmos de várias regiões, a conta deve ter gravações multi-regiões ativadas.

Você pode ativar a redundância de zona ao adicionar uma região a contas novas ou existentes do Azure Cosmos. Para ativar a redundância de zona na sua conta Azure Cosmos, deverá definir `isZoneRedundant` a bandeira para uma `true` localização específica. Você pode definir esta bandeira dentro da propriedade local. Por exemplo, o seguinte corte PowerShell permite a redundância de zona para a região "Sudeste Asiático":

As Zonas de Disponibilidade podem ser ativadas através de:

* [Portal do Azure](how-to-manage-database-account.md#addremove-regions-from-your-database-account)

* [Azure PowerShell](manage-with-powershell.md#create-account)

* [CLI do Azure](manage-with-cli.md#add-or-remove-regions)

* [Modelos de gestor de recursos Azure](./manage-with-templates.md)

## <a name="building-highly-available-applications"></a>Construir aplicações altamente disponíveis

* Reveja o comportamento esperado [dos SDKs Azure Cosmos](troubleshoot-sdk-availability.md) durante estes eventos e quais são as configurações que o afetam.

* Para garantir uma elevada disponibilidade de escrita e leitura, configure a sua conta Azure Cosmos para abranger pelo menos duas regiões com regiões de escrita múltipla. Esta configuração proporcionará a maior disponibilidade, menor latência e melhor escalabilidade para tanto leituras como escritos apoiados por SLAs. Para saber mais, veja como configurar a [sua conta Azure Cosmos com várias regiões de escrita.](tutorial-global-distribution-sql-api.md)

* Para contas Azure Cosmos multi-regiões configuradas com uma região de escrita única, [permita a falha automática utilizando o portal Azure CLI ou Azure](how-to-manage-database-account.md#automatic-failover). Depois de ativar o failover automático, sempre que houver um desastre regional, a Cosmos DB falhará automaticamente na sua conta.  

* Mesmo que a sua conta Azure Cosmos esteja altamente disponível, a sua aplicação pode não estar corretamente concebida para permanecer altamente disponível. Para testar a elevada disponibilidade da sua aplicação, como parte dos exercícios de teste de aplicação ou recuperação de desastres (DR), desativar temporariamente a falha automática da conta, invocar a falha manual utilizando o [powerShell, o portal Azure CLI ou a Azure,](how-to-manage-database-account.md#manual-failover)e depois monitorizar a falha da sua aplicação. Uma vez concluído, pode voltar à região primária e restaurar a falha automática da conta.

* Dentro de um ambiente de base de dados distribuído globalmente, existe uma relação direta entre o nível de consistência e a durabilidade dos dados na presença de uma paralisação a nível regional. À medida que desenvolve o seu plano de continuidade de negócios, precisa entender o tempo máximo aceitável antes que a aplicação recupere totalmente após um evento disruptivo. O tempo necessário para uma aplicação de recuperação total é conhecido como objetivo do tempo de recuperação (RTO). Também precisa entender o período máximo de atualizações de dados recentes que a aplicação pode tolerar perder ao recuperar após um evento disruptivo. O período de tempo de atualizações que poderá perder é conhecido como o objetivo de ponto de recuperação (RPO). Para ver o RPO e o RTO para Azure Cosmos DB, consulte [os níveis de consistência e durabilidade dos dados](./consistency-levels.md#rto)

## <a name="next-steps"></a>Próximos passos

Em seguida, pode ler os seguintes artigos:

* [Disponibilidade e compensações de desempenho para vários níveis de consistência](./consistency-levels.md)

* [Dimensionamento global de débito aprovisionado](./request-units.md)

* [Distribuição global - em segundo plano](global-dist-under-the-hood.md)

* [Níveis de consistência no Azure Cosmos DB](consistency-levels.md)

* [Como configurar a sua conta Cosmos com várias regiões de escrita](how-to-multi-master.md)

* [Comportamento do SDK em ambientes multi-regionais](troubleshoot-sdk-availability.md)