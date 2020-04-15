---
title: Alta disponibilidade em Azure Cosmos DB
description: Este artigo descreve como o Azure Cosmos DB proporciona alta disponibilidade
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 2afeae937d56a84c39167ad55a57c86f2623e52d
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382712"
---
# <a name="high-availability-with-azure-cosmos-db"></a>Elevada disponibilidade com o Azure Cosmos DB

A Azure Cosmos DB replica de forma transparente os seus dados em todas as regiões azure associadas à sua conta Cosmos. Cosmos DB emprega várias camadas de redundância para os seus dados, como mostra a seguinte imagem:

![Partição física](./media/high-availability/cosmosdb-data-redundancy.png)

- Os dados dentro dos contentores cosmos são [horizontalmente divididos.](partitioning-overview.md)

- Em cada região, cada partição é protegida por uma réplica com todas as escritas replicadas e duramente cometidas pela maioria das réplicas. As réplicas são distribuídas por até 10-20 domínios de falha.

- Cada divisão em todas as regiões é replicada. Cada região contém todas as divisórias de dados de um contentor cosmos e pode aceitar escritos e servir leituras.  

Se a sua conta Cosmos for distribuída pelas regiões *de N* Azure, haverá pelo menos *n* x 4 cópias de todos os seus dados. Além de fornecer acesso de dados de baixa latência e escalação de entradas de escrita/leitura em todas as regiões associadas à sua conta Cosmos, ter mais regiões *(N)* mais elevadas melhora ainda mais a disponibilidade.  

## <a name="slas-for-availability"></a>SLAs para disponibilidade

Como base de dados distribuída globalmente, a Cosmos DB fornece SLAs abrangentes que englobam a produção, latência no percentil 99, consistência e alta disponibilidade. O quadro abaixo mostra as garantias de elevada disponibilidade fornecidas pela Cosmos DB para contas únicas e multi-regiões. Para uma elevada disponibilidade, configure sempre as suas contas Cosmos para ter várias regiões de escrita.

|Tipo de operação  | Região única |Multi-região (escreve uma região única)|Multi-região (escreve várias regiões) |
|---------|---------|---------|-------|
|Escreve    | 99,99    |99,99   |99,999|
|Lê     | 99,99    |99,999  |99,999|

> [!NOTE]
> Na prática, a disponibilidade real de escrita para a estagnação limitada, sessão, prefixo consistente e modelos de consistência eventual é significativamente maior do que os SLAs publicados. A disponibilidade de leitura real para todos os níveis de consistência é significativamente maior do que as SLAs publicadas.

## <a name="high-availability-with-cosmos-db-in-the-event-of-regional-outages"></a>High availability with Cosmos DB in the event of regional outages (Elevada disponibilidade com o Cosmos DB em caso de falhas regionais)

As falhas regionais não são invulgares e o Azure Cosmos DB garante que a disponibilidade da sua base de dados é sempre elevada. Os seguintes detalhes captam o comportamento da Cosmos DB durante uma paragem, dependendo da configuração da sua conta Cosmos:

- Com a Cosmos DB, antes de uma operação de escrita ser reconhecida ao cliente, os dados são duramente cometidos por um quórum de réplicas dentro da região que aceita as operações de escrita.

- As contas multi-regiões configuradas com regiões de múltiplas escritas estarão altamente disponíveis tanto para escritos como para leituras. As falhas regionais são instantâneas e não requerem alterações da aplicação.

- As contas unipessoais podem perder a disponibilidade na sequência de uma paralisação regional. É sempre recomendado configurar **pelo menos duas regiões** (de preferência, pelo menos duas regiões de escrita) com a sua conta Cosmos para garantir uma alta disponibilidade em todos os momentos.

### <a name="multi-region-accounts-with-a-single-write-region-write-region-outage"></a>Contas multi-regiões com uma região de escrita única (interrupção da região de escrita)

