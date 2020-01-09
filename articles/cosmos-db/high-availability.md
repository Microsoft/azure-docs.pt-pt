---
title: Alta disponibilidade no Azure Cosmos DB
description: Este artigo descreve como Azure Cosmos DB fornece alta disponibilidade
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: daa98d703a115e663032639d78f51b26ed1c7ba3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75441858"
---
# <a name="high-availability-with-azure-cosmos-db"></a>Alta disponibilidade com o Azure Cosmos DB

Azure Cosmos DB Replica de forma transparente seus dados em todas as regiões do Azure associadas à sua conta do cosmos. O Cosmos DB emprega várias camadas de redundância para seus dados, conforme mostrado na imagem a seguir:

![Particionamento físico](./media/high-availability/cosmosdb-data-redundancy.png)

- Os dados em contêineres cosmos são [particionados horizontalmente](partitioning-overview.md).

- Dentro de cada região, cada partição é protegida por um conjunto de réplicas com todas as gravações replicadas e permanentemente confirmadas pela maioria das réplicas. As réplicas são distribuídas entre até 10-20 domínios de falha.

- Cada partição em todas as regiões é replicada. Cada região contém todas as partições de dados de um contêiner Cosmos e pode aceitar gravações e fornecer leituras.  

Se sua conta do cosmos for distribuída entre *n* regiões do Azure, haverá pelo menos *n* x 4 cópias de todos os seus dados. Além de fornecer acesso a dados de baixa latência e aumentar a taxa de transferência de gravação/leitura entre as regiões associadas à sua conta do cosmos, ter mais regiões (mais altas *N*) melhora ainda mais a disponibilidade.  

## <a name="slas-for-availability"></a>SLAs para disponibilidade

Como um banco de dados distribuído globalmente, o Cosmos DB fornece SLAs abrangentes que abrangem a taxa de transferência, a latência no 99 º percentil, a consistência e a alta disponibilidade. A tabela a seguir mostra as garantias de alta disponibilidade fornecidas por Cosmos DB para contas de uma e várias regiões. Para alta disponibilidade, sempre configure suas contas do cosmos para ter várias regiões de gravação.

|Tipo de operação  | Região única |Várias regiões (gravações de região única)|Várias regiões (gravações de várias regiões) |
|---------|---------|---------|-------|
|Escritas    | 99,99    |99,99   |99,999|
|Leituras     | 99,99    |99,999  |99,999|

> [!NOTE]
> Na prática, a disponibilidade de gravação real para desatualização limitada, sessão, prefixo consistente e modelos de consistência eventual é significativamente maior do que os SLAs publicados. A disponibilidade de leitura real para todos os níveis de consistência é significativamente maior do que os SLAs publicados.

## <a name="high-availability-with-cosmos-db-in-the-event-of-regional-outages"></a>Alta disponibilidade com Cosmos DB em caso de interrupções regionais

As falhas regionais não são invulgares e o Azure Cosmos DB garante que a disponibilidade da sua base de dados é sempre elevada. Os detalhes a seguir capturam Cosmos DB comportamento durante uma interrupção, dependendo da configuração da sua conta do Cosmos:

- Com Cosmos DB, antes que uma operação de gravação seja confirmada para o cliente, os dados são permanentemente confirmados por um quorum de réplicas dentro da região que aceita as operações de gravação.

- As contas de várias regiões configuradas com regiões de várias gravações estarão altamente disponíveis para gravações e leituras. Failovers regionais são instantâneos e não exigem nenhuma alteração do aplicativo.

- Contas de região única podem perder a disponibilidade após uma interrupção regional. É sempre recomendável configurar **pelo menos duas regiões** (preferencialmente, pelo menos duas regiões de gravação) com sua conta do cosmos para garantir a alta disponibilidade em todos os momentos.

