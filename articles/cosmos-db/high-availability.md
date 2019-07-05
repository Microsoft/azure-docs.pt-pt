---
title: Elevada disponibilidade no Azure Cosmos DB
description: Este artigo descreve como o Azure Cosmos DB fornece elevada disponibilidade
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 928c943e21e7d00b87ac1e506b98d47107ac4348
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508570"
---
# <a name="high-availability-with-azure-cosmos-db"></a>Elevada disponibilidade com o Azure Cosmos DB

O Azure Cosmos DB replica de forma transparente os dados em todas as regiões do Azure à conta do Cosmos. O cosmos DB emprega várias camadas de redundância para os seus dados, conforme mostrado na imagem seguinte:

![Criação de partições físicas](./media/high-availability/cosmosdb-data-redundancy.png)

- Os dados dentro de contentores de Cosmos [particionada horizontalmente](partitioning-overview.md).

- Em cada região, cada partição é protegida por um conjunto de réplicas com todas as gravações replicadas e consolidada de maneira duradoura, a maioria das réplicas. As réplicas são distribuídas por domínios de falha de até 10 a 20.

- Cada partição em todas as regiões é replicada. Cada região contém todas as partições de dados de um contentor do Cosmos e pode aceitar escritas e servir leituras.  

Se a sua conta do Cosmos é distribuída em várias *N* regiões do Azure, haverá menos *N* x 4 cópias de todos os seus dados. Além de fornecer acesso de dados de baixa latência e dimensionar o débito de leitura/escrita em todas as regiões à conta do Cosmos, ter mais regiões (superior *N*) melhora ainda mais a disponibilidade.  

## <a name="slas-for-availability"></a>SLAs de disponibilidade

Como uma base de dados globalmente distribuída, o Cosmos DB oferece SLAs abrangentes que abranger o débito, latência no percentil de 99, a consistência e a elevada disponibilidade. A tabela abaixo mostra as garantias de elevada disponibilidade fornecida pelo Cosmos DB para contas únicas e várias regiões. Para elevada disponibilidade, configure sempre as contas do Cosmos para ter várias regiões de escrita.

|Tipo de operação  | Região única |Várias regiões (gravações de região única)|Várias regiões (gravações de várias regiões) |
|---------|---------|---------|-------|
|Escritas    | 99.99    |99.99   |99.999|
|Leituras     | 99.99    |99.999  |99.999|

> [!NOTE]
> Na prática, a disponibilidade de gravação real para estagnação limitada, sessão, prefixo consistente e modelos de consistência eventual é significativamente maior do que os SLAs publicados. A disponibilidade de leitura real para todos os níveis de consistência é significativamente maior do que os SLAs publicados.

## <a name="high-availability-with-cosmos-db-in-the-event-of-regional-outages"></a>Elevada disponibilidade com o Cosmos DB em caso de falhas regionais

Falhas regionais não são invulgares e Azure Cosmos DB torna-se de que a sua base de dados sempre é de elevada disponibilidade. Os seguintes detalhes de capturar o comportamento do Cosmos DB durante um período de inatividade, consoante a configuração de conta do Cosmos:

- Com o Cosmos DB, antes de uma operação de escrita é reconhecida para o cliente, os dados de maneira duradoura consolidados por um quórum de réplicas na região que aceita as operações de escrita.

- Contas de várias regiões configuradas com a escrita de várias regiões será elevada disponibilidade para escritas e leituras. As ativações pós-falha regionais são instantâneas e não necessitam de quaisquer alterações da aplicação.