- Durante uma paragem da região de escrita, a conta Cosmos promoverá automaticamente uma região secundária para ser a nova região de escrita primária quando permitir a **falha automática** é configurada na conta azure cosmos. Quando ativado, a falha ocorrerá a outra região na ordem da prioridade da região que especificou.
- Quando a região anteriormente impactada está novamente online, quaisquer dados de escrita que não foram replicados quando a região falhou, são disponibilizados através do feed de [conflitos.](how-to-manage-conflicts.md#read-from-conflict-feed) As aplicações podem ler os conflitos, resolver os conflitos com base na lógica específica da aplicação, e escrever os dados atualizados de volta ao contentor Azure Cosmos conforme apropriado.
- Uma vez que a região de escrita anteriormente impactada recupera, fica automaticamente disponível como uma região de leitura. Pode voltar para a região recuperada como a região de escrita. Pode mudar as regiões utilizando o [portal PowerShell, Azure CLI ou Azure](how-to-manage-database-account.md#manual-failover). Não há nenhuma perda de **dados ou disponibilidade** antes, durante ou depois de mudar a região de escrita e a sua aplicação continua altamente disponível.

> [!IMPORTANT]
> Recomenda-se vivamente que configure as contas do Azure Cosmos utilizadas para cargas de trabalho de produção para permitir a **falha automática**. A falha manual requer conectividade entre a região de escrita secundária e primária para completar uma verificação de consistência para garantir que não há perda de dados durante a falha. Se a região primária não estiver disponível, esta verificação de consistência não poderá ser concluída e o failover manual não será bem sucedido, resultando na perda de disponibilidade de escrita.

### <a name="multi-region-accounts-with-a-single-write-region-read-region-outage"></a>Contas multi-regiões com uma região de escrita única (ler a paragem da região)

- Durante uma paragem da região de leitura, as contas da Cosmos usando qualquer nível de consistência ou forte consistência com três ou mais regiões lidas permanecerão altamente disponíveis para leituras e escritos.
- A região afetada é desligada automaticamente e será marcada offline. Os [SDKs DB Azure Cosmos](sql-api-sdk-dotnet.md) vão redirecionar as chamadas para a próxima região disponível na lista de região preferida.
- Se nenhuma das regiões na lista de regiões preferenciais estiver disponível, as chamadas revertem automaticamente para a região de escrita atual.
- Não são necessárias alterações no seu código de aplicação para lidar com a interrupção da região. Quando a região de leitura impactada estiver de volta on-line, sincronizará automaticamente com a atual região de escrita e estará novamente disponível para atender pedidos de leitura.
- As leituras subsequentes são redirecionadas para a região recuperada sem necessidade de alterar o código da aplicação. Durante a failover e a reintegração de uma região anteriormente fracassada, as garantias de consistência lidas continuam a ser honradas pela Cosmos DB.

> [!IMPORTANT]
> As contas da Azure Cosmos, usando uma forte consistência com duas ou menos regiões de leitura, perderão a disponibilidade por escrito durante uma paragem da região de leitura, mas manterão a disponibilidade de leitura para as restantes regiões.

- Mesmo num evento raro e infeliz quando a região de Azure é permanentemente irrecuperável, não há perda de dados se a sua conta cosmos multi-região estiver configurada com *forte* consistência. Em caso de uma região de escrita permanentemente irrecuperável, uma conta cosmos multi-região configurada com consistência deslimitada, a janela de perda de dados potencial é restrita à janela de estagnação *(K* ou *T*), onde k=100.000 atualizações e T=5 minutos. Para sessão, prefixo consistente e eventuais níveis de consistência, a janela de perda de dados potencial está restrita a um máximo de 15 minutos. Para obter mais informações sobre os alvos de RTO e RPO para o Azure Cosmos DB, consulte níveis de [consistência e durabilidade](consistency-levels-tradeoffs.md#rto) de dados

## <a name="availability-zone-support"></a>Suporte da Zona de Disponibilidade

Além da resiliência transversal da região, pode agora ativar a **redundância** da zona ao selecionar uma região para se associar à sua base de dados Azure Cosmos.

Com o suporte da Zona de Disponibilidade, o Azure Cosmos DB garantirá que as réplicas são colocadas em várias zonas dentro de uma determinada região para fornecer alta disponibilidade e resiliência durante falhas zonais. Não existem alterações à latência e a outros SLAs nesta configuração. Em caso de falha de zona única, o despedimento da zona proporciona uma durabilidade total dos dados com RPO=0 e disponibilidade com RTO=0.

A redundância da zona é uma *capacidade suplementar* para a funcionalidade [de replicação multi-master.](how-to-multi-master.md) Por si só, não se pode confiar no despedimento de zonas para alcançar a resiliência regional. Por exemplo, em caso de interrupções regionais ou de baixo acesso à latência em todas as regiões, é aconselhável ter várias regiões de escrita, além de redundância de zonas.

Ao configurar os escritos multi-regiões para a sua conta Azure Cosmos, pode optar por redundância de zona sem custos adicionais. Caso contrário, consulte a nota abaixo relativa ao preço do apoio ao despedimento da zona. Você pode permitir a redundância de zona em uma região existente da sua conta Azure Cosmos, removendo a região e adicionando-a de volta com a área de redundância ativada.

Esta funcionalidade está disponível nas seguintes regiões do Azure:

- Sul do Reino Unido

- Ásia Sudeste

- E.U.A. Leste

- E.U.A. Leste 2

- E.U.A. Central

- Europa ocidental

- E.U.A.Oeste 2

> [!NOTE]
> Permitir zonas de disponibilidade para uma única região a conta Azure Cosmos resultará em encargos equivalentes a adicionar uma região adicional à sua conta. Para mais detalhes sobre os preços, consulte a página de [preços](https://azure.microsoft.com/pricing/details/cosmos-db/) e o [custo multi-região nos artigos da Azure Cosmos DB.](optimize-cost-regions.md)

O quadro seguinte resume a elevada capacidade de disponibilidade de várias configurações de conta:

|KPI  |Região única sem Zonas de Disponibilidade (Não-AZ)  |Região única com Zonas de Disponibilidade (AZ)  |Multi-região escreve com Zonas de Disponibilidade (AZ, 2 regiões) – A configuração mais recomendada |
|---------|---------|---------|---------|
|Escreva disponibilidade SLA | 99,99% | 99,99% | 99,999% |
|Ler disponibilidade SLA  | 99,99% | 99,99% | 99,999% |
|Preço | Taxa de faturação da região única | Taxa de faturação da Zona de Disponibilidade da região Única | Taxa de faturação multi-região |
|Falhas na zona – perda de dados | Perda de dados | Sem perda de dados | Sem perda de dados |
|Falhas na zona – disponibilidade | Perda de disponibilidade | Sem perda de disponibilidade | Sem perda de disponibilidade |
|Ler latência | Região transversal | Região transversal | Baixa |
|Escrever latência | Região transversal | Região transversal | Baixa |
|Interrupção regional – perda de dados | Perda de dados |  Perda de dados | Perda de dados <br/><br/> Quando se utiliza a consistência de estagnação limitada com multi-mestre e mais de uma região, a perda de dados limita-se à estagnação limitada configurada na sua conta <br /><br />Pode evitar a perda de dados durante uma paragem regional, configurando uma forte consistência com várias regiões. Esta opção vem com compensações que afetam a disponibilidade e o desempenho. Só pode ser configurado em contas configuradas para escritas de uma região única. |
|Paralisação regional – disponibilidade | Perda de disponibilidade | Perda de disponibilidade | Sem perda de disponibilidade |
|Débito | X RU/s provisão de entrada | X RU/s provisão de entrada | 2X RU/s provisão de entrada <br/><br/> Este modo de configuração requer o dobro da quantidade de entrada em comparação com uma única região com Zonas de Disponibilidade porque existem duas regiões. |

> [!NOTE]
> Para permitir o suporte da Zona de Disponibilidade para uma conta multi-região azure Cosmos, a conta deve ter escritos multi-master habilitados.

Você pode permitir o despedimento de zona ao adicionar uma região a contas novas ou existentes do Azure Cosmos. Para permitir a redundância da zona na sua `isZoneRedundant` conta `true` Azure Cosmos, deverá definir a bandeira para uma localização específica. Você pode colocar esta bandeira dentro da propriedade de locais. Por exemplo, o seguinte corte powerShell permite o despedimento de zonas para a região do "Sudeste Asiático":

```powershell
$locations = @(
    @{ "locationName"="Southeast Asia"; "failoverPriority"=0; "isZoneRedundant"= "true" },
    @{ "locationName"="East US"; "failoverPriority"=1; "isZoneRedundant"= "true" }
)
```

O seguinte comando mostra como permitir o despedimento de zonas para as regiões "EastUS" e "WestUS2":

```azurecli-interactive
az cosmosdb create \
  --name mycosmosdbaccount \
  --resource-group myResourceGroup \
  --kind GlobalDocumentDB \
  --default-consistency-level Session \
  --locations regionName=EastUS failoverPriority=0 isZoneRedundant=True \
  --locations regionName=WestUS2 failoverPriority=1 isZoneRedundant=True
```

Pode ativar zonas de disponibilidade utilizando o portal Azure ao criar uma conta Azure Cosmos. Quando criar uma conta, certifique-se de ativar o **Geo-redundância,** **Escreve multi-região,** e escolha uma região onde as Zonas de Disponibilidade são suportadas:

![Ativar zonas de disponibilidade utilizando o portal Azure](./media/high-availability/enable-availability-zones-using-portal.png) 

## <a name="building-highly-available-applications"></a>Construção de aplicações altamente disponíveis

- Para garantir uma elevada escrita e leitura de disponibilidade, configure a sua conta Cosmos para abranger pelo menos duas regiões com regiões de múltiplas escritas. Esta configuração proporcionará a maior disponibilidade, latência mais baixa e melhor escalabilidade tanto para leituras como para escritos apoiados por SLAs. Para saber mais, veja como configurar a [sua conta Cosmos com várias regiões de escrita.](tutorial-global-distribution-sql-api.md)

- Para contas cosmos multi-regiões que estejam configuradas com uma região de escrita única, permita a [falha automática utilizando o portal Azure CLI ou Azure](how-to-manage-database-account.md#automatic-failover). Depois de ativar a falha automática, sempre que houver um desastre regional, a Cosmos DB falhará automaticamente na sua conta.  

- Mesmo que a sua conta Azure Cosmos esteja altamente disponível, a sua aplicação pode não estar corretamente concebida para permanecer altamente disponível. Para testar a elevada disponibilidade da sua aplicação, como parte dos seus exercícios de teste de aplicação ou recuperação de desastres (DR), desative temporariamente a falha automática para a conta, invoque a [falha manual utilizando o portal PowerShell, Azure CLI ou Azure,](how-to-manage-database-account.md#manual-failover)e, em seguida, monitorize a falha da sua aplicação. Uma vez concluída, pode voltar à região primária e restaurar a falha automática para a conta.

- Num ambiente de base de dados distribuído globalmente, existe uma relação direta entre o nível de consistência e a durabilidade dos dados na presença de uma paralisação em toda a região. À medida que desenvolve o seu plano de continuidade de negócios, precisa de compreender o tempo máximo aceitável antes que a aplicação recupere totalmente após um evento disruptivo. O tempo necessário para uma candidatura para recuperar totalmente é conhecido como objetivo de tempo de recuperação (RTO). Também precisa entender o período máximo de atualizações de dados recentes que a aplicação pode tolerar perder quando se recupera após um evento disruptivo. O período de tempo de atualizações que poderá perder é conhecido como o objetivo de ponto de recuperação (RPO). Para ver o RPO e o RTO para O BD, consulte os níveis de [consistência e a durabilidade](consistency-levels-tradeoffs.md#rto) dos dados

## <a name="next-steps"></a>Passos seguintes

Em seguida, pode ler os seguintes artigos:

- [Trocas de disponibilidade e desempenho para vários níveis de consistência](consistency-levels-tradeoffs.md)
- [Dimensionamento global de débito aprovisionado](scaling-throughput.md)
- [Distribuição global - em segundo plano](global-dist-under-the-hood.md)
- [Níveis de consistência em Azure Cosmos DB](consistency-levels.md)
- [Como configurar a sua conta Cosmos com várias regiões de escrita](how-to-multi-master.md)
