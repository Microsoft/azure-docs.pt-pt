---
title: Elevada disponibilidade no Azure Cosmos DB
description: Este artigo descreve como o Azure Cosmos DB fornece elevada disponibilidade
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 3/24/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 1e866560ceab342f08a98ba3db05980a2b0947d2
ms.sourcegitcommit: 280d9348b53b16e068cf8615a15b958fccad366a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58407560"
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

- **Contas de várias regiões com uma região de escrita única (falha de região de escrita):** Durante uma falha de região de escrita, estas contas serão permanecem altamente disponíveis para leitura. No entanto, para escritas tem **"ativar a ativação pós-falha automática"** no seu Cosmos conta para ativação pós-falha, a região afetada para outra região. A ativação pós-falha irá ocorrer por ordem de prioridade da região que especificou. Quando a região afetada esteja novamente online, os dados não replicados presentes na região de escrita afetados durante o período de inatividade são disponibilizados através do [conflitos de feed](how-to-manage-conflicts.md#read-from-conflict-feed). Aplicações podem ler os conflitos de feed, resolver conflitos com base na lógica específico do aplicativo e escrever os dados atualizados de volta para o contentor do Cosmos conforme apropriado. Assim que a região de escrita anteriormente afetado recupera, esta fica automaticamente disponível como uma região de leitura. Pode invocar uma ativação pós-falha manual e configurar a região afetada como a região de escrita. Novamente pode fazer uma ativação pós-falha manual usando [CLI do Azure ou o portal do Azure](how-to-manage-database-account.md#manual-failover). Há **sem perda de dados ou disponibilidade** antes, durante ou após a ativação pós-falha manual. A aplicação continua a ser de elevada disponibilidade. 

- **Contas de várias regiões com uma região de escrita única (falha de região de leitura):** Durante uma falha de região de leitura, estas contas irão continuar altamente disponíveis para leituras e gravações. A região afetada é automaticamente desligada a partir da região de escrita e será marcada como offline. O [Cosmos DB SDKs](sql-api-sdk-dotnet.md) redirecionamento lerá chamadas para a próxima região disponível na lista de região preferencial. Se nenhuma das regiões na lista de região preferencial estiver disponível, chamadas automaticamente revertam para a região de escrita atual. Sem alterações são necessárias no código da aplicação para lidar com indisponibilidade da região de leitura. Eventualmente, quando a região afetada esteja novamente online, a região de leitura anteriormente afetado serão automaticamente sincronizados com a região de escrita atual e vai estar disponível novamente para atender a solicitações de leitura. Leituras subseqüentes são redirecionadas para a região recuperada sem exigir alterações ao código da aplicação. Durante a ativação pós-falha tanto rejoining de uma região anteriormente falhada, garantias continuam a ser cumpridas do Cosmos DB de consistência de leitura.

- Contas de região única podem perder disponibilidade após uma falha regional. É sempre recomendado para configurar **, pelo menos, duas regiões** (de preferência, pelo menos dois escrever regiões) na sua conta do Cosmos para garantir a elevada disponibilidade em todos os momentos.

- Mesmo num evento extremamente raro e uma pena quando a região do Azure é permanentemente irrecuperável, não é sem perda de dados se a sua conta do Cosmos de várias regiões é configurada com o nível de consistência predefinido dos *forte*. Em caso de uma região de escrita permanentemente irrecuperável, para as contas do Cosmos de várias regiões configurado com consistência de prescrição vinculada, a janela de perda de dados potenciais é restrita para a janela de envelhecimento (*K* ou *T*); na sessão, níveis de prefixo consistente e eventual consistência, a janela de perda de dados potenciais está limitada a um máximo de cinco segundos.

## <a name="building-highly-available-applications"></a>Criar aplicações de elevada disponibilidade

- Certifique-se de escrita elevada e disponibilidade de leitura, configure a conta do Cosmos para abranger, pelo menos, duas regiões com a escrita de várias regiões. Esta configuração irá fornecer a maior disponibilidade, latência mais baixa e melhor escalabilidade para as leituras e escritas isto suportado por SLAs. Para obter mais informações, consulte como [configure a conta do Cosmos com várias regiões de escrita](tutorial-global-distribution-sql-api.md).

- Para contas do Cosmos de várias regiões que estão configuradas com uma região de escrita única [ativar a ativação pós-falha automática utilizando a CLI do Azure ou o portal do Azure](how-to-manage-database-account.md#automatic-failover). Depois de ativar a ativação pós-falha automática, sempre que houver um desastre regional, Cosmos DB será automaticamente ativação pós-falha sua conta.  

- Mesmo que a sua conta do Cosmos está altamente disponível, seu aplicativo pode não ser criado corretamente para continuar altamente disponível. Para testar a disponibilidade elevada de ponto a ponto da sua aplicação, invocar periodicamente o [ativação pós-falha manual ao utilizar a CLI do Azure ou o portal do Azure](how-to-manage-database-account.md#manual-failover), como parte do seu teste de aplicativos ou a recuperação após desastre (DR) explorações.

- Dentro de um ambiente de base de dados globalmente distribuída existe uma relação direta entre a durabilidade de dados e de nível de consistência na presença de uma interrupção de toda a região. Desenvolver o seu plano de continuidade do negócio, precisa entender o tempo máximo aceitável antes da aplicação recuperar totalmente após um evento problemático. O tempo necessário para uma aplicação recuperar totalmente é conhecido como o objetivo de tempo de recuperação (RTO). Também precisa entender o período máximo de atualizações de dados recentes, a aplicação pode tolerar perder ao recuperar após um evento problemático. O período de tempo de atualizações que poderá estar a perder é conhecido como o objetivo de ponto de recuperação (RPO). Para ver o RTO e RPO para o Azure Cosmos DB, consulte [durabilidade de dados e níveis de consistência](consistency-levels-tradeoffs.md#rto)

## <a name="next-steps"></a>Passos Seguintes

Em seguida pode ler os artigos seguintes:

* [Desvantagens de desempenho e disponibilidade para vários níveis de consistência](consistency-levels-tradeoffs.md)
* [Globalmente dimensionar o débito aprovisionado](scaling-throughput.md)
* [Distribuição global - sob definições avançadas](global-dist-under-the-hood.md)
* [Níveis de consistência no Azure Cosmos DB](consistency-levels.md)
* [Como configurar a sua conta do Cosmos com várias regiões de escrita](how-to-multi-master.md)