- **Contas de várias regiões com uma região de escrita única (falha de região de escrita):** Durante uma falha de região de escrita, estas contas serão permanecem altamente disponíveis para leitura. No entanto, para escritas tem **"ativar a ativação pós-falha automática"** no seu Cosmos conta para ativação pós-falha, a região afetada para outra região. A ativação pós-falha irá ocorrer por ordem de prioridade da região que especificou. Quando a região afetada esteja novamente online, os dados unreplicated presentes na região de escrita afetados durante o período de inatividade são disponibilizados através do [conflitos de feed](how-to-manage-conflicts.md#read-from-conflict-feed). Aplicações podem ler os conflitos de feed, resolver conflitos com base na lógica específico do aplicativo e escrever os dados atualizados de volta para o contentor do Cosmos conforme apropriado. Assim que a região de escrita anteriormente afetado recupera, esta fica automaticamente disponível como uma região de leitura. Pode invocar uma ativação pós-falha manual e configurar a região afetada como a região de escrita. Novamente pode fazer uma ativação pós-falha manual usando [CLI do Azure ou o portal do Azure](how-to-manage-database-account.md#manual-failover). Há **sem perda de dados ou disponibilidade** antes, durante ou após a ativação pós-falha manual. A aplicação continua a ser de elevada disponibilidade. 

- **Contas de várias regiões com uma região de escrita única (falha de região de leitura):** Durante uma falha de região de leitura, estas contas irão continuar altamente disponíveis para leituras e gravações. A região afetada é automaticamente desligada a partir da região de escrita e será marcada como offline. O [Cosmos DB SDKs](sql-api-sdk-dotnet.md) redirecionamento lerá chamadas para a próxima região disponível na lista de região preferencial. Se nenhuma das regiões na lista de região preferencial estiver disponível, chamadas automaticamente revertam para a região de escrita atual. Sem alterações são necessárias no código da aplicação para lidar com indisponibilidade da região de leitura. Eventualmente, quando a região afetada esteja novamente online, a região de leitura anteriormente afetado serão automaticamente sincronizados com a região de escrita atual e vai estar disponível novamente para atender a solicitações de leitura. Leituras subseqüentes são redirecionadas para a região recuperada sem exigir alterações ao código da aplicação. Durante a ativação pós-falha tanto rejoining de uma região anteriormente falhada, garantias continuam a ser cumpridas do Cosmos DB de consistência de leitura.

- Contas de região única podem perder disponibilidade após uma falha regional. É sempre recomendado para configurar **, pelo menos, duas regiões** (de preferência, pelo menos dois escrever regiões) na sua conta do Cosmos para garantir a elevada disponibilidade em todos os momentos.

- Mesmo num evento raro e uma pena quando a região do Azure é permanentemente irrecuperável, não é sem perda de dados se a sua conta do Cosmos de várias regiões é configurada com o nível de consistência predefinido dos *forte*. Em caso de uma região de escrita permanentemente irrecuperável, para as contas do Cosmos de várias regiões configurado com consistência de prescrição vinculada, a janela de perda de dados potenciais é restrita para a janela de envelhecimento (*K* ou *T*); na sessão, níveis de prefixo consistente e eventual consistência, a janela de perda de dados potenciais está limitada a um máximo de cinco segundos. 

## <a name="availability-zone-support"></a>Suporte de zona de disponibilidade

O Azure Cosmos DB é um serviço de base de dados distribuída globalmente, vários mestres que fornece elevada disponibilidade e resiliência durante falhas regionais. Além disso cruzar a resiliência de região, agora pode habilitar **redundância de zona** ao selecionar uma região para associar a sua base de dados do Cosmos do Azure. 

Com suporte de zona de disponibilidade, do Azure Cosmos DB garante que as réplicas são colocadas em várias zonas dentro de uma determinada região para fornecer elevada disponibilidade e resiliência durante falhas zonais. Não foram efetuadas alterações a latência e outros SLAs nesta configuração. No caso de falha de uma única zona, redundância de zona fornece a durabilidade de dados completo com RPO = 0 e disponibilidade com RTO = 0. 

Redundância de zona é um *capacidade suplementar* para o [replicação multimestre](how-to-multi-master.md) funcionalidade. Redundância de zona apenas não pode ser confiada para obter resiliência regional. Por exemplo, em caso de falhas regionais ou acesso de baixa latência em todas as regiões, é recomendado que tenha várias regiões de escrita, além de redundância de zona. 

Ao configurar gravações de várias regiões para a sua conta do Cosmos do Azure, pode optar por receber redundância de zona ao não custos adicionais. Caso contrário, consulte a nota abaixo sobre os preços para o suporte de redundância de zona. Pode ativar a redundância de zona numa região existente da sua conta do Cosmos do Azure ao remover a região e adicioná-la novamente com a redundância de zona ativada.

Esta funcionalidade está disponível nas seguintes regiões do Azure:

* Reino Unido Sul
* Sudeste Asiático 
* East US
* EUA Leste 2 
* EUA Central

> [!NOTE] 
> Ativar zonas de disponibilidade para uma conta do Cosmos do Azure de região única resulta em custos que são equivalentes à adição de uma região adicional à sua conta. Para obter detalhes sobre preços, consulte a [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/) e o [custo de várias regiões do Azure Cosmos DB](optimize-cost-regions.md) artigos. 

A tabela seguinte resume a capacidade de elevada disponibilidade de várias configurações de conta: 

|KPI  |Região única sem zonas de disponibilidade (não-AZ)  |Região única com zonas de disponibilidade (AZ)  |Escritas de várias regiões com zonas de disponibilidade (AZ, 2 regiões) – a maioria de configuração recomendada |
|---------|---------|---------|---------|
|SLA de disponibilidade de escrita     |   99,99%      |    99,99%     |  99,999%  |
|Ler o SLA de disponibilidade   |   99,99%      |   99,99%      |  99,999%       |
|Preço  |  Taxa de faturação de região única |  Taxa de faturação de zona de disponibilidade de região única |  Taxa de faturação de várias regiões       |
|Falhas de zona – perda de dados   |  Perda de dados  |   Sem perda de dados |   Sem perda de dados  |
|Falhas de zona – disponibilidade |  Perda de disponibilidade  | Sem perda de disponibilidade  |  Sem perda de disponibilidade  |
|Latência de leitura    |  Cross região    |   Cross região   |    Baixa  |
|Latência de escrita    |   Cross região   |  Cross região    |   Baixa   |
|Falha regional – perda de dados    |   Perda de dados      |  Perda de dados       |   Perda de dados <br/><br/> Quando utilizar ligados a consistência de envelhecimento com principal múltiplo e mais do que uma região, perda de dados está limitada a estagnação limitada configurada na sua conta. <br/><br/> Perda de dados durante uma falha regional, pode ser evitada ao configurar a consistência forte com várias regiões. Esta opção é fornecido com as compensações que afetam a disponibilidade e desempenho.      |
|Falha regional – disponibilidade  |  Perda de disponibilidade       |  Perda de disponibilidade       |  Sem perda de disponibilidade  |
|Débito    |  X RU/s aprovisionada débito      |  X RU/s aprovisionada débito       |  2 x débito aprovisionado de RU/s <br/><br/> Esse modo de configuração exige duas vezes a quantidade de débito em comparação com uma única região com zonas de disponibilidade devido a existirem duas regiões.   |

> [!NOTE] 
> Para ativar o suporte de zona de disponibilidade, a conta do Azure Cosmos DB tem de ter gravações de várias-master/várias-region ativadas. 

Pode ativar a redundância de zona ao adicionar uma região para contas do Cosmos do Azure novas ou existentes. Atualmente, apenas pode ativar a redundância de zona ao utilizar o Azure portal, modelos de PowerShell e Azure Resource Manager. Para ativar a redundância de zona na sua conta do Cosmos do Azure, deve definir os `isZoneRedundant` sinalizador para `true` para uma localização específica. Pode definir este sinalizador dentro da propriedade de localizações. Por exemplo, o seguinte fragmento de powershell permite que a redundância de zona para a região "Sudeste Asiático":

```powershell
$locations = @( 
    @{ "locationName"="Southeast Asia"; "failoverPriority"=0; "isZoneRedundant"= "true" }, 
    @{ "locationName"="East US"; "failoverPriority"=1 } 
) 
```

Pode ativar as zonas de disponibilidade com o portal do Azure quando criar uma conta do Cosmos do Azure. Quando criar uma conta, certifique-se ativar a **georredundância**, **escreve de várias regiões**e escolha uma região onde as zonas de disponibilidade são suportadas: 

![Ativar zonas de disponibilidade com o portal do Azure](./media/high-availability/enable-availability-zones-using-portal.png) 

## <a name="building-highly-available-applications"></a>Criar aplicações de elevada disponibilidade

- Certifique-se de escrita elevada e disponibilidade de leitura, configure a conta do Cosmos para abranger, pelo menos, duas regiões com a escrita de várias regiões. Esta configuração irá fornecer a maior disponibilidade, latência mais baixa e melhor escalabilidade para as leituras e escritas isto suportado por SLAs. Para obter mais informações, consulte como [configure a conta do Cosmos com várias regiões de escrita](tutorial-global-distribution-sql-api.md).

- Para contas do Cosmos de várias regiões que estão configuradas com uma região de escrita única [ativar a ativação pós-falha automática utilizando a CLI do Azure ou o portal do Azure](how-to-manage-database-account.md#automatic-failover). Depois de ativar a ativação pós-falha automática, sempre que houver um desastre regional, Cosmos DB será automaticamente ativação pós-falha sua conta.  

- Mesmo que a sua conta do Cosmos está altamente disponível, seu aplicativo pode não ser criado corretamente para continuar altamente disponível. Para testar a disponibilidade elevada de ponto a ponto da sua aplicação, invocar periodicamente o [ativação pós-falha manual ao utilizar a CLI do Azure ou o portal do Azure](how-to-manage-database-account.md#manual-failover), como parte do seu teste de aplicativos ou a recuperação após desastre (DR) explorações.

- Dentro de um ambiente de base de dados globalmente distribuída, existe uma relação direta entre a durabilidade de dados e de nível de consistência na presença de uma interrupção de toda a região. Desenvolver o seu plano de continuidade do negócio, precisa entender o tempo máximo aceitável antes da aplicação recuperar totalmente após um evento problemático. O tempo necessário para uma aplicação recuperar totalmente é conhecido como o objetivo de tempo de recuperação (RTO). Também precisa entender o período máximo de atualizações de dados recentes, a aplicação pode tolerar perder ao recuperar após um evento problemático. O período de tempo de atualizações que poderá perder é conhecido como o objetivo de ponto de recuperação (RPO). Para ver o RTO e RPO para o Azure Cosmos DB, consulte [durabilidade de dados e níveis de consistência](consistency-levels-tradeoffs.md#rto)

## <a name="next-steps"></a>Passos Seguintes

Em seguida pode ler os artigos seguintes:

* [Desvantagens de desempenho e disponibilidade para vários níveis de consistência](consistency-levels-tradeoffs.md)
* [Globalmente dimensionar o débito aprovisionado](scaling-throughput.md)
* [Distribuição global - sob definições avançadas](global-dist-under-the-hood.md)
* [Níveis de consistência no Azure Cosmos DB](consistency-levels.md)
* [Como configurar a sua conta do Cosmos com várias regiões de escrita](how-to-multi-master.md)