- **Contas de várias regiões com uma região de gravação única (interrupção da região de gravação):**
  - Durante uma interrupção da região de gravação, a conta Cosmos promoverá automaticamente uma região secundária para ser a nova região de gravação primária quando **habilitar o failover automático** for configurado na conta do Azure Cosmos. Quando habilitado, o failover ocorrerá em outra região na ordem de prioridade de região que você especificou.
  - Os clientes também podem optar por usar o **failover manual** e monitorar suas próprias URL de ponto de extremidade de gravação Cosmos usando um agente criado por si só. Para clientes com necessidades de monitoramento de integridade complexas e sofisticadas, isso pode proporcionar um RTO reduzido caso ocorra uma falha na região de gravação.
  - Quando a região afetada anteriormente estiver novamente online, todos os dados de gravação que não foram replicados quando a região falharem serão disponibilizados por meio do [feed de conflitos](how-to-manage-conflicts.md#read-from-conflict-feed). Os aplicativos podem ler o feed de conflitos, resolver os conflitos com base na lógica específica do aplicativo e gravar os dados atualizados de volta no contêiner Cosmos do Azure, conforme apropriado.
  - Depois que a região de gravação afetada anteriormente for recuperada, ela ficará automaticamente disponível como uma região de leitura. Você pode alternar de volta para a região recuperada como a região de gravação. Você pode alternar as regiões usando [CLI do Azure ou portal do Azure](how-to-manage-database-account.md#manual-failover). Não há **perda de dados ou disponibilidade** antes, durante ou depois que você alterna a região de gravação e seu aplicativo continua a ser altamente disponível.

- **Contas de várias regiões com uma região de gravação única (interrupção da região de leitura):**
  - Durante uma interrupção da região de leitura, essas contas permanecerão altamente disponíveis para leituras e gravações.
  - A região afetada é desconectada automaticamente e será marcada como offline. Os [SDKs de Azure Cosmos DB](sql-api-sdk-dotnet.md) redirecionarão as chamadas de leitura para a próxima região disponível na lista de regiões preferenciais.
  - Se nenhuma das regiões na lista de regiões preferenciais estiver disponível, as chamadas revertem automaticamente para a região de escrita atual.
  - Nenhuma alteração é necessária no código do aplicativo para lidar com a interrupção da região de leitura. Eventualmente, quando a região afetada estiver novamente online, a região de leitura afetada anteriormente será sincronizada automaticamente com a região de gravação atual e estará disponível novamente para atender às solicitações de leitura.
  - As leituras subsequentes são redirecionadas para a região recuperada sem necessidade de alterar o código da aplicação. Durante o failover e a rejunção de uma região com falha anteriormente, as garantias de consistência de leitura continuam a ser respeitadas pelo Cosmos DB.

- Mesmo em um evento raro e infeliz quando a região do Azure for permanentemente irrecuperável, não haverá perda de dados se sua conta de Cosmos de várias regiões estiver configurada com consistência *forte* . No caso de uma região de gravação permanentemente irrecuperável, uma conta Cosmos de várias regiões configurada com consistência de desatualização limitada, a janela potencial de perda de dados é restrita à janela de desatualização (*k* ou *T*) em que k = 100.000 atualizações e T = 5 minutos. Para os níveis de sessão, de prefixo consistente e de consistência eventual, a janela potencial de perda de dados é restrita a um máximo de 15 minutos. Para obter mais informações sobre os destinos RTO e RPO para Azure Cosmos DB, consulte [níveis de consistência e durabilidade de dados](consistency-levels-tradeoffs.md#rto)

## <a name="availability-zone-support"></a>Suporte à zona de disponibilidade

Além da resiliência entre regiões, agora você pode habilitar a **redundância de zona** ao selecionar uma região para associar ao banco de dados Cosmos do Azure.

Com o suporte à zona de disponibilidade, Azure Cosmos DB garantirá que as réplicas sejam colocadas em várias zonas em uma determinada região para fornecer alta disponibilidade e resiliência durante falhas zonais. Não há nenhuma alteração na latência e outros SLAs nessa configuração. No caso de uma falha de zona única, a redundância de zona fornece durabilidade total de dados com RPO = 0 e disponibilidade com RTO = 0.

A redundância de zona é uma *funcionalidade suplementar* para o recurso de [replicação de vários mestres](how-to-multi-master.md) . Não pode confiar na redundância de zona isoladamente para garantir a resiliência regional. Por exemplo, no caso de interrupções regionais ou acesso de baixa latência em todas as regiões, é aconselhável ter várias regiões de gravação além da redundância de zona.

Ao configurar gravações de várias regiões para sua conta do Azure Cosmos, você pode aceitar a redundância de zona sem custo adicional. Caso contrário, consulte a observação abaixo sobre os preços do suporte à redundância de zona. Pode ativar a redundância de zona numa região existente da sua conta do Azure Cosmos ao remover a região e ao voltar a adicioná-la com a redundância de zona ativada.

Esse recurso está disponível nas seguintes regiões do Azure:

- Sul do Reino Unido

- Sudeste Asiático

- Este dos E.U.A.

- Este dos E.U.A. 2

- Centro dos E.U.A.

- Europa Ocidental

- E.U.A. Oeste 2

> [!NOTE]
> A habilitação de Zonas de Disponibilidade para uma única conta do Azure Cosmos de região resultará em encargos equivalentes à adição de uma região adicional à sua conta. Para obter detalhes sobre preços, consulte a [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/) e o custo de [várias regiões em artigos Azure Cosmos DB](optimize-cost-regions.md) .

A tabela a seguir resume a capacidade de alta disponibilidade de várias configurações de conta:

|KPI  |Região única sem Zonas de Disponibilidade (não AZ)  |Região única com Zonas de Disponibilidade (AZ)  |Gravações de várias regiões com Zonas de Disponibilidade (AZ, 2 regiões) – configuração mais recomendada |
|---------|---------|---------|---------|
|SLA de disponibilidade de gravação | 99,99% | 99,99% | 99,999% |
|SLA de leitura de disponibilidade  | 99,99% | 99,99% | 99,999% |
|Preço | Taxa de cobrança de região única | Taxa de cobrança da zona de disponibilidade de região única | Taxa de cobrança de várias regiões |
|Falhas de zona – perda de dados | Perda de dados | Sem perda de dados | Sem perda de dados |
|Falhas de zona – disponibilidade | Perda de disponibilidade | Sem perda de disponibilidade | Sem perda de disponibilidade |
|Latência de leitura | Entre regiões | Entre regiões | Baixo |
|Latência de gravação | Entre regiões | Entre regiões | Baixo |
|Interrupção regional – perda de dados | Perda de dados |  Perda de dados | Perda de dados <br/><br/> Ao usar a consistência de desatualização limitada com vários mestres e mais de uma região, a perda de dados é limitada à desatualização limitada configurada em sua conta. <br/><br/> A perda de dados durante a interrupção regional pode ser evitada com a configuração de uma forte consistência com várias regiões. Essa opção é obtida com compensações que afetam a disponibilidade e o desempenho.      |
|Interrupção regional – disponibilidade | Perda de disponibilidade | Perda de disponibilidade | Sem perda de disponibilidade |
|Débito | Taxa de transferência provisionada X RU/s | Taxa de transferência provisionada X RU/s | taxa de transferência provisionada de 2 RU/s <br/><br/> Esse modo de configuração requer duas vezes a quantidade de taxa de transferência quando comparado a uma única região com Zonas de Disponibilidade porque há duas regiões. |

> [!NOTE]
> Para habilitar o suporte à zona de disponibilidade para uma conta do Azure Cosmos de várias regiões, a conta deve ter gravações de vários mestres habilitadas.

Você pode habilitar a redundância de zona ao adicionar uma região a contas novas ou existentes do Azure Cosmos. Para habilitar a redundância de zona em sua conta do Azure Cosmos, você deve definir o sinalizador `isZoneRedundant` como `true` para um local específico. Você pode definir esse sinalizador dentro da propriedade Locations. Por exemplo, o seguinte trecho do PowerShell habilita a redundância de zona para a região "sudeste asiático":

```powershell
$locations = @(
    @{ "locationName"="Southeast Asia"; "failoverPriority"=0; "isZoneRedundant"= "true" },
    @{ "locationName"="East US"; "failoverPriority"=1 }
)
```

O comando a seguir mostra como habilitar a redundância de zona para as regiões "Eastus" e "WestUS2":

```azurecli-interactive
az cosmosdb create \
  --name mycosmosdbaccount \
  --resource-group myResourceGroup \
  --kind GlobalDocumentDB \
  --default-consistency-level Session \
  --locations regionName=EastUS failoverPriority=0 isZoneRedundant=True \
  --locations regionName=WestUS2 failoverPriority=1 isZoneRedundant=True
```

Você pode habilitar Zonas de Disponibilidade usando portal do Azure ao criar uma conta do Azure Cosmos. Ao criar uma conta, certifique-se de habilitar a **redundância geográfica**, as **gravações de várias regiões**e escolher uma região onde zonas de disponibilidade têm suporte:

![Habilitar Zonas de Disponibilidade usando portal do Azure](./media/high-availability/enable-availability-zones-using-portal.png) 

## <a name="building-highly-available-applications"></a>Criando aplicativos altamente disponíveis

- Para garantir alta disponibilidade de gravação e leitura, configure sua conta do cosmos para abranger pelo menos duas regiões com regiões de várias gravações. Essa configuração fornecerá a mais alta disponibilidade, a menor latência e a melhor escalabilidade para leituras e gravações apoiadas por SLAs. Para saber mais, Confira como [configurar sua conta do cosmos com várias regiões de gravação](tutorial-global-distribution-sql-api.md).

- Para contas de Cosmos de várias regiões configuradas com uma região de gravação única, [habilite o failover automático usando CLI do Azure ou portal do Azure](how-to-manage-database-account.md#automatic-failover). Depois de habilitar o failover automático, sempre que houver um desastre regional, Cosmos DB fará automaticamente o failover da sua conta.  

- Mesmo que sua conta do cosmos esteja altamente disponível, seu aplicativo pode não ter sido projetado corretamente para permanecer altamente disponível. Para testar a alta disponibilidade de ponta a ponta de seu aplicativo, como parte do teste de aplicativo ou de recuperação de desastres (DR), Desabilite temporariamente o failover automático da conta, invoque o [failover manual usando CLI do Azure ou portal do Azure](how-to-manage-database-account.md#manual-failover)e, em seguida, monitore o failover do aplicativo. Uma vez concluído, você pode fazer failover para a região primária e restaurar o failover automático para a conta.

- Em um ambiente de banco de dados distribuído globalmente, há uma relação direta entre o nível de consistência e a durabilidade dos dados na presença de uma interrupção em toda a região. Ao desenvolver seu plano de continuidade de negócios, você precisa entender o tempo máximo aceitável antes que o aplicativo se recupere completamente após um evento de interrupção. O tempo necessário para que um aplicativo se recupere totalmente é conhecido como RTO (objetivo de tempo de recuperação). Você também precisa entender o período máximo de atualizações de dados recentes que o aplicativo pode tolerar perder ao recuperar após um evento de interrupção. O período de tempo de atualizações que poderá perder é conhecido como o objetivo de ponto de recuperação (RPO). Para ver o RPO e o RTO para Azure Cosmos DB, consulte [níveis de consistência e durabilidade de dados](consistency-levels-tradeoffs.md#rto)

## <a name="next-steps"></a>Passos seguintes

Em seguida, você pode ler os seguintes artigos:

- [Desvantagens de desempenho e disponibilidade para vários níveis de consistência](consistency-levels-tradeoffs.md)
- [Dimensionamento global da taxa de transferência provisionada](scaling-throughput.md)
- [Distribuição global-nos bastidores](global-dist-under-the-hood.md)
- [Níveis de consistência no Azure Cosmos DB](consistency-levels.md)
- [Como configurar sua conta do cosmos com várias regiões de gravação](how-to-multi-master.md)
